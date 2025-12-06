<script setup lang="ts">
import { ref } from "vue";
const finnishGame = ref(false);

const notes = ["c", "d", "e", "f", "g", "a", "b"];

const correctNoteSequence = ["c", "f", "f", "g", "f", "e", "d", "d"];
let playerSequence: string[] = [];

const c = new URL("../../assets/notes/c.mp3", import.meta.url).href;
const d = new URL("../../assets/notes/d.mp3", import.meta.url).href;
const e = new URL("../../assets/notes/e.mp3", import.meta.url).href;
const f = new URL("../../assets/notes/f.mp3", import.meta.url).href;
const g = new URL("../../assets/notes/g.mp3", import.meta.url).href;
const a = new URL("../../assets/notes/a.mp3", import.meta.url).href;
const b = new URL("../../assets/notes/b.mp3", import.meta.url).href;
import image from "../../assets/notes/note.png";
const noteMap: Record<string, string> = { c, d, e, f, g, a, b };
const rotations = [-15, 8, -5, 12, -10, 6, -8];
const checkAnswer = (arr: any) => {
  const index = arr.length - 1;

  for (let i = 0; i < arr.length; i++) {
    if (correctNoteSequence[index] === arr[index]) {
      if (correctNoteSequence.join("") === arr.join("")) {
        finnishGame.value = true;
      }
    } else {
      playerSequence = [];
    }
  }
  
};

const playNote = (note: string) => {
  const src = noteMap[note];
  if (!src) return;
  const audio = new Audio(src);
  audio.play();
  playerSequence.push(note);
  checkAnswer(playerSequence);
};
</script>

<template>
  <div :class="['seven-container', finnishGame ? 'finished' : '']">
    <h1>
      {{
        finnishGame
          ? `Jättefint! "Bra jävla låt"`
          : "We Wish You A Merry Christmas!"
      }}
    </h1>
    <div class="button-container">
      <button
        :key="note"
        v-for="(note, index) in notes"
        @click="playNote(note)"
        :style="{ transform: `rotate(${rotations[index]}deg)` }"
      >
        <img :src="image" alt="Note" />
      </button>
    </div>
  </div>
</template>

<style scoped>
.seven-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  height: 100vh;
  min-width: 100%;
  overflow: hidden;
}

.finished {
  box-shadow: 0 0 40px 15px rgba(255, 215, 0, 0.9),
    0 0 80px 30px rgba(255, 215, 0, 0.6), 0 0 120px 45px rgba(255, 215, 0, 0.4);
}
.seven-container h1 {
  color: #358b56;

  text-shadow: 0 0 10px rgba(0, 255, 0, 0.5), 0 0 20px rgba(255, 215, 0, 0.3),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  margin-top: 1.5rem;
  font-size: clamp(1rem, 3vw, 2rem);
}

.button-container {
  display: grid;
  grid-template-columns: repeat(3, minmax(70px, 1fr));
  gap: 0.5rem;
  width: 100%;
  max-width: 260px;
  margin-bottom: 2rem;
}

.button-container > button {
  background: none;
  outline: none;
  border: none;
  transition: all 0.2s ease;
}
.button-container > button:hover {
  scale: 1.1;
}
.button-container > button:active {
  scale: 1;
}
.button-container > button > img {
  height: 40px;
  cursor: pointer;
}
.button-container > button:last-child {
  grid-column: span 3;
  margin-top: 0px;
}
@media (max-width: 600px) {
  .seven-container > h1 {
    margin-top: 0px;
  }
  .button-container {
    margin-top: 10px;
    gap: 2px;
    grid-template-columns: repeat(2, minmax(70px, 1fr));
  }
  .button-container > button > img {
    height: 25px;
  }
  .button-container > button:last-child {
    grid-column: span 2;
    margin: -20px auto 0 auto;
    width: 40px;
  }
}
</style>
