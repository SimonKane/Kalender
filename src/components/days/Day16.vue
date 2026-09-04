<script setup lang="ts">
import { ref, computed } from "vue";

const clickedWords = ref<string[]>([]);

const toggleWord = (word: string) => {
  if (clickedWords.value.includes(word)) {
    clickedWords.value = clickedWords.value.filter((w) => w !== word);
  } else {
    clickedWords.value.push(word);
  }
};

const isClicked = (word: string) => clickedWords.value.includes(word);

const puzzleWords = [
  { word: "Children", suffix: " " },
  { word: "hung", suffix: " " },
  { word: "ribbons", suffix: " " },
  { word: "inviting", suffix: " " },
  { word: "snowflakes", suffix: " " },
  { word: "to", suffix: " " },
  { word: "mingle", suffix: ". " },
  { word: "Angels", suffix: " " },
  { word: "sang", suffix: "; " },
  { word: "trees", suffix: " " },
  { word: "reached", suffix: " " },
  { word: "ever", suffix: " " },
  { word: "eastward", suffix: "." },
];

const revealedLetters = computed(() => {
  return puzzleWords
    .map(({ word }) => word.toLowerCase())
    .filter((word) => clickedWords.value.includes(word))
    .map((word) => word[0])
    .join("");
});
</script>

<template>
  <div class="day16">
    <div class="text-container">
      <h2>A message is hiding in plain sight</h2>
      <p class="christmas-text">
        Click the festive words below and watch what remains.
      </p>
      <p class="christmas-text puzzle-sentence">
        <span
          v-for="item in puzzleWords"
          :key="item.word"
          class="clickable-word"
          :class="{ clicked: isClicked(item.word.toLowerCase()) }"
          @click="toggleWord(item.word.toLowerCase())"
        ><span
            v-for="(letter, index) in item.word"
            :key="index"
            class="letter"
            :class="{ first: index === 0 }"
          >{{ letter }}</span>{{ item.suffix }}</span>
      </p>

      <div v-if="clickedWords.length === puzzleWords.length" class="result">
        <p class="success-message">
          The code is: <strong>{{ revealedLetters }}</strong>
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.day16 {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 20px;
  box-sizing: border-box;
  overflow-y: auto;
  -ms-overflow-style: none;
  scrollbar-width: none;
}

.day16::-webkit-scrollbar {
  display: none;
}

.text-container {
  max-width: 800px;
  width: 100%;
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}

.christmas-text {
  font-family: Georgia, serif;
  font-size: 14px;
  line-height: 1.4;
  color: #2d2d2d;
  margin-bottom: 10px;
  text-align: justify;
}

.clickable-word {
  cursor: pointer;
  font-style: italic;

  transition: all 0.3s ease;
  display: inline-block;
}

.letter {
  display: inline-block;
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.clicked .letter:not(.first) {
  opacity: 0;
  transform: scale(0);
}

.result {
  margin-top: 15px;
  padding: 15px;
  background: linear-gradient(135deg, #0f4d2e 0%, #1a5c3a 100%);
  border-radius: 10px;
  text-align: center;
}

.success-message {
  color: white;
  font-size: 22px;
  margin: 0;
  font-family: ui-sans-serif, system-ui;
}

.success-message strong {
  color: #ffd700;
  font-size: 28px;
}

@media (max-width: 480px) {
  .day16 {
    padding: 0;
  }

  .text-container {
    padding: 15px;
    width: 100%;
    max-width: 100%;
  }

  .christmas-text {
    font-size: 9px;
    line-height: 1.5;
    text-align: left;
    margin-bottom: 10px;
  }

  .result {
    margin-top: 12px;
    padding: 12px;
  }

  .success-message {
    font-size: 16px;
  }

  .success-message strong {
    font-size: 20px;
  }
}
</style>
