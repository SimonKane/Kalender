<script setup lang="ts">
import { ref, computed } from "vue";

const code = ref(`lightCandle(4);

const lightCandle = (nr) => {
  fourthCandleLit.value = true
};`);

const showCandles = ref(false);
const candleLit = ref(false);
const showCode = ref(false);
const displayCode = ref("advent4");
const errorMessage = ref("");

const isCodeCorrect = computed(() => {
  const lines = code.value
    .trim()
    .split("\n")
    .filter((line) => line.trim());

  let declarationIndex = -1;
  let callIndex = -1;

  lines.forEach((line, index) => {
    if (
      line.includes("const lightCandle") ||
      line.includes("let lightCandle")
    ) {
      declarationIndex = index;
    }
    if (
      line.includes("lightCandle(4)") &&
      !line.includes("const") &&
      !line.includes("let")
    ) {
      callIndex = index;
    }
  });

  return (
    declarationIndex !== -1 && callIndex !== -1 && declarationIndex < callIndex
  );
});

function runCode() {
  errorMessage.value = "";

  if (!isCodeCorrect.value) {
    errorMessage.value = "Something is error";
    return;
  }

  // Code is correct! Show candles
  showCandles.value = true;

  setTimeout(() => {
    candleLit.value = true;
    setTimeout(() => {
      showCode.value = true;
    }, 1200);
  }, 300);
}
</script>

<template>
  <div class="container21">
    <div v-if="!showCandles" class="puzzle-container">
      <textarea v-model="code" class="code-editor" spellcheck="false" />
      <button class="run-button" @click="runCode">Run Code</button>
      <transition name="error-fade">
        <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
      </transition>
    </div>

    <transition name="fade">
      <div v-if="showCandles" class="candles-section">
        <transition name="fade">
          <h1 class="advent-code" v-if="showCode">{{ displayCode }}</h1>
        </transition>
        <div class="candle-container">
          <div class="candle lit">
            <div class="flame"></div>
          </div>
          <div class="candle lit">
            <div class="flame"></div>
          </div>
          <div class="candle lit">
            <div class="flame"></div>
          </div>
          <div :class="['candle', candleLit ? 'lit' : '']">
            <div class="flame"></div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<style scoped>
.container21 {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgb(46, 45, 45);
  overflow: hidden;
  padding: clamp(0.2rem, 1vw, 0.5rem);
}

.puzzle-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: clamp(0.3rem, 1vw, 0.5rem);
  width: 100%;
  max-width: 600px;
  padding: 0;
}

.title {
  color: #ffee00;
  font-size: clamp(1.2rem, 4vw, 2rem);
  margin: 0;
  text-shadow: 0 0 10px rgba(255, 238, 0, 0.5);
}

.instruction {
  color: #ffffff;
  font-size: clamp(0.8rem, 2vw, 1rem);
  margin: 0;
  text-align: center;
  opacity: 0.9;
}

.code-editor {
  width: 100%;
  min-height: clamp(80px, 20vh, 120px);
  max-height: clamp(130px, 28vh, 180px);
  padding: clamp(0.3rem, 1vw, 0.5rem);
  font-family: "Courier New", monospace;
  font-size: clamp(0.7rem, 1.5vw, 0.85rem);
  line-height: 1.3;
  background: #1e1e1e;
  color: #d4d4d4;
  border: 2px solid #ffee00;
  border-radius: 8px;
  resize: vertical;
  outline: none;
  transition: border-color 0.3s;
  resize: none;
}

.code-editor:focus {
  border-color: #fff;
  box-shadow: 0 0 10px rgba(255, 238, 0, 0.3);
}

.run-button {
  padding: clamp(0.25rem, 1vw, 0.4rem) clamp(1rem, 3vw, 1.5rem);
  font-size: clamp(0.7rem, 1.5vw, 0.85rem);
  background: #ffee00;
  color: #000;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.3s;
}

.run-button:hover {
  background: #fff;
  transform: scale(1.05);
  box-shadow: 0 0 15px rgba(255, 238, 0, 0.5);
}

.run-button:active {
  transform: scale(0.98);
}

.error-message {
  color: #ff4444;
  font-family: "Courier New", monospace;
  font-size: clamp(0.65rem, 1.5vw, 0.75rem);
  margin: 0;
  text-align: center;
  background: rgba(255, 68, 68, 0.1);
  padding: clamp(0.2rem, 0.5vw, 0.3rem) clamp(0.4rem, 1vw, 0.6rem);
  border-radius: 5px;
  border: 1px solid #ff4444;
}

.error-fade-enter-active,
.error-fade-leave-active {
  transition: opacity 0.3s ease;
}

.error-fade-enter-from,
.error-fade-leave-to {
  opacity: 0;
}

.candles-section {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.advent-code {
  position: absolute;
  top: clamp(0.5rem, 2vw, 1rem);
  left: 50%;
  transform: translateX(-50%);
  color: #ffee0080;
  font-size: clamp(1.2rem, 4vw, 2rem);
  text-shadow: 0 0 20px rgb(251, 255, 0), 0 0 20px rgb(255, 255, 255),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  margin: 0;
  z-index: 10;
}

.fade-enter-active {
  transition: opacity 1.5s ease;
}

.fade-enter-from {
  opacity: 0;
}

.fade-enter-to {
  opacity: 1;
}

.candle-container {
  position: relative;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: flex-end;
  gap: clamp(0.8rem, 4vw, 1.5rem);
  padding: 0 0.5rem;
  min-height: clamp(100px, 20vh, 150px);
}

.candle {
  margin-bottom: clamp(3px, 1vw, 5px);
  position: relative;
  width: clamp(0.5rem, 2.5vw, 0.8rem);
  height: clamp(3.5rem, 12vw, 5.5rem);
  background: #fff8dc;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.candle:nth-child(1) {
  height: clamp(2.5rem, 8vw, 3.5rem);
}

.candle:nth-child(2) {
  height: clamp(2.5rem, 8vw, 3.8rem);
}

.candle:nth-child(3) {
  height: clamp(3rem, 10vw, 4.8rem);
}

.flame {
  position: absolute;
  top: clamp(-0.4rem, -1.5vw, -0.6rem);
  left: 50%;
  transform: translateX(-50%);
  width: clamp(0.05rem, 0.3vw, 0.1rem);
  height: clamp(0.6rem, 2vw, 1rem);
  background: black;
  animation: flicker 1s infinite;
}

.lit .flame {
  width: clamp(0.4rem, 1.5vw, 0.6rem);
  height: clamp(0.8rem, 2.5vw, 1.2rem);
  background: radial-gradient(
    circle at 50% 20%,
    #fffae3,
    #ebc73b 40%,
    #ff9900 70%,
    transparent 100%
  );
  border-radius: 50%;
  box-shadow: 0 0 20px 5px yellow;
}

@keyframes flicker {
  0%,
  100% {
    transform: translateX(-50%) scaleY(1) scaleX(1);
    opacity: 1;
  }
  50% {
    transform: translateX(-50%) scaleY(1.1) scaleX(0.9);
    opacity: 0.8;
  }
}

@media (max-width: 768px) {
  .puzzle-container {
    padding: 0.5rem;
  }

  .code-editor {
    font-size: 0.7rem;
  }
}

@media (max-width: 430px) {
  .candle-container {
    gap: 0.8rem;
  }

  .candle {
    width: 0.7rem;
    height: 4.5rem;
  }

  .candle:nth-child(1) {
    height: 2.8rem;
  }
  .candle:nth-child(2) {
    height: 3.5rem;
  }
  .candle:nth-child(3) {
    height: 4rem;
  }

  .code-editor {
    font-size: 0.5rem;
    min-height: 100px;
  }

  .run-button {
    padding: 0.4rem 1.2rem;
    font-size: 0.85rem;
  }
}
</style>
