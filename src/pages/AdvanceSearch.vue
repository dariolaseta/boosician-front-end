<template>
    <div class="d-flex filters">
        <section class="my_bg px-4 py-4">
            <h5>Filtra per:</h5>

            <div class="form-check">
                <input class="form-check-input" type="checkbox" value="" id="flexCheckDefault" v-model="isAverage">
                <label class="form-check-label" for="flexCheckDefault">Media di voto</label>
            </div>

            <div class="form-check">
                <input class="form-check-input" type="checkbox" value="" id="flexCheckDefault" v-model="isTotal">
                <label class="form-check-label" for="flexCheckDefault">Recensioni totali</label>
            </div>

            
        </section>

        <div class="my_container">
            <section class="search-zone">
                <div class=" mx-auto d-flex flex-column align-items-center py-3">
                    <p>Inserisci uno strumento che vuoi cercare</p>
                    <form class="d-flex" role="search" @submit.prevent>
                        <select id="musicalInstruments" v-model="genreValue"  @change="searchBarOnChange">
                            <option value="none">Scegli lo strumento</option>
                            <option value="tromba">tromba</option>
                            <option value="batteria">batteria</option>
                            <option value="chitarra">chitarra</option>
                            <option value="basso">basso</option>
                            <option value="sax">sax</option>
                            <option value="violino">violino</option>
                        </select>
                    </form>
                </div>
            </section>

            <section class="results-zone justify-content-center py-3">
                <div class="container">
                    <div class="row">
                        <div v-if="isAverage && isTotal" class="col-lg-4 col-md-6 col-sm-12" v-for="musician in mergedMusicians">
                            <MusicianCard :musicianInfo="musician" @average-num="onAverageNumChanged"/>

                        </div>

                        <div v-else-if="isAverage" class="col-lg-4 col-md-6 col-sm-12" v-for="musician in orderedMusicians">
                            
                            <MusicianCard :musicianInfo="musician" @average-num="onAverageNumChanged"/>
                        </div>

                        <div v-else-if="isTotal" class="col-lg-4 col-md-6 col-sm-12" v-for="musician in orderedSumMusicians">
                            
                            <MusicianCard :musicianInfo="musician" @average-num="onAverageNumChanged"/>
                        </div>

                        <div v-else-if="!isTotal && !isAverage" class="col-lg-4 col-md-6 col-sm-12" v-for="musician in filteredMusicians">
                            
                            <MusicianCard :musicianInfo="musician" @average-num="onAverageNumChanged"/>
                        </div>

                    </div>


                    <!--Rotella caricamento-->
                    <div class="my_loading_container d-flex align-items-center justify-content-center" v-if="isLoading">
                        <div class="my_loading_anim"><div></div><div></div><div></div><div></div></div>
                    </div>

                </div>

            </section>
        </div>
    </div>
</template>



<script>
import axios from 'axios';
import MusicianCard from '../components/MusicianCard.vue'

export default {
    name: 'AdvanceSearch',

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

                this.orderedMusicians = this.musicians.map((musician) => {
                const totalVotes = musician.reviews.reduce((sum, review) => sum + review.vote, 0);
                musician.averageNum = totalVotes / musician.reviews.length;
                return musician;
    });
            
            this.orderedMusicians.sort((a, b) => b.averageNum - a.averageNum);
            

            this.mergedMusicians = this.orderedSumMusicians.concat(this.orderedMusicians);

            this.mergedMusicians.sort((a, b) => {

                if (b.averageNum !== a.averageNum) {
                    return b.averageNum - a.averageNum;
                }

                this.originalOrderedMusicians = [...this.orderedMusicians];
                

                return b.totalVotes - a.totalVotes;
            });
            })
            .catch(function (error) {
                console.log(error);
            })
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


        searchBarOnChange() {
    if (this.genreValue !== 'none') {

        this.filteredMusicians = this.musicians.filter((musician) => {
            return musician.musical_instruments.some((instrument) => {
                return instrument.name.toLowerCase() === this.genreValue;
            });
        });
        this.orderedMusicians = this.originalOrderedMusicians.filter((musician) => {
            return musician.musical_instruments.some((instrument) => {
                return instrument.name.toLowerCase() === this.genreValue;
            });
        });
        this.orderedSumMusicians = this.originalOrderedSumMusicians.filter((musician) => {
            return musician.musical_instruments.some((instrument) => {
                return instrument.name.toLowerCase() === this.genreValue;
            });
        });
        this.mergedMusicians = this.musicians.filter((musician) => {
            return this.filterMusiciansByKeyword([musician], '').length > 0;
        });
    } else {
        // Se "Scegli lo strumento" è selezionato, mostra tutti i musicisti
        this.filteredMusicians = this.musicians;
        this.orderedMusicians = this.originalOrderedMusicians;
        this.orderedSumMusicians = this.originalOrderedSumMusicians;
        this.mergedMusicians = this.musicians;
    }
},


    //     searchBar() {
    //     let searchedText = this.filteredText.toLowerCase();

    //     this.filteredMusicians = this.filterMusiciansByKeyword(this.musicians, searchedText);
    //     this.orderedMusicians = this.filterMusiciansByKeyword(this.originalOrderedMusicians, searchedText);
    //     this.orderedSumMusicians = this.filterMusiciansByKeyword(this.originalOrderedSumMusicians, searchedText);

    //     this.mergedMusicians = this.musicians.filter((musician) => {
    //         return this.filterMusiciansByKeyword([musician], searchedText).length > 0;
    //     });
    // },

    filterMusiciansByKeyword(musiciansArray, keyword) {
        return musiciansArray.filter((musician) => {
            return musician.musical_instruments.some((instrument) => {
                return instrument.name.toLowerCase().includes(keyword);
            });
        });
    },


        onAverageNumChanged(averageNum){
            this.averageNumFromChild = averageNum;
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
    
    mounted() {
    },
}
</script>


<style lang="scss" scoped>
@use '../styles/partials/_variables.scss' as variables;

section.results-zone{
    background-color: variables.$secondaryColor;
    width: calc(100vw - 250px);
}


section.my_bg{
    background-color: variables.$primaryColor;
}

//Animazione caricamento
.my_loading_anim {
    display: inline-block;
    position: relative;
    width: 80px;
    height: 80px;

    div {
    box-sizing: border-box;
    display: block;
    position: absolute;
    width: 64px;
    height: 64px;
    margin: 8px;
    border: 8px solid #fff;
    border-radius: 50%;
    animation: loadingAnim 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
    border-color: #fff transparent transparent transparent;
}

div:nth-child(1) {
    animation-delay: -0.45s;
}

div:nth-child(2) {
    animation-delay: -0.3s;
}

div:nth-child(3) {
    animation-delay: -0.15s;
}

@keyframes loadingAnim {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
}
}



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