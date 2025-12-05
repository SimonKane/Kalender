<script setup lang="ts">
function onCopy(e: ClipboardEvent) {
  e.preventDefault();
  alert("Nej du, no copy copy tillåten 🎄");
}
</script>

<template>
  <div class="functionCall">
    <h3>
      gissaOutput("<span class="text"
        >Gammal Ost Doftar Julmust Utan Lukt")</span
      >
    </h3>
  </div>
  <div class="code-wrapper">
    <pre class="snippet" @copy="onCopy">
function gissaOutput(text: string) {
  const words = text.split(" ");
  let result = "";

  for (let word of words) {
    result += word.charAt(1 - 1);
    if (word[word.length - 1] === "r") break;
  }

  return (
    result.replace(result, "JUL") +
    text.split(" ")[3]?.charAt(0) +
    text.split(" ")[4]?.charAt(0) +
    text.split(" ")[5]?.charAt(0)
  );
}
    </pre>
  </div>
</template>

<style scoped>
h3 {
  font-family: "Courier New", Courier, monospace;
}
.functionCall {
  text-align: center;
  margin-bottom: 10px;
  position: absolute;
  top: -3rem;
  color: white;
  font-size: clamp(10px, 1.5vw, 15px);

  text-wrap: nowrap;
}
.text {
  font-family: "Mountains of Christmas", cursive;
}
.code-wrapper {
  width: min(65vw, 400px);
  height: min(45vh, 230px);
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.snippet {
  box-sizing: border-box;
  text-align: left;
  font-family: "Courier New", Courier, monospace;
  white-space: pre;
  border-radius: 8px;
  overflow-y: hidden;
  overflow-x: hidden;
  font-size: clamp(8px, 1.2vw, 11px);
  height: 100%;
  margin-top: 20px;
  line-height: 1.4;
  padding: 10px;
}

@media (max-width: 600px) {
  .snippet {
    font-size: 7px;
    line-height: 1.4;
    letter-spacing: -0.1px;
    text-indent: 5px;
  }
}
</style>
