<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Valentine?</title>
  <style>
    :root {
      --bg1: #ffeff5;
      --bg2: #fff7e6;
      --card: rgba(255, 255, 255, 0.85);
      --shadow: 0 12px 40px rgba(0,0,0,0.12);
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      height: 100vh;
      display: grid;
      place-items: center;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Arial, "Apple Color Emoji","Segoe UI Emoji";
      background: radial-gradient(circle at 20% 20%, var(--bg2), transparent 55%),
                  radial-gradient(circle at 80% 0%, var(--bg1), transparent 60%),
                  linear-gradient(135deg, #ffffff, #ffeaf2);
      overflow: hidden;
    }

    .card {
      width: min(560px, 92vw);
      padding: 28px 22px;
      border-radius: 22px;
      background: var(--card);
      box-shadow: var(--shadow);
      backdrop-filter: blur(10px);
      text-align: center;
      position: relative;
    }

    h1 {
      margin: 0 0 18px;
      font-size: clamp(26px, 4vw, 38px);
      letter-spacing: -0.02em;
    }

    p {
      margin: 0 0 22px;
      opacity: 0.75;
    }

    .btn-row {
      display: flex;
      gap: 14px;
      justify-content: center;
      align-items: center;
      position: relative;
      height: 70px;
    }

    button {
      border: 0;
      padding: 14px 22px;
      border-radius: 999px;
      font-size: 16px;
      cursor: pointer;
      transition: transform 0.12s ease, box-shadow 0.12s ease, opacity 0.12s ease;
      box-shadow: 0 10px 25px rgba(0,0,0,0.12);
      user-select: none;
    }

    button:active { transform: scale(0.98); }

    #yesBtn {
      background: #ff3b7a;
      color: white;
    }
    #yesBtn:hover { transform: translateY(-1px); }

    /* The "No" button will be positioned absolutely so it can run away */
    #noBtn {
      background: #222;
      color: white;
      position: absolute;
      left: 52%;
      top: 50%;
      transform: translate(-50%, -50%);
    }

    .message {
      margin-top: 18px;
      font-size: 22px;
      font-weight: 700;
      display: none;
    }

    .hearts {
      position: absolute;
      inset: 0;
      pointer-events: none;
      overflow: hidden;
      border-radius: 22px;
    }

    .heart {
      position: absolute;
      bottom: -20px;
      font-size: 18px;
      opacity: 0.8;
      animation: floatUp linear forwards;
    }

    @keyframes floatUp {
      to {
        transform: translateY(-140px) rotate(12deg);
        opacity: 0;
      }
    }
  </style>
</head>
<body>
  <div class="card" id="card">
    <h1 id="title">Jisu will you be my valentine?</h1>
    <p id="subtitle">Choose wisely 😈</p>

    <div class="btn-row" id="btnRow">
      <button id="yesBtn">Yes</button>
      <button id="noBtn">No</button>
    </div>

    <div class="message" id="message">wooooo, i love you baby</div>

    <div class="hearts" id="hearts"></div>
  </div>

  <script>
    const card = document.getElementById("card");
    const btnRow = document.getElementById("btnRow");
    const yesBtn = document.getElementById("yesBtn");
    const noBtn = document.getElementById("noBtn");
    const message = document.getElementById("message");
    const title = document.getElementById("title");
    const subtitle = document.getElementById("subtitle");
    const hearts = document.getElementById("hearts");

    let locked = false;

    function clamp(n, min, max) {
      return Math.max(min, Math.min(max, n));
    }

    function moveNoButton() {
      if (locked) return;

      const rowRect = btnRow.getBoundingClientRect();
      const btnRect = noBtn.getBoundingClientRect();

      // Keep the button inside the button-row area
      const maxX = rowRect.width - btnRect.width;
      const maxY = rowRect.height - btnRect.height;

      const x = Math.random() * maxX;
      const y = Math.random() * maxY;

      noBtn.style.left = `${x}px`;
      noBtn.style.top = `${y}px`;
      noBtn.style.transform = `translate(0, 0)`;
    }

    // If the mouse gets close to the No button, make it run away
    document.addEventListener("mousemove", (e) => {
      if (locked) return;

      const btnRect = noBtn.getBoundingClientRect();
      const cx = btnRect.left + btnRect.width / 2;
      const cy = btnRect.top + btnRect.height / 2;

      const dx = e.clientX - cx;
      const dy = e.clientY - cy;
      const dist = Math.hypot(dx, dy);

      // "Fear radius" — tune this if you want it more/less aggressive
      if (dist < 120) moveNoButton();
    });

    // Also run away if she tries to hover it directly
    noBtn.addEventListener("mouseenter", moveNoButton);

    function popHearts(count = 18) {
      for (let i = 0; i < count; i++) {
        const s = document.createElement("div");
        s.className = "heart";
        s.textContent = Math.random() < 0.5 ? "💗" : "💖";

        const x = Math.random() * 100;
        const dur = 900 + Math.random() * 900;

        s.style.left = `${x}%`;
        s.style.animationDuration = `${dur}ms`;

        hearts.appendChild(s);
        setTimeout(() => s.remove(), dur + 50);
      }
    }

    yesBtn.addEventListener("click", () => {
      locked = true;

      title.textContent = "";
      subtitle.style.display = "none";
      btnRow.style.display = "none";

      message.style.display = "block";
      popHearts(28);

      // little celebration pulse
      card.animate(
        [{ transform: "scale(1)" }, { transform: "scale(1.02)" }, { transform: "scale(1)" }],
        { duration: 450, easing: "ease-out" }
      );
    });

    // Initialize No button position nicely within btnRow
    // Convert from % positioning to px-based once layout is ready
    window.addEventListener("load", () => {
      const rowRect = btnRow.getBoundingClientRect();
      const btnRect = noBtn.getBoundingClientRect();
      const startX = clamp(rowRect.width * 0.62, 0, rowRect.width - btnRect.width);
      const startY = clamp((rowRect.height - btnRect.height) / 2, 0, rowRect.height - btnRect.height);

      noBtn.style.left = `${startX}px`;
      noBtn.style.top = `${startY}px`;
      noBtn.style.transform = "translate(0, 0)";
    });
  </script>
</body>
</html>
