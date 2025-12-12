<script setup lang="ts">
import video from "../../assets/mountain2.mp4";

import { ref } from "vue";

const videoRef = ref<HTMLVideoElement | null>(null);

function onPlaying() {
  if (videoRef.value) videoRef.value.playbackRate = 3.0;
}
</script>

<template>
  <div class="container13">
    <div class="video-box">
      <video
        ref="videoRef"
        :src="video"
        autoplay
        muted
        loop
        playsinline
        @playing="onPlaying"
      ></video>
      <div class="time-bar"></div>
    </div>
  </div>
</template>

<style scoped>
div.container13 {
  min-height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #000;
  overflow: hidden;
}

.video-box {
  width: min(400px, 100%);
  aspect-ratio: 4 / 3;
  border-radius: 12px;
  overflow: hidden;
  background: transparent;
  box-shadow: 0 0 20px 6px rgba(255, 255, 255, 0.4),
    0 0 60px 20px rgba(255, 255, 255, 0.35),
    0 0 120px 40px rgba(255, 255, 255, 0.2);
}

.video-box video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
  transform: translateY(-1mm);
  border: 10px solid rgba(255, 255, 255, 0.2);
}
.time-bar {
  margin-top: 1rem;
  position: absolute;
  left: 0;
  height: 6px;
  width: 100%;
  background: linear-gradient(90deg, #00ff88, #ff0044);
  animation: timer 77s linear infinite;
  border-radius: 50px;
}

@keyframes timer {
  from {
    transform: scaleX(1);
  }
  to {
    transform: scaleX(0);
  }
}
</style>
