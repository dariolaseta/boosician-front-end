<template>

<section class="results-zone justify-content-center py-3">



                <div class="container">
                    <div class="row">
                        <div class="col-lg-4 col-md-6 col-sm-12" v-for="musician in filterActiveMusicians()" :key="musician.id" :class="getSponsorClass(musician)">
                        <MusicianCard :musicianInfo="musician" />
                              <!-- <p>Tipo di Sponsor: {{ getSponsorType(musician) }}</p> -->
                              <!-- <p v-if="getSponsorType(musician) === 'gold'">
                                <i class="fa-solid fa-certificate" style="color: gold;"></i>
                              </p>
                              <p v-else-if="getSponsorType(musician) === 'silver'">
                                <i class="fa-solid fa-certificate" style="color: silver;"></i>
                              </p>
                              <p v-else>
                                <i class="fa-solid fa-certificate" style="color: chocolate;"></i>
                              </p> -->
                        </div>
                    </div>
                    <!--Rotella caricamento-->
                    <div class="my_loading_container d-flex align-items-center justify-content-center" v-if="isLoading">
                        <div class="my_loading_anim"></div>
                    </div>

                </div>

            </section>
</template>




<script>
import axios from 'axios';
import MusicianCard from '../components/MusicianCard.vue'

export default {
    name: 'SponsoredMusician',

    data(){
        return{
            apiUrl:"http://127.0.0.1:8000/api/musicians",
            //apiUserUrl: "http://127.0.0.1:8000/api/user",
            //apiInstrumentUrl: "http://127.0.0.1:8000/api/instrument",
            //apiReviewUrl: "http://127.0.0.1:8000/api/review",
            musicians : [],
            filteredMusicians : [],
            orderedMusicians : [],
            orderedSumMusicians : [],
            mergedMusicians : [],

            filteredText : '',

            isLoading: true,

            canShow: true,

            isAverage: false,
            isTotal: false,

            sliderValue: 0,
            genreValue: '',

            averageNumFromChild: 0,
        }
    },

    components:{
        MusicianCard
    },

    methods: {
        getMusiciansApi(){
            axios.get(this.apiUrl)
            .then((response)=> {
                this.musicians=response.data.results.data;
                this.filteredMusicians = this.musicians;
            })
            .catch(function (error) {
                console.log(error);
            })
        },

        isMusicianActive(musician) {
        // Verifica se "sponsors" è un array valido
        if (Array.isArray(musician.sponsors)) {
            // Cicla attraverso gli sponsor
            for (const sponsor of musician.sponsors) {
            // Verifica se "pivot.active" è uguale a 1
            if (sponsor.pivot && sponsor.pivot.active === 1) {
                return true; // Il musicista è attivo
            }
            }
        }
            return false; // Il musicista non è attivo o "sponsors" non è un array valido
        },


        filterActiveMusicians() {
        return this.filteredMusicians.filter((musician) => {
            return this.isMusicianActive(musician);
            });
        },


        getSponsorType(musician) {
            // Verifica se "sponsors" è un array valido all'interno dell'oggetto "musician"
            if (Array.isArray(musician.sponsors)) {
                // Cicla attraverso gli sponsor
                for (const sponsor of musician.sponsors) {
                // Verifica se "sponsor_type" esiste ed è una stringa valida
                if (sponsor.sponsor_type && typeof sponsor.sponsor_type === 'string') {
                    // console.log(sponsor.sponsor_type);
                    return sponsor.sponsor_type; // Restituisci il tipo di sponsor
                }
                }
            }
            return ''; // Restituisci una stringa vuota se il tipo di sponsor non è trovato o "sponsors" non è un array valido
            },


            getSponsorClass(musician) {
                // Verifica il valore di "sponsor_type" e restituisci una classe appropriata
                if (musician.sponsors && musician.sponsors.length > 0) {
                const sponsorType = musician.sponsors[0].sponsor_type;
                if (sponsorType === 'gold') {
                    return 'gold-sponsor'; // Classe CSS per gli sponsor di tipo "gold"
                } else if (sponsorType === 'silver') {
                    return 'silver-sponsor'; // Classe CSS per gli sponsor di tipo "silver"
                }
                }
                // Restituisci una classe predefinita se non è presente uno sponsor o il tipo di sponsor non è noto
                return 'default-sponsor';
            },

        getMusiciansSumApi() {
            axios.get(this.apiUrl)
                .then((response) => {
                this.musicians = response.data.results.data;
                this.filteredMusicians = this.musicians;

                this.orderedSumMusicians = this.musicians.map((musician) => {
                    const totalVotes = musician.reviews.reduce((sum, review) => sum + review.vote, 0);
                    musician.totalVotes = totalVotes;
                    return musician;
                });

                this.orderedSumMusicians.sort((a, b) => b.totalVotes - a.totalVotes);

                this.originalOrderedSumMusicians = [...this.orderedSumMusicians];
                })
                .catch(function (error) {
                console.log(error);
                });
        },


        //Ottiene il value del dato passato nelle parentesi
        getSelectValue(selectValue){
            this.genreValue = selectValue.target.value;

        },


    },

    created() {
        //Chiamata api
        this.getMusiciansApi(),
        this.getMusiciansSumApi(),
        //Rimuove l'animazione del caricamento dopo 1 secondo, da cambiare con un metodo migliore
        setTimeout(() => {
            this.isLoading = false
        }, 1000);
    },
}
</script>




<style lang="scss">
    .gold-sponsor {
    order: 1;
}

.silver-sponsor {
  order: 2;
}

.default-sponsor {
  order: 3;
}
</style>