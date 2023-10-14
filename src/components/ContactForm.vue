

<template>

    <form @submit.prevent="sendContactForm" @reset.prevent="clearForm" class="d-flex my-3 col-10 pb-3">

        <div class="form-element info" v-if="response === false">
            <h3>
                There are errors in your form:
                {{ errors }}
            </h3>
        </div>

        <div class="form-element">
            <label class="form-label">Inserisci il tuo nome</label>
            <input type="text" class="form-control" v-model="name">
        </div>
        <div class="form-element">
            <label class="form-label">Inserisci mail per esser ricontattati</label>
            <input type="email" class="form-control" v-model="mail">
        </div>

        <div class="form-element">
            <label class="form-label">Scrivi qui il tuo messaggio</label>
            <input type="text" class="form-control" v-model="message">
        </div>

        <div class="task-bar">
            <button type="submit" class="btn btn-outline-success">Invia messaggio</button>
            <button type="reset" class="btn btn-outline-secondary">Resetta form</button>
        </div>

        <div :class="activeAlert">
            <transition name="fade" mode="out-in">
                <div class="my_alert">
                    <p>Messaggio inviato con successo</p>
                </div>
            </transition>
        </div>
    </form>
</template>

<script>
import axios from 'axios';

export default {
    name : 'ContactForm',

    data() {
        return {
            apiUrl: "http://127.0.0.1:8000/api/musicians",
            apiContact: 'http://127.0.0.1:8000/api/contact-form',
            name: '',
            mail: '',
            message: '',
            musicianId: '',
            response: true,

            activeAlert: 'd-none'
        }  
    },
    
    methods: {

        GetMusiciansApi() {
            axios.get(`${this.apiUrl}/${this.$route.params.id}`).then((response) => {
                this.musicianId= response.data.results.id;
                console.log(this.musicianId)
            });
        },

        sendContactForm(){
            const data = {
                name : this.name,
                mail : this.mail,
                message : this.message,
                musician_id : this.musicianId 
            };
            axios
            .post(this.apiContact, data)
            .then((response) =>{
                const responseData = response.data
                console.log(responseData)
                console.log(data)
                if (response.data.success) {
                this.clearForm();
                this.setActiveAlert();
                } else {
                    this.errors = response.data.errors;
                }
            })
            .catch((error) => {
                console.error('Errore Axios:', error);
                this.response = false;
                this.errors = error.response.data ? error.response.data.message : 'Errore sconosciuto';
                // console.log(this.response);
            });
        },

        clearForm() {
            this.name = '';
            this.mail = '';
            this.message = '';

        },

        setActiveAlert(){
            if(this.activeAlert == 'd-none'){
                this.activeAlert = 'd-block my_alert_container';

                setTimeout(() => {
                    this.activeAlert = 'd-none';
                }, 2000);
            }else{
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
    
</style>