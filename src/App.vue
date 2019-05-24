<template>
  <div id="app">
    <electrolog-dashboard></electrolog-dashboard>
  </div>
</template>

<script>
import electrologDashboard from './components/electrolog-dashboard'

export default {
  name: 'App',
  components: {
    electrologDashboard
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
