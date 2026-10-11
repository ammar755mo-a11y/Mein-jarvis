
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#050b18">
<title>JARVIS – Persönlicher Assistent</title>

<style>
* { box-sizing: border-box; }

body {
  margin: 0;
  min-height: 100vh;
  background: radial-gradient(ellipse at top, #102b50, #050914 65%);
  color: #eaf7ff;
  font-family: system-ui, sans-serif;
}

main {
  width: min(680px, 100%);
  margin: auto;
  padding: 24px 16px;
}

header { text-align: center; }

h1 {
  letter-spacing: 8px;
  color: #67dcff;
  text-shadow: 0 0 22px #168dff;
  margin-bottom: 4px;
}

.sub { color: #91aeca; font-size: 13px; }

.core {
  width: 126px;
  height: 126px;
  margin: 28px auto;
  border: 2px solid #54cfff;
  border-radius: 50%;
  display: grid;
  place-items: center;
  box-shadow: 0 0 22px #168dff, inset 0 0 22px #168dff;
  animation: pulse 3s infinite ease-in-out;
}

.core span { font-size: 43px; color: #70e8ff; }

@keyframes pulse {
  50% { box-shadow: 0 0 38px #168dff, inset 0 0 30px #168dff; }
}

.status {
  text-align: center;
  color: #75e6ff;
  font-size: 13px;
  margin-bottom: 18px;
}

.panel {
  border: 1px solid #234568;
  border-radius: 16px;
  background: #091526dd;
  padding: 16px;
  margin: 14px 0;
}

#chat {
  height: 240px;
  overflow-y: auto;
}

.msg {
  padding: 10px 12px;
  border-radius: 12px;
  margin: 8px 0;
  white-space: pre-wrap;
  overflow-wrap: anywhere;
}

.jarvis { background: #102b46; }
.user { background: #173b56; text-align: right; }

.controls { display: flex; gap: 8px; margin-top: 10px; }

input {
  min-width: 0;
  flex: 1;
  background: #050d19;
  color: white;
  border: 1px solid #315476;
  border-radius: 10px;
  padding: 12px;
  font-size: 16px;
}

button {
  border: 1px solid #48bde8;
  border-radius: 10px;
  padding: 12px 14px;
  background: #10314b;
  color: #eafaff;
  font-size: 15px;
  cursor: pointer;
}

button:active { background: #20587a; }

#mic { width: 100%; margin-top: 10px; }

.small { color: #a7bbcf; font-size: 12px; line-height: 1.5; }
</style>
</head>

<body>
<main>
  <header>
    <h1>JARVIS</h1>
    <div class="sub">PERSÖNLICHER KI-ASSISTENT</div>
  </header>

  <div class="core"><span>◈</span></div>
  <div class="status" id="status">SYSTEM BEREIT</div>

  <section class="panel">
    <strong>Kommunikation</strong>
    <div id="chat" aria-live="polite">
      <div class="msg jarvis">
        JARVIS: Guten Tag. Systeme gestartet.
        Wie kann ich dir helfen?
      </div>
    </div>

    <form id="form" class="controls">
      <input id="input" placeholder="Schreib mir etwas …"
        autocomplete="off" aria-label="Nachricht">
      <button type="submit">Senden</button>
    </form>
    <button id="mic">🎙 Mit JARVIS sprechen</button>
  </section>

  <section class="panel">
    <strong>Testbefehle</strong>
    <p class="small">
      „Wie spät ist es?“ · „Welches Datum ist heute?“ ·
      „Öffne YouTube“ · „Wer bist du?“ · „Sprich nicht“
    </p>
  </section>

  <p class="small">
    Prototyp 1.0 · Sprachfunktionen benötigen einen kompatiblen
    Browser und gegebenenfalls Mikrofonfreigabe.
    Noch keine Verbindung zu einer echten KI.
  </p>
</main>

<script>
const chat = document.getElementById("chat");
const input = document.getElementById("input");
const status = document.getElementById("status");
let speechEnabled = true;

function message(text, who) {
  const div = document.createElement("div");
  div.className = "msg " + who;
  div.textContent = text;
  chat.appendChild(div);
  chat.scrollTop = chat.scrollHeight;
}

function speak(text) {
  if (!speechEnabled || !("speechSynthesis" in window)) return;

  window.speechSynthesis.cancel();
  const utterance = new SpeechSynthesisUtterance(text);
  utterance.lang = "de-DE";
  utterance.rate = 1;
  window.speechSynthesis.speak(utterance);
}

function reply(text) {
  message("JARVIS: " + text, "jarvis");
  speak(text);
}

function handleCommand(raw) {
  const text = raw.trim();
  if (!text) return;

  message("Du: " + text, "user");
  input.value = "";
  const q = text.toLowerCase();

  if (q.includes("wie spät") || q.includes("uhrzeit")) {
    reply("Es ist " + new Date().toLocaleTimeString("de-DE",
      { hour: "2-digit", minute: "2-digit" }) + " Uhr.");
  } else if (q.includes("datum") || q.includes("welcher tag")) {
    reply("Heute ist " + new Date().toLocaleDateString("de-DE",
      { weekday: "long", day: "numeric", month: "long", year: "numeric" }) + ".");
  } else if (q.includes("wer bist du")) {
    reply("Ich bin JARVIS, dein selbst gebauter digitaler Assistent. Meine Fähigkeiten werden Schritt für Schritt erweitert.");
  } else if (q.includes("öffne youtube")) {
    reply("Ich öffne YouTube.");
    window.open("https://www.youtube.com/", "_blank", "noopener");
  } else if (q.includes("sprich nicht")) {
    speechEnabled = false;
    reply("Sprachausgabe ausgeschaltet.");
  } else if (q.includes("sprich wieder")) {
    speechEnabled = true;
    reply("Sprachausgabe eingeschaltet.");
  } else if (q.includes("hilfe")) {
    reply("Du kannst nach Uhrzeit und Datum fragen oder YouTube öffnen. Weitere Funktionen bauen wir als Nächstes ein.");
  } else {
    reply("Diesen Befehl kenne ich noch nicht. In der nächsten Ausbaustufe verbinden wir mich mit einer echten KI.");
  }
}

document.getElementById("form").addEventListener("submit", e => {
  e.preventDefault();
  handleCommand(input.value);
});

const SpeechRecognition =
  window.SpeechRecognition || window.webkitSpeechRecognition;

document.getElementById("mic").addEventListener("click", () => {
  if (!SpeechRecognition) {
    status.textContent = "SPRACHE NICHT UNTERSTÜTZT";
    reply("Die Spracherkennung ist in diesem Browser nicht verfügbar. Bitte nutze die Texteingabe oder probiere einen anderen Browser.");
    return;
  }

  const recognition = new SpeechRecognition();
  recognition.lang = "de-DE";
  recognition.interimResults = false;
  recognition.maxAlternatives = 1;

  recognition.onstart = () => status.textContent = "ICH HÖRE ZU …";
  recognition.onresult = event => {
    input.value = event.results[0][0].transcript;
    handleCommand(input.value);
  };
  recognition.onerror = () => status.textContent = "MIKROFON ODER SPRACHE FEHLGESCHLAGEN";
  recognition.onend = () => {
    if (status.textContent === "ICH HÖRE ZU …") {
      status.textContent = "SYSTEM BEREIT";
    }
  };

  try {
    recognition.start();
  } catch {
    status.textContent = "BITTE ERNEUT VERSUCHEN";
  }
});
</script>
</body>
</html>
# Mein-jarvis
Alles
