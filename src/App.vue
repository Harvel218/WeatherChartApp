<template>
  <div id="app">
    <Weather />
  </div>
</template>

<script>
import Weather from "./components/Weather.vue";

export default {
  name: "App",
  components: {
    Weather,
  },
};
</script>

<style lang="scss">
body {
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: rgba(0, 0, 0, 0.9);
  margin-top: 0;
  min-width: 98%;
  min-height: calc(100vh - 48px);
  position: relative;
  background-image: url("~@/assets/weather.webp");
  background-repeat: no-repeat;
  background-size: cover;
  background-attachment: fixed;
  padding: 24px 0;
}
</style>
