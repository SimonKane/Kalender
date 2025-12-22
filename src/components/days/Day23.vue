<script setup lang="ts">
import { ref } from "vue";

const instructionLine = `Byt plats på första och sista bokstaven i strängen`;
const functionHeader = `function swapLetters(str) {`;
const functionFooter = `}`;
const userFunctionBody = ref<string>(`// skriv din kod här`);

const testResult = ref<string | null>(null);
const showCode = ref(false);

function normalize(s: string) {
  console.log(s);
  return String(s).replace(/\s+/g, "").toLowerCase();
}

function runUserFunction(str: string): string {
  try {
    const fn = new Function(`
      ${functionHeader}
      ${userFunctionBody.value}
      ${functionFooter}
      return swapLetters;
    `)();

    const out = fn(str);
    return typeof out === "string" ? out : "";
  } catch (e: any) {
    testResult.value = `Fel i koden: ${e?.message ?? "okänt fel"}`;
    return "";
  }
}
function testFunction() {
  testResult.value = null;
  showCode.value = false;

  const t1 = normalize(runUserFunction("lodjug")) === "godjul";
  const t2 = normalize(runUserFunction("eomtt")) === "tomte";
  const t3 = normalize(runUserFunction("aarisopph")) === "harisoppa";

  if (t1 && t2 && t3) {
    showCode.value = true;
    testResult.value = "bra";
  } else {
    if (!testResult.value) {
      testResult.value = "Nope";
      setTimeout(() => {
        testResult.value = null;
      }, 3000);
    }
  }
}
</script>

<template>
  <div class="starry-bg">
    <div v-if="!showCode" class="puzzle-container">
      <div class="code-block" aria-label="Kodruta">
        <pre class="code-line locked">{{ instructionLine }}</pre>
        <pre class="code-line locked">{{ functionHeader }}</pre>

        <textarea
          v-model="userFunctionBody"
          class="code-textarea"
          spellcheck="false"
          placeholder="Skriv kod här...."
        />
        <pre class="code-line locked">{{ functionFooter }}</pre>
      </div>

      <button class="test-btn" type="button" @click="testFunction">
        {{ testResult === "Nope" ? "Nope" : `swapLetters("lodjug")` }}
      </button>
    </div>
    <div v-if="showCode" class="code">
      <h3>"Bra"</h3>
      <h1>God jul på dig med!</h1>
    </div>
  </div>
</template>

<style scoped>
/* Inget får läcka utanför föräldern */
.starry-bg {
  position: relative;
  width: 100%;
  height: 100%;
  min-width: 0;
  min-height: 0;
  max-width: 100%;
  max-height: 100%;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, #0d1b3c 0%, #1a237e 60%, #21213b 100%);
}

/* Själva kortet (ingen vit extra ruta någon annanstans) */
.puzzle-container {
  width: 100%;
  max-width: 420px;
  box-sizing: border-box;

  padding: 1rem;
  margin: 0; /* viktigt: inga externa marginaler som kan skapa overflow */

  display: flex;
  flex-direction: column;
  gap: 0.75rem;

  border-radius: 14px;
  background: transparent; /* tar bort "vit ruta" */
  box-shadow: none; /* (om du vill ha skugga: sätt tillbaka) */
  overflow: scroll;
  min-width: 0;
  min-height: 0;
}

/* Kodblock */
/* Kodblock: lite mer "editor"-känsla men samma plats */
.code-block {
  border-radius: 12px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  border: 1px solid rgba(255, 255, 255, 0.18);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.18);
}

/* Bas för alla låsta rader */
.code-line {
  margin: 0;
  padding: 0.6rem; /* behåller din spacing */
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", monospace;
  font-size: 0.9rem;
  display: block;
  white-space: normal;
  border-top: 0;

  /* ny “editor” look */
  background: rgba(255, 255, 255, 0.96);
  color: #0b1220;
}

/* Instruktionsraden: ser ut som en kommentar */
.code-line:first-child {
  color: #2f6f4e;
  background: rgba(255, 255, 255, 0.92);
  border-bottom: 1px dashed rgba(0, 0, 0, 0.12);
  font-size: 0.88rem;
}

/* Funktionsraden: tydligare “header” */
.code-line:nth-child(2) {
  color: #1a237e;
  font-weight: 800;
  letter-spacing: 0.2px;
  background: rgba(255, 255, 255, 0.96);
  border-bottom: 1px solid rgba(0, 0, 0, 0.08);
}

/* Textarea: ser ut som kodkropp med liten “indent”-känsla */
.code-textarea {
  width: 100%;
  box-sizing: border-box;
  min-width: 0;
  height: 120px;
  max-height: 260px;
  resize: none;
  overflow: scroll;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", monospace;
  font-size: 0.85rem;
  line-height: 1.25;

  padding: 0.6rem; /* behåller din spacing */
  padding-left: 1.25rem; /* liten “kod-indent”, påverkar inte ytan utåt */

  background: rgba(255, 255, 255, 0.96);
  color: #111;
  outline: none;

  border: 0; /* tar bort kant mellan raderna */
  border-left: 3px solid rgba(26, 35, 126, 0.25); /* subtil markör */
}

/* Fokus: gul glow men subtil */
.code-textarea:focus {
  box-shadow: inset 0 0 0 1px rgba(255, 215, 0, 0.55);
}

/* Sista raden "}" – lite mörkare så den känns som avslut */
.code-block .code-line:last-child {
  color: #0b1220;
  font-weight: 700;
  background: rgba(255, 255, 255, 0.94);
  border-top: 1px solid rgba(0, 0, 0, 0.08);
}

/* Knappen: lite mer “glans” utan större storlek */
.test-btn {
  padding: 0.5rem 0.85rem;
  font-size: 0.95rem;
  font-weight: 800;
  background: linear-gradient(135deg, #e63946 0%, #b71c1c 100%);
  color: #fff;
  border: 0;
  border-radius: 10px;
  cursor: pointer;
  align-self: center;
  box-shadow: 0 10px 22px rgba(0, 0, 0, 0.22);
}

.test-btn:hover {
  filter: brightness(1.03);
}

.test-btn:hover {
  background: #b71c1c;
}

.code {
  color: #ffd700;
  font-size: 1em;
  text-shadow: 0 0 10px rgba(255, 215, 0, 0.8), 0 0 20px rgba(255, 215, 0, 0.6),
    0 0 30px rgba(255, 215, 0, 0.4);
}
/* iPhone / touch-enheter: skala ner hela blocket */
@media (max-width: 600px) {
  .starry-bg {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .puzzle-container {
    transform: scaleY(0.6);
    transform-origin: center;
    width: 100%;
    max-width: 520px;
    gap: 0.4rem;
    overflow: hidden;
    width: 150%;
  }

  .code-block {
    box-shadow: none;

    transform: scaleX(0.8);
  }

  .code-line {
    padding: 0.35rem;
    line-height: 1.1;
    font-size: 5px;
  }

  .code-textarea {
    height: 70px;
    max-height: 70px;
    padding: 0.35rem;
    font-size: 5px;
    line-height: 1.1;
  }
  .test-btn {
    padding: 0.35rem 0.6rem;
    font-size: 0.8rem;
    transform: scaleX(0.5);
  }
}
</style>
