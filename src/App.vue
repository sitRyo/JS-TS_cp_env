<template>
  <div id="app">
    <i-o-pos></i-o-pos>
  </div>
</template>

<script>
import IOPos from './components/IO.vue'

export default {
  name: 'app',
  components: {
    IOPos,
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
