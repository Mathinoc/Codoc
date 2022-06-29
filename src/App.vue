<script>
import AppHeader from "./components/AppHeader.vue";
import PatientPortal from "./components/PatientPortal.vue";

export default {
  name: "App",
  components: {
    AppHeader,
    PatientPortal,
  },
};
</script>


<template>
  <div id="app">
    <AppHeader />
    <main>
      <PatientPortal />
    </main>
  </div>
</template>


<style>
:root {
  --header-height: 50px;
  --font-color-blue: #233C73;
  --font-color-grey: #686F78;
  --font-color-light-grey: #A0A7B0;
  --color-border: #D5D7DE;
  --global-radius: 5px;
  --background-color: #F8F9F9;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
}
main {
  padding-top: var(--header-height);
  margin: 0 auto;
}
</style>
