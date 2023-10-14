<template >
    <footer class="text-center">
        <ul>
            <li>
                <a href="#">Chi siamo</a>
            </li>

            <li>
                <a href="#">Contatti</a>
            </li>

            <li>
                <a href="#">Partnership</a>
            </li>

            <li>
                <a href="#">Privacy</a>
            </li>

            <li>
                <p class="credits">
                    Questa Web App è stata prodotta e testata da Alessio Giovinetti, Daniel Marella, Dario La Seta, Francesco Valentino e Marco Antonio Vigilio, studenti Boolean. #98alltheway!
                </p>
            </li>
        </ul>
    </footer>
</template>
<script>
export default {
    name: 'FooterApp'
}
</script>
<style lang="scss">
@use '../styles/partials/_variables.scss' as variables;

    footer{
        color: variables.$textColor;
        background-color: variables.$primaryColor;
        padding: 1rem 3rem .5rem 3rem;

        ul{
            list-style-type: none;
        }

        .credits{
        font-size: small;
        padding-top: 2rem;
        }
    }
</style>