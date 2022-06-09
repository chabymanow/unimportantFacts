<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <Trivia />
</template>

<script>
import Trivia from './components/Trivia.vue';

export default {
  name: 'App',
  components: {
    Trivia
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
