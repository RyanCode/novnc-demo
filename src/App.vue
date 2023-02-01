<template>
  <!-- <Xterm></Xterm> -->
  <NoVnc> </NoVnc> 
</template>

<script>
import Xterm from './components/Xterm.vue'
import NoVnc from './components/NoVnc.vue'

export default {
  name: 'App',
  components: {
    Xterm,
    NoVnc
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
