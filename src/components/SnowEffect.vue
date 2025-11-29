<script setup lang="ts">
import { onMounted, onUnmounted } from "vue";
import { Application, Graphics } from "pixi.js";

interface Particle {
  graphic: Graphics;
  x: number;
  y: number;
  radius: number;
  speed: number;
  drift: number;
}

let app: Application | null = null;
const particles: Particle[] = [];

onMounted(async () => {
  const canvas = document.getElementById("snow-canvas") as HTMLCanvasElement;
  if (!canvas) return;

  app = new Application();
  await app.init({
    canvas,
    width: window.innerWidth,
    height: window.innerHeight,
    backgroundAlpha: 0,
    resizeTo: window,
    antialias: false,
  });

  // Create reusable snowflake graphics (only 50 particles)
  for (let i = 0; i < 50; i++) {
    const graphic = new Graphics();
    const radius = Math.random() * 3 + 1;

    graphic.circle(0, 0, radius);
    graphic.fill({ color: 0xffffff, alpha: 0.7 });

    app.stage.addChild(graphic);

    particles.push({
      graphic,
      x: Math.random() * window.innerWidth,
      y: Math.random() * window.innerHeight,
      radius,
      speed: Math.random() * 1 + 0.5,
      drift: Math.random() * 0.5 - 0.25,
    });
  }

  // Optimized animation loop
  app.ticker.add(() => {
    particles.forEach((particle) => {
      particle.y += particle.speed;
      particle.x += particle.drift;

      if (particle.y > window.innerHeight) {
        particle.y = -10;
        particle.x = Math.random() * window.innerWidth;
      }

      if (particle.x > window.innerWidth) {
        particle.x = 0;
      } else if (particle.x < 0) {
        particle.x = window.innerWidth;
      }

      particle.graphic.x = particle.x;
      particle.graphic.y = particle.y;
    });
  });
});

onUnmounted(() => {
  app?.destroy(true, { children: true, texture: true });
  particles.length = 0;
});
</script>

<template>
  <canvas id="snow-canvas" class="snow-canvas"></canvas>
</template>

<style scoped>
.snow-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
}
</style>
