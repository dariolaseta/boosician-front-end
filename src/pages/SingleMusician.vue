<template>
    <div class="container">
        <!-- Scheda tecnica musicista -->
        <div class="row">
            <div class="card col-10 mx-auto p-0 my-4">
                <div class="card-header my_card_header">
                    <h3 class="text-white">Schede Musicista</h3>
                </div>
                <div class="card-body row py-0">
                    <!-- Scheda di sx-->
                    <div class="musicianInfo col-6 d-flex flex-column align-items-center justify-content-around">
                        <h3 class="card-title text-capitalize">
                            {{ user.name }} {{ musicians.surname }}
                        </h3>

                        <div class="imageWrapper">
                            <img v-if="musicians.image.startsWith('http')" :src="musicians.image"
                                :alt="user.name + musicians.surname + ' image'" class="my_propic">
                            <img v-else :src="'http://127.0.0.1:8000/storage/' + musicians.image"
                                :alt="user.name + musicians.surname + ' image'" class="my_propic">
                        </div>

                        <p class="card-text fs-5">
                            {{ musicians.address }}
                        </p>
                    </div>

                    <!-- Scheda di dx -->
                    <div class="musician-skill col-6 border-start">
                        <ul class="list-group list-group-flush">
                            <li class="list-group-item gap-3">
                                <div class="mb-3"><strong>Strumenti :</strong></div>
                                <ul>
                                    <li v-for="instrument in musicalInstrument" class="m-0 text-capitalize">
                                        <p>{{ instrument.name }}</p>
                                    </li>
                                </ul>

                            </li>

                            <li class="list-group-item">
                                <strong>Generi</strong>: {{ musicians.musical_genre }}
                            </li>

                            <li class="list-group-item">
                                <strong>Prezzo</strong>: &euro;{{ musicians.price }}
                            </li>

                            <li class="list-group-item">
                                <span> <strong>Curriculm Vitae</strong> </span>
                                <div class="cvWrapper py-2">
                                    <img v-if="musicians.cv.startsWith('http')" :src="musicians.cv"
                                        :alt="user.name + musicians.surname + ' cv'">
                                    <img v-else :src="'http://127.0.0.1:8000/storage/' + musicians.cv"
                                        :alt="user.name + musicians.surname + ' cv'">
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>

                <!-- Form Recensione -->
                <div class="card-footer text-center">
                    <button type="button" class="my_btn" @click="becomesActiveReview()">Lascia una recensione</button>
                    <ReviewForm :class="activeReview" />
                    <MusicianReview :class="activeReview" />

                </div>
            </div>
        </div>

        <!-- Form Messaggio -->
        <div class="row">
            <div class="card col-10 mx-auto my-4 p-0">
                <div class="card-header">
                    <h3 class="text-white">Contatti</h3>
                </div>
                <div class="card-body">
                    <div class="d-flex justify-content-evenly">
                        <p>
                            Cellulare: {{ musicians.num_phone }}
                        </p>

                        <p>
                            EMAIL : {{ user.email }}
                        </p>
                    </div>
                    <div class="text-center">
                        <button type="button" class="my_btn" @click="becomesActiveMex()">Contatta il musicista</button>
                    </div>
                    <ContactForm :class="activeMex" />
                </div>
            </div>
        </div>
    </div>
</template>



<script>
import axios from 'axios';
import ReviewForm from '../components/ReviewForm.vue';
import ContactForm from '../components/ContactForm.vue';
import MusicianReview from '../components/MusicianReview.vue';


export default {
    name: 'SingleMusician',

    components: {
        ReviewForm,
        ContactForm,
        MusicianReview
    },

    props: [
        'musician',
    ],

    data() {
        return {
            apiUrl: "http://127.0.0.1:8000/api/musicians",
            apiReview: 'http://127.0.0.1:8000/api/review-form',
            musicians: [],
            user: [],

            activeReview: 'd-none',
            activeMex: 'd-none'

        };
    },
    methods: {
        GetMusiciansApi() {
            axios.get(`${this.apiUrl}/${this.$route.params.id}`).then((response) => {
                this.musicians = response.data.results;
                this.musicalInstrument = response.data.results.musical_instruments;
                this.user = response.data.results.user;
                // this.musicians = response.data;
                console.log(this.user);
            });
        },

        becomesActiveReview() {
            if (this.activeReview == 'd-none') {
                this.activeReview = 'd-block';
            } else {
                this.activeReview = 'd-none';
            }
        },

        becomesActiveMex() {
            if (this.activeMex == 'd-none') {
                this.activeMex = 'd-block';
            } else {
                this.activeMex = 'd-none';
            }
        }


    },
    created() {
        this.GetMusiciansApi();

    },
}
</script>



<style lang="scss">
div.imageWrapper {
    img {
        width: 250px;
        height: 250px;
        border-radius: 50%;
        object-fit: cover;
    }
}

div.cvWrapper {
    img {
        width: 100%;
        height: 300px;
    }
}

div.my_bg_orange {
    background-color: #f88936;
}

button.my_btn {
    background-color: #f88936;
    color: white;
    border: none;
    padding: 10px 20px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    margin: 4px 2px;
    cursor: pointer;
    border-radius: .4rem;
}

button.my_btn:hover {
    background-color: #f07313;
}

@media(max-width: 768px) {
    div.imageWrapper {
        img.my_propic {
            width: 100px;
            height: 100px;
        }
    }
}

@media(min-width: 992px) {
    div.imageWrapper {
        img.my_propic {
            width: 250px;
            height: 250px;
        }
    }
}
</style>