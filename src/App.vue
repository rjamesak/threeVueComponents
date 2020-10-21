<template>
  <div id="app">
    <threeBase></threeBase>
  </div>
</template>

<script>
import threeBase from './components/threeBase.vue'

export default {
  name: 'App',
  components: {
    threeBase
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
