<script setup lang="ts">
import { ref, computed } from "vue";

const maxClicks = 30;
const clickCount = ref(0);
const boxGone = ref(false);

function handleClick() {
  if (clickCount.value < maxClicks) {
    clickCount.value++;
    if (clickCount.value === maxClicks) {
      boxGone.value = true;
    }
  }
}

const boxScale = computed(() => {
  const minScale = 0.3;
  const scale = 1 - (clickCount.value / maxClicks) * (1 - minScale);
  return scale;
});

const boxRotate = computed(() => {
  const deg = (clickCount.value / maxClicks) * 30;
  return `rotate(${deg}deg)`;
});

const insetShadow = computed(() => {
  const baseBlur = 24;
  const stepBlur = 100;
  const blur = baseBlur + clickCount.value * stepBlur;
  const darkness = Math.floor(255 - (clickCount.value / maxClicks) * 255);
  const color = `rgb(${darkness},${darkness},${darkness})`;
  return `inset 0 0 ${blur}px ${color}, inset 0 0 ${blur * 0.7}px #000`;
});
</script>

<template>
  <div class="container" :style="{ boxShadow: insetShadow }">
    <transition name="box-fade">
      <div
        v-if="!boxGone"
        class="white-box"
        :style="{
          transform: `scale(${boxScale}) ${boxRotate}`,
        }"
        @click="handleClick"
      ></div>
    </transition>

    <transition name="code-grow">
      <div v-if="boxGone" class="code-box">
        <div class="secret-code">eyjafjallajökull</div>
      </div>
    </transition>
  </div>
</template>

<style scoped>
.container {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100%;
  width: 100%;
  height: 100%;
  overflow: hidden;
  top: 0;
}

.white-box,
.code-box {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.white-box {
  background: white;
  box-shadow: inset 0 0 6px #aaa;
  padding: 2rem 1.5rem;
  width: 100%;
  height: 100%;

  transition: box-shadow 0.2s cubic-bezier(0.4, 2, 0.3, 1),
    transform 0.6s cubic-bezier(0.4, 2, 0.3, 1);
  user-select: none;
}

.code-box {
  padding: 2rem 1.5rem;
  min-width: 220px;
  min-height: 120px;
  gap: 1rem;
}

.secret-code {
  font-size: 1.5rem;
  font-family: monospace;
  color: #c41e3a;
  letter-spacing: 2px;
  text-align: center;
}

.code-grow-enter-active {
  transition: transform 1.3s cubic-bezier(0.4, 2, 0.3, 1), opacity 1s;
}
.code-grow-enter-from {
  transform: scale(0.02);
  opacity: 0;
}
.code-grow-enter-to {
  transform: scale(1);
  opacity: 1;
}

.box-fade-leave-active {
  transition: opacity 0.6s;
}
.box-fade-leave-to {
  opacity: 0;
}

@media (max-width: 600px) {
  .container {
    min-height: 180px;
  }
  .white-box,
  .code-box {
    min-width: 140px;
    padding: 1rem 0.5rem;
  }
  .secret-code {
    font-size: 1rem;
    letter-spacing: 1px;
  }
}
</style>
