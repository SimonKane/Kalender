<script setup lang="js">
  import kodimage from "../../assets/kodsnippet.png";

window.a = "tihi fel"
window.b="Korrekt! lösen = b"
window.c="very wrong"
window.d="dunderfel";
</script>

<template>
  <div class="container10">
    <img class="kodbild" :src="kodimage" alt="kodis" />
    <div class="answer-container">
      <h4>A) NaN</h4>
      <h4>B) "8102"</h4>
      <h4>C) "102"</h4>
      <h4>D) 34</h4>
      <h3
        style="
          color: #ffee00;
          text-shadow: 0 0 20px rgb(251, 255, 0), 0 0 20px rgb(255, 255, 255),
            2px 2px 4px rgba(0, 0, 0, 0.5);
        "
      >
        console.log(answer)
      </h3>
    </div>
  </div>
</template>

<style scoped>
.container10 {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background-color: #2d2a2e;
  overflow: hidden;
}
.answer-container {
  display: flex;
  flex-direction: column;
  margin-top: -10px;
  width: 100%;
}
.answer-container > h4 {
  color: white;
  text-align: left;
  font-size: clamp(0.65rem, 2.5vw, 1rem);
  margin-left: clamp(10px, 3vw, 20px);
  z-index: 2;
}
.answer-container > h3 {
  color: #ffee00;
  text-shadow: 0 0 20px rgb(251, 255, 0), 0 0 20px rgb(255, 255, 255),
    2px 2px 4px rgba(0, 0, 0, 0.5);
  font-size: clamp(0.8rem, 3vw, 1.17rem);
  margin-bottom: 3px;
}
.kodbild {
  position: relative;
  top: 1px;
  width: 100%;
  max-width: 600px;
}
</style>
