<template>

    <!-- navbar -->
<nav class="navbar navbar-expand-lg">
    <div class="container">

        <!-- logo -->
        <a class="navbar-brand text-dark logo" href="#"><span class="specialText">BOO</span>SICIAN</a>

        <!-- Toggle btn -->
        <button class="navbar-toggler" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasNavbar" aria-controls="offcanvasNavbar" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
        </button>

            <!-- sidebar -->
        <div class="sidebar offcanvas offcanvas-start" tabindex="-1" id="offcanvasNavbar" aria-labelledby="offcanvasNavbarLabel">

            <!-- sidebar header -->
            <div class="offcanvas-header text-white border-bottom">
                <h5 class="offcanvas-title" id="offcanvasNavbarLabel">Nome Sito</h5>
                <button type="button" class="btn-close btn-close-white shadow-none" data-bs-dismiss="offcanvas" aria-label="Close"></button>
            </div>

                <!-- sidebar body -->
            <div class="offcanvas-body d-flex flex-column flex-lg-row p-4 p-lg-0">
                <ul class="navbar-nav justify-content-center align-items-center flex-grow-1 pe-3">
                    <li class="nav-item mx-2 my-3" v-for="link in links">
                        <router-link :to="link.route" class="text-black text-decoration-none px-3">
                        {{ link.name }}
                        </router-link>
                    </li>
                </ul>

                <!-- Login / Register -->
                <div class="d-flex justify-content-center  align-items-center">
                    <ul class="d-flex flex-column  flex-lg-row m-0 gap-3 p-0">
                        <li class="list-unstyled">
                            <button class="btn btn-small btnSignIn" ><a href="http://127.0.0.1:8000/" class="firstBtnText fw-700">Accedi</a></button>
                            <button @click="redirectToLaravelLogin" class="btn btn-small btnLogIn"><a href="http://127.0.0.1:8000/register" class="fw-700">Registrati</a></button>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</nav>















</template>


<script>

export default {
    name: 'HeaderApp',

    data() {
        return {
            // all pages route
            links : [
                {
                    route: './',
                    name: 'Home'
                },
                {
                    route: './advance-search',
                    name: 'Ricerca'
                }
            ],
            

        }
    },
    

}


</script>




<style lang="scss" scoped>
@use '../styles/partials/_variables.scss' as variables;

    @media (max-width: 991px) {
        .sidebar{
            background-color: rgba(255, 255, 255, .15);
            backdrop-filter: blur(10px);
        }
    }

    a{
        text-decoration: none;
        color: white;
    }

    nav{
        background-color: variables.$primaryColor;

        a.logo{
            font-weight: 900;
            font-size: 2.5rem;
        }
    }

    .specialText{
        color: variables.$secondaryColor;
    }

    .firstBtnText{
        color: variables.$primaryColor;
    }

        button.btn.btnSignIn{
            background-color: variables.$secondaryColor;
            border-color: variables.$secondaryColor;
            margin-right: 1rem;
        }
        button.btn.btnLogIn{
            background-color: variables.$accentColor;
            border-color: variables.$accentColor;
        }
</style>