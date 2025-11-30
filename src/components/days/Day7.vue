<script setup lang="ts">
const today = new Date().toLocaleDateString("sv-SE", {
  weekday: "long",
  year: "numeric",
  month: "long",
  day: "numeric",
});
</script>

<template>
  <h1>{{ today }}</h1>
</template>

<style scoped>
h1 {
  text-align: center;
  font-size: 2rem;
  margin: 0;
}
</style>
