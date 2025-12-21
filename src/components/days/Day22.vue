<script setup lang="ts">
import { ref, onUnmounted } from "vue";
import grinchImage from "../../assets/grinch.png";
import noteC from "../../assets/notes/c.mp3";
import noteA from "../../assets/notes/a.mp3";
import noteG from "../../assets/notes/g.mp3";
import noteF from "../../assets/notes/f.mp3";
import noteD from "../../assets/notes/d.mp3";
import noteE from "../../assets/notes/e.mp3";

const score = ref(0);
const gameOver = ref(false);
const gameWon = ref(false);
const gameMessage = ref("");
const emojiVisible = ref(false);
const currentEmoji = ref("🥷");
const emojiPosition = ref({ top: "50%", left: "50%" });
const gameStarted = ref(false);
const showCode = ref(false);

let spawnTimeout: number | null = null;
let hideTimeout: number | null = null;

const useImage = ref(false);

// Note sequence: c,a,a,g,g,f,d,c,c,d,d,g,e,c
const noteSequence = [
  noteC,
  noteA,
  noteA,
  noteG,
  noteG,
  noteF,
  noteD,
  noteC,
  noteC,
  noteD,
  noteD,
  noteG,
  noteE,
  noteC,
];

function playNote(index: number) {
  if (index >= 0 && index < noteSequence.length) {
    const audio = new Audio(noteSequence[index]);
    audio.play().catch(() => {});
  }
}

function getRandomPosition() {
  const top = Math.random() * 75 + 12.5;
  const left = Math.random() * 75 + 12.5;
  return { top: `${top}%`, left: `${left}%` };
}

function spawnEmoji() {
  if (gameOver.value || gameWon.value) return;

  if (score.value === 7) {
    currentEmoji.value = "🎅";
    useImage.value = false;
  } else {
    // Always use Grinch image for regular targets
    currentEmoji.value = "🥷"; // Reset to non-Santa emoji
    useImage.value = true;
  }

  emojiPosition.value = getRandomPosition();
  emojiVisible.value = true;

  // Hide emoji after 1 second (short time)
  hideTimeout = setTimeout(() => {
    if (emojiVisible.value) {
      // If it's Santa, user succeeded by NOT clicking - continue game
      if (currentEmoji.value === "🎅") {
        emojiVisible.value = false;
        score.value++; // Award point for avoiding Santa

        // Check if won
        if (score.value >= 14) {
          winGame();
          return;
        }

        // Spawn next emoji
        spawnTimeout = setTimeout(() => {
          spawnEmoji();
        }, 500);
      } else {
        // User missed a regular target - game over
        endGame("Lite seg där, slowie");
      }
    }
  }, 1000);
}

function clickEmoji() {
  if (!emojiVisible.value) return;

  // Clear the hide timeout
  if (hideTimeout) {
    clearTimeout(hideTimeout);
    hideTimeout = null;
  }

  // Check if user clicked Santa
  if (currentEmoji.value === "🎅") {
    endGame("Oh no, inte tomten ju!");
    return;
  }

  // Successful click
  playNote(score.value); // Play the note for current score (0-indexed)
  score.value++;
  emojiVisible.value = false;

  // Check if won
  if (score.value >= 14) {
    winGame();
    return;
  }

  // Spawn next emoji after short delay
  spawnTimeout = setTimeout(() => {
    spawnEmoji();
  }, 500);
}

function endGame(message: string) {
  gameOver.value = true;
  gameMessage.value = message;
  emojiVisible.value = false;
  if (spawnTimeout) clearTimeout(spawnTimeout);
  if (hideTimeout) clearTimeout(hideTimeout);
}

function winGame() {
  gameWon.value = true;
  gameMessage.value = "Woho!";
  emojiVisible.value = false;
  showCode.value = true;
  if (spawnTimeout) clearTimeout(spawnTimeout);
  if (hideTimeout) clearTimeout(hideTimeout);
}

function startGame() {
  score.value = 0;
  gameOver.value = false;
  gameWon.value = false;
  gameMessage.value = "";
  emojiVisible.value = false;
  gameStarted.value = true;
  showCode.value = false;
  spawnEmoji();
}

function playAgain() {
  startGame();
}

onUnmounted(() => {
  if (spawnTimeout) clearTimeout(spawnTimeout);
  if (hideTimeout) clearTimeout(hideTimeout);
});
</script>

<template>
  <div class="game-container">
    <div v-if="!gameStarted" class="start-screen">
      <h2>Skjut grinchen!</h2>
      <button @click="startGame" class="start-btn">Starta spel</button>
    </div>

    <div
      v-else-if="gameOver || gameWon"
      class="game-over"
      :class="{ failed: gameOver, won: gameWon }"
    >
      <h2>{{ gameMessage }}</h2>
      <div v-if="showCode" class="code-reveal">
        <div class="code">grinchskyttekingen</div>
      </div>
      <button v-if="!gameWon" @click="playAgain" class="play-again-btn">
        Spela igen
      </button>
    </div>

    <div v-else class="game-area">
      <div class="white-container">
        <div
          v-if="emojiVisible"
          class="emoji"
          :style="{ top: emojiPosition.top, left: emojiPosition.left }"
          @click="clickEmoji"
        >
          <img
            v-if="useImage"
            :src="grinchImage"
            alt="Grinch"
            class="grinch-img"
          />
          <span v-else>{{ currentEmoji }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.game-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px;
  background: linear-gradient(to bottom, #0a0e27 0%, #1a1f4a 50%, #0a0e27 100%);
  background-image: radial-gradient(2px 2px at 20% 30%, white, transparent),
    radial-gradient(2px 2px at 60% 70%, white, transparent),
    radial-gradient(1px 1px at 50% 50%, white, transparent),
    radial-gradient(1px 1px at 80% 10%, white, transparent),
    radial-gradient(2px 2px at 90% 60%, white, transparent),
    radial-gradient(1px 1px at 33% 80%, white, transparent),
    radial-gradient(1px 1px at 15% 60%, white, transparent),
    linear-gradient(to bottom, #0a0e27 0%, #1a1f4a 50%, #0a0e27 100%);
}

.start-screen,
.game-over {
  text-align: center;
}

.start-screen h2,
.game-over h2 {
  margin: 0 0 10px 0;
  color: #ffd700;
  font-size: 1.5em;
  text-shadow: 0 0 10px rgba(255, 215, 0, 0.8), 0 0 20px rgba(255, 215, 0, 0.6),
    0 0 30px rgba(255, 215, 0, 0.4);
}

.game-over.failed h2 {
  color: #ff4444;
  text-shadow: 0 0 10px rgba(255, 68, 68, 0.8), 0 0 20px rgba(255, 68, 68, 0.6),
    0 0 30px rgba(255, 68, 68, 0.4);
}

.start-screen p {
  margin: 10px 0;
  font-size: 1.2em;
  color: #555;
}

.start-btn,
.play-again-btn {
  margin-top: 10px;
  padding: 8px 20px;
  font-size: 0.9em;
  background: linear-gradient(135deg, #c41e3a 0%, #165b33 100%);
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  font-weight: bold;
  box-shadow: 0 2px 8px rgba(196, 30, 58, 0.3);
}

.start-btn:hover,
.play-again-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(196, 30, 58, 0.5);
}

.start-btn:active,
.play-again-btn:active {
  transform: translateY(0);
}

.code-reveal p {
  margin: 0 0 8px 0;
  font-size: 0.9em;
  color: #ffd700;
  text-shadow: 0 0 5px rgba(255, 215, 0, 0.5);
}

.code {
  font-size: 1.3em;
  font-weight: bold;
  color: #ffd700;
  font-family: "Courier New", monospace;
  letter-spacing: 1px;
  padding: 10px;
  text-shadow: 0 0 10px rgba(255, 215, 0, 0.8);
}

.game-area {
  width: 100%;
  max-height: 100%;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.white-container {
  position: relative;
  width: 100%;
  height: 500px;
  background: linear-gradient(to bottom, #0a0e27 0%, #1a1f4a 50%, #0a0e27 100%);
  background-image: radial-gradient(2px 2px at 20% 30%, white, transparent),
    radial-gradient(2px 2px at 60% 70%, white, transparent),
    radial-gradient(1px 1px at 50% 50%, white, transparent),
    radial-gradient(1px 1px at 80% 10%, white, transparent),
    radial-gradient(2px 2px at 90% 60%, white, transparent),
    radial-gradient(1px 1px at 33% 80%, white, transparent),
    radial-gradient(1px 1px at 15% 60%, white, transparent),
    linear-gradient(to bottom, #0a0e27 0%, #1a1f4a 50%, #0a0e27 100%);
  overflow: hidden;
}

.emoji {
  position: absolute;
  font-size: 2.5em;
  cursor: pointer;
  transform: translate(-50%, -50%);
  animation: pop-in 0.2s ease-out;
  user-select: none;
  transition: transform 0.1s;
  display: flex;
  align-items: center;
  justify-content: center;
}

.grinch-img {
  width: 50px;
  height: 50px;
  object-fit: contain;
  border-radius: 50%;
  padding: 5px;
}

.emoji:hover {
  transform: translate(-50%, -50%) scale(1.2);
}

.emoji:active {
  transform: translate(-50%, -50%) scale(0.9);
}

@keyframes pop-in {
  0% {
    transform: translate(-50%, -50%) scale(0);
  }
  50% {
    transform: translate(-50%, -50%) scale(1.2);
  }
  100% {
    transform: translate(-50%, -50%) scale(1);
  }
}

@media (max-width: 768px) {
  .white-container {
    height: 400px;
  }

  .emoji {
    font-size: 2em;
  }

  .grinch-img {
    width: 40px;
    height: 40px;
  }

  .code {
    font-size: 0.6rem;
  }
}
</style>
