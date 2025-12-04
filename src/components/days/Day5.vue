<script setup lang="ts">
import { ref } from "vue";

const question = ref("Hans mamma är min mammas enda barn");

function typeWriterEffect() {
  console.log(question.value.split(""));
}
</script>

<template>
  <h1>Hans mamma är min mammas enda barn</h1>
  <h2>Vem är jag?</h2>
</template>

<style scoped>
h1 {
  text-align: center;
  font-size: 2rem;
  margin: 0;
}
</style>
