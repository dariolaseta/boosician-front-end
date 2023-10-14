

<template>
    <form @submit.prevent="sendReviewForm" @reset.prevent="clearForm" class="d-flex my-3 col-10 pb-3">
        <h3> Inserisci la tua recensione </h3>

        <div class="form-element info" v-if="response === false">
            <h3>
                There are errors in your form:
                {{ errors }}
            </h3>
        </div>

        <div class="form-element">
            <label class="form-label">Recensiona qui il musicista</label>
            <input type="text" class="form-control" v-model="content">
        </div>

        <div class="form-element">
            <label class="form-label">Lascia un voto</label>
            <input type="range" class="form-control" min="1" max="5" v-model="vote">
            <p class="form-text">Voto</p>
            <span class="d-flex justify-content-center align-items-center w-0">
                <i v-for="star in parseInt(vote)" :key="star" class="fa-solid fa-star gold-color px-1"></i>
            </span>
        </div>

        <div class="task-bar">
            <button type="submit" class="btn btn-outline-success mt-4">Invia recensione</button>
            <button type="reset" class="btn btn-outline-secondary mt-4">Resetta form</button>
        </div>

        <div :class="activeAlert">
            <transition name="fade" mode="out-in">
                <div class="my_alert">
                    <p>Recensione inviata con successo</p>
                </div>
            </transition>
        </div>
    </form>
</template>

<script>
import axios from 'axios';

export default {
    name: 'ReviewForm',
    data() {
        return {
            apiUrl: "http://127.0.0.1:8000/api/musicians",
            apiReview: 'http://127.0.0.1:8000/api/review-form',
            content: '',
            vote: 1,
            musicianId: '',

            activeAlert: 'd-none'
        };
    },
    methods: {

        GetMusiciansApi() {
            axios.get(`${this.apiUrl}/${this.$route.params.id}`).then((response) => {
                this.musicianId = response.data.results.id;
            });
        },

        sendReviewForm() {
            const data = {
                content: this.content,
                vote: this.vote,
                musician_id: this.musicianId,
            };
            axios.post(this.apiReview, data)
                .then((response) => {
                    const responseData = response.data
                    //console.log(responseData)
                    //console.log(data)


                    if (responseData.success) {
                        this.clearForm();
                        this.setActiveAlert();
                    }
                    else {
                        this.errors = response.data.errors;
                        console.log(this.errors);
                    }
                })
                .catch((error) => {
                    console.error('Errore Axios:', error);
                    this.response = false;
                    this.errors = error.response.data ? error.response.data.message : 'Errore sconosciuto';
                    console.log(this.response);
                });
        },
        clearForm() {
            this.vote = 1;
            this.content = '';
        },

        setActiveAlert() {
            if (this.activeAlert == 'd-none') {
                this.activeAlert = 'd-block my_alert_container';

                setTimeout(() => {
                    this.activeAlert = 'd-none';
                }, 2000);
            } else {
                this.activeAlert = 'd-none';
            }
        }
    },
    created() {
        this.GetMusiciansApi();
    },
}
</script>

<style lang="scss">
form {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    width: 100%;
    margin: 0 auto;

    div.form-element {
        width: 70%;
        margin-bottom: 1rem;

        * {
            //width: 100%;
        }

        i {
            font-size: 1.4rem;
            color: #FFD700;
        }
    }

    button {
        padding: 1rem 2rem;
        margin-right: 1rem;
    }
}

div.my_alert_container {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 999;

    div.my_alert {
        background-color: white;
        padding: 20px;
        border-radius: 5px;
        text-align: center;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    }
}
</style>