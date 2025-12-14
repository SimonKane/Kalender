<script setup lang="ts">
import { ref, computed } from "vue";

const userCode = ref(`const unlock = () = {
  return [12, 15, 19, 5, 14, 15, 18, 4]
    .map(n => String.fromCharCode(n + 64))
    .join("")
}
`);

const result = ref("");
const error = ref("");

const isRunnable = computed(() => {
  return /\(\)\s*=>/.test(userCode.value);
});

const runCode = () => {
  error.value = "";
  result.value = "";

  if (!isRunnable.value) {
    error.value = "Nix pix";
    return;
  }

  result.value = "lösenord";
};
</script>

<template>
  <div class="day15">
    <div class="panel">
      <p class="question">
        <strong>Varför funkar den inte?</strong>
      </p>

      <textarea class="text15" v-model="userCode" spellcheck="false"></textarea>

      <div class="button-row">
        <button v-if="!result" class="button15" @click="runCode">Kör</button>
        <p v-if="error" class="error">❌ {{ error }}</p>
      </div>

      <p v-if="result" class="success">
        output = "<strong>{{ result.toLowerCase() }}</strong
        >"
      </p>
    </div>
  </div>
</template>

<style scoped>
.day15 {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 16px;
  box-sizing: border-box;
  overflow-y: auto;
}

.panel {
  width: min(720px, 100%);
  max-width: 100%;
  display: flex;
  flex-direction: column;
  gap: 12px;
  box-sizing: border-box;
}

.question {
  margin: 0;
  font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial;
  font-size: 16px;
  line-height: 1.3;
}

.text15 {
  width: 100%;
  height: 140px;
  padding: 10px;
  box-sizing: border-box;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", monospace;
  font-size: 13px;
  line-height: 1.5;
  border-radius: 8px;
  border: 1px solid rgba(0, 0, 0, 0.18);
  outline: none;
  resize: none;
  overflow: hidden;
}

.text15:focus {
  border-color: rgba(46, 125, 50, 0.6);
  box-shadow: 0 0 0 3px rgba(46, 125, 50, 0.12);
}

.button-row {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
}

.button15 {
  padding: 10px 14px;
  cursor: pointer;

  border: 0;
  border-radius: 8px;
  background: #2e7d32;
  color: white;
  font-weight: 800;
  font-size: 14px;
}

.button15:hover {
  filter: brightness(1.05);
}

.button15:active {
  transform: scale(0.98);
}

.error {
  margin: 0;
  color: #c62828;
  font-weight: 800;
  font-family: ui-monospace, monospace;
  font-size: 14px;

  max-width: 100%;
  word-break: break-word;
}

.success {
  margin: 0;
  color: #2e7d32;
  font-weight: 900;
  font-size: 16px;
  font-family: ui-sans-serif, system-ui;
  word-break: break-word;
}

@media (max-width: 480px) {
  .day15 {
    padding: 8px;
    align-items: flex-start;
    overflow: hidden;
  }

  .panel {
    gap: 6px;
    width: 100%;
    max-height: 100%;
  }

  .question {
    font-size: 13px;
    line-height: 1.2;
  }

  .text15 {
    height: 105px;
    font-size: 9px;
    line-height: 1.25;
    padding: 6px;
    overflow: hidden;
  }

  .button-row {
    gap: 8px;
  }

  .button15 {
    padding: 7px 10px;
    font-size: 12px;
  }

  .error {
    font-size: 11px;
  }

  .success {
    font-size: 12px;
    line-height: 1.2;
  }
}
</style>
