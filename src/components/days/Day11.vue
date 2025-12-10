<script setup lang="ts">
import { ref } from "vue";
const count = ref(0);
const guess = ref("");
const errorMessage = ref("");
const win = ref(false);

const movie1 = "Snakes on a plane";
const movie2 = "top gun";

const handleGuess = (title: string) => {
  let movie = "";

  if (count.value === 1) {
    movie = movie1;
  } else if (count.value === 2) {
    movie = movie2;
  }

  if (title.toLowerCase() === movie.toLowerCase()) {
    count.value++;
    guess.value = "";
    errorMessage.value = "";

    if (count.value === 3) {
      win.value = true;
    }
  } else {
    errorMessage.value = "NOPE";
  }
};
</script>

<template>
  <div class="container11">
    <div v-if="count === 0" class="startContainer">
      <h1>Gissa filmen</h1>
      <button @click="count = 1" class="go">GO -></button>
    </div>

    <div v-if="count > 0 && !win" class="img-container">
      <img
        v-if="count === 1"
        src="https://img.buzzfeed.com/buzzfeed-static/static/2017-07/14/8/enhanced/buzzfeed-prod-fastlane-01/enhanced-1799-1500035920-10.jpg?output-format=auto&output-quality=auto"
        alt="movietitle"
      />
      <img
        v-else
        src="https://img.buzzfeed.com/buzzfeed-static/static/2017-07/14/5/enhanced/buzzfeed-prod-fastlane-01/enhanced-29256-1500026035-1.jpg?output-format=auto&output-quality=auto"
        alt="movie2"
      />
      <div v-if="!win" class="guess-container">
        <input
          :placeholder="errorMessage ? errorMessage : 'Movie title...'"
          v-model="guess"
          type="text"
        /><button @click="handleGuess(guess)">Gissa!</button>
      </div>
    </div>
    <div v-if="win" class="code"><h1>Snyggt jobbat "filmrebuskingen"</h1></div>
  </div>
</template>

<style scoped>
.container11 {
  height: 100%;
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
  height: 100%;
}
.img-container > img {
  max-height: 100%;
  object-fit: cover;
  max-width: 100%;
}
.guess-container > input {
  border-radius: 4px;
  padding: 4px 5px;
  outline: none;
  border: 1px solid #767272;
  margin-right: 10px;
}
.guess-container > button {
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
.code {
  max-width: 100%;
}
h1 {
  text-align: center;
  font-size: 1.5rem;
  margin: 0;
}
</style>
