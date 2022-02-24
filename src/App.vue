<template>
  <div id="app">
    <my-portfolio msg="My Crypto Portfolio" email="rondwalker22@yahoo.com" 
    auth="{{location.href.split('#')[1]).split('&')[1].split('=')[1]}}}"
    jwt="love"
    />
  </div>
</template>

<script>
import MyPortfolio from './components/Portfolio.vue'

export default {
  name: 'App',
  components: {
    MyPortfolio
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
