<script setup lang="ts">
import door from "../../assets/door.png";
import open from "../../assets/door-open.png";
import key from "../../assets/nyckel.png";

import { ref, computed } from "vue";

const opened = ref(false);
const styleVars = computed(() => ({
  "--key-cursor": `url(${key}) 16 16, auto`,
}));
const isOnMobile = ref(false);
const haveKey = ref(false);
const showText = ref(false);
const code = ref(false);

const handleClick = () => {
  if (
    window.matchMedia("(pointer: coarse)").matches &&
    !haveKey.value &&
    !isOnMobile.value
  ) {
    alert(
      "Detta pussel kräver HELST mus och DevTools, men det finns en nyckel"
    );
    isOnMobile.value = true;
  }

  if (window.matchMedia("(pointer: coarse)").matches && haveKey.value) {
    opened.value = true;
    setTimeout(() => {
      code.value = true;
    }, 1500);
  }

  const el = document.querySelector(".container14");
  if (el) {
    if (getComputedStyle(el).cursor.includes("nyckel.png")) {
      opened.value = true;
      setTimeout(() => {
        code.value = true;
      }, 1500);
    }
  }
};

const handleKeyClick = () => {
  haveKey.value = true;
  showText.value = true;
  setTimeout(() => {
    showText.value = false;
  }, 1000);
};
</script>

<template>
  <div class="container14" :style="styleVars">
    <img
      class="closed"
      v-on:click="handleClick"
      v-if="!opened"
      :src="door"
      alt="door"
    />
    <img v-else class="open" :src="open" alt="door" />
    <h1 v-if="code" class="code14">{{ opened ? '"tomtetoakingen"' : "" }}</h1>
    <Teleport v-if="isOnMobile" to="body">
      <Transition name="key-pickup">
        <img
          v-if="!haveKey"
          @click="handleKeyClick"
          class="key"
          :src="key"
          alt="nyckel"
        />
      </Transition>
      <h3
        class="key-text"
        style="
          position: fixed;
          top: 4rem;
          left: 1rem;
          color: red;
          font-family: inherit;
          font-weight: 400;
          font-family: 'Mountains of Christmas', cursive;
        "
        v-if="showText"
      >
        "Nyckel" collected
      </h3>
    </Teleport>
  </div>
</template>

<style scoped>
.container14.inner-content {
  min-height: 100%;
  min-width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  cursor: url(/src/assets/nyckel.png?t=1765580827889) 16 16, auto;
  cursor: not-allowed;
}
.container14 img {
  width: 100%;
  height: auto;
}
.code14 {
  position: fixed;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  font-size: 1.5em;
  color: #ffd700;
  text-shadow: 0 0 10px rgba(255, 215, 0, 0.5), 0 0 20px rgba(255, 215, 0, 0.3),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  font-family: "Mountains of Christmas", cursive;
}
.key {
  position: fixed;
  top: 0;
  left: 4.6rem;
  z-index: 9999;
  cursor: pointer;
  z-index: 0;
}

.key-pickup-leave-active {
  transition: all 1.3s ease;
}

.key-pickup-leave-to {
  transform: scale(1.5);
  opacity: 0;
  z-index: 2;
  left: 3.3rem;
}
</style>
