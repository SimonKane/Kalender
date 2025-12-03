<script setup lang="ts">
import DayContainer from "./components/DayContainer.vue";
import SnowEffect from "./components/SnowEffect.vue";
</script>

<template>
  <div class="app-container">
    <SnowEffect />
    <h1 class="title">🎄THE Julkalender 2025!🎄</h1>
    <DayContainer />
  </div>
</template>

<style scoped>
.app-container {
  width: 100vw;
  height: 100vh;
  padding: 0;
  margin: 0;
  overflow: scro;
  background: linear-gradient(135deg, #1a472a 0%, #2d5f3f 50%, #1a472a 100%);
  font-family: "Mountains of Christmas", cursive;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding-top: 8rem;
}

.title {
  position: fixed;
  top: 1rem;
  left: 50%;
  transform: translateX(-50%);
  font-size: 2.5em;
  z-index: 10;
  margin: 0;
  color: #ffd700;
  text-shadow: 0 0 10px rgba(255, 215, 0, 0.5), 0 0 20px rgba(255, 215, 0, 0.3),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  font-weight: 700;
  text-align: center;
  width: 90vw;
  max-width: 600px;
  pointer-events: none;
  white-space: nowrap;
  overflow: hidden;
}

@media (max-width: 600px) {
  .title {
    font-size: 1.5em;
    top: 0.5rem;
    max-width: 95vw;
  }
  .app-container {
    padding-top: 5rem;
  }
}

@media (max-width: 375px) {
  .title {
    font-size: 1.2em;
    top: 0.3rem;
  }
  .app-container {
    padding-top: 4rem;
  }
}
</style>
