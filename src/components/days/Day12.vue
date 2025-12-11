<script setup lang="ts">
declare global {
  interface Window {
    code: string;
  }
}

const code = "vad du vill";

window.code = code;

function handleClick() {
  const input = prompt(
    "Gör en funktion eller logga koden. Tänk på att koden är vad du vill. let code = ???;  "
  );
  if (input !== null) {
    const result = eval(input);
    alert(result !== undefined ? result : "Vart hittar du detta nu då?");
  }
}
</script>

<template>
  <div class="container12">
    <button @click="handleClick" class="big-red-button"></button>
  </div>
</template>

<style scoped>
.container12 {
  height: 100%;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(
    135deg,
    #0f4d2e 0%,
    #c41e3a 25%,
    #0f4d2e 50%,
    #c41e3a 75%,
    #0f4d2e 100%
  );
}
.big-red-button {
  background-color: #dc3545;
  color: white;
  font-size: 2rem;
  height: 4rem;
  width: 4rem;
  border: none;
  border-radius: 50%;
  cursor: pointer;
  font-weight: bold;

  transition: all 0.3s ease;
  text-transform: uppercase;
}

.big-red-button:hover {
  background-color: #c82333;
  transform: scale(1.05);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.4);
}

.big-red-button:active {
  transform: scale(0.98);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}
</style>
