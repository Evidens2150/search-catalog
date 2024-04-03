<template>
  <PostCatalog />
</template>

<script>
import PostCatalog from './components/PostCatalog.vue'

export default {
  name: 'App',
  components: {
    PostCatalog
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  /* margin-top: 60px; */
  min-height: 100vh;
}
body {
  margin: 0;
}
</style>
