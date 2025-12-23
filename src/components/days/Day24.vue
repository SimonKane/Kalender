<script setup lang="ts">
function clickedStar() {
  console.clear();
  console.log(
    "%c⭐ Nästan, men testa igen (igen).. ⭐\n\n",
    "color: gold; font-size: 24px; text-shadow: 0 0 10px gold;"
  );

  window.history.pushState({ message: "hej-hej" }, "", "/dethärärkoden");
}

function doubleClick() {
  console.group("Detaljer");
  console.info("hmmm");
  console.log("Tid:", new Date().toLocaleTimeString());
  console.error("neee");
  console.log("URL:", "???");
  console.groupEnd();
}

function touchStart() {
  alert("🎄✨ Inte riktigt här ännu… ✨🎄 Men varsågod – en gran ändå ⭐");
  alert(
    `
             ⭐
             /\\
            /**\\
           /*u*\\
          /*****\\
         /******r\\
        /*********\\
       /***l?******\\
      /*************\\
             ||
             ||
  `
  );
  window.history.pushState({ message: "hej-hej" }, "", "/dethärärkoden");
}
</script>

<template>
  <div class="container24">
    <div
      @touchstart="touchStart"
      @dblclick="doubleClick"
      @click="clickedStar"
      class="star24"
    ></div>
  </div>
</template>

<style scoped>
.container24 {
  background: radial-gradient(1px 1px at 20% 30%, white 100%, transparent),
    radial-gradient(1px 1px at 80% 40%, white 100%, transparent),
    radial-gradient(1px 1px at 50% 70%, white 100%, transparent),
    radial-gradient(1px 1px at 10% 80%, white 100%, transparent),
    radial-gradient(1px 1px at 90% 20%, white 100%, transparent),
    radial-gradient(1px 1px at 60% 10%, white 100%, transparent),
    linear-gradient(180deg, #0d1b3c 0%, #1a237e 60%, #21213b 100%);
  height: 100%;
  width: 100%;
  display: grid;
  place-items: center;
  overflow: hidden;
}

.star24 {
  width: 20vmin;
  height: 20vmin;
  background: gold;
  cursor: pointer;
  position: relative;
  isolation: isolate;
  clip-path: polygon(
    50% 0%,
    61% 35%,
    98% 35%,
    68% 57%,
    79% 91%,
    50% 70%,
    21% 91%,
    32% 57%,
    2% 35%,
    39% 35%
  );
  filter: drop-shadow(0 0 30px gold);
  animation: glow 2s infinite, sway 4s infinite;
}
.star24:active {
  scale: 0.9;
}
@keyframes sway {
  0% {
    transform: rotate(-3deg);
  }
  50% {
    transform: rotate(3deg);
  }
  100% {
    transform: rotate(-3deg);
  }
}
@keyframes glow {
  0%,
  100% {
    filter: drop-shadow(0 0 20px gold);
  }
  50% {
    filter: drop-shadow(0 0 40px gold);
  }
}
</style>
