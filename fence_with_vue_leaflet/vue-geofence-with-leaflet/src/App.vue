<template>
  <div id="app">
    <h1>电子围栏地图</h1>
    <Map />
  </div>
</template>

<script>
import Map from "./components/MapComponent.vue";

export default {
  name: "App",
  components: {
    Map
  }
};
</script>

<style>
#app {
  text-align: center;
}
</style>
