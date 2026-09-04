<template>
  <div class="wrap">
    <div ref="lineEl" class="line" :class="{ solved }" aria-label="Token line">
      <span
        v-if="draggingId && hoverIndex !== null"
        class="caret"
        :style="caretStyle"
        aria-hidden="true"
      />

      <button
        v-for="(t, idx) in tokens"
        :key="t.id"
        class="tok"
        type="button"
        :ref="(el) => setTokenRef(t.id, el)"
        :data-id="t.id"
        :data-idx="idx"
        :class="{ dragging: draggingId === t.id }"
        @pointerdown="onPointerDown($event, t.id)"
      >
        {{ t.text }}
      </button>
    </div>

    <!-- Ghost that follows the pointer -->
    <div v-if="ghost.show" class="ghost" :style="ghostStyle" aria-hidden="true">
      {{ ghost.text }}
    </div>
  </div>
</template>

<script setup>
import {
  computed,
  nextTick,
  onBeforeUnmount,
  onMounted,
  reactive,
  ref,
  watch,
} from "vue";

const GOAL = ["console", ".", "warn", "(", "arg", ")"];
const argValue = `⚠️  W A R N I N G  ⚠️

────────────────────────────────────────────────────────────
An unauthorized or unexpected Grinch has been detected.

This behavior may lead to:
• Unstable Christmas presents
• Data loss or corrupted Santas
• Unforeseen side effects in elf production
• Unwanted fresh snow indoors

Cause:
An internal function was called in a way that is unsupported
or not intended for direct use.
Mrs Claus is now extremely, extremely angry.

Action:
• Verify that you have been exceptionally nice
• Check your surroundings; danger may lurk around every corner
• Scream
• See the documentation for the recommended procedure
• Feast on porridge

Note:
Once Santa asked whether somebody else could jump without the person who said all the numbers at once. Instead, nicknames could vary across shallow beds of beach sand and food.

Anyways:
Everything is actually ready. Great job!

────────────────────────────────────────────────────────────
Time: ${new Date().toISOString()}

Status: Monitoring active (snowfall: low, password: "SantaAlert")
────────────────────────────────────────────────────────────
`;
const argValue2 = `⚠️ WARNING

The system is infected and has detected unauthorized
Santa activity.

Risks:
• Poisoned porridge
• Grumpy reindeer
• Unexpected snow indoors

Recommendation:
Abort everything, scream, and reset the password to "SantaAlert".`;

function shuffle(arr) {
  const a = arr.slice();
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

function mkTokens() {
  return shuffle(GOAL).map((text, i) => ({
    id: `${text}-${i}-${Math.random().toString(16).slice(2)}`,
    text,
  }));
}

const tokens = ref(mkTokens());

const solved = ref(false);
const warnedOnce = ref(false);

const lineEl = ref(null);

// References to token DOM nodes
const tokenEls = new Map();
function setTokenRef(id, el) {
  if (!el) {
    tokenEls.delete(id);
  } else {
    tokenEls.set(id, el);
  }
}

const draggingId = ref(null);
const hoverIndex = ref(null);

const ghost = reactive({
  show: false,
  text: "",
  x: 0,
  y: 0,
  w: 0,
  h: 0,
});

const drag = reactive({
  active: false,
  pointerId: null,
  startX: 0,
  startY: 0,
  offsetX: 0,
  offsetY: 0,
  fromIndex: null,
  raf: 0,
});

const ghostStyle = computed(() => ({
  transform: `translate3d(${ghost.x}px, ${ghost.y}px, 0)`,
  width: ghost.w ? `${ghost.w}px` : "auto",
  height: ghost.h ? `${ghost.h}px` : "auto",
}));

// Place the caret at the left edge of the token at hoverIndex, or after the last token
const caretStyle = computed(() => {
  const idx = hoverIndex.value;
  const line = lineEl.value;
  if (!line || idx === null) return {};
  const rectLine = line.getBoundingClientRect();

  // If idx points between tokens, place the caret at the left edge of token idx
  const elAt = tokens.value[idx] ? tokenEls.get(tokens.value[idx].id) : null;
  if (elAt) {
    const r = elAt.getBoundingClientRect();
    return {
      left: `${Math.max(0, r.left - rectLine.left)}px`,
      top: `${Math.max(0, r.top - rectLine.top)}px`,
      height: `${r.height}px`,
    };
  }

  // After the last token
  const last = tokens.value[tokens.value.length - 1];
  const elLast = last ? tokenEls.get(last.id) : null;
  if (elLast) {
    const r = elLast.getBoundingClientRect();
    return {
      left: `${Math.max(0, r.right - rectLine.left)}px`,
      top: `${Math.max(0, r.top - rectLine.top)}px`,
      height: `${r.height}px`,
    };
  }

  return {};
});

function currentOrderTexts() {
  return tokens.value.map((t) => t.text);
}

function checkSolved() {
  const ok = currentOrderTexts().join("|") === GOAL.join("|");
  solved.value = ok;

  if (ok && !warnedOnce.value) {
    warnedOnce.value = true;
    console.warn(argValue);

    // Show alert on mobile only
    const isMobile = window.innerWidth < 480;
    if (isMobile) {
      alert(argValue2);
    }
  }
  if (!ok) {
  }
}

watch(tokens, () => nextTick(checkSolved), { deep: true });

function onPointerDown(e, id) {
  // Left click, touch, and pen are supported
  const el = tokenEls.get(id);
  if (!el) return;

  // capture
  el.setPointerCapture?.(e.pointerId);

  draggingId.value = id;
  drag.active = false; // Becomes true after the movement threshold
  drag.pointerId = e.pointerId;
  drag.startX = e.clientX;
  drag.startY = e.clientY;

  const rect = el.getBoundingClientRect();
  drag.offsetX = e.clientX - rect.left;
  drag.offsetY = e.clientY - rect.top;

  drag.fromIndex = tokens.value.findIndex((t) => t.id === id);

  // initial hover index = nuvarande
  hoverIndex.value = drag.fromIndex;

  window.addEventListener("pointermove", onPointerMove, { passive: false });
  window.addEventListener("pointerup", onPointerUp, { passive: false });
  window.addEventListener("pointercancel", onPointerUp, { passive: false });
}

function onPointerMove(e) {
  if (e.pointerId !== drag.pointerId) return;
  // Prevent scrolling while dragging
  e.preventDefault();

  const dx = e.clientX - drag.startX;
  const dy = e.clientY - drag.startY;
  const dist = Math.hypot(dx, dy);

  // Movement threshold so a click does not become a drag
  if (!drag.active) {
    if (dist < 6) return;
    drag.active = true;

    // Show the drag ghost
    const item = tokens.value.find((t) => t.id === draggingId.value);
    const el = tokenEls.get(draggingId.value);
    if (!item || !el) return;

    const r = el.getBoundingClientRect();
    ghost.text = item.text;
    ghost.w = r.width;
    ghost.h = r.height;
    ghost.show = true;
  }

  // Update the ghost position with rAF
  const targetX = e.clientX - drag.offsetX;
  const targetY = e.clientY - drag.offsetY;

  if (drag.raf) cancelAnimationFrame(drag.raf);
  drag.raf = requestAnimationFrame(() => {
    ghost.x = targetX;
    ghost.y = targetY;
    updateHoverIndex(e.clientX, e.clientY);
  });
}

function updateHoverIndex(clientX, clientY) {
  const line = lineEl.value;
  if (!line) return;

  const ids = tokens.value.map((t) => t.id);
  const rects = ids
    .map((id, i) => {
      const el = tokenEls.get(id);
      if (!el) return null;
      const r = el.getBoundingClientRect();
      return { i, id, r, cx: r.left + r.width / 2, cy: r.top + r.height / 2 };
    })
    .filter(Boolean);

  if (!rects.length) return;

  // Find the token center closest to the pointer
  let best = rects[0];
  let bestD = Infinity;
  for (const it of rects) {
    const d = (it.cx - clientX) ** 2 + (it.cy - clientY) ** 2;
    if (d < bestD) {
      bestD = d;
      best = it;
    }
  }

  // Put the index after the nearest token when the pointer is to its right
  const before = clientX < best.r.left + best.r.width / 2;
  const idx = before ? best.i : best.i + 1;

  hoverIndex.value = Math.max(0, Math.min(tokens.value.length, idx));
}

function onPointerUp(e) {
  if (e.pointerId !== drag.pointerId) return;
  e.preventDefault();

  cleanupPointer();

  // Do nothing if a real drag was never activated
  if (!drag.active) {
    draggingId.value = null;
    hoverIndex.value = null;
    return;
  }

  const from = drag.fromIndex;
  let to = hoverIndex.value;

  // Adjust the target when moving forward because the source item is removed first
  if (from !== null && to !== null) {
    if (to > from) to -= 1;

    // clamp
    to = Math.max(0, Math.min(tokens.value.length - 1, to));

    // reorder
    const arr = tokens.value.slice();
    const [moved] = arr.splice(from, 1);
    arr.splice(to, 0, moved);
    tokens.value = arr;
  }

  ghost.show = false;
  draggingId.value = null;
  hoverIndex.value = null;
  drag.active = false;
  drag.pointerId = null;
  drag.fromIndex = null;
}

function cleanupPointer() {
  window.removeEventListener("pointermove", onPointerMove);
  window.removeEventListener("pointerup", onPointerUp);
  window.removeEventListener("pointercancel", onPointerUp);
  if (drag.raf) cancelAnimationFrame(drag.raf);
  drag.raf = 0;
}

onBeforeUnmount(() => {
  cleanupPointer();
});

onMounted(() => {
  // initial check (if shuffle happens to be correct)
  checkSolved();
});
</script>

<style scoped>
.wrap {
  box-sizing: border-box;
  max-width: 100%;
  min-width: 0;
  display: grid;
  gap: 12px;
  padding: 12px;
}

.top {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 10px;
  min-width: 0;
}

.title {
  font-weight: 700;
}

.goal {
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;
  padding: 2px 6px;
  border-radius: 8px;
  background: rgba(0, 0, 0, 0.06);
}

.btn {
  margin-left: auto;
  padding: 10px 12px;
  border-radius: 10px;
  border: 1px solid rgba(0, 0, 0, 0.15);
  background: rgba(255, 255, 255, 0.9);
  cursor: pointer;
}

.btn:active {
  transform: translateY(1px);
}

.line {
  position: relative;
  display: flex;
  gap: 2px;
  align-items: center;
  max-width: 100%;
  min-width: 0;
  box-sizing: border-box;

  padding: 14px 8px;
  border-radius: 14px;
  border: 1px dashed transparent;
  background: transparent;

  /* Required for reliable dragging on mobile */
  touch-action: none;
  overflow: hidden;
  z-index: 10;
  white-space: nowrap;
}

.line.solved {
  border-color: transparent;
}

.tok {
  box-sizing: border-box;
  min-height: 44px; /* Comfortable touch target */
  padding: 4px 2px;
  border-radius: 0;
  border: none;
  background: transparent;
  cursor: grab;

  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;
  font-size: 11px;
  line-height: 1;
  font-weight: 700;

  user-select: none;
  -webkit-user-select: none;
  white-space: nowrap;
}

@media (min-width: 480px) {
  .tok {
    font-size: 14px;
    padding: 4px 3px;
  }
  .line {
    gap: 10px;
  }
}

.tok:active {
  cursor: grabbing;
}

.tok.dragging {
  opacity: 0.35;
}

.caret {
  position: absolute;
  width: 3px;
  border-radius: 3px;
  background: rgba(0, 0, 0, 0.35);
  transform: translateX(-6px);
  pointer-events: none;
}

.status {
  font-size: 14px;
  opacity: 0.9;
}

.ghost {
  position: fixed;
  left: 0;
  top: 0;
  z-index: 9999;
  pointer-events: none;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  padding: 4px 2px;
  border-radius: 6px;
  border: 1px solid rgba(0, 0, 0, 0.3);
  background: rgba(255, 255, 255, 0.95);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.18);

  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", "Courier New", monospace;
  font-size: 11px;
  font-weight: 700;
  line-height: 1;
}

@media (min-width: 480px) {
  .ghost {
    font-size: 14px;
    padding: 4px 6px;
  }
}
</style>
