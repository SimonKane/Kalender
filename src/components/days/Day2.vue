<script setup lang="ts">
import { ref } from "vue";

const refValue = ref("ERROR 404");

setTimeout(() => {
  throw new Error("Error level super high threat: knut");
}, 2000);
</script>

<template>
  <h1 class="error">{{ refValue }}</h1>
</template>

<style scoped>
h1 {
  text-align: center;
  font-size: 2rem;
  margin: 0;
}
</style>
