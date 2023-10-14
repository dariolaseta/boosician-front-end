<template>
    <router-link :to="{name:'SingleMusician', params:{ id: musicianInfo.user_id }}" @click="saveCurrentId(musicianInfo.user_id)">
        <div class="card text-center my-4 my_card p-2">
            <span class="fs-2 text-capitalize">{{ musicianInfo.user.name }}</span>
            <span class="fs-2 text-capitalize">{{ musicianInfo.surname }}</span>
            
            <div class="image">
                <img v-if="musicianInfo.image.startsWith('http')" :src="musicianInfo.image" :alt="musicianInfo.user.name + ' ' + musicianInfo.surname + ' image'">
                <img v-else :src="'http://127.0.0.1:8000/storage/' + musicianInfo.image " :alt="musicianInfo.user.name + ' ' + musicianInfo.surname + ' image'">
            </div>
            
            <p class="fs-4 text-capitalize">
                Strumenti: {{ musicianInfo.musical_instruments.map(instrument => instrument.name).join(', ') }}
            </p>
            <p class="fs-4">Genere: {{ musicianInfo.musical_genre }} </p>
            <p class="fs-4">A serata: {{ musicianInfo.price }} &euro;</p>



            <div>
                <span v-for="n in getAvarage()">
                    <i class="fa-solid fa-music pe-2 text-primary"></i>
                </span>
                <span v-for="n in ( 5 - getAvarage())">
                    <i class="fa-solid fa-music pe-2 text-secondary"></i>
                </span>
            </div>
        </div>
    </router-link>

</template>

<script>

export default {
    name:'MusicianCard',

    data(){
        return{
            averageNum: 0,
        }


    },

    props:{
        musicianInfo : Object,
        userInfo : Object,
    },

    methods:{
        saveCurrentId(info){
            localStorage.setItem('currentUserId',info)
            console.log(localStorage.getItem('currentUserId'))

        },

        getAvarage(){
            let numberOfReview = this.musicianInfo.reviews.length;
            console.log(this.musicianInfo.reviews);
            let totVotes = 0;
            this.musicianInfo.reviews.forEach(element => {
                totVotes = totVotes + element.vote;
            });
            const avarage = totVotes / numberOfReview;

            this.averageNum = Math.floor(avarage);
            this.$emit('average-num', this.averageNum);
            
            return this.averageNum;
        },
    },

    created(){
    },

}
</script>

<style lang="scss" scoped>
div.my_card{
    transition: all .3s ease-in-out;
    z-index: 0;

    div.image{
        img{
            width: 150px;
            height: 150px;
            border-radius: 50%;
            object-fit: cover;
        }
    }
}

div.my_card:hover{
    cursor: pointer;
    transform: scale(1.3);
    z-index: 1;
    box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px, rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px, rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;
}

a{
    text-decoration: none;
    color: black;
}
    
</style>