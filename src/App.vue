<template>
  <div id="app">
    <VaccineCheck />
    <Cowin />
  </div>
</template>

<script>
import VaccineCheck from "./components/VaccineCheck";
import Cowin from "./components/Cowin";

export default {
  name: "App",
  components: {
    VaccineCheck,
    Cowin,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
