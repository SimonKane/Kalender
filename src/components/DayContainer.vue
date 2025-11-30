<script setup lang="ts">
import { ref, defineAsyncComponent, shallowRef } from "vue";
import dayCodes from "../data/dayCodes.json";
import tomteBild from "../assets/tomte.png";

// const today = new Date();
const testDay = 1;
// const dayOfMonth = computed(() => today.getDate());
const isOpen = ref(false);
const dayComponent = shallowRef<any>(null);
const expectedCode = ref<string | null>(null);
const inputCode = ref("");
const codeError = ref("");

const completedDays = ref<Record<number, boolean>>({});
// const currentDay = computed(() => Math.min(24, Math.max(1, dayOfMonth.value)));
const completed = ref(false);

// try {
//   const raw = localStorage.getItem("kalender_completed");
//   if (raw) completedDays.value = JSON.parse(raw);
// } catch (e) {
//   completedDays.value = {};
// }

function persist() {
  try {
    localStorage.setItem(
      "kalender_completed",
      JSON.stringify(completedDays.value)
    );
  } catch (e) {}
}

const openDoor = () => {
  const day = testDay;
  const todaysCode = dayCodes[`day${day}`];
  expectedCode.value = todaysCode ?? null;

  if (completedDays.value[day]) return;

  if (day >= 1 && day <= 24) {
    dayComponent.value = defineAsyncComponent(
      () => import(`./days/Day${day}.vue`)
    );
  } else {
    dayComponent.value = null;
  }

  inputCode.value = "";
  isOpen.value = true;
  completed.value = false;
};

function onSolved(code: string) {
  expectedCode.value = code;
}

function onClickSubmit() {
  verifyCode();
}

function verifyCode() {
  const day = testDay;
  if (!expectedCode.value) return;
  if (
    inputCode.value.trim().toUpperCase() ===
    String(expectedCode.value).toUpperCase()
  ) {
    completedDays.value[day] = true;
    persist();
    isOpen.value = false;
    completed.value = true;

    setTimeout(() => {}, 200);
  } else {
    inputCode.value = "";
    codeError.value = "Tyvärr fel kod, försök igen!";
    setTimeout(() => (codeError.value = ""), 2000);
  }
}
</script>

<template>
  <div class="container">
    <div class="door-wrapper">
      <div class="reveal-container" :class="{ open: isOpen }">
        <component
          v-if="isOpen && dayComponent"
          :is="dayComponent"
          class="inner-content"
          @solved="onSolved"
        />
      </div>

      <div class="door-container" @click="openDoor">
        <div class="door left-door" :class="{ open: isOpen }"></div>
        <div class="door right-door" :class="{ open: isOpen }"></div>
        <div class="door-number" v-if="!isOpen">{{ testDay }}</div>
        <div v-if="completed" class="check-overlay">
          <img :src="tomteBild" alt="Tomte" />
        </div>
      </div>
    </div>

    <div class="input-container" :style="{ opacity: isOpen ? 1 : 0.0 }">
      <input
        type="text"
        v-model="inputCode"
        :placeholder="codeError || 'Skriv in koden här'"
        @keyup.enter="verifyCode"
        :class="{ 'input-error': !!codeError }"
      />
      <button type="button" @click="onClickSubmit" class="submit-btn">
        Submit
      </button>
    </div>
    <div class="completed" v-if="completed">
      Bra jobbat! Kom tillbaka igen imorgon
    </div>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}
.container {
  position: relative;
  width: 100%;
  max-width: 900px;
  margin: 0 auto;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 640px;
}
.completed {
  color: white;
  font-weight: bold;
  margin-top: 1rem;
}
.door-wrapper {
  position: relative;
}
.input-error {
  border: 2px solid #ff4d4d !important;
}

.input-error::placeholder {
  color: #ff4d4d !important;
  opacity: 1;
}

.door-container {
  width: 500px;
  height: 500px;
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
  font-size: 9em;
  font-weight: bold;
  color: #16160f;
  font-family: "Mountains of Christmas", cursive;
  text-shadow: 0 0 15px rgba(255, 215, 0, 0.8), 0 0 30px rgba(255, 215, 0, 0.5),
    3px 3px 6px rgba(0, 0, 0, 0.7);
  user-select: none;
  z-index: 5;
  pointer-events: none;
  width: 90%;
  max-width: 420px;
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
  max-width: 260px;
  min-width: 120px;
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

.reveal-container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 98%;
  max-width: 85vw;
  min-height: 280px;
  background-color: white;
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.35);
  box-sizing: border-box; /* ensure padding counts in size */
  max-height: 70vh; /* cap height so it fits on small screens */
  z-index: 1;
  opacity: 0;
  transform-origin: center;
  transition: opacity 0.35s ease, transform 0.35s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  pointer-events: none; /* don't block clicks when closed */
}

.reveal-container.open {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1);
  pointer-events: auto; /* allow interaction when opened */
}

.inner-content {
  width: 100%;
  max-width: 100%;
  max-height: 100%;
  box-sizing: border-box;
  overflow: auto; /* allow inner content to scroll if it is too large */
  display: flex;
  align-items: center;
  justify-content: center;
}

.check-overlay {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-45%, -55%);

  z-index: 10;
}

.input-container {
  flex-direction: column;
  margin-top: 0.8rem;
  display: flex;
  gap: 0.5rem;
  align-items: center;
  justify-content: center;
  z-index: 10;
  position: relative;
  transition: opacity 2s ease;
}
.input-container input {
  padding: 0.45rem 0.6rem;
  border-radius: 6px;
  border: none;
  background: rgba(255, 255, 255, 0.9);
  border: 2px solid transparent;
}
.input-container input:focus {
  outline: none;
  box-shadow: none;
}
.input-container .submit-btn {
  padding: 0.42rem 0.7rem;
  background: #2b6b2b;
  color: #fff;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}
.input-container .submit-btn:focus {
  outline: none;
  box-shadow: none;
}
.code-hint {
  position: absolute;
  bottom: 8px;
  left: 12px;
  font-size: 0.85rem;
  color: #444;
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
    min-height: 260px;
    max-width: 95vw;
  }
  .reveal-container {
    height: 98%;
    border-radius: 12px;
  }

  .door-container {
    width: 220px;
    height: 220px;
    max-width: 95vw;
    max-height: 60vw;
  }
  .door-number {
    font-size: 4em;
    max-width: 160px;
  }
  .door {
    max-width: 120px;
    min-width: 60px;
  }
  .reveal-container {
    min-height: 160px;
    max-height: 65vh;
  }
  .inner-content {
    padding: 0.25rem;
  }
}
</style>
