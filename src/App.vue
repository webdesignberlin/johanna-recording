<template>
  <div id="app">
    <record></record>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
    Record: () => import('./components/Record.vue'),
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
}
</style>
