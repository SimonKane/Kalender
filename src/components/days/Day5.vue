<script setup lang="ts">
import { ref, onMounted } from "vue";

const displayedText = ref("");
const fullText = "Vem är jag?";
const showTypewriter = ref(false);

onMounted(() => {
  setTimeout(() => {
    showTypewriter.value = true;
    let index = 0;
    const interval = setInterval(() => {
      if (index < fullText.length) {
        displayedText.value += fullText[index];
        index++;
      } else {
        clearInterval(interval);
      }
    }, 100);
  }, 6000);
});
</script>

<template>
  <div class="content-wrapper">
    <h1>Hans mamma är min mammas enda barn</h1>
    <h2 v-if="showTypewriter" class="typewriter">{{ displayedText }}</h2>
  </div>
</template>

<style scoped>
.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  padding: 1rem;
  height: 100%;
  width: 100%;
}

h1 {
  text-align: center;
  font-size: clamp(1rem, 3.5vw, 1.6rem);
  margin: 0;
  line-height: 1.3;
  max-width: 90%;
}

h2 {
  text-align: center;
  font-size: clamp(1rem, 3vw, 1.5rem);
  margin: 0;
  color: #ffd700;
}

.typewriter {
  border-right: 2px solid #ffd700;
  padding-right: 5px;
  animation: blink 0.7s step-end infinite;
}

@keyframes blink {
  50% {
    border-color: transparent;
  }
}

@media (max-width: 600px) {
  .content-wrapper {
    gap: 1rem;
    padding: 0.5rem;
  }

  h1 {
    font-size: clamp(0.85rem, 4.5vw, 1.3rem);
  }

  h2 {
    font-size: clamp(0.9rem, 4vw, 1.2rem);
  }
}
</style>
