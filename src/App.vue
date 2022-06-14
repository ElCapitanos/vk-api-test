<template>
  <div id="app">
    <main-component />
  </div>
</template>
<script>
import MainComponent from "./components/MainComponent.vue";
export default {
  components: {
    MainComponent,
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
}
</style>
