<template>
  <weather />
</template>
<script>
export default {
  name: "App",
  components: {
    weather,
  },
};
</script>
<script setup>
import weather from "./views/weather.vue";
</script>
