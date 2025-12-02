<script setup lang="ts">
import {
  ref,
  onMounted,
  onBeforeUnmount,
  computed,
  defineEmits,
  nextTick,
} from "vue";

const TARGET = "ADVENT";
const WORD_LENGTH = TARGET.length;
const MAX_GUESSES = 6;

const guesses = ref<string[]>(Array(MAX_GUESSES).fill(""));
const results = ref<Array<Array<"correct" | "present" | "absent" | "pending">>>(
  Array.from({ length: MAX_GUESSES }, () => Array(WORD_LENGTH).fill("pending"))
);
const current = ref("");
const row = ref(0);
const message = ref("");
const won = ref(false);

const rows = computed(() => Array.from({ length: MAX_GUESSES }, (_, i) => i));

const emit = defineEmits<{
  (e: "solved", code: string): void;
}>();

const sanitizeInput = (s: string) => {
  return s
    .replace(/[^a-zA-ZÅÄÖåäö]/g, "")
    .toUpperCase()
    .slice(0, WORD_LENGTH);
};

const cellClass = (r: number, c: number) => {
  return results.value[r] && results.value[r][c]
    ? results.value[r][c]
    : "pending";
};

const resetGame = () => {
  guesses.value = Array(MAX_GUESSES).fill("");
  results.value = Array.from({ length: MAX_GUESSES }, () =>
    Array(WORD_LENGTH).fill("pending")
  );
  current.value = "";
  row.value = 0;
  message.value = "";
  won.value = false;
};

const submitGuess = () => {
  if (current.value.length !== WORD_LENGTH) {
    message.value = `Behöver ${WORD_LENGTH} bokstäver`;
    return;
  }

  const guess = current.value.toUpperCase();
  const targetArr = TARGET.split("");
  const guessArr = guess.split("");

  const status: Array<"correct" | "present" | "absent"> =
    Array(WORD_LENGTH).fill("absent");

  const counts: Record<string, number> = {};
  for (let i = 0; i < WORD_LENGTH; i++) {
    const t = targetArr[i];
    if (t !== undefined && guessArr[i] !== t) {
      counts[t] = (counts[t] || 0) + 1;
    }
  }

  for (let i = 0; i < WORD_LENGTH; i++) {
    if (guessArr[i] === targetArr[i]) status[i] = "correct";
  }

  for (let i = 0; i < WORD_LENGTH; i++) {
    if (status[i] === "correct") continue;
    const g = guessArr[i];
    if (g !== undefined && counts[g]) {
      status[i] = "present";
      counts[g]--;
    }
  }

  results.value[row.value] = status.map((s) => s);
  guesses.value[row.value] = guess;

  if (guess === TARGET) {
    message.value = "WOHO!";
    won.value = true;
    row.value = MAX_GUESSES;

    emit("solved", "advent");
  } else {
    row.value++;
    current.value = "";
    message.value = "";
  }
};
const handleKeyDown = (e: KeyboardEvent) => {
  if (row.value >= MAX_GUESSES) return;
  const key = e.key;
  if (key === "Enter") {
    submitGuess();
  } else if (key === "Backspace") {
    current.value = current.value.slice(0, -1);
  } else if (/^[a-zA-ZÅÄÖåäö]$/.test(key)) {
    if (current.value.length < WORD_LENGTH) {
      current.value = sanitizeInput(current.value + key);
    }
  }
};

onMounted(() => {
  window.addEventListener("keydown", handleKeyDown);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleKeyDown);
});

const rootRef = ref<HTMLElement | null>(null);
let ro: ResizeObserver | null = null;
const fit = () => {
  if (!rootRef.value) return;
  const el = rootRef.value;
  const parent = el.parentElement as HTMLElement | null;
  if (!parent) return;

  const availW = parent.clientWidth;
  const availH = parent.clientHeight;

  el.style.transform = "none";
  const naturalW = el.offsetWidth;
  const naturalH = el.offsetHeight;

  const scale = Math.min(1, availW / naturalW, availH / naturalH);
  el.style.transform = `scale(${scale})`;
  el.style.transformOrigin = "top center";
};

onMounted(() => {
  nextTick(() => fit());
  ro = new ResizeObserver(() => fit());
  if (rootRef.value && rootRef.value.parentElement) {
    ro.observe(rootRef.value.parentElement);
  }
  window.addEventListener("resize", fit);
});

onBeforeUnmount(() => {
  if (ro && rootRef.value && rootRef.value.parentElement)
    ro.unobserve(rootRef.value.parentElement);
  ro = null;
  window.removeEventListener("resize", fit);
});
</script>

<template>
  <div ref="rootRef" class="wordle-root">
    <div class="card-header">
      <h2 v-if="!won && row < MAX_GUESSES">Ordle</h2>
    </div>

    <div v-if="won" class="secret-wrap">
      <h1>WOHO! Då vet du koden</h1>
    </div>

    <div v-else v-if="!won && row < MAX_GUESSES" class="grid">
      <div v-for="r in rows" :key="r" class="row">
        <div
          v-for="c in WORD_LENGTH"
          :key="c"
          class="cell"
          :class="cellClass(r, c - 1)"
        >
          <span>
            {{
              r === row && c - 1 < current.length
                ? current[c - 1]
                : guesses[r]
                ? guesses[r][c - 1]
                : ""
            }}
          </span>
        </div>
      </div>
    </div>
    <div v-if="!won && row >= MAX_GUESSES" class="loser">
      <p>BUHU</p>
      <button class="btn" @click="resetGame">
        Verkar som du behöver extra försök
      </button>
    </div>

    <div class="controls">
      <div class="input-row">
        <!-- <label for="hiddenInput" class="sr-only">Skriv gissning</label> -->
        <input
          id="hiddenInput"
          class="hidden-input"
          v-model="current"
          @input="current = sanitizeInput(current)"
          :maxlength="WORD_LENGTH"
          autocomplete="off"
        />
        <!-- <button @click="submitGuess" class="btn">Skicka</button> -->
      </div>
    </div>
  </div>
</template>

<style scoped>
.wordle-root {
  max-height: 99%;
  width: 80%;
  max-width: 360px;
  margin: 0 auto;
  text-align: center;
  padding: 0.45rem;
  background: rgba(255, 255, 255, 0.95);
  position: relative;
  overflow: hidden;
  transition: transform 0.12s ease;
  will-change: transform;
  flex-direction: column;
}

.grid {
  display: grid;
  gap: 6px;
  margin: 0.6rem 0;
  width: 100%;
}
.row {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 6px;
}
h2 {
  margin-top: 10px;
}
.cell {
  width: 100%;
  aspect-ratio: 1 / 1;
  border: 2px solid #979191;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 800;
  font-size: clamp(0.6rem, 1.6vw, 1rem);
  text-transform: uppercase;
  background: #fff;
  color: #111;
  user-select: none;
  border-radius: 4px;
}
.cell.correct {
  background: linear-gradient(180deg, #6aaa64, #4f8f4a);
  border-color: #4f8f4a;
  color: white;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.12) inset;
}
.cell.present {
  background: linear-gradient(180deg, #d6c06a, #b99a3f);
  border-color: #b99a3f;
  color: white;
}
.cell.absent {
  background: linear-gradient(180deg, #8a8d90, #5f6163);
  border-color: #5f6163;
  color: white;
}
.cell.pending {
  background: #fff;
}
.controls {
  margin-top: 0.45rem;
}
.input-row {
  display: flex;
  gap: 0.5rem;
  justify-content: center;
  align-items: center;
}
.hidden-input {
  width: 0.1px;
  height: 0.1px;
  opacity: 0;
  position: absolute;
  pointer-events: none;
}
.btn {
  padding: 0.32rem 0.5rem;
  border: none;
  background: linear-gradient(180deg, #c41e3a, #8b0f1f);
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.95rem;
}
.btn:hover {
  transform: translateY(-1px);
}
.btn.ghost {
  background: transparent;
  border: 1px solid #c41e3a;
  color: #c41e3a;
}
.small {
  color: #666;
  font-size: 0.85rem;
  margin-top: 0.5rem;
}
.loser {
  margin-top: 0.5rem;
  font-size: 2rem;
}

.loser p {
  margin-bottom: 0.4rem;
  font-weight: 600;
}

@media (max-width: 600px) {
  .wordle-root {
    max-width: 90vw;
    padding: 0.25rem;
    border-radius: 6px;
  }

  .card-header h2 {
    font-size: 0.95rem;
  }

  .grid {
    gap: 4px;
    width: 80%;
  }

  .row {
    gap: 4px;
  }

  .cell {
    border-radius: 4px;
    font-size: clamp(0.55rem, 3.2vw, 0.85rem);
  }

  .secret-code {
    font-size: 0.95rem;
  }
}
</style>
