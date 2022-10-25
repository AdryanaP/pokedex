<template>
  <Home msg="Welcome to Your Vue.js App"/>
</template>

<script>
import Home from './components/Home.vue'

export default {
  name: 'App',
  components: {
    Home
  }
}
</script>

<style>
@font-face {
  font-family: "Pokemon";
  src: local("Pokemon"),
   url("~@/assets/fonts/PokemonHollowNormal-pyPZ.ttf") format("truetype");
}
</style>