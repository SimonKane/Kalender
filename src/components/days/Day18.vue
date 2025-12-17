<script setup lang="ts">
import { ref } from "vue";
const count = ref(0);
const guess = ref("");
const errorMessage = ref("");
const win = ref(false);

const block1 = "Star Wars";
const block2 = "Family Guy";
const block3 = "nalle puh";
const block4 = "toy story";
const block5 = "sagan om ringen";
const block6 = "tomten";

const handleGuess = (title: string) => {
  let movie = "";

  if (count.value === 1) {
    movie = block1;
  } else if (count.value === 2) {
    movie = block2;
  } else if (count.value === 3) {
    movie = block3;
  } else if (count.value === 4) {
    movie = block4;
  } else if (count.value === 5) {
    movie = block5;
  } else if (count.value === 6) {
    movie = block6;
  }

  if (title.toLowerCase() === movie.toLowerCase()) {
    count.value++;
    guess.value = "";
    errorMessage.value = "";

    if (count.value === 7) {
      win.value = true;
    }
  } else {
    errorMessage.value = "NOPE";
  }
};
</script>

<template>
  <div class="container18">
    <div v-if="count === 0" class="startContainer">
      <h1>Gissa Filmen / Serien / Karaktären</h1>
      <button @click="count = 1" class="go">GO -></button>
    </div>

    <div v-if="count > 0 && !win" class="img-container">
      <img v-if="count === 1" src="/src/assets/block1.png" alt="movietitle" />
      <img v-if="count === 2" src="/src/assets/block2.png" alt="movie2" />
      <img v-if="count === 3" src="/src/assets/block3.png" alt="movie3" />
      <img v-if="count === 4" src="/src/assets/block4.png" alt="movie4" />
      <img v-if="count === 5" src="/src/assets/block5.png" alt="movie5" />
      <img v-if="count === 6" src="/src/assets/block6.png" alt="movie6" />

      <div v-if="!win" class="guess-container">
        <input
          :placeholder="errorMessage ? errorMessage : 'Gissa här...'"
          v-model="guess"
          type="text"
        /><button @click="handleGuess(guess)">Gissa!</button>
      </div>
    </div>
    <div v-if="win" class="code">
      <h1>Snyggt jobbat</h1>
      <span class="code-text">"filmseriekaraktärsgissarkingen"</span>
    </div>
  </div>
</template>

<style scoped>
.container18 {
  height: 100%;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  padding: 10px;
  box-sizing: border-box;
}
.startContainer {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 20px;
  padding: 10px;
}
.startContainer > h1 {
  font-size: 1rem;
  text-align: center;
}
.go {
  background: linear-gradient(135deg, #1a472a 0%, #2d5f3f 50%, #1a472a 100%);
  color: #ffee00;
  text-shadow: 0 0 20px rgb(251, 255, 0), 0 0 20px rgb(255, 255, 255),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  border: none;
  padding: 4px 5px;
  font-size: 1.2rem;
  font-family: "Mountains of Christmas", cursive;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
  transition: all 0.3s ease;
  letter-spacing: 2px;
}

.go:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
}

.go:active {
  transform: translateY(0);
  box-shadow: 0 2px 10px rgba(102, 126, 234, 0.4);
}
.img-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  max-height: calc(100% - 20px);
  max-width: 100%;
  width: 100%;
  height: 100%;
  gap: 10px;
}
.img-container > img {
  max-height: 60%;
  max-width: 95%;
  width: auto;
  height: auto;
  object-fit: contain;
  flex-shrink: 1;
}
.guess-container {
  margin-top: 0.5rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 5px;
  width: 100%;
  max-width: 300px;
  text-align: center;
}
.guess-container > input {
  border-radius: 4px;
  padding: 4px 5px;
  outline: none;
  border: 1px solid #767272;
  max-width: 80%;
}
.guess-container > button {
  background: linear-gradient(135deg, #1a472a 0%, #2d5f3f 50%, #1a472a 100%);
  color: #ffee00;
  text-shadow: 0 0 20px rgb(251, 255, 0), 0 0 20px rgb(255, 255, 255),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  border: none;
  padding: 4px 5px;
  font-size: 0.5rem;
  font-family: "Mountains of Christmas", cursive;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
  transition: all 0.3s ease;
  letter-spacing: 2px;
}
.code {
  max-width: 95%;
  text-align: center;
  padding: 10px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.code > h1 {
  font-size: clamp(1rem, 5vw, 2rem);
  line-height: 1.2;
  margin-top: -40px;
  margin-bottom: 40px;
}
.code-text {
  font-size: clamp(0.5rem, 2.5vw, 0.9rem);
  display: block;
  white-space: nowrap;
  max-width: 100%;
  overflow: hidden;
  font-weight: bold;
}
</style>
