<template>
  <div class="flagle-game">
    <h1 v-if="showCode" class="code-heading">Bra jobbat "Flaggkingen"</h1>

    <template v-else>
      <div class="flag-wrapper" v-if="currentFlag">
        <img
          class="flag-image"
          :src="currentFlag.image"
          :alt="`Flagga för ${currentFlag.name}`"
        />

        <div class="overlay">
          <div
            v-for="(covered, index) in coveredTiles"
            :key="index"
            class="tile"
            :class="{ covered }"
          />
        </div>
      </div>

      <div class="controls" v-if="currentFlag">
        <form class="guess-form" @submit.prevent="checkGuess">
          <input
            v-model="guess"
            class="guess-input"
            type="text"
            placeholder="Country..."
            autocomplete="off"
            :disabled="isFinished"
          />
          <button class="primary-btn" type="submit" :disabled="isFinished">
            GUESS
          </button>
        </form>

        <p class="message">
          {{
            message ? message : currentIndex > 0 ? "  " : "Kör hårt flaggnörd"
          }}
        </p>
      </div>

      <p v-else class="message">No flags configured.</p>
    </template>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";

const emit = defineEmits(["solved"]);

interface Flag {
  name: string;
  image: string;
  alternatives?: string[];
}

const rows = 2;
const cols = 3;
const tileCount = rows * cols;

const flags = ref<Flag[]>([
  {
    name: "Gibraltar",
    image:
      "https://upload.wikimedia.org/wikipedia/commons/0/02/Flag_of_Gibraltar.svg",
  },
  {
    name: "Serbien",
    image:
      "https://upload.wikimedia.org/wikipedia/commons/f/ff/Flag_of_Serbia.svg",
  },
  {
    name: "Cypern",
    image:
      "https://upload.wikimedia.org/wikipedia/commons/d/d4/Flag_of_Cyprus.svg",
  },
  {
    name: "Portugal",
    image:
      "https://upload.wikimedia.org/wikipedia/commons/5/5c/Flag_of_Portugal.svg",
  },
  {
    name: "Chile",
    image:
      "https://upload.wikimedia.org/wikipedia/commons/7/78/Flag_of_Chile.svg",
  },
]);
flags.value = flags.value.sort(() => Math.random() - 0.5);
const currentIndex = ref(0);
const guess = ref("");
const message = ref("");
const isFinished = ref(false);
const guessesMade = ref(0);
const wrongGuesses = ref(0);

const showCode = ref(false);

const coveredTiles = ref<boolean[]>(Array(tileCount).fill(true));

const currentFlag = computed<Flag | null>(() => {
  if (!flags.value.length) return null;
  return flags.value[currentIndex.value % flags.value.length] ?? null;
});

const allTilesRevealed = computed(() =>
  coveredTiles.value.every((covered) => !covered)
);

function resetRound() {
  coveredTiles.value = Array(tileCount).fill(true);
  guess.value = "";
  message.value = "";
  isFinished.value = false;
  guessesMade.value = 0;
  wrongGuesses.value = 0;
  showCode.value = false;
}

function revealNextTile() {
  const closedIndexes: number[] = [];
  coveredTiles.value.forEach((covered, index) => {
    if (covered) closedIndexes.push(index);
  });

  if (!closedIndexes.length) return;

  const randomIndex =
    closedIndexes[Math.floor(Math.random() * closedIndexes.length)];

  if (randomIndex !== undefined) {
    coveredTiles.value[randomIndex] = false;
  }
}
const maxWrongGuesses = 4;

function normalize(str: string): string {
  return str
    .toLowerCase()
    .trim()
    .normalize("NFD")
    .replace(/[\u0300-\u036f]/g, "");
}
function nextFlag() {
  currentIndex.value = (currentIndex.value + 1) % flags.value.length;
  resetRound();
}
function checkGuess() {
  if (!currentFlag.value || isFinished.value || showCode.value) return;
  if (!guess.value.trim()) return;

  guessesMade.value += 1;

  const userGuess = normalize(guess.value);
  const correctNames = [
    currentFlag.value.name,
    ...(currentFlag.value.alternatives ?? []),
  ].map(normalize);

  if (correctNames.includes(userGuess)) {
    isFinished.value = true;
    showCode.value = true;
    emit("solved", "flaggkingen");
  } else {
    wrongGuesses.value += 1;
    revealNextTile();
    guess.value = "";
    if (wrongGuesses.value >= maxWrongGuesses) {
      message.value = "För många fel. NY flagga coming up!";
      setTimeout(() => {
        nextFlag();
      }, 800);
      return;
    }

    if (allTilesRevealed.value) {
      message.value = `Out of guesses. It was ${currentFlag.value.name}.`;
      isFinished.value = true;
    } else {
      message.value = `Fel, testa igen (${wrongGuesses.value}/${maxWrongGuesses})`;
    }
  }
}
</script>

<style scoped>
.flagle-game {
  width: 100%;
  max-width: 320px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.flag-wrapper {
  position: relative;
  width: 100%;
  aspect-ratio: 3 / 2;
  border-radius: 10px;
  overflow: hidden;
  scale: 0.8;
}

.flag-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.overlay {
  position: absolute;
  inset: 0;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 1fr);
}
.code-heading {
  font-size: 2rem;
  text-align: center;
  margin: 0;
}

.tile {
  border: 1px solid #000;
  box-sizing: border-box;
  background: transparent;
}

.tile.covered {
  background: #d4d4d4;
}

.controls {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.guess-form {
  display: flex;
  gap: 6px;

  scale: 0.5;
}

.guess-input {
  flex: 1;
  padding: 6px 8px;
  font-size: 0.85rem;
  border-radius: 6px;
  border: 1px solid #444;
  outline: none;
}

.guess-input::placeholder {
  color: #777;
}

.guess-input:focus {
  border-color: #000;
}

.primary-btn,
.secondary-btn {
  padding: 6px 10px;
  font-size: 0.85rem;
  border-radius: 6px;
  border: 1px solid #000;
  background: #000;
  color: #fff;
  cursor: pointer;
}

.primary-btn:disabled,
.secondary-btn:disabled {
  opacity: 0.5;
  cursor: default;
}

.secondary-btn.full-width {
  width: 100%;
}

.message {
  font-size: 0.8rem;
  text-align: center;
  min-height: 1.2em;
}
</style>
