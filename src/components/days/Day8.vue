<script setup lang="ts">
import { ref, onMounted } from "vue";
const candleLit = ref(false);
const code = ref("function handler");
const showCode = ref(false);
const hints = ref<{ [key: number]: string | null }>({
  1: null,
  2: null,
  3: null,
  4: null,
});
const isMobile = ref(false);

onMounted(() => {
  if (window.matchMedia("(hover: none) and (pointer: coarse)").matches) {
    isMobile.value = true;
  }
});

function lightCandle(nr: any) {
  if (nr != 2) {
    code.value = "Nope";
    return;
  }
  candleLit.value = true;
  code.value = "";
  setTimeout(() => {
    showCode.value = true;
    code.value = "advent2";
  }, 1200);
}

function runCode() {
  try {
    (window as any).lightCandle = lightCandle;
    // eslint-disable-next-line no-eval
    if (code.value !== "lightCandle(2)") {
      code.value = "Nope";
      return;
    }
    eval(code.value);
  } catch (e) {
    console.error("Error in eval:", e);
  }
}
function showHintForCandle(nr: number, text: string) {
  if (!isMobile.value) return;

  hints.value = { 1: null, 2: null, 3: null, 4: null };

  hints.value[nr] = text;
}
</script>

<template>
  <div class="container8">
    <div v-if="!candleLit" class="code-box">
      <textarea v-model="code" class="code-input" />
      <button class="run-button" @click="runCode">Run Code</button>
    </div>

    <transition name="fade">
      <h1 class="code" v-if="showCode">{{ code }}</h1>
    </transition>
    <div class="candle-container">
      <div
        class="candle lit"
        title="lightCandle(1)"
        @click="showHintForCandle(1, 'lightCandle(1)')"
      >
        <div class="flame"></div>
        <div v-if="hints[1]" class="hint-bubble">
          {{ hints[1] }}
        </div>
      </div>
      <div
        :class="['candle', candleLit ? 'lit' : '']"
        title="???"
        @click="showHintForCandle(2, '???')"
      >
        <div class="flame"></div>
        <div v-if="hints[2]" class="hint-bubble">
          {{ hints[2] }}
        </div>
      </div>
      <div class="candle">
        <div class="flame"></div>
      </div>
      <div class="candle">
        <div class="flame"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container8 {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  background: rgb(46, 45, 45);
  overflow: hidden;
}
.code-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: clamp(0.3rem, 2vw, 0.8rem);
  gap: 0.3rem;
  background: rgb(46, 45, 45);
}
.hint-text {
  color: #ffee00;
  text-align: center;
  margin-top: 0.5rem;
  font-size: clamp(0.8rem, 2vw, 1rem);
  opacity: 0.85;
  position: absolute;
}
.code-input {
  resize: none;
  margin: 0;
  text-align: center;
  outline: none;
  border: none;
  border-radius: 5px;
  width: clamp(150px, 70%, 250px);
  padding: 0.3rem 0.5rem;
  font-size: clamp(0.7rem, 1.5vw, 0.9rem);
  min-height: 1.5rem;
}
.run-button {
  padding: 0.3rem 1rem;
  font-size: clamp(0.7rem, 1.5vw, 0.9rem);
  background: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
}
.code {
  margin-top: 20px;
  color: #ffee0080;
  text-shadow: 0 0 20px rgb(251, 255, 0), 0 0 20px rgb(255, 255, 255),
    2px 2px 4px rgba(0, 0, 0, 0.5);
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
  background: rgb(46, 45, 45);
  display: flex;
  justify-content: center;
  align-items: flex-end;
  height: 100%;
  gap: clamp(1rem, 5vw, 2rem);
  padding: 0 1rem;
}

.candle {
  margin-bottom: clamp(5px, 2vw, 10px);
  position: relative;
  width: clamp(0.6rem, 3vw, 1rem);
  height: clamp(5rem, 15vw, 8rem);
  background: #fff8dc;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
.hint-text {
  color: #ffee00;
  text-align: center;
  margin-top: 0.5rem;
  font-size: clamp(0.8rem, 2vw, 1rem);
  opacity: 0.85;
  position: absolute;
}
.candle:nth-child(1) {
  height: clamp(3rem, 10vw, 5rem);
  bottom: 0;
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
.lit {
  .flame {
    width: clamp(0.4rem, 1.5vw, 0.6rem);
    height: clamp(0.8rem, 2.5vw, 1.2rem);
    background: radial-gradient(
      circle at 50% 20%,
      #fffae3,
      #ebc73b 40%,
      #ff9900 70%,
      transparent 100%
    );
    margin-bottom: 10px;
    border-radius: 50%;
    box-shadow: 0 0 20px 5px yellow;
  }
}
.hint-bubble {
  position: absolute;
  bottom: 0rem;
  left: 50%;
  transform: translateX(-50%);
  background: #ffee00;
  color: black;
  padding: 1px 6px;
  font-size: 0.4rem;
  border-radius: 4px;
  white-space: nowrap;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
  z-index: 10;
  opacity: 0.6;
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
  .run-button {
    scale: 0.5;
  }
  .code-input {
    scale: 0.5;
  }
}
</style>
