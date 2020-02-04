<template>
  <div id="app">
    <Map :initial-config="config" />
  </div>
</template>

<script>
import Map from "./components/Map.vue";
import config from "./config.json";

export default {
  name: "app",
  data: function() {
    return {
      config
    };
  },
  components: {
    Map
  }
};
</script>
