<template>
    <div class="card mt-2">
        <div class="card-header">
            <h2>Recensioni</h2>
        </div>

        <div class="card-body">
            <div v-for="(review, index) in reviews" class="my_review_container">
                <div :class="['my_reviews mb-3', { 'no_border': isLastReview(index) }]">
                    <p class="fs-5">"{{ review.content }}"</p>
                    <span v-for="n in 5" :class="getVoteIcon(n, review.vote)"></span>
                    <p class="my_fs">Scritta in data: {{ review.created_at.slice(0, 10) }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'MusicianReview',
    data() {
        return {
            apiUrl: "http://127.0.0.1:8000/api/musicians",
            content: '',
            vote: '',
            musicianId: '',

            reviews: []

        };
    },
    methods: {

        GetMusiciansApi() {
            axios.get(`${this.apiUrl}/${this.$route.params.id}`).then((response) => {
                this.musicianId = response.data.results.id;

                this.reviews = response.data.results.reviews;
            });
        },

        getVoteIcon(iconNumber, vote) {
            return {
                "fa-solid fa-music pe-2 pb-3 text-primary": iconNumber <= vote,
                "fa-solid fa-music pe-2 pb-3 text-secondary": iconNumber > vote
            }
        },

        isLastReview(index) {
            return index === this.reviews.length - 1;
        }
    },
    created() {
        this.GetMusiciansApi();
    },
}
</script>

<style lang="scss">
div.my_reviews {
    border-bottom: .5px solid lightgray;

    p.my_fs {
        font-size: .8rem;
    }
}

div.my_reviews.no_border {
    border-bottom: none;
}

div.card-header {
    background-color: #ffffff;
}

//colore arancione: #f88936
</style>