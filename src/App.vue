<template>
  <ticTacToe></ticTacToe>
</template>

<script>
import ticTacToe from './components/ticTacToe.vue';
export default {
  name: 'App',
  components: {
    ticTacToe
  }
}
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
