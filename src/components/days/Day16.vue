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

const revealedLetters = computed(() => {
  const wordOrder = [
    "under",
    "granen",
    "lagen",
    "nissar",
    "renar",
    "advent",
    "jultomten",
  ];
  return wordOrder
    .filter((word) => clickedWords.value.includes(word))
    .map((word) => word[0])
    .join("");
});
</script>

<template>
  <div class="day16">
    <div class="text-container">
      <p class="christmas-text">
        I fjärran hördes klockorna ringa från kyrkan, och granarna stod höga och
        gröna med sina vita snötäcken. Familjer samlades runt eldstaden för att
        dela berättelser om gamla
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('under') }"
          @click="toggleWord('under')"
          ><span class="letter first">u</span><span class="letter">n</span
          ><span class="letter">d</span><span class="letter">e</span
          ><span class="letter">r</span></span
        >
        och nya traditioner.
      </p>
      <p class="christmas-text">
        Små ljus tändes i fönstren och skuggor dansade mot de snötäckta
        markerna. Vid
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('granen') }"
          @click="toggleWord('granen')"
          ><span class="letter first">g</span><span class="letter">r</span
          ><span class="letter">a</span><span class="letter">n</span
          ><span class="letter">e</span><span class="letter">n</span></span
        >
        hängde glittrande kulor och katten
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('lagen') }"
          @click="toggleWord('lagen')"
          ><span class="letter first">l</span><span class="letter">a</span
          ><span class="letter">g</span><span class="letter">e</span
          ><span class="letter">n</span></span
        >
        nära kaminen.
      </p>
      <p class="christmas-text">
        Barn sjöng julsånger medan de äldre log och mindes sina barndomsjular.
        Små
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('nissar') }"
          @click="toggleWord('nissar')"
          ><span class="letter first">n</span><span class="letter">i</span
          ><span class="letter">s</span><span class="letter">s</span
          ><span class="letter">a</span><span class="letter">r</span></span
        >
        spred glädje överallt.
      </p>
      <p class="christmas-text">
        Utanför galopperade
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('renar') }"
          @click="toggleWord('renar')"
          ><span class="letter first">r</span><span class="letter">e</span
          ><span class="letter">n</span><span class="letter">a</span
          ><span class="letter">r</span></span
        >
        genom snön. I köket bakades kakor och doften blandades med värmen från
        glöggen. Varje
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('advent') }"
          @click="toggleWord('advent')"
          ><span class="letter first">a</span><span class="letter">d</span
          ><span class="letter">v</span><span class="letter">e</span
          ><span class="letter">n</span><span class="letter">t</span></span
        >
        hade sitt speciella.
      </p>
      <p class="christmas-text">
        Det var en kall decemberkväll när snön föll tätt över stugornas tak.
        Barnen väntade spänt på
        <span
          class="clickable-word"
          :class="{ clicked: isClicked('jultomten') }"
          @click="toggleWord('jultomten')"
          ><span class="letter first">j</span><span class="letter">u</span
          ><span class="letter">l</span><span class="letter">t</span
          ><span class="letter">o</span><span class="letter">m</span
          ><span class="letter">t</span><span class="letter">e</span
          ><span class="letter">n</span></span
        >
        medan stjärnorna glittrade.
      </p>

      <div v-if="clickedWords.length === 7" class="result">
        <p class="success-message">
          Koden är ish: <strong>{{ revealedLetters }}</strong>
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
