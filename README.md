<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Tirth Spam Experience</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: radial-gradient(circle at top, #1f2937, #020617);
      color: #f9fafb;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 16px;
    }

    /* ---------- LOADING ---------- */

    #loadingScreen {
      position: fixed;
      inset: 0;
      background: #020617;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 14px;
      z-index: 9999;
    }

    #spinner {
      width: 46px;
      height: 46px;
      border-radius: 50%;
      border: 4px solid rgba(148, 163, 184, 0.4);
      border-top-color: #6366f1;
      animation: spin 0.9s linear infinite;
    }

    @keyframes spin {
      to { transform: rotate(360deg); }
    }

    #loadingScreen.fade-out {
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.4s ease-out;
    }

    /* ---------- MAIN CARD ---------- */

    .container {
      width: 100%;
      max-width: 520px;
      background: rgba(15, 23, 42, 0.96);
      border-radius: 18px;
      padding: 18px;
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.55);
      border: 1px solid rgba(148, 163, 184, 0.35);
      backdrop-filter: blur(14px);
      position: relative;
      overflow: hidden;
      display: none; /* show after loading */
    }

    .container::before {
      content: "";
      position: absolute;
      inset: -40%;
      background:
        radial-gradient(circle at 0% 0%, rgba(96, 165, 250, 0.35), transparent 55%),
        radial-gradient(circle at 100% 100%, rgba(244, 114, 182, 0.3), transparent 55%);
      opacity: 0.7;
      mix-blend-mode: soft-light;
      pointer-events: none;
    }

    .inner { position: relative; z-index: 1; }

    .page { display: none; animation: fade 0.3s ease-out; }
    .page.active { display: block; }

    @keyframes fade {
      from { opacity: 0; transform: translateY(6px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    h2 { font-size: 1.4rem; margin-bottom: 6px; }
    p  { font-size: 0.9rem; margin-bottom: 8px; line-height: 1.45rem; color: #e5e7eb; }
    .tiny { font-size: 0.78rem; color: #9ca3af; }

    button {
      width: 100%;
      padding: 11px 14px;
      border-radius: 999px;
      border: none;
      cursor: pointer;
      font-size: 0.9rem;
      font-weight: 500;
      margin-top: 10px;
      transition: transform 0.08s ease-out, box-shadow 0.12s ease-out;
    }

    .btn-primary {
      background: linear-gradient(135deg, #4f46e5, #6366f1);
      color: #f9fafb;
      box-shadow: 0 14px 28px rgba(79, 70, 229, 0.55);
    }
    .btn-primary:hover {
      transform: translateY(-1px);
      box-shadow: 0 18px 36px rgba(79, 70, 229, 0.7);
    }
    .btn-secondary {
      background: #0f172a;
      color: #e5e7eb;
      border: 1px solid #64748b;
    }

    label {
      font-size: 0.8rem;
      color: #9ca3af;
      display: block;
      margin-top: 8px;
      margin-bottom: 2px;
    }

    select, input {
      width: 100%;
      padding: 9px 10px;
      border-radius: 10px;
      border: 1px solid #64748b;
      background: #020617;
      color: #e5e7eb;
      font-size: 0.9rem;
      outline: none;
    }

    select:focus, input:focus {
      border-color: #818cf8;
      box-shadow: 0 0 0 1px rgba(129, 140, 248, 0.6);
    }

    .reason-box {
      margin-top: 4px;
      padding: 8px;
      border-radius: 10px;
      border: 1px solid #64748b;
      background: #020617;
      font-size: 0.85rem;
    }
    .reason-box label {
      display: flex;
      align-items: center;
      gap: 6px;
      margin-bottom: 6px;
      cursor: pointer;
    }

    .status { font-size: 0.8rem; min-height: 18px; margin-top: 4px; }
    .error   { color: #f87171; }
    .success { color: #4ade80; }

    #typing {
      white-space: pre-wrap;
      min-height: 120px;
      font-size: 0.9rem;
      padding-right: 4px;
      border-right: 2px solid #e5e7eb;
      animation: caretBlink 0.8s infinite;
    }
    @keyframes caretBlink {
      0%,100% { border-color: transparent; }
      50%     { border-color: #e5e7eb; }
    }

    .info-card {
      border-radius: 10px;
      border: 1px solid #475569;
      background: #020617;
      padding: 10px;
      margin-top: 10px;
      font-size: 0.86rem;
    }

    .fact-box {
      border-radius: 10px;
      border: 1px dashed #64748b;
      background: #020617;
      padding: 10px;
      margin-top: 10px;
      min-height: 70px;
      font-size: 0.86rem;
    }

    .meter {
      width: 100%;
      height: 14px;
      border-radius: 999px;
      border: 1px solid #475569;
      background: #020617;
      overflow: hidden;
      margin: 8px 0;
    }
    .meter-fill {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #22c55e, #f97316, #ef4444);
      transition: width 0.25s ease-out;
    }

    .checklist div {
      border-radius: 8px;
      border: 1px solid #475569;
      background: #020617;
      padding: 7px;
      margin-bottom: 5px;
      font-size: 0.86rem;
    }

    .badge {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      padding: 4px 9px;
      border-radius: 999px;
      border: 1px solid #64748b;
      background: #020617;
      color: #94a3b8;
      font-size: 0.72rem;
      margin-bottom: 8px;
    }
    .dot-online {
      width: 7px;
      height: 7px;
      border-radius: 999px;
      background: #22c55e;
      box-shadow: 0 0 0 4px rgba(34,197,94,0.18);
    }

    #promiseLockedText {
      font-size: 0.82rem;
      color: #4ade80;
      text-align: center;
      margin-top: 6px;
      display: none;
    }
  </style>
</head>

<body>

  <!-- LOADING SCREEN -->
  <div id="loadingScreen">
    <div id="spinner"></div>
    <p>Loading Tirth’s drama…</p>
  </div>

  <!-- MAIN APP -->
  <div class="container" id="appContainer">
    <div class="inner">

      <!-- PAGE 1: WELCOME -->
      <div id="page1" class="page active">
        <div class="badge">
          <span class="dot-online"></span> Tirth’s AI Assistant
        </div>
        <h2>👋 Hello, I’m Tirth’s AI Assistant</h2>
        <p>
          I will help you find the real reason why Tirth spammed your phone.
          No flex, no sorry, only roast and truth.
        </p>
        <p class="tiny">
          Next pages are only for fun. Take it in comedy way.
        </p>
        <button class="btn-primary" onclick="goTo(2)">Start →</button>
      </div>

      <!-- PAGE 2: NAME + REASON + CODE -->
      <div id="page2" class="page">
        <h2>🧠 Reason Finder</h2>
        <label>Select your name:</label>
        <select id="name">
          <option value="">-- Select --</option>
          <option>Price Goswami</option>
          <option>Pari Verma</option>
          <option>Ved Patel</option>
          <option>Raj Chaudhary</option>
          <option>Sneha Prajapati</option>
          <option>Nidhi Patel</option>
          <option>Dancy Patel</option>
          <option>Jiya Patel</option>
        </select>

        <label>Why he spammed you?</label>
        <div class="reason-box">
          <label>
            <input type="radio" name="reason" value="bored" />
            He was bored 🥱 (Actually he never gets bored, he just gets noisy.)
          </label>
          <label>
            <input type="radio" name="reason" value="teasing" />
            He was teasing you 😁 (Dark jokes, overacting, full time pass.)
          </label>
          <label>
            <input type="radio" name="reason" value="prank" />
            It was a prank 🎉 (He doesn’t like pranks, but he likes your reaction.)
          </label>
          <label>
            <input type="radio" name="reason" value="ignored" />
            You ignored his message 👀 (His ego got hurt, spam mode on.)
          </label>
          <label>
            <input type="radio" name="reason" value="testing" />
            He was testing new Python spam code on you 🤖 (Call, SMS, email, WhatsApp – full package.)
          </label>
        </div>

        <label>Enter your secret code:</label>
        <input id="code" placeholder="Code Tirth gave you" />

        <p id="status" class="status"></p>

        <button class="btn-primary" onclick="verify()">Unlock next page →</button>
        <button class="btn-secondary" onclick="goTo(1)">← Back</button>
      </div>

      <!-- PAGE 3: DETAILED REASON MESSAGE -->
      <div id="page3" class="page">
        <h2>💌 Private Message</h2>
        <div id="typing"></div>
        <button class="btn-primary" onclick="goTo(4)">Next →</button>
      </div>

      <!-- PAGE 4: BIRTHDAY + NICKNAME QUIZ -->
      <div id="page4" class="page">
        <h2>🕵️ Pro Level Check</h2>
        <p class="tiny">If you really know Tirth, you should know this.</p>

        <label>Enter Tirth’s birth date:</label>
        <input id="birthInput" placeholder="DD-MM-YYYY" />

        <label>Enter Tirth’s real nickname:</label>
        <input id="nickInput" placeholder="Example: Ravi Patel" />
        <p class="tiny">Hint: Same as the example. Yes, his nickname is “Ravi Patel”.</p>

        <p id="quizStatus" class="status"></p>

        <button class="btn-primary" onclick="checkQuiz()">Check details</button>

        <div id="quizResult" class="info-card" style="display:none;">
          <p>
            Tirth has his own life and he is a very busy person (according to himself).
            Do you really think you can take revenge on Tirth?
            Even I can’t. I am only his assistant and still I can’t control him.
          </p>
          <button class="btn-primary" onclick="goTo(5)">Okay, show secrets →</button>
        </div>

        <button class="btn-secondary" onclick="goTo(3)">← Back</button>
      </div>

      <!-- PAGE 5: SECRET INFO WITH "PROMISE ME" -->
      <div id="page5" class="page">
        <h2>🤫 Tirth’s Secret Info</h2>
        <p>
          Don’t tell anyone. I am giving you some secret information about Tirth.
          Click on <b>“Promise me”</b> to see each line.
        </p>
        <div id="factBox" class="fact-box">
          Tap “Promise me” to show first secret.
        </div>
        <button id="factBtn" class="btn-primary" onclick="nextFact()">Promise me 🤝</button>
      </div>

      <!-- PAGE 6: ANGER METER + SUFFERING -->
      <div id="page6" class="page">
        <h2>🔥 Anger & Suffering</h2>
        <label>Choose your anger level (1–100):</label>
        <input id="angerInput" type="range" min="1" max="100" value="50"
               oninput="updateAngerLabel(this.value)" />
        <p id="angerLabel">Anger Level: 50%</p>

        <div id="sufferingBlock" style="display:none;">
          <p id="sufferText"></p>
          <p>
            After all this, do you really feel Tirth said sorry from his heart
            for what he did to you? Or are we just enjoying the drama?
          </p>
          <button class="btn-primary" onclick="goTo(7)">Let’s go to final chapter →</button>
        </div>

        <button id="sufferBtn" class="btn-primary" onclick="showSuffering()">
          Next · Show suffering level
        </button>
      </div>

      <!-- PAGE 7: PROMISE LIST -->
      <div id="page7" class="page">
        <h2>📜 Promise List (Formality)</h2>
        <p class="tiny">
          Here is the “good boy” list. I have to show this because I am his AI assistant.
        </p>

        <div class="checklist" id="promiseBox">
          <div>✔ No unnecessary spam calls</div>
          <div>✔ No “Are you alive?” messages 27 times</div>
          <div>✔ No extra teasing (okay, maybe little)</div>
          <div>✔ No over-dramatic reactions</div>
          <div>✔ Will reply normally</div>
          <div>✔ Will respect your time</div>
          <div>✔ Will not test Python spam scripts on you</div>
        </div>

        <p id="promiseLockedText">Promise locked! Let’s see in real life what happens. 😏</p>

        <button class="btn-primary" onclick="lockPromise()">
          Lock this promise 🔒
        </button>
        <button class="btn-secondary" onclick="goTo(8)">
          Skip · Go to last page →
        </button>
      </div>

      <!-- PAGE 8: FINAL + WHATSAPP SORRY COUNTER -->
      <div id="page8" class="page">
        <h2>⚠️ Final Page · Be Careful</h2>
        <p>
          You are still reading everything. I never saw a bigger idiot than Tirth…
          and now maybe you are also joining the list.
        </p>
        <p>
          Any details about him from point 1–3 may be wrong.
          One of 4 or 5 is 100% real. Because understanding Tirth is like
          trying to find a dinosaur.
        </p>

        <label>How many times should Tirth say “sorry” to you?</label>
        <input id="sorryCount" type="number" min="1" placeholder="Example: 500" />

        <button class="btn-primary" onclick="sendWhatsApp()">
          End chat on WhatsApp · Send message →
        </button>

        <button class="btn-secondary" onclick="goTo(1)">
          Restart whole chaos 🔁
        </button>
      </div>

    </div>
  </div>

  <script>
    // ---------- LOADER ----------
    window.addEventListener("load", function () {
      const loader = document.getElementById("loadingScreen");
      const app    = document.getElementById("appContainer");
      setTimeout(function () {
        loader.classList.add("fade-out");
        app.style.display = "block";
      }, 1200);
    });

    // ---------- DATA ----------
    const codes = {
      "Price Goswami": "PG-0427",
      "Pari Verma": "PV-1121",
      "Ved Patel": "VP-7789",
      "Raj Chaudhary": "RC-3012",
      "Sneha Prajapati": "SP-5566",
      "Nidhi Patel": "NP-0903",
      "Dancy Patel": "DP-8801",
      "Jiya Patel": "JP-4517"
    };

    const REAL_BIRTH = "24-03-2008";
    const REAL_NICK  = "ravi patel";

    let currentName   = "";
    let currentReason = "";
    let factIndex     = -1;

    const facts = [
      "1. 45% of people never return to their sport after ACL injury, but he actually came back.",
      "2. No revenge. He likes the person he became because of his past experiences.",
      "3. Nowadays he doesn’t care about small things. He is simply enjoying his own life.",
      "4. He plays LW and LB. He likes more ‘halalball’ play style. Favourite players: Messi, Pedri, Gavi, Fermín, Yamal, Szczęsny, Balde, Cubarsí, Kounde, Eric, Frenkie de Jong, Lewandowski, Raphinha, Dayal, Neymar, Dani Alves, Julián Álvarez and more. Favourite clubs: Barcelona, PSG, Man City, Inter Miami – basically where Messi played. Chutiya Tirth never tells this himself.",
      "5. If there is an award for doing bakchodi on every topic, it will go to him directly."
    ];

    const finalSecretText =
      "You are still reading? I never saw a bigger idiot than me. Any details about me from 1–3 may be wrong, " +
      "but one of 4 or 5 is 100% real. Understanding Tirth is like trying to find a dinosaur.";

    // ---------- PAGE SWITCH ----------
    function goTo(n) {
      document.querySelectorAll(".page").forEach(p => p.classList.remove("active"));
      document.getElementById("page" + n).classList.add("active");
      if (n === 3) startTyping();
    }

    // ---------- PAGE 2: VERIFY CODE ----------
    function verify() {
      const nameEl   = document.getElementById("name");
      const codeEl   = document.getElementById("code");
      const statusEl = document.getElementById("status");
      const reasonEl = document.querySelector("input[name='reason']:checked");

      statusEl.textContent = "";
      statusEl.className = "status";

      const name   = nameEl.value.trim();
      const code   = codeEl.value.trim();

      if (!name) {
        statusEl.textContent = "Select your name first.";
        statusEl.classList.add("error");
        return;
      }
      if (!reasonEl) {
        statusEl.textContent = "Select at least one reason.";
        statusEl.classList.add("error");
        return;
      }
      if (!code) {
        statusEl.textContent = "Enter your secret code.";
        statusEl.classList.add("error");
        return;
      }

      const correctCode = codes[name];
      if (!correctCode) {
        statusEl.textContent = "No code found for this name. Ask Tirth.";
        statusEl.classList.add("error");
        return;
      }

      if (code === correctCode) {
        currentName   = name;
        currentReason = reasonEl.value;
        statusEl.textContent = "Code correct. Going next…";
        statusEl.classList.add("success");
        setTimeout(() => goTo(3), 600);
      } else {
        statusEl.textContent = "Wrong code.";
        statusEl.classList.add("error");
        alert("Wrong code! Tirth will spam you again. 😈");
      }
    }

    // ---------- PAGE 3: MESSAGE ----------
    function buildMessage() {
      switch (currentReason) {
        case "bored":
          return (
            `Hey ${currentName},\n\n` +
            "He was not really bored. He just saw your name and his brain said:\n" +
            "“Chalo, thodi shanti todte hain.”\n\n" +
            "So no, it was not boredom. It was a planned disturbance only for you."
          );
        case "teasing":
          return (
            `Hey ${currentName},\n\n` +
            "For him, teasing you is entertainment, not crime.\n" +
            "Dark jokes, pulling your leg, little flirty tone sometimes… all in combo pack.\n\n" +
            "He knows you understand his vibe, that’s why he keeps pushing the limit."
          );
        case "prank":
          return (
            `Hey ${currentName},\n\n` +
            "Official reason: “It was a prank.”\n" +
            "Real story: he wanted to see your drama reaction live.\n\n" +
            "He doesn’t love pranks, he loves your angry face more."
          );
        case "ignored":
          return (
            `Hey ${currentName},\n\n` +
            "You ignored his message one time and his ego took it very personally.\n" +
            "Overthinking started, full Bollywood background music in his head.\n\n" +
            "So yes, he spammed you. Not because he hates you, but because your 'seen' hurt more than exam result."
          );
        case "testing":
          return (
            `Hey ${currentName},\n\n` +
            "You were not chatting with a normal person. You were chatting with a coder in experiment mode.\n" +
            "He has Python scripts for call, SMS, email and WhatsApp spam.\n\n" +
            "And you became the test case. If anything worked properly, you helped his project for free."
          );
        default:
          return (
            `Hey ${currentName},\n\n` +
            "Reason is not clear, but one thing is sure: he did some bakchodi on purpose."
          );
      }
    }

    function startTyping() {
      const typingEl = document.getElementById("typing");
      const text = buildMessage();
      typingEl.textContent = "";
      let i = 0;
      const speed = 30;
      const interval = setInterval(() => {
        typingEl.textContent += text.charAt(i);
        i++;
        if (i >= text.length) clearInterval(interval);
      }, speed);
    }

    // ---------- PAGE 4: QUIZ ----------
    function checkQuiz() {
      const birth = document.getElementById("birthInput").value.trim();
      const nick  = document.getElementById("nickInput").value.trim().toLowerCase();
      const status = document.getElementById("quizStatus");
      const result = document.getElementById("quizResult");

      status.textContent = "";
      status.className = "status";
      result.style.display = "none";

      if (!birth || !nick) {
        status.textContent = "Fill both answers.";
        status.classList.add("error");
        return;
      }

      if (birth === REAL_BIRTH && nick === REAL_NICK) {
        status.textContent = "Correct. You know him too well.";
        status.classList.add("success");
        result.style.display = "block";
      } else {
        status.textContent = "Wrong details.";
        status.classList.add("error");
        alert("Wrong details. Revenge access denied. 😏");
      }
    }

    // ---------- PAGE 5: SECRETS ----------
    function nextFact() {
      const factBox = document.getElementById("factBox");
      const factBtn = document.getElementById("factBtn");

      factIndex++;

      if (factIndex < facts.length) {
        factBox.textContent = facts[factIndex];
        factBtn.textContent = "Next secret →";
      } else if (factIndex === facts.length) {
        factBox.textContent = finalSecretText;
        factBtn.textContent = "Go to anger meter →";
      } else {
        goTo(6);
      }
    }

    // ---------- PAGE 6: ANGER & SUFFERING ----------
    function updateAngerLabel(val) {
      document.getElementById("angerLabel").textContent =
        "Anger Level: " + val + "%";
      const fill = document.querySelector(".meter-fill");
      fill.style.width = val + "%";
    }

    function showSuffering() {
      const anger = parseInt(document.getElementById("angerInput").value, 10) || 1;
      // suffering: random but around anger level
      let suffer = anger + Math.floor(Math.random() * 20) - 10;
      if (suffer < 1) suffer = 1;
      if (suffer > 100) suffer = 100;

      document.getElementById("sufferText").textContent =
        "Suffering Level: " + suffer + "%";

      document.getElementById("sufferingBlock").style.display = "block";
      document.getElementById("sufferBtn").style.display = "none";
    }

    // ---------- PAGE 7: PROMISE LOCK ----------
    let promiseLocked = false;

    function lockPromise() {
      if (promiseLocked) return;
      promiseLocked = true;

      const box = document.getElementById("promiseBox");
      box.style.boxShadow = "0 0 18px rgba(96, 165, 250, 0.7)";
      box.style.borderColor = "#a855f7";

      const text = document.getElementById("promiseLockedText");
      text.style.display = "block";
    }

    // ---------- PAGE 8: WHATSAPP SEND ----------
    function sendWhatsApp() {
      const countInput = document.getElementById("sorryCount");
      let n = parseInt(countInput.value, 10);
      if (isNaN(n) || n < 1) n = 1;

      const msg = `Say sorry ${n} times to me.`;
      const url = "https://wa.me/+91701664774?text=" + encodeURIComponent(msg);
      window.location.href = url;
    }
  </script>
</body>
</html>
