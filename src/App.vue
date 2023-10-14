<script>
import HeaderApp from './components/HeaderApp.vue'
import FooterApp from './components/FooterApp.vue'
import 'bootstrap/scss/bootstrap.scss'
  export default{
    name : 'App',

      components: {
        HeaderApp,
        FooterApp,
      }
  }

</script>

<template>

  <HeaderApp />

<router-view>

</router-view>

  <FooterApp />
</template>

<style lang="scss">
@use './styles/general.scss' as *;


</style>
