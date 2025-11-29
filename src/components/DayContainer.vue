<script setup lang="ts">
import { ref, computed } from "vue";
import DayContent from "./DayContent.vue";

const today = new Date();
const dayOfMonth = computed(() => today.getDate());
const isOpen = ref(false);

const openDoor = () => {
  isOpen.value = true;
};
</script>

<template>
  <div class="container">
    <div class="door-wrapper">
      <div class="door-container" @click="openDoor">
        <div class="door-number" v-if="!isOpen">{{ dayOfMonth }}</div>
        <div class="door left-door" :class="{ open: isOpen }"></div>
        <div class="door right-door" :class="{ open: isOpen }"></div>
      </div>
      <div v-if="isOpen" class="content-revealed">
        <DayContent />
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  position: relative;
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 400px;
}

.door-wrapper {
  position: relative;
}

.door-container {
  width: 300px;
  height: 300px;
  margin: 0 auto;
  position: relative;
  cursor: pointer;
  perspective: 1200px;
  max-width: 90vw;
  max-height: 60vw;
}

.door-number {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 7em;
  font-weight: bold;
  color: #ffd700;
  font-family: "Mountains of Christmas", cursive;
  text-shadow: 0 0 15px rgba(255, 215, 0, 0.8), 0 0 30px rgba(255, 215, 0, 0.5),
    3px 3px 6px rgba(0, 0, 0, 0.7);
  user-select: none;
  z-index: 5;
  pointer-events: none;
  width: 90%;
  max-width: 250px;
  text-align: center;
}

.door {
  position: absolute;
  width: 50%;
  height: 100%;
  background: linear-gradient(135deg, #c41e3a 0%, #8b0000 50%, #c41e3a 100%);
  border: 3px solid #ffd700;
  transition: transform 0.8s ease;
  transform-style: preserve-3d;
  box-shadow: 0 0 20px rgba(255, 215, 0, 0.6), 0 0 40px rgba(255, 215, 0, 0.4),
    0 10px 30px rgba(0, 0, 0, 0.5), inset 0 0 20px rgba(255, 255, 255, 0.1);
  max-width: 150px;
  min-width: 80px;
}

.left-door {
  left: 0;
  border-radius: 12px 0 0 12px;
  border-right: 1.5px solid #ffd700;
  transform-origin: left;
}

.right-door {
  right: 0;
  border-radius: 0 12px 12px 0;
  border-left: 1.5px solid #ffd700;
  transform-origin: right;
}

.door.open.left-door {
  transform: rotateY(-120deg);
}

.door.open.right-door {
  transform: rotateY(120deg);
}

.door-container:hover .door:not(.open) {
  box-shadow: 0 15px 40px rgba(0, 0, 0, 0.4);
}

.content-revealed {
  margin-top: 2rem;
  animation: fadeIn 0.8s ease-in 0.4s both;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 600px) {
  .container {
    min-height: 220px;
    max-width: 95vw;
  }
  .door-container {
    width: 160px;
    height: 160px;
    max-width: 95vw;
    max-height: 60vw;
  }
  .door-number {
    font-size: 3em;
    max-width: 120px;
  }
  .door {
    max-width: 80px;
    min-width: 40px;
  }
}
</style>
