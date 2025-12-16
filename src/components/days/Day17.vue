<script setup lang="ts">
const codeSnippets = [
  'console.log(1 + "7")',
  'console.log([] + " " + []);',
  'console.log((+("1" + "7")).toString())',
  'console.log(2 != "2")',
  'console.log("snö" * 3 + "3")',
];
</script>

<template>
  <div class="day17">
    <div class="code-container">
      <pre class="code-block">{{ codeSnippets.join("\n+\n") }}</pre>
    </div>
  </div>
</template>

<style scoped>
.day17 {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  background: radial-gradient(2px 2px at 20% 30%, white, transparent),
    radial-gradient(2px 2px at 60% 70%, white, transparent),
    radial-gradient(1px 1px at 50% 50%, white, transparent),
    radial-gradient(1px 1px at 80% 10%, white, transparent),
    radial-gradient(2px 2px at 90% 60%, white, transparent),
    radial-gradient(1px 1px at 33% 80%, white, transparent),
    radial-gradient(2px 2px at 15% 90%, white, transparent),
    radial-gradient(1px 1px at 70% 20%, white, transparent),
    radial-gradient(1px 1px at 40% 40%, white, transparent),
    radial-gradient(2px 2px at 25% 60%, white, transparent),
    radial-gradient(1px 1px at 85% 85%, white, transparent),
    radial-gradient(1px 1px at 10% 50%, white, transparent),
    linear-gradient(135deg, #0a1929 0%, #1a2744 50%, #0d1b2a 100%);
  background-size: 100% 100%;
}

.code-container {
  width: 100%;
  height: auto;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
}

.code-block {
  font-family: "Courier New", Consolas, monospace;
  font-size: 11px;
  font-weight: bold;
  line-height: 1.8;
  color: #ffe066;
  margin: 0;
  padding: 20px;
  border-radius: 10px;
  overflow: hidden;
  white-space: pre;
  word-break: normal;
  text-shadow: 0 0 10px rgba(255, 224, 102, 0.8),
    0 0 20px rgba(255, 224, 102, 0.6), 0 0 30px rgba(255, 224, 102, 0.4),
    0 2px 4px rgba(0, 0, 0, 0.5);
}

@media (max-width: 480px) {
  .day17 {
    padding: 0;
  }

  .code-container {
    max-width: 100%;
    padding: 0;
  }

  .code-block {
    font-size: 7px;
    line-height: 1.8;
    padding: 0;
  }
}
</style>
