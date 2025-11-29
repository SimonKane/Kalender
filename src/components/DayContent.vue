<script setup lang="ts">
import { computed } from "vue";

const today = new Date();
const dayOfMonth = computed(() => today.getDate());
</script>

<template>
  <div class="day-content">
    <p>{{ dayOfMonth }}</p>
  </div>
</template>

<style scoped>
.day-content {
  text-align: center;
}

p {
  font-size: 1.5em;
}
</style>
