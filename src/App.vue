<template>
  <div id="app">
    <h1>Simulador de Modulação de Sinais</h1>
    <Content />
  </div>
</template>

<script>
import Content from './components/Content.vue'

export default {
  name: 'App',

  components: {
    Content
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0px;
  background-color: #efefef;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 30px;
}
h1 {
  text-align: center;
}
</style>
