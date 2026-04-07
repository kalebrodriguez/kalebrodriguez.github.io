# kalebrodriguez.github.io

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Posture+ | AI Posture Coach</title>
  <script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/@mediapipe/drawing_utils/drawing_utils.js" crossorigin="anonymous"></script>
  <script src="https://cdn.jsdelivr.net/npm/@mediapipe/pose/pose.js" crossorigin="anonymous"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap');

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg:       #06091a;
      --surface:  #0c1128;
      --card:     #101830;
      --card2:    #131e38;
      --border:   rgba(255,255,255,0.07);
      --accent:   #00d4ff;
      --accent2:  #0050ff;
      --good:     #00f593;
      --fair:     #ffc940;
      --poor:     #ff4d6d;
      --text:     #e2e8ff;
      --muted:    #5a6a9a;
      --r:        16px;
    }

    html, body { height: 100%; }

    body {
      font-family: 'Inter', system-ui, sans-serif;
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      overflow-x: hidden;
    }

    /* ── Animated background grid ─────────────────────────── */
    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background-image:
        linear-gradient(rgba(0,212,255,0.03) 1px, transparent 1px),
        linear-gradient(90deg, rgba(0,212,255,0.03) 1px, transparent 1px);
      background-size: 60px 60px;
      pointer-events: none;
      z-index: 0;
    }

    body::after {
      content: '';
      position: fixed;
      top: -40%;
      left: -20%;
      width: 80%;
      height: 80%;
      background: radial-gradient(ellipse, rgba(0,80,255,0.07) 0%, transparent 70%);
      pointer-events: none;
      z-index: 0;
      animation: drift 12s ease-in-out infinite alternate;
    }

    @keyframes drift {
      from { transform: translate(0, 0); }
      to   { transform: translate(10%, 8%); }
    }

    /* ── Header ───────────────────────────────────────────── */
    header {
      position: relative;
      z-index: 10;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 16px 32px;
      background: rgba(12,17,40,0.85);
      border-bottom: 1px solid var(--border);
      backdrop-filter: blur(16px);
    }

    .logo {
      display: flex;
      align-items: center;
      gap: 12px;
      font-size: 1.45rem;
      font-weight: 800;
      letter-spacing: -0.5px;
    }

    .logo-icon {
      width: 38px; height: 38px;
      background: linear-gradient(135deg, var(--accent), var(--accent2));
      border-radius: 11px;
      display: flex; align-items: center; justify-content: center;
      font-size: 1.1rem;
      box-shadow: 0 0 18px rgba(0,212,255,0.35);
    }

    .logo-plus { color: var(--accent); }

    .beta-pill {
      font-size: 0.65rem;
      font-weight: 700;
      letter-spacing: 1.5px;
      background: linear-gradient(90deg, var(--accent), var(--accent2));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      border: 1px solid rgba(0,212,255,0.3);
      padding: 3px 9px;
      border-radius: 20px;
    }

    .header-right {
      display: flex;
      align-items: center;
      gap: 14px;
    }

    .tye-badge {
      font-size: 0.72rem;
      color: var(--muted);
      border: 1px solid var(--border);
      padding: 5px 13px;
      border-radius: 20px;
      backdrop-filter: blur(8px);
    }

    .status-pill {
      display: flex;
      align-items: center;
      gap: 6px;
      font-size: 0.72rem;
      font-weight: 600;
      padding: 5px 13px;
      border-radius: 20px;
      background: rgba(0,245,147,0.1);
      border: 1px solid rgba(0,245,147,0.25);
      color: var(--good);
      transition: all 0.4s ease;
    }

    .status-pill.bad {
      background: rgba(255,77,109,0.1);
      border-color: rgba(255,77,109,0.3);
      color: var(--poor);
    }

    .pulse-dot {
      width: 7px; height: 7px;
      border-radius: 50%;
      background: currentColor;
      animation: pulse-dot 1.5s ease-in-out infinite;
    }

    @keyframes pulse-dot {
      0%, 100% { opacity: 1; transform: scale(1); }
      50%       { opacity: 0.4; transform: scale(0.7); }
    }

    /* ── Main Layout ──────────────────────────────────────── */
    main {
      position: relative;
      z-index: 1;
      flex: 1;
      display: grid;
      grid-template-columns: 1fr 380px;
      gap: 18px;
      padding: 20px 28px;
      max-width: 1440px;
      width: 100%;
      margin: 0 auto;
    }

    /* ── Left Column ──────────────────────────────────────── */
    .left-col { display: flex; flex-direction: column; gap: 16px; }

    /* ── Video ────────────────────────────────────────────── */
    .video-card {
      position: relative;
      border-radius: var(--r);
      overflow: hidden;
      border: 1px solid var(--border);
      background: #000;
      aspect-ratio: 16/9;
      box-shadow: 0 0 0 1px rgba(0,212,255,0.1), 0 20px 60px rgba(0,0,0,0.5);
      transition: box-shadow 0.5s ease;
    }

    .video-card.good-posture {
      box-shadow: 0 0 0 1px rgba(0,245,147,0.25), 0 0 40px rgba(0,245,147,0.08), 0 20px 60px rgba(0,0,0,0.5);
    }

    .video-card.bad-posture {
      box-shadow: 0 0 0 1px rgba(255,77,109,0.35), 0 0 40px rgba(255,77,109,0.1), 0 20px 60px rgba(0,0,0,0.5);
    }

    .video-card canvas { width: 100%; height: 100%; display: block; }
    video { display: none; }

    .live-badge {
      position: absolute;
      top: 14px;
      left: 14px;
      display: flex;
      align-items: center;
      gap: 6px;
      background: rgba(6,9,26,0.7);
      padding: 5px 12px;
      border-radius: 20px;
      font-size: 0.72rem;
      font-weight: 700;
      letter-spacing: 1px;
      backdrop-filter: blur(10px);
      border: 1px solid rgba(255,255,255,0.08);
    }

    /* ── Score Row ────────────────────────────────────────── */
    .score-row {
      display: grid;
      grid-template-columns: auto 1fr auto;
      gap: 16px;
      align-items: center;
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: var(--r);
      padding: 18px 24px;
    }

    /* Score ring */
    .ring-wrap {
      position: relative;
      width: 88px; height: 88px;
      flex-shrink: 0;
    }

    .ring-wrap svg { transform: rotate(-90deg); }

    .ring-track { fill: none; stroke: rgba(255,255,255,0.06); stroke-width: 8; }

    .ring-fill {
      fill: none;
      stroke-width: 8;
      stroke-linecap: round;
      transition: stroke-dashoffset 0.7s cubic-bezier(.4,0,.2,1), stroke 0.5s ease;
      filter: drop-shadow(0 0 6px currentColor);
    }

    .ring-label {
      position: absolute;
      inset: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      line-height: 1;
    }

    .ring-num {
      font-size: 1.55rem;
      font-weight: 800;
      transition: color 0.4s;
    }

    .ring-sub { font-size: 0.6rem; color: var(--muted); margin-top: 2px; }

    /* Glow ring pulse when poor */
    @keyframes ring-pulse {
      0%, 100% { opacity: 1; }
      50%       { opacity: 0.5; }
    }

    .ring-fill.poor-anim { animation: ring-pulse 1.4s ease-in-out infinite; }

    /* Score meta */
    .score-meta h3 {
      font-size: 0.7rem;
      font-weight: 600;
      letter-spacing: 1.5px;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 6px;
    }

    .score-status {
      font-size: 1.7rem;
      font-weight: 800;
      letter-spacing: -0.5px;
      transition: color 0.4s;
    }

    .score-status.good { color: var(--good); }
    .score-status.fair { color: var(--fair); }
    .score-status.poor { color: var(--poor); }
    .score-status.none { color: var(--muted); }

    .score-sub { font-size: 0.8rem; color: var(--muted); margin-top: 4px; }

    /* Session quick stats */
    .quick-stats {
      display: flex;
      flex-direction: column;
      gap: 8px;
      padding-left: 16px;
      border-left: 1px solid var(--border);
    }

    .qs-item {
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 0.78rem;
    }

    .qs-val { font-weight: 700; color: var(--accent); min-width: 38px; }
    .qs-lbl { color: var(--muted); }

    /* ── Right Column ─────────────────────────────────────── */
    .right-col { display: flex; flex-direction: column; gap: 14px; }

    .card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: var(--r);
      padding: 18px 20px;
    }

    .card-title {
      font-size: 0.68rem;
      font-weight: 700;
      letter-spacing: 1.8px;
      text-transform: uppercase;
      color: var(--muted);
      margin-bottom: 14px;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .card-title::after {
      content: '';
      flex: 1;
      height: 1px;
      background: var(--border);
    }

    /* ── Body Breakdown ───────────────────────────────────── */
    .breakdown-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
    }

    .part-card {
      background: var(--card2);
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 14px;
      display: flex;
      flex-direction: column;
      gap: 8px;
      transition: border-color 0.4s, background 0.4s;
    }

    .part-card.ok   { border-color: rgba(0,245,147,0.2); background: rgba(0,245,147,0.04); }
    .part-card.warn { border-color: rgba(255,201,64,0.25); background: rgba(255,201,64,0.04); }
    .part-card.bad  { border-color: rgba(255,77,109,0.3);  background: rgba(255,77,109,0.05); }

    .part-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .part-icon { font-size: 1.3rem; }

    .part-dot {
      width: 10px; height: 10px;
      border-radius: 50%;
      background: var(--muted);
      transition: background 0.4s, box-shadow 0.4s;
    }

    .part-dot.ok   { background: var(--good); box-shadow: 0 0 8px var(--good); }
    .part-dot.warn { background: var(--fair); box-shadow: 0 0 8px var(--fair); }
    .part-dot.bad  { background: var(--poor); box-shadow: 0 0 8px var(--poor); animation: dot-blink 1s infinite; }

    @keyframes dot-blink {
      0%, 100% { opacity: 1; }
      50%       { opacity: 0.3; }
    }

    .part-name { font-size: 0.8rem; font-weight: 600; }
    .part-status { font-size: 0.7rem; color: var(--muted); margin-top: 2px; }

    /* ── Exercise Card ────────────────────────────────────── */
    .exercise-section {
      overflow: hidden;
      max-height: 0;
      transition: max-height 0.5s cubic-bezier(.4,0,.2,1), opacity 0.4s ease;
      opacity: 0;
    }

    .exercise-section.visible {
      max-height: 600px;
      opacity: 1;
    }

    .exercise-list { display: flex; flex-direction: column; gap: 10px; }

    .ex-card {
      background: linear-gradient(135deg, rgba(0,80,255,0.08), rgba(0,212,255,0.04));
      border: 1px solid rgba(0,212,255,0.15);
      border-radius: 12px;
      padding: 13px 15px;
      display: flex;
      gap: 12px;
      align-items: flex-start;
      animation: slideUp 0.4s ease both;
    }

    @keyframes slideUp {
      from { opacity: 0; transform: translateY(8px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    .ex-icon {
      font-size: 1.4rem;
      flex-shrink: 0;
      margin-top: 1px;
    }

    .ex-name {
      font-size: 0.82rem;
      font-weight: 700;
      color: var(--accent);
      margin-bottom: 3px;
    }

    .ex-desc { font-size: 0.75rem; color: var(--muted); line-height: 1.5; }

    /* Good posture celebration */
    .good-state {
      text-align: center;
      padding: 16px 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 6px;
    }

    .good-state .big-check {
      font-size: 2.2rem;
      animation: pop 0.4s cubic-bezier(.34,1.56,.64,1);
    }

    @keyframes pop {
      from { transform: scale(0); }
      to   { transform: scale(1); }
    }

    .good-state p {
      font-size: 0.85rem;
      font-weight: 600;
      color: var(--good);
    }

    .good-state small { font-size: 0.73rem; color: var(--muted); }

    /* ── Tips ─────────────────────────────────────────────── */
    .tip-list { display: flex; flex-direction: column; gap: 9px; }

    .tip-item {
      display: flex;
      align-items: flex-start;
      gap: 10px;
      font-size: 0.8rem;
      color: var(--muted);
      line-height: 1.5;
    }

    .tip-num {
      flex-shrink: 0;
      width: 20px; height: 20px;
      background: linear-gradient(135deg, rgba(0,212,255,0.25), rgba(0,80,255,0.25));
      border: 1px solid rgba(0,212,255,0.2);
      border-radius: 50%;
      font-size: 0.65rem;
      font-weight: 800;
      display: flex; align-items: center; justify-content: center;
      color: var(--accent);
    }

    /* ── Alert ────────────────────────────────────────────── */
    #alert-banner {
      position: fixed;
      top: -120px;
      left: 50%;
      transform: translateX(-50%);
      background: linear-gradient(90deg, #ff1744, #ff4d6d);
      color: #fff;
      padding: 14px 28px;
      border-radius: 30px;
      font-weight: 700;
      font-size: 0.9rem;
      box-shadow: 0 8px 40px rgba(255,77,109,0.6), 0 0 0 1px rgba(255,255,255,0.1);
      transition: top 0.5s cubic-bezier(.34,1.56,.64,1);
      z-index: 999;
      display: flex;
      align-items: center;
      gap: 10px;
      white-space: nowrap;
    }

    #alert-banner.show { top: 76px; }

    /* ── Footer ───────────────────────────────────────────── */
    footer {
      position: relative;
      z-index: 10;
      text-align: center;
      padding: 12px;
      font-size: 0.7rem;
      color: var(--muted);
      border-top: 1px solid var(--border);
      background: rgba(12,17,40,0.6);
    }

    footer .accent { color: var(--accent); font-weight: 600; }
  </style>
</head>
<body>

<!-- ── Header ─────────────────────────────────────────────────── -->
<header>
  <div class="logo">
    <div class="logo-icon">🧍</div>
    Posture<span class="logo-plus">+</span>
    <span class="beta-pill">AI BETA</span>
  </div>
  <div class="header-right">
    <div class="status-pill" id="header-pill">
      <div class="pulse-dot"></div>
      <span id="header-pill-text">Starting…</span>
    </div>
    <div class="tye-badge">TYE Tampa Bay · Kaleb Rodriguez &amp; Samuel Harris George</div>
  </div>
</header>

<!-- ── Main ───────────────────────────────────────────────────── -->
<main>

  <!-- Left Column -->
  <div class="left-col">

    <!-- Video -->
    <div class="video-card" id="video-card">
      <video id="input-video"></video>
      <canvas id="output-canvas"></canvas>
      <div class="live-badge">
        <div class="pulse-dot" style="color:var(--good)"></div>
        LIVE&nbsp;AI
      </div>
    </div>

    <!-- Score Row -->
    <div class="score-row">
      <!-- Ring -->
      <div class="ring-wrap">
        <svg viewBox="0 0 88 88" width="88" height="88">
          <circle class="ring-track" cx="44" cy="44" r="36"/>
          <circle id="ring-fill" class="ring-fill" cx="44" cy="44" r="36"
                  stroke-dasharray="226.2"
                  stroke-dashoffset="0"
                  stroke="var(--good)"/>
        </svg>
        <div class="ring-label">
          <span class="ring-num" id="ring-num">--</span>
          <span class="ring-sub">/100</span>
        </div>
      </div>

      <!-- Status -->
      <div class="score-meta">
        <h3>Posture Score</h3>
        <div class="score-status none" id="score-status">Connecting…</div>
        <div class="score-sub" id="score-sub">Allow camera access to begin.</div>
      </div>

      <!-- Quick stats -->
      <div class="quick-stats">
        <div class="qs-item"><span class="qs-val" id="qs-time">0:00</span><span class="qs-lbl">session</span></div>
        <div class="qs-item"><span class="qs-val" id="qs-good">0%</span><span class="qs-lbl">good</span></div>
        <div class="qs-item"><span class="qs-val" id="qs-alerts">0</span><span class="qs-lbl">alerts</span></div>
      </div>
    </div>

  </div>

  <!-- Right Column -->
  <div class="right-col">

    <!-- Body Breakdown -->
    <div class="card">
      <div class="card-title">Body Breakdown</div>
      <div class="breakdown-grid">
        <div class="part-card" id="part-spine">
          <div class="part-header">
            <span class="part-icon">🦴</span>
            <div class="part-dot" id="dot-spine"></div>
          </div>
          <div class="part-name">Spine</div>
          <div class="part-status" id="status-spine">Analyzing…</div>
        </div>
        <div class="part-card" id="part-shoulders">
          <div class="part-header">
            <span class="part-icon">🏋️</span>
            <div class="part-dot" id="dot-shoulders"></div>
          </div>
          <div class="part-name">Shoulders</div>
          <div class="part-status" id="status-shoulders">Analyzing…</div>
        </div>
        <div class="part-card" id="part-head">
          <div class="part-header">
            <span class="part-icon">🧠</span>
            <div class="part-dot" id="dot-head"></div>
          </div>
          <div class="part-name">Head Position</div>
          <div class="part-status" id="status-head">Analyzing…</div>
        </div>
        <div class="part-card" id="part-neck">
          <div class="part-header">
            <span class="part-icon">🔗</span>
            <div class="part-dot" id="dot-neck"></div>
          </div>
          <div class="part-name">Neck Tilt</div>
          <div class="part-status" id="status-neck">Analyzing…</div>
        </div>
      </div>
    </div>

    <!-- Exercises / Good state -->
    <div class="card">
      <div class="card-title">Fix It Now</div>

      <!-- Good posture state -->
      <div class="good-state" id="good-state">
        <span class="big-check">✅</span>
        <p>Posture looks great!</p>
        <small>Keep it up — your spine thanks you.</small>
      </div>

      <!-- Exercise suggestions -->
      <div class="exercise-section" id="exercise-section">
        <div class="exercise-list" id="exercise-list"></div>
      </div>
    </div>

    <!-- Quick Tips -->
    <div class="card">
      <div class="card-title">Daily Habits</div>
      <div class="tip-list">
        <div class="tip-item">
          <div class="tip-num">1</div>
          <span>Ears aligned directly above shoulders — check every 20 mins.</span>
        </div>
        <div class="tip-item">
          <div class="tip-num">2</div>
          <span>Hips back in chair, feet flat on floor for spinal support.</span>
        </div>
        <div class="tip-item">
          <div class="tip-num">3</div>
          <span>Screen at eye level — raise your laptop or use a stand.</span>
        </div>
        <div class="tip-item">
          <div class="tip-num">4</div>
          <span>Stand up and walk 2 minutes for every 30 minutes seated.</span>
        </div>
      </div>
    </div>

  </div>
</main>

<!-- ── Alert Banner ───────────────────────────────────────────── -->
<div id="alert-banner">
  <span>⚠️</span>
  <span id="alert-text">Bad posture detected — fix it now!</span>
</div>

<footer>
  Powered by <span class="accent">Posture+ AI</span> &nbsp;·&nbsp; MediaPipe Pose Detection &nbsp;·&nbsp; Real-Time Computer Vision
</footer>

<video id="input-video" style="display:none"></video>

<script>
// ── Constants ────────────────────────────────────────────────
const CIRC      = 2 * Math.PI * 36;   // r = 36
const COOLDOWN  = 9000;

// ── Exercises database ───────────────────────────────────────
const EXERCISES = {
  "Slouching": {
    icon: "🤸",
    name: "Chest Opener Stretch",
    desc: "Stand up, clasp hands behind your back. Squeeze shoulder blades together, lift your chest and hold for 15 seconds. Repeat 3×."
  },
  "Uneven shoulders": {
    icon: "🔄",
    name: "Shoulder Rolls",
    desc: "Roll both shoulders backward in slow circles, 10 reps. Then shrug them up to ears, hold 3 seconds, release. Repeat 5×."
  },
  "Head drooping forward": {
    icon: "↩️",
    name: "Chin Tuck",
    desc: "Without tilting your head, pull your chin straight back (creating a 'double chin'). Hold 5 seconds, release. Do 10 reps every hour."
  },
  "Head tilting": {
    icon: "↔️",
    name: "Neck Side Stretch",
    desc: "Gently tilt your right ear toward your right shoulder. Hold 20 seconds. Switch sides. Keep shoulders relaxed and down."
  }
};

// ── Body part config ─────────────────────────────────────────
const PARTS = {
  spine:     { issue: "Slouching",           ok: "Aligned",          bad: "Slouching forward" },
  shoulders: { issue: "Uneven shoulders",    ok: "Level",            bad: "Uneven / tilted"   },
  head:      { issue: "Head drooping forward", ok: "Well positioned", bad: "Drooping forward"  },
  neck:      { issue: "Head tilting",        ok: "Centered",         bad: "Tilting sideways"  }
};

// ── State ────────────────────────────────────────────────────
let sessionStart = Date.now(), lastAlertAt = 0, alertTimeout = null;
let alertCount = 0, totalChecks = 0, goodChecks = 0;

// ── Helpers ──────────────────────────────────────────────────
function scoreColor(s) { return s >= 78 ? "var(--good)" : s >= 50 ? "var(--fair)" : "var(--poor)"; }
function statusClass(s) { return s === "Good" ? "good" : s === "Fair" ? "fair" : s === "Poor" ? "poor" : "none"; }

// ── Posture analysis ─────────────────────────────────────────
  function analyzePosture(lm) {
  const issues = [];
  const nose = lm[0], lEar = lm[7], rEar = lm[8];
  const lS = lm[11], rS = lm[12], lH = lm[23], rH = lm[24];

  const midS = { x:(lS.x+rS.x)/2, y:(lS.y+rS.y)/2 };
  const midH = { x:(lH.x+rH.x)/2, y:(lH.y+rH.y)/2 };
  const midE = { x:(lEar.x+rEar.x)/2, y:(lEar.y+rEar.y)/2 };
  const sw = Math.max(Math.abs(lS.x-rS.x), 0.001);
  const th = Math.max(Math.abs(midS.y-midH.y), 0.001);

  const spineAngle = Math.atan2(midS.x-midH.x, midH.y-midS.y)*180/Math.PI;
  if (Math.abs(spineAngle) > 12) issues.push("Slouching");

  if (Math.abs(lS.y-rS.y)/sw > 0.12) issues.push("Uneven shoulders");

  if (Math.abs(lEar.y-rEar.y)/sw > 0.12) issues.push("Head tilting");

  const neckDroop = (midE.y-midS.y)/th;
  if (neckDroop > -0.22) issues.push("Head drooping forward");

  const score  = Math.max(0, 100 - issues.length * 22);
  const status = issues.length === 0 ? "Good" : issues.length === 1 ? "Fair" : "Poor";
  return { issues, score, status, alert: issues.length > 0 };
}

// ── Update UI ─────────────────────────────────────────────────
function updateUI(data, detected) {
  totalChecks++;
  const { score, status, issues } = data;

  // Ring
  const ring = document.getElementById("ring-fill");
  ring.style.strokeDashoffset = CIRC - (score/100)*CIRC;
  ring.style.stroke = scoreColor(score);
  ring.classList.toggle("poor-anim", status === "Poor" && detected);
  document.getElementById("ring-num").textContent = detected ? score : "--";

  // Score status
  const ss = document.getElementById("score-status");
  ss.textContent  = detected ? status : "No Person";
  ss.className    = "score-status " + (detected ? statusClass(status) : "none");
  document.getElementById("score-sub").textContent =
    !detected ? "Position yourself in the camera frame." :
    issues.length === 0 ? "Excellent posture — keep it up!" :
    `${issues.length} issue${issues.length>1?"s":""} detected.`;

  // Header pill
  const pill     = document.getElementById("header-pill");
  const pillText = document.getElementById("header-pill-text");
  pill.className  = "status-pill" + (data.alert && detected ? " bad" : "");
  pillText.textContent = !detected ? "No Person" : status + " Posture";

  // Video card glow
  const vc = document.getElementById("video-card");
  vc.className = "video-card" + (!detected ? "" : status==="Good" ? " good-posture" : " bad-posture");

  // Quick stats
  if (detected) { if (status==="Good") goodChecks++; }
  const pct = totalChecks>0 ? Math.round(goodChecks/totalChecks*100) : 0;
  document.getElementById("qs-good").textContent = pct + "%";
  document.getElementById("qs-alerts").textContent = alertCount;

  // Body breakdown
  for (const [partKey, cfg] of Object.entries(PARTS)) {
    const hasIssue = issues.includes(cfg.issue);
    const dot  = document.getElementById("dot-" + partKey);
    const card = document.getElementById("part-" + partKey);
    const stat = document.getElementById("status-" + partKey);

    const cls = !detected ? "" : hasIssue ? "bad" : "ok";
    dot.className  = "part-dot " + cls;
    card.className = "part-card " + cls;
    stat.textContent = !detected ? "Waiting…" : hasIssue ? cfg.bad : cfg.ok;
  }

  // Exercises / good state
  const goodState  = document.getElementById("good-state");
  const exSection  = document.getElementById("exercise-section");
  const exList     = document.getElementById("exercise-list");

  if (!detected || issues.length === 0) {
    goodState.style.display = "flex";
    exSection.classList.remove("visible");
  } else {
    goodState.style.display = "none";
    exSection.classList.add("visible");
    exList.innerHTML = issues.map((iss, i) => {
      const ex = EXERCISES[iss];
      if (!ex) return "";
      return `<div class="ex-card" style="animation-delay:${i*0.08}s">
        <span class="ex-icon">${ex.icon}</span>
        <div>
          <div class="ex-name">${ex.name}</div>
          <div class="ex-desc">${ex.desc}</div>
        </div>
      </div>`;
    }).join("");
  }

  // Alert
  if (data.alert && detected && Date.now()-lastAlertAt > COOLDOWN) {
    lastAlertAt = Date.now(); alertCount++;
    document.getElementById("qs-alerts").textContent = alertCount;
    document.getElementById("alert-text").textContent =
      issues.length > 0 ? `${issues[0]} detected — try the exercise below!` : "Bad posture detected!";
    const b = document.getElementById("alert-banner");
    b.classList.add("show");
    clearTimeout(alertTimeout);
    alertTimeout = setTimeout(() => b.classList.remove("show"), 5000);
  }
}

// ── Timer ─────────────────────────────────────────────────────
setInterval(() => {
  const e = Math.floor((Date.now()-sessionStart)/1000);
  document.getElementById("qs-time").textContent = `${Math.floor(e/60)}:${String(e%60).padStart(2,"0")}`;
}, 1000);

// ── MediaPipe Pose ────────────────────────────────────────────
const videoEl  = document.getElementById("input-video");
const canvasEl = document.getElementById("output-canvas");
const ctx      = canvasEl.getContext("2d");

const pose = new Pose({ locateFile: f => `https://cdn.jsdelivr.net/npm/@mediapipe/pose/${f}` });
pose.setOptions({ modelComplexity:1, smoothLandmarks:true, minDetectionConfidence:0.5, minTrackingConfidence:0.5 });

pose.onResults(results => {
  canvasEl.width  = videoEl.videoWidth  || 640;
  canvasEl.height = videoEl.videoHeight || 480;
  ctx.save();
  ctx.clearRect(0, 0, canvasEl.width, canvasEl.height);
  ctx.drawImage(results.image, 0, 0, canvasEl.width, canvasEl.height);

  if (results.poseLandmarks) {
    const analysis = analyzePosture(results.poseLandmarks);
    const good = analysis.status === "Good";

    // Skeleton
    drawConnectors(ctx, results.poseLandmarks, POSE_CONNECTIONS, {
      color: good ? "rgba(0,245,147,0.7)" : "rgba(255,77,109,0.7)", lineWidth: 3
    });
    drawLandmarks(ctx, results.poseLandmarks, {
      color: "rgba(255,255,255,0.9)", lineWidth: 1, radius: 3
    });

    // Top bar overlay
    ctx.fillStyle = "rgba(6,9,26,0.62)";
    ctx.fillRect(0, 0, canvasEl.width, 80);

    // Brand
    ctx.font = "bold 18px Inter, sans-serif";
    ctx.fillStyle = "#00d4ff";
    ctx.fillText("Posture+", 18, 46);

    // Status center
    const sColor = good ? "#00f593" : analysis.status==="Fair" ? "#ffc940" : "#ff4d6d";
    ctx.font = "bold 22px Inter, sans-serif";
    ctx.fillStyle = sColor;
    ctx.textAlign = "center";
    ctx.fillText(analysis.status.toUpperCase(), canvasEl.width/2, 46);
    ctx.textAlign = "right";

    // Score right
    ctx.font = "600 16px Inter, sans-serif";
    ctx.fillStyle = "rgba(255,255,255,0.85)";
    ctx.fillText(`${analysis.score}/100`, canvasEl.width - 18, 46);
    ctx.textAlign = "left";

    // Issue labels
    analysis.issues.forEach((iss, i) => {
      ctx.font = "500 14px Inter, sans-serif";
      ctx.fillStyle = "rgba(255,77,109,0.9)";
      ctx.fillText(`⚠ ${iss}`, 18, 110 + i*30);
    });

    updateUI(analysis, true);

  } else {
    ctx.fillStyle = "rgba(6,9,26,0.55)";
    ctx.fillRect(0, 0, canvasEl.width, 68);
    ctx.font = "500 15px Inter, sans-serif";
    ctx.fillStyle = "rgba(90,106,154,0.9)";
    ctx.fillText("Posture+  ·  Position yourself in the camera frame", 18, 40);
    updateUI({ issues:[], score:0, status:"None", alert:false }, false);
  }

  ctx.restore();
});

const camera = new Camera(videoEl, {
  onFrame: async () => { await pose.send({ image: videoEl }); },
  width: 1280, height: 720
});
camera.start();
</script>
</body>
</html>
