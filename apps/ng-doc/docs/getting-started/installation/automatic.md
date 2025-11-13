<!-- ======================================================
MAILCHIMP GAME CAMPAIGN SETUP
File: mailchimp-game.html
Author: Vignesh
====================================================== -->

<!-- ==============================
PART 1: MAILCHIMP EMAIL HTML
(Paste this into your Mailchimp HTML campaign editor)
============================== -->

<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Play & Win!</title>
    <style>
      .button {
        display:inline-block;
        padding:12px 22px;
        text-decoration:none;
        border-radius:8px;
        background:#0073ff;
        color:#fff;
        font-weight:600;
        font-size:16px;
      }
    </style>
  </head>
  <body style="margin:0;padding:0;background:#f6f9fc;font-family:Arial,Helvetica,sans-serif;">
    <table width="100%" cellspacing="0" cellpadding="0" border="0">
      <tr>
        <td align="center">
          <table width="600" cellpadding="0" cellspacing="0" style="background:#fff;margin:20px auto;border-radius:12px;overflow:hidden;">
            <tr>
              <td align="center" style="padding:30px;">
                <h1 style="color:#111;margin-bottom:10px;">🎮 Play & Win!</h1>
                <p style="color:#444;margin-bottom:20px;">Think you're quick? Tap below to play our 15-second speed challenge and win exciting rewards!</p>

                <!-- Animated preview or image -->
                <a href="https://yourdomain.com/game/index.html?utm_source=newsletter&utm_medium=email&utm_campaign=game" target="_blank">
                  <img src="https://yourdomain.com/game/game-preview.gif" width="520" style="max-width:100%;border-radius:10px;" alt="Play Now">
                </a>

                <div style="margin-top:20px;">
                  <a href="https://yourdomain.com/game/index.html?utm_source=newsletter&utm_medium=email&utm_campaign=game" class="button" target="_blank">Play Now</a>
                </div>

                <p style="font-size:12px;color:#999;margin-top:20px;">
                  If the button doesn’t work, copy this link:<br>
                  <a href="https://yourdomain.com/game/index.html" style="color:#0073ff;">https://yourdomain.com/game/</a>
                </p>
              </td>
            </tr>
          </table>
        </td>
      </tr>
    </table>
  </body>
</html>

<!-- ==============================
PART 2: HOSTED GAME PAGE
(Save this as /game/index.html on your web host)
============================== -->

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Quick Click Game</title>
    <style>
      body {
        margin:0;
        font-family:Arial,Helvetica,sans-serif;
        background:linear-gradient(180deg,#eef4ff,#ffffff);
        display:flex;
        align-items:center;
        justify-content:center;
        min-height:100vh;
      }
      #game {
        width:100%;
        max-width:480px;
        background:#fff;
        border-radius:14px;
        box-shadow:0 8px 20px rgba(30,40,60,0.12);
        padding:18px;
      }
      header { text-align:center; }
      h1 { margin:0;font-size:22px;color:#111; }
      p.lead { color:#555;font-size:14px;margin:4px 0 16px; }
      #arena {
        position:relative;
        width:100%;
        background:#f6f9ff;
        border-radius:8px;
        overflow:hidden;
      }
      .circle {
        position:absolute;
        border-radius:50%;
        background:#0073ff;
        display:flex;
        align-items:center;
        justify-content:center;
        color:#fff;
        font-weight:700;
        cursor:pointer;
        user-select:none;
        touch-action:none;
      }
      .meta {
        display:flex;
        justify-content:space-between;
        align-items:center;
        margin-top:12px;
        font-size:14px;
      }
      .btn {
        background:#0073ff;
        color:#fff;
        padding:8px 14px;
        border-radius:8px;
        text-decoration:none;
        font-weight:600;
        border:none;
      }
      #endScreen {
        margin-top:16px;
        display:none;
        text-align:center;
      }
      #claimForm input {
        width:80%;
        padding:8px;
        border-radius:6px;
        border:1px solid #ccc;
      }
      #thanks { color:green;margin-top:10px;display:none; }
    </style>
  </head>
  <body>
    <div id="game">
      <header>
        <h1>Quick Click Challenge</h1>
        <p class="lead">Tap the circle as many times as possible in 15 seconds!</p>
      </header>

      <div id="arena"></div>

      <div class="meta">
        <div>⏱ Time: <span id="time">15</span>s</div>
        <div>⭐ Score: <span id="score">0</span></div>
        <button id="startBtn" class="btn">Start</button>
      </div>

      <div id="endScreen">
        <h2>Great job!</h2>
        <p>Your final score: <strong id="finalScore">0</strong></p>
        <form id="claimForm">
          <label>Enter your email to claim a surprise:</label><br>
          <input type="email" id="email" placeholder="you@example.com" required>
          <div style="margin-top:10px;">
            <button class="btn" type="submit">Submit</button>
          </div>
        </form>
        <div id="thanks">✅ Thanks! We’ll be in touch soon.</div>
      </div>
    </div>

    <script>
      const arena = document.getElementById("arena");
      const timeEl = document.getElementById("time");
      const scoreEl = document.getElementById("score");
      const startBtn = document.getElementById("startBtn");
      const endScreen = document.getElementById("endScreen");
      const finalScore = document.getElementById("finalScore");
      const claimForm = document.getElementById("claimForm");
      const thanks = document.getElementById("thanks");

      let timeLeft = 15;
      let score = 0;
      let timer = null;
      let activeCircle = null;

      function setArenaHeight() {
        // keep 56% aspect ratio (same as original padding-top:56%)
        arena.style.height = Math.round(arena.clientWidth * 0.56) + "px";
      }

      function spawnCircle() {
        if (activeCircle) activeCircle.remove();
        const circle = document.createElement("div");
        circle.className = "circle";
        const size = 40 + Math.random() * 60; // 40 - 100px
        circle.style.width = size + "px";
        circle.style.height = size + "px";

        // compute available area
        const maxX = Math.max(0, arena.clientWidth - size);
        const maxY = Math.max(0, arena.clientHeight - size);
        circle.style.left = Math.random() * maxX + "px";
        circle.style.top = Math.random() * maxY + "px";

        const pts = Math.max(1, Math.round(100 / size));
        circle.textContent = pts;

        // use pointerdown for better touch responsiveness
        circle.addEventListener("pointerdown", () => {
          score += pts;
          scoreEl.textContent = score;
          spawnCircle();
        }, { passive: true });

        arena.appendChild(circle);
        activeCircle = circle;
      }

      function startGame() {
        if (timer) clearInterval(timer);
        score = 0;
        timeLeft = 15;
        scoreEl.textContent = 0;
        timeEl.textContent = timeLeft;
        endScreen.style.display = "none";
        claimForm.style.display = "block";
        thanks.style.display = "none";
        spawnCircle();
        timer = setInterval(() => {
          timeLeft--;
          timeEl.textContent = timeLeft;
          if (timeLeft <= 0) finishGame();
        }, 1000);
      }

      function finishGame() {
        if (timer) {
          clearInterval(timer);
          timer = null;
        }
        if (activeCircle) activeCircle.remove();
        activeCircle = null;
        finalScore.textContent = score;
        endScreen.style.display = "block";
      }

      startBtn.addEventListener("click", (e) => {
        e.preventDefault();
        startGame();
      });

      claimForm.addEventListener("submit", (e) => {
        e.preventDefault();
        claimForm.style.display = "none";
        thanks.style.display = "block";
        // optionally send data:
        // const email = document.getElementById('email').value;
        // fetch('https://your-api.com/claim', { method:'POST', headers:{'Content-Type':'application/json'}, body: JSON.stringify({email, score}) })
      });

      // responsive sizing
      function onResize() {
        setArenaHeight();
        // ensure existing circle stays inside or respawn it
        if (activeCircle) {
          const size = activeCircle.offsetWidth;
          const maxX = Math.max(0, arena.clientWidth - size);
          const maxY = Math.max(0, arena.clientHeight - size);
          const left = parseFloat(activeCircle.style.left) || 0;
          const top = parseFloat(activeCircle.style.top) || 0;
          if (left > maxX || top > maxY) spawnCircle();
        }
      }

      window.addEventListener('resize', onResize);
      // initial layout
      setArenaHeight();
    </script>
  </body>
</html>
