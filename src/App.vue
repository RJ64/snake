<template>
  <SnakeGame/>
</template>

<script>
import SnakeGame from './SnakeGame.vue'

export default {
  name: 'App',
  components: {
    SnakeGame
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
