<!-- Fireworks.vue -->
<script setup lang="ts">
import { onMounted, onBeforeUnmount, ref } from "vue";

type Particle = {
  x: number;
  y: number;
  vx: number;
  vy: number;
  life: number;
  maxLife: number;
  size: number;
  hue: number;
  gravity: number;
  drag: number;
};

type Rocket = {
  x: number;
  y: number;
  vx: number;
  vy: number;
  targetY: number;
  hue: number;
  alive: boolean;
};

const canvasRef = ref<HTMLCanvasElement | null>(null);

let ctx: CanvasRenderingContext2D | null = null;
let rafId = 0;
let lastTime = 0;

let width = 0;
let height = 0;
let dpr = 1;

const rockets: Rocket[] = [];
const particles: Particle[] = [];

function rand(min: number, max: number) {
  return Math.random() * (max - min) + min;
}
function clamp(n: number, min: number, max: number) {
  return Math.max(min, Math.min(max, n));
}

function resize() {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const parent = canvas.parentElement;
  const rect = parent
    ? parent.getBoundingClientRect()
    : canvas.getBoundingClientRect();

  dpr = window.devicePixelRatio || 1;
  width = Math.max(1, Math.floor(rect.width));
  height = Math.max(1, Math.floor(rect.height));

  canvas.width = Math.floor(width * dpr);
  canvas.height = Math.floor(height * dpr);
  canvas.style.width = `${width}px`;
  canvas.style.height = `${height}px`;

  ctx = canvas.getContext("2d");
  if (!ctx) return;
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
}

function spawnRocket(x?: number) {
  const hue = rand(0, 360);
  rockets.push({
    x: x ?? rand(width * 0.1, width * 0.9),
    y: height + 10,
    vx: rand(-0.6, 0.6),
    vy: rand(-14.5, -12.5), // ⬆️ lite mer skjuts
    targetY: rand(height * 0.04, height * 0.22), // ⬆️ lite högre mål
    hue,
    alive: true,
  });
}

function explode(r: Rocket) {
  const count = Math.floor(rand(50, 90));
  for (let i = 0; i < count; i++) {
    const angle = rand(0, Math.PI * 2);
    const speed = rand(1.8, 5.2);
    particles.push({
      x: r.x,
      y: r.y,
      vx: Math.cos(angle) * speed,
      vy: Math.sin(angle) * speed,
      life: 0,
      maxLife: rand(55, 95),
      size: rand(1.0, 2.4),
      hue: (r.hue + rand(-25, 25) + 360) % 360,
      gravity: rand(0.05, 0.11),
      drag: rand(0.985, 0.994),
    });
  }
}

function tick(time: number) {
  if (!ctx) return;
  const dt = clamp((time - lastTime) / 16.6667, 0.5, 2.0);
  lastTime = time;

  // “fade trail” istället för att clear:a helt
  ctx.globalCompositeOperation = "source-over";
  ctx.fillStyle = "rgba(0,0,0,0.18)";
  ctx.fillRect(0, 0, width, height);

  // ibland skjut en ny raket
  if (Math.random() < 0.06) spawnRocket();

  // uppdatera & rita raketer
  for (const r of rockets) {
    if (!r.alive) continue;

    r.x += r.vx * dt;
    r.y += r.vy * dt;
    r.vy += 0.18 * dt; // gravitation på raketen lite grann

    // rita raket som liten glödpunkt
    ctx.beginPath();
    ctx.fillStyle = `hsla(${r.hue}, 100%, 65%, 0.9)`;
    ctx.arc(r.x, r.y, 2.0, 0, Math.PI * 2);
    ctx.fill();

    // explosion när den når target eller börjar falla
    if (r.y <= r.targetY || r.vy > -1.2) {
      r.alive = false;
      explode(r);
    }
  }

  // städa döda raketer
  for (let i = rockets.length - 1; i >= 0; i--) {
    if (rockets[i]?.alive === false) rockets.splice(i, 1);
  }

  // uppdatera & rita partiklar
  ctx.globalCompositeOperation = "lighter"; // gör att ljus “adderas” = nice glow
  for (let i = particles.length - 1; i >= 0; i--) {
    const p = particles[i];
    if (!p) continue;
    p.life += 1 * dt;

    p.vx *= Math.pow(p.drag, dt);
    p.vy *= Math.pow(p.drag, dt);
    p.vy += p.gravity * dt;

    p.x += p.vx * dt;
    p.y += p.vy * dt;

    const t = 1 - p.life / p.maxLife;
    const alpha = clamp(t, 0, 1);

    ctx.beginPath();
    ctx.fillStyle = `hsla(${p.hue}, 100%, 60%, ${alpha})`;
    ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
    ctx.fill();

    if (p.life >= p.maxLife) particles.splice(i, 1);
  }

  // håll lagom mängd partiklar
  if (particles.length > 2500) particles.splice(0, particles.length - 2500);

  rafId = requestAnimationFrame(tick);
}

function onPointerDown(e: PointerEvent) {
  // klick/tap = skjut raket där man tryckte
  const canvas = canvasRef.value;
  if (!canvas) return;
  const rect = canvas.getBoundingClientRect();
  const x = e.clientX - rect.left;
  spawnRocket(x);
}

onMounted(() => {
  resize();
  window.addEventListener("resize", resize, { passive: true });

  const canvas = canvasRef.value;
  canvas?.addEventListener("pointerdown", onPointerDown, { passive: true });

  // starta med några
  for (let i = 0; i < 3; i++) spawnRocket();

  lastTime = performance.now();
  rafId = requestAnimationFrame(tick);
});

onBeforeUnmount(() => {
  cancelAnimationFrame(rafId);
  window.removeEventListener("resize", resize);

  const canvas = canvasRef.value;
  canvas?.removeEventListener("pointerdown", onPointerDown);

  rockets.length = 0;
  particles.length = 0;
});
</script>

<template>
  <canvas ref="canvasRef" class="fireworks" />
</template>

<style scoped>
.fireworks {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: auto; /* så man kan klicka/tappa för extra fyrverkeri */
}
</style>
