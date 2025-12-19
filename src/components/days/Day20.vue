<script setup lang="ts"></script>

<template>
  <div class="container20">
    <div class="content">
      <div class="item">
        <span class="letter">A</span>
        <span class="text">Exakt två av B, C och D är sanna</span>
      </div>
      <div class="item">
        <span class="letter">B</span>
        <span class="text">E är sann</span>
      </div>
      <div class="item">
        <span class="letter">C</span>
        <span class="text">A är falsk.</span>
      </div>
      <div class="item">
        <span class="letter">D</span>
        <span class="text">Raden ovanför (C) ljuger.</span>
      </div>
      <div class="item">
        <span class="letter">E</span>
        <span class="text">Exakt en av A och D är sann</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container20 {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem;
  box-sizing: border-box;
  overflow: hidden;
  background: linear-gradient(to bottom, #0a0e27 0%, #1a1a3e 50%, #0a0e27 100%);
  position: relative;
}

.container20::before {
  content: "";
  position: absolute;
  width: 100%;
  max-height: 100%;
  background-image: radial-gradient(2px 2px at 20% 30%, white, transparent),
    radial-gradient(2px 2px at 60% 70%, white, transparent),
    radial-gradient(1px 1px at 50% 50%, white, transparent),
    radial-gradient(1px 1px at 80% 10%, white, transparent),
    radial-gradient(2px 2px at 90% 60%, white, transparent),
    radial-gradient(1px 1px at 33% 80%, white, transparent),
    radial-gradient(1px 1px at 15% 60%, white, transparent),
    radial-gradient(2px 2px at 70% 40%, white, transparent);
  background-size: 200% 200%;
  background-position: 0% 0%;
  opacity: 0.7;
  animation: twinkle 3s ease-in-out infinite;
}

@keyframes twinkle {
  0%,
  100% {
    opacity: 0.7;
  }
  50% {
    opacity: 0.4;
  }
}

.content {
  display: flex;
  flex-direction: column;
  gap: 0.8rem;
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
  position: relative;
  z-index: 1;
}

.item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  width: 100%;
  box-sizing: border-box;
}

.letter {
  font-size: 1.5rem;
  font-weight: bold;
  flex-shrink: 0;
  min-width: 2rem;
  color: #ffd700;
  text-shadow: 0 0 10px #ffd700, 0 0 20px #ffd700, 0 0 30px #ffd700,
    0 0 40px #ffed4e;
}

.text {
  font-size: 1.2rem;
  flex: 1;
  word-wrap: break-word;
  overflow-wrap: break-word;
  color: #ffd700;
  text-shadow: 0 0 8px #ffd700, 0 0 15px #ffd700;
}

/* Mobile adjustments */
@media (max-width: 768px) {
  .container {
    padding: 0.5rem;
  }

  .content {
    gap: 0.5rem;
  }

  .letter {
    font-size: 1rem;
    min-width: 1.2rem;
  }

  .text {
    font-size: 0.8rem;
  }
}

/* Extra small screens */
@media (max-width: 480px) {
  .container {
    padding: 0.3rem;
  }

  .letter {
    font-size: 1rem;
    min-width: 1rem;
  }

  .text {
    font-size: 0.5rem;
  }
}
</style>
