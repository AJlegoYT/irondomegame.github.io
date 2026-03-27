<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Iron Dome</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@700&display=swap');

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: #0a0f1a;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    font-family: 'Share Tech Mono', monospace;
    color: #7effd4;
    overflow: hidden;
  }

  h1 {
    font-family: 'Orbitron', sans-serif;
    font-size: 1.6rem;
    letter-spacing: 0.15em;
    color: #7effd4;
    text-shadow: 0 0 12px #7effd490;
    margin-bottom: 10px;
  }

  #hud {
    display: flex;
    gap: 40px;
    margin-bottom: 8px;
    font-size: 1rem;
    letter-spacing: 0.08em;
  }

  #hud span { color: #fff; }
  #hud label { color: #7effd4; }

  #gameCanvas {
    border: 2px solid #7effd440;
    border-radius: 4px;
    box-shadow: 0 0 30px #7effd420, inset 0 0 60px #00000080;
    background: #060d18;
    display: block;
  }

  #instructions {
    margin-top: 8px;
    font-size: 0.75rem;
    color: #7effd488;
    letter-spacing: 0.07em;
  }

  #overlay {
    position: absolute;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 14px;
    background: #060d18ee;
    border: 2px solid #7effd440;
    border-radius: 8px;
    padding: 36px 50px;
    text-align: center;
  }

  #overlay h2 {
    font-family: 'Orbitron', sans-serif;
    font-size: 1.4rem;
    color: #ff6b6b;
    text-shadow: 0 0 10px #ff6b6b80;
    letter-spacing: 0.12em;
  }

  #overlay p { font-size: 0.95rem; color: #ccc; line-height: 1.6; }

  #overlay button {
    margin-top: 6px;
    padding: 10px 32px;
    background: transparent;
    border: 2px solid #7effd4;
    color: #7effd4;
    font-family: 'Orbitron', sans-serif;
    font-size: 0.85rem;
    letter-spacing: 0.1em;
    cursor: pointer;
    border-radius: 4px;
    transition: background 0.2s, color 0.2s;
  }

  #overlay button:hover { background: #7effd4; color: #060d18; }
  #overlay button.danger { border-color: #ff6b6b; color: #ff6b6b; }
  #overlay button.danger:hover { background: #ff6b6b; color: #060d18; }
  .btn-row { display: flex; gap: 12px; }
  #scoreboard { width: 100%; margin-top: 4px; }
  #scoreboard h3 {
    font-family: 'Orbitron', sans-serif; font-size: 0.78rem;
    letter-spacing: 0.12em; color: #7effd4; margin-bottom: 8px; text-align: center;
  }
  #scoreboard ol { list-style: none; padding: 0; width: 100%; }
  #scoreboard ol li {
    display: flex; justify-content: space-between;
    font-size: 0.82rem; color: #ccc; padding: 3px 6px;
    border-bottom: 1px solid #7effd418;
  }
  #scoreboard ol li:first-child { color: #ffe66d; }
  #scoreboard .rank { color: #7effd460; margin-right: 8px; min-width: 18px; }
  #scoreboard .no-scores { font-size: 0.8rem; color: #7effd460; text-align: center; }


  /* ── LOGIN SCREEN ── */
  #loginScreen {
    position: fixed; inset: 0;
    background: #060d18;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    gap: 20px; z-index: 999;
  }
  #loginScreen h1 {
    font-family: 'Orbitron', sans-serif;
    font-size: 2rem; letter-spacing: 0.2em;
    color: #7effd4; text-shadow: 0 0 20px #7effd470;
    margin-bottom: 4px;
  }
  #loginScreen p.subtitle {
    font-size: 0.8rem; color: #7effd480;
    letter-spacing: 0.1em; margin-top: -12px;
  }
  #loginBox {
    display: flex; flex-direction: column; gap: 12px;
    background: #0d1c2e; border: 2px solid #7effd430;
    border-radius: 8px; padding: 30px 40px;
    min-width: 300px;
  }
  #loginBox input {
    background: #060d18; border: 1.5px solid #7effd450;
    color: #fff; font-family: 'Share Tech Mono', monospace;
    font-size: 0.95rem; padding: 9px 14px;
    border-radius: 4px; outline: none;
    transition: border-color 0.2s;
  }
  #loginBox input:focus { border-color: #7effd4; }
  #loginBox input::placeholder { color: #7effd440; }
  #loginBox button {
    margin-top: 4px; padding: 11px;
    background: transparent; border: 2px solid #7effd4;
    color: #7effd4; font-family: 'Orbitron', sans-serif;
    font-size: 0.85rem; letter-spacing: 0.1em;
    cursor: pointer; border-radius: 4px;
    transition: background 0.2s, color 0.2s;
  }
  #loginBox button:hover { background: #7effd4; color: #060d18; }
  #loginBox button:disabled { opacity: 0.5; cursor: not-allowed; }
  #loginError {
    color: #ff6b6b; font-size: 0.8rem;
    text-align: center; min-height: 18px;
    letter-spacing: 0.05em;
  }
  #loggedInBar {
    font-size: 0.78rem; color: #7effd470;
    letter-spacing: 0.08em; margin-bottom: -4px;
  }
  #loggedInBar span { color: #7effd4; }
</style>
</head>
<body>

<div id="loginScreen">
  <h1>🛡️ IRON DOME</h1>
  <p class="subtitle">DEFEND THE CITY</p>
  <div id="loginBox">
    <input id="loginName" type="text" placeholder="Name" autocomplete="off" />
    <input id="loginPass" type="password" placeholder="Password" autocomplete="off" />
    <div id="loginError"></div>
    <button id="loginBtn" onclick="doLogin()">ENTER</button>
  </div>
</div>

<div id="loggedInBar">Logged in as: <span id="playerNameDisplay"></span></div>
<h1>🛡️ IRON DOME</h1>

<div id="hud">
  <div><label>SCORE </label><span id="scoreDisplay">0</span></div>
  <div><label>LIVES </label><span id="livesDisplay">3</span></div>
  <div><label>📦 TO CITY </label><span id="caughtDisplay">0</span></div>
</div>

<div style="position:relative;display:inline-block;">
  <canvas id="gameCanvas" width="520" height="500"></canvas>
  <div id="supplyCounter" style="
    position:absolute; top:10px; right:12px;
    font-family:'Orbitron',sans-serif; font-size:0.75rem;
    letter-spacing:0.1em; color:#7effd4;
    text-shadow:0 0 8px #7effd490;
    pointer-events:none;
  ">📦 <span id="supplyCounterVal">0</span></div>
</div>

<div id="instructions">← → MOVE &nbsp;|&nbsp; SPACE SHOOT &nbsp;|&nbsp; Let 📦 reach the city · Shoot 💣 bombs</div>

<div id="overlay">
  <h2 id="overlayTitle">IRON DOME</h2>
  <p id="overlayMsg">Stop falling bombs and make sure that<br>Supply crates reach the city.</p>
  <div id="scoreboard"></div>
  <div class="btn-row">
    <button id="homeBtn" style="display:none" onclick="showHome()">HOME</button>
    <button id="startBtn" onclick="startGame()">START MISSION</button>
  </div>
</div>

<script>

// ─── SOUND ENGINE (Web Audio API) ─────────────────────────────
// All sounds are synthesised — no files needed.
const AudioCtx = window.AudioContext || window.webkitAudioContext;
let audioCtx = null;

// Lazily create the AudioContext on first user interaction
function getAudio() {
  if (!audioCtx) audioCtx = new AudioCtx();
  return audioCtx;
}

// Generic helper: plays an oscillator with an envelope
function playTone({ freq = 440, type = 'sine', vol = 0.4,
                    attack = 0.01, decay = 0.1, sustain = 0, release = 0.1,
                    duration = 0.2, freqEnd = null, detune = 0 } = {}) {
  try {
    const ac = getAudio();
    const osc = ac.createOscillator();
    const gain = ac.createGain();
    osc.connect(gain); gain.connect(ac.destination);
    osc.type = type;
    osc.frequency.setValueAtTime(freq, ac.currentTime);
    if (freqEnd !== null)
      osc.frequency.exponentialRampToValueAtTime(freqEnd, ac.currentTime + duration);
    osc.detune.setValueAtTime(detune, ac.currentTime);
    gain.gain.setValueAtTime(0, ac.currentTime);
    gain.gain.linearRampToValueAtTime(vol, ac.currentTime + attack);
    gain.gain.linearRampToValueAtTime(vol * sustain, ac.currentTime + attack + decay);
    gain.gain.linearRampToValueAtTime(0, ac.currentTime + duration + release);
    osc.start(ac.currentTime);
    osc.stop(ac.currentTime + duration + release + 0.05);
  } catch(e) {}
}

// White-noise burst for explosions and impacts
function playNoise({ vol = 0.3, duration = 0.25, filterFreq = 800 } = {}) {
  try {
    const ac = getAudio();
    const bufSize = ac.sampleRate * duration;
    const buf = ac.createBuffer(1, bufSize, ac.sampleRate);
    const data = buf.getChannelData(0);
    for (let i = 0; i < bufSize; i++) data[i] = Math.random() * 2 - 1;
    const src = ac.createBufferSource();
    src.buffer = buf;
    const filter = ac.createBiquadFilter();
    filter.type = 'bandpass';
    filter.frequency.value = filterFreq;
    filter.Q.value = 0.8;
    const gain = ac.createGain();
    gain.gain.setValueAtTime(vol, ac.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.001, ac.currentTime + duration);
    src.connect(filter); filter.connect(gain); gain.connect(ac.destination);
    src.start(); src.stop(ac.currentTime + duration + 0.05);
  } catch(e) {}
}

// ── Individual sound effects ───────────────────────────────────

// Pew! Laser shot
function sfxShoot() {
  playTone({ freq: 880, freqEnd: 220, type: 'square', vol: 0.18,
             attack: 0.001, decay: 0.08, sustain: 0, release: 0.05, duration: 0.1 });
}

// Satisfying deep boom when bomb is intercepted mid-air
function sfxBombShot() {
  playNoise({ vol: 0.5, duration: 0.18, filterFreq: 300 });
  playTone({ freq: 120, freqEnd: 40, type: 'sine', vol: 0.5,
             attack: 0.005, decay: 0.15, sustain: 0, release: 0.1, duration: 0.2 });
}

// Crunchy explosion when bomb hits player or city
function sfxExplosion() {
  playNoise({ vol: 0.7, duration: 0.4, filterFreq: 200 });
  playTone({ freq: 80, freqEnd: 25, type: 'sine', vol: 0.6,
             attack: 0.001, decay: 0.3, sustain: 0, release: 0.2, duration: 0.4 });
  // Extra rumble layer
  playTone({ freq: 55, freqEnd: 20, type: 'sawtooth', vol: 0.3,
             attack: 0.01, decay: 0.25, sustain: 0, release: 0.15, duration: 0.35 });
}

// Bright chime when supply reaches the city
function sfxSupplyLanded() {
  playTone({ freq: 660, freqEnd: 880, type: 'sine', vol: 0.35,
             attack: 0.005, decay: 0.08, sustain: 0.2, release: 0.15, duration: 0.25 });
  playTone({ freq: 990, type: 'sine', vol: 0.2,
             attack: 0.005, decay: 0.1, sustain: 0, release: 0.1, duration: 0.2 });
}

// Painful buzzing thud when supply hits you
function sfxHitBySupply() {
  playNoise({ vol: 0.4, duration: 0.15, filterFreq: 1200 });
  playTone({ freq: 180, freqEnd: 80, type: 'sawtooth', vol: 0.4,
             attack: 0.001, decay: 0.12, sustain: 0, release: 0.08, duration: 0.18 });
}

// Satisfying thump when bomb touches player (intercept)
function sfxBombIntercept() {
  playNoise({ vol: 0.45, duration: 0.2, filterFreq: 400 });
  playTone({ freq: 200, freqEnd: 80, type: 'sine', vol: 0.45,
             attack: 0.002, decay: 0.18, sustain: 0, release: 0.1, duration: 0.25 });
  // Metallic ring
  playTone({ freq: 1200, freqEnd: 600, type: 'sine', vol: 0.15,
             attack: 0.001, decay: 0.12, sustain: 0, release: 0.1, duration: 0.2 });
}

// Building collapse rumble
function sfxBuildingCollapse() {
  playNoise({ vol: 0.55, duration: 0.5, filterFreq: 180 });
  playTone({ freq: 60, freqEnd: 20, type: 'sine', vol: 0.5,
             attack: 0.01, decay: 0.4, sustain: 0, release: 0.2, duration: 0.55 });
}

// Sad descending sound on game over
function sfxGameOver() {
  [440, 330, 220, 165].forEach((f, i) => {
    setTimeout(() => playTone({ freq: f, type: 'sine', vol: 0.35,
      attack: 0.01, decay: 0.2, sustain: 0.1, release: 0.2, duration: 0.35 }), i * 180);
  });
}

// Energetic startup jingle
function sfxStart() {
  [330, 440, 550, 660].forEach((f, i) => {
    setTimeout(() => playTone({ freq: f, type: 'sine', vol: 0.3,
      attack: 0.01, decay: 0.08, sustain: 0.1, release: 0.1, duration: 0.18 }), i * 80);
  });
}

// ─── CANVAS SETUP ──────────────────────────────────────────────
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const W = canvas.width, H = canvas.height;

// ─── GAME STATE ────────────────────────────────────────────────
let score, lives, caught, gameRunning, animId;
let player, items, bullets, particles;
let spawnTimer, spawnInterval, speed, gameFrame;
let keys = {};

// ─── CONSTANTS ─────────────────────────────────────────────────
const PLAYER_W = 52, PLAYER_H = 20;
const ITEM_SIZE = 26, BULLET_W = 4, BULLET_H = 12;

// City skyline fills the bottom ~90px of the canvas.
// GROUND_Y is the top edge of the city (where buildings start from).
const GROUND_Y = H - 90;

// Player is fixed at 1/3 up from the bottom of the sky area.
// "1/3 up the screen" means y = 2/3 of canvas height.
const PLAYER_Y = Math.round(H * 0.55) - PLAYER_H;

// ─── CITY SKYLINE DATA ─────────────────────────────────────────
// We build the city once at load time so it doesn't change mid-game.
// Each building has a position, size, color, and window grid.
const BUILDINGS = generateCity();

function generateCity() {
  const buildings = [];
  let x = 0;
  while (x < W) {
    const w = 28 + Math.floor(Math.random() * 38);  // 28–66px wide
    const h = 55 + Math.floor(Math.random() * 90);  // 55–145px tall
    buildings.push({
      x, w, h,
      color: pickBuildingColor(),
      winCols: Math.max(1, Math.floor(w / 14)),
      winRows: Math.max(1, Math.floor(h / 18)),
      dy: 0,      // current vertical offset (positive = fallen down)
      vy: 0,      // falling velocity
      falling: false
    });
    x += w + 2;
  }
  return buildings;
}

function pickBuildingColor() {
  const palette = ['#1a2a3a','#1e2d40','#152030','#1b2535','#243040','#0f1d2a'];
  return palette[Math.floor(Math.random() * palette.length)];
}

// Pre-decide which windows are lit so they stay consistent each frame.
const WIN_LIT = BUILDINGS.map(b =>
  Array.from({length: b.winRows * b.winCols}, () => Math.random() < 0.45)
);

// ─── INPUT HANDLING ────────────────────────────────────────────
document.addEventListener('keydown', e => {
  keys[e.code] = true;
  if (['ArrowLeft','ArrowRight','Space'].includes(e.code)) e.preventDefault();
  if (e.code === 'Space' && gameRunning) spawnBullet();
});
document.addEventListener('keyup', e => keys[e.code] = false);

// ─── START / RESTART ───────────────────────────────────────────
function startGame() {
  sfxStart();
  document.getElementById('overlay').style.display = 'none';
  document.getElementById('homeBtn').style.display = 'none';
  document.getElementById('scoreboard').style.display = '';

  score = 0; lives = 3; caught = 0;
  spawnTimer = 0; spawnInterval = 90; speed = 1.8; gameFrame = 0;

  // Player x is movable; y is always PLAYER_Y (fixed row in the sky).
  player = { x: W / 2 - PLAYER_W / 2, y: PLAYER_Y, speed: 4 };
  items = [];
  bullets = [];
  particles = [];
  BUILDINGS.forEach(b => { b.dy = 0; b.vy = 0; b.falling = false; });

  updateHUD();
  gameRunning = true;
  if (animId) cancelAnimationFrame(animId);
  loop();
}

// ─── MAIN GAME LOOP ────────────────────────────────────────────
function loop() {
  if (!gameRunning) return;
  update();
  draw();
  animId = requestAnimationFrame(loop);
}

// ─── UPDATE (logic) ────────────────────────────────────────────
function update() {
  // 1. Move player left/right — y stays fixed at PLAYER_Y
  if (keys['ArrowLeft'])  player.x = Math.max(0, player.x - player.speed);
  if (keys['ArrowRight']) player.x = Math.min(W - PLAYER_W, player.x + player.speed);

  // 2. Move bullets upward; remove when off-screen
  bullets.forEach(b => b.y -= 9);
  bullets = bullets.filter(b => b.y > -BULLET_H);

  // 3. Spawn items on a timer; difficulty increases both per-spawn and over time
  gameFrame++;
  // Every 5 seconds (300 frames) bump the base fall speed from the passage of time alone
  if (gameFrame % 300 === 0) {
    speed = Math.min(6, speed + 0.2);
    spawnInterval = Math.max(28, spawnInterval - 3);
  }
  spawnTimer++;
  if (spawnTimer >= spawnInterval) {
    spawnTimer = 0;
    spawnInterval = Math.max(28, spawnInterval - 1);
    speed = Math.min(6, speed + 0.04);
    spawnItem();
  }

  // 4. Move all items downward
  items.forEach(item => item.y += item.speed);

  // 5. Bullet ↔ item collisions
  bullets.forEach(b => {
    items.forEach(item => {
      if (item.dead || b.dead) return;
      if (rectsOverlap(b.x, b.y, BULLET_W, BULLET_H,
                       item.x, item.y, ITEM_SIZE, ITEM_SIZE)) {
        if (item.type === 'bomb') {
          // Great — destroyed a bomb
          score += 15;
          sfxBombShot();
          spawnParticles(item.x + ITEM_SIZE/2, item.y + ITEM_SIZE/2, '#ff9f43', 10);
        } else {
          // Shot a supply crate — lose a life!
          sfxHitBySupply();
          loseLife(item.x + ITEM_SIZE/2, item.y + ITEM_SIZE/2);
          spawnParticles(item.x + ITEM_SIZE/2, item.y + ITEM_SIZE/2, '#ff6b6b', 8);
        }
        item.dead = true;
        b.dead = true;
        updateHUD();
      }
    });
  });

  // 6. Item ↔ player collision
  //    - Supply hits player  → catch it! +10pts, no life lost
  //    - Bomb   hits player  → lose a life
  items.forEach(item => {
    if (item.dead) return;
    if (rectsOverlap(item.x, item.y, ITEM_SIZE, ITEM_SIZE,
                     player.x, player.y, PLAYER_W, PLAYER_H)) {
      if (item.type === 'supply') {
        // Supply hit the player — lose a life!
        sfxHitBySupply();
        loseLife(item.x + ITEM_SIZE/2, item.y + ITEM_SIZE/2);
        item.dead = true;
      } else {
        // Bomb touched the player — destroy it for points (same as shooting)
        sfxBombIntercept();
        score += 15;
        spawnParticles(item.x + ITEM_SIZE/2, item.y + ITEM_SIZE/2, '#ff9f43', 10);
        item.dead = true;
        updateHUD();
      }
    }
  });

  // 7. Items that pass the player and reach the city/ground
  //    - Supply reaches city → just missed, no penalty (just a puff of smoke)
  //    - Bomb   reaches city → lose a life (it hit the city!)
  items.forEach(item => {
    if (item.dead) return;
    if (item.y + ITEM_SIZE >= GROUND_Y) {
      if (item.type === 'bomb') {
        sfxExplosion();
        loseLife(item.x + ITEM_SIZE/2, GROUND_Y);
        spawnParticles(item.x + ITEM_SIZE/2, GROUND_Y, '#ff4444', 18);
        collapseBuildings(item.x + ITEM_SIZE/2);
      } else {
        // Supply reached the city — reward points, increment counter, boost speed
        sfxSupplyLanded();
        score += 5;
        caught++;
        player.speed = Math.min(8, player.speed + 0.15);
        spawnParticles(item.x + ITEM_SIZE/2, GROUND_Y, '#7effd4', 7);
        updateHUD();
      }
      item.dead = true;
    }
  });

  // 7b. Animate building collapses
  updateBuildings();

  // 8. Remove dead objects
  items   = items.filter(i => !i.dead);
  bullets = bullets.filter(b => !b.dead);

  // 9. Animate particles (mini physics: velocity + gravity)
  particles.forEach(p => {
    p.x += p.vx; p.y += p.vy;
    p.vy += 0.15;
    p.life--;
  });
  particles = particles.filter(p => p.life > 0);
}

// ─── DRAW (rendering) ──────────────────────────────────────────
function draw() {
  ctx.clearRect(0, 0, W, H);

  drawSky();
  drawStars();
  drawCity();
  drawGround();

  // Particles (drawn before player so player appears on top)
  particles.forEach(p => {
    ctx.globalAlpha = p.life / p.maxLife;
    ctx.fillStyle = p.color;
    ctx.fillRect(p.x, p.y, p.size, p.size);
  });
  ctx.globalAlpha = 1;

  drawPlayer();

  items.forEach(item => {
    if (item.type === 'supply') drawCrate(item.x, item.y);
    else                        drawBomb(item.x, item.y);
  });

  // Bullets
  bullets.forEach(b => {
    ctx.fillStyle = '#ffe66d';
    ctx.shadowColor = '#ffe66d'; ctx.shadowBlur = 8;
    ctx.fillRect(b.x, b.y, BULLET_W, BULLET_H);
  });
  ctx.shadowBlur = 0;
}

// ─── DRAW HELPERS ──────────────────────────────────────────────

function drawSky() {
  // Deep night sky gradient
  const grad = ctx.createLinearGradient(0, 0, 0, GROUND_Y);
  grad.addColorStop(0,   '#060d18');
  grad.addColorStop(0.7, '#0a1525');
  grad.addColorStop(1,   '#112035');
  ctx.fillStyle = grad;
  ctx.fillRect(0, 0, W, GROUND_Y);
}

// Stars are placed only in the upper sky, created once
const stars = Array.from({length: 70}, () => ({
  x: Math.random() * W,
  y: Math.random() * (H * 0.55),
  r: Math.random() * 1.2 + 0.3,
  a: Math.random()
}));

function drawStars() {
  stars.forEach(s => {
    ctx.globalAlpha = s.a * 0.7;
    ctx.fillStyle = '#fff';
    ctx.beginPath();
    ctx.arc(s.x, s.y, s.r, 0, Math.PI * 2);
    ctx.fill();
  });
  ctx.globalAlpha = 1;
}

function drawCity() {
  BUILDINGS.forEach((b, bi) => {
    // dy shifts the building downward — 0 = normal, b.h = fully sunk
    const top = GROUND_Y - b.h + b.dy;

    // Clip drawing so sunken buildings don't bleed below the ground strip
    ctx.save();
    ctx.beginPath();
    ctx.rect(b.x, 0, b.w, GROUND_Y);
    ctx.clip();

    // Building body
    ctx.fillStyle = b.color;
    ctx.fillRect(b.x, top, b.w, b.h);

    // Thin border
    ctx.strokeStyle = '#2a3a4a';
    ctx.lineWidth = 1;
    ctx.strokeRect(b.x, top, b.w, b.h);

    // Windows
    const winW = 6, winH = 7, rowGap = 5;
    const colGap = Math.floor((b.w - b.winCols * winW) / (b.winCols + 1));

    for (let r = 0; r < b.winRows; r++) {
      for (let c = 0; c < b.winCols; c++) {
        const wx = b.x + colGap + c * (winW + colGap);
        const wy = top + 8 + r * (winH + rowGap);
        if (wy + winH > GROUND_Y) continue;

        const idx = r * b.winCols + c;
        if (WIN_LIT[bi][idx]) {
          ctx.fillStyle = '#ffe88a';
          ctx.shadowColor = '#ffe88a'; ctx.shadowBlur = 4;
          ctx.fillRect(wx, wy, winW, winH);
          ctx.shadowBlur = 0;
        } else {
          ctx.fillStyle = '#0a1520';
          ctx.fillRect(wx, wy, winW, winH);
        }
      }
    }
    ctx.restore();
  });
}

function drawGround() {
  // Ground strip below city
  const grad = ctx.createLinearGradient(0, GROUND_Y, 0, H);
  grad.addColorStop(0, '#1a3a2a');
  grad.addColorStop(1, '#0a1a12');
  ctx.fillStyle = grad;
  ctx.fillRect(0, GROUND_Y, W, H - GROUND_Y);

  // Glowing horizon line
  ctx.shadowColor = '#7effd4'; ctx.shadowBlur = 10;
  ctx.strokeStyle = '#7effd460';
  ctx.lineWidth = 2;
  ctx.beginPath(); ctx.moveTo(0, GROUND_Y); ctx.lineTo(W, GROUND_Y); ctx.stroke();
  ctx.shadowBlur = 0;
}

function drawPlayer() {
  const px = player.x, py = player.y;

  // Hover glow beneath tank
  const hg = ctx.createRadialGradient(
    px + PLAYER_W/2, py + PLAYER_H + 4, 2,
    px + PLAYER_W/2, py + PLAYER_H + 4, 22
  );
  hg.addColorStop(0, '#7effd430');
  hg.addColorStop(1, 'transparent');
  ctx.fillStyle = hg;
  ctx.fillRect(px - 10, py + 10, PLAYER_W + 20, 20);

  // Tank body
  ctx.fillStyle = '#2ecc71';
  ctx.fillRect(px + 4, py + 8, PLAYER_W - 8, PLAYER_H - 8);

  // Turret dome
  ctx.fillStyle = '#27ae60';
  ctx.beginPath();
  ctx.arc(px + PLAYER_W/2, py + 8, 10, Math.PI, 0);
  ctx.fill();

  // Gun barrel (points upward)
  ctx.fillStyle = '#7effd4';
  ctx.shadowColor = '#7effd4'; ctx.shadowBlur = 6;
  ctx.fillRect(px + PLAYER_W/2 - 2, py - 10, 4, 14);
  ctx.shadowBlur = 0;

  // Outline
  ctx.strokeStyle = '#7effd450'; ctx.lineWidth = 1;
  ctx.strokeRect(px + 4, py + 8, PLAYER_W - 8, PLAYER_H - 8);
}

function drawCrate(x, y) {
  ctx.fillStyle = '#c0722a';
  ctx.fillRect(x, y, ITEM_SIZE, ITEM_SIZE);
  ctx.strokeStyle = '#e8952a'; ctx.lineWidth = 1.5;
  ctx.strokeRect(x + 2, y + 2, ITEM_SIZE - 4, ITEM_SIZE - 4);
  ctx.beginPath();
  ctx.moveTo(x, y + ITEM_SIZE/2); ctx.lineTo(x + ITEM_SIZE, y + ITEM_SIZE/2);
  ctx.moveTo(x + ITEM_SIZE/2, y); ctx.lineTo(x + ITEM_SIZE/2, y + ITEM_SIZE);
  ctx.strokeStyle = '#e8952a60'; ctx.lineWidth = 1; ctx.stroke();
  ctx.font = '14px sans-serif';
  ctx.fillText('📦', x + 4, y + 19);
}

function drawBomb(x, y) {
  ctx.fillStyle = '#922b21';
  ctx.shadowColor = '#ff4444'; ctx.shadowBlur = 10;
  ctx.beginPath();
  ctx.arc(x + ITEM_SIZE/2, y + ITEM_SIZE/2 + 2, ITEM_SIZE/2 - 2, 0, Math.PI*2);
  ctx.fill();
  ctx.shadowBlur = 0;
  ctx.fillStyle = '#ffe66d';
  ctx.shadowColor = '#ffe66d'; ctx.shadowBlur = 6;
  ctx.beginPath();
  ctx.arc(x + ITEM_SIZE/2, y + 2, 3, 0, Math.PI*2);
  ctx.fill();
  ctx.shadowBlur = 0;
  ctx.font = '14px sans-serif';
  ctx.fillText('💣', x + 4, y + 21);
}

// ─── BUILDING COLLAPSE ─────────────────────────────────────────
// Called when a bomb hits the ground at x-position bx.
// Collapses 2–3 nearest buildings by giving them a downward velocity.
function collapseBuildings(bx) {
  // Sort buildings by distance from blast, pick closest 2-3
  const sorted = BUILDINGS
    .map((b, i) => ({ b, i, dist: Math.abs((b.x + b.w / 2) - bx) }))
    .filter(entry => !entry.b.falling && entry.b.dy < entry.b.h) // not already fully fallen
    .sort((a, z) => a.dist - z.dist);

  const count = 2 + Math.floor(Math.random() * 2); // 2 or 3 buildings
  sorted.slice(0, count).forEach(entry => {
    entry.b.falling = true;
    entry.b.vy = 1.5 + Math.random() * 2; // initial fall speed
    sfxBuildingCollapse();
    // Spawn rubble particles at the base of the building
    spawnParticles(entry.b.x + entry.b.w / 2, GROUND_Y, '#8a6a3a', 12);
  });
}

// Called every frame to animate falling buildings
function updateBuildings() {
  BUILDINGS.forEach(b => {
    if (!b.falling) return;
    b.dy += b.vy;
    b.vy += 0.4; // gravity acceleration
    // Cap at building height (fully underground)
    if (b.dy >= b.h) {
      b.dy = b.h;
      b.vy = 0;
      b.falling = false;
    }
  });
}

// ─── SPAWN HELPERS ─────────────────────────────────────────────
function spawnItem() {
  const type = Math.random() < 0.35 ? 'bomb' : 'supply';
  items.push({
    x: Math.random() * (W - ITEM_SIZE),
    y: -ITEM_SIZE,
    speed: speed * (0.85 + Math.random() * 0.3),
    type,
    dead: false
  });
}

function spawnBullet() {
  sfxShoot();
  bullets.push({
    x: player.x + PLAYER_W/2 - BULLET_W/2,
    y: player.y - BULLET_H,
    dead: false
  });
}

function spawnParticles(x, y, color, count) {
  for (let i = 0; i < count; i++) {
    particles.push({
      x, y,
      vx: (Math.random() - 0.5) * 5,
      vy: (Math.random() - 0.5) * 4 - 1,
      color,
      size: Math.random() * 4 + 2,
      life: 28, maxLife: 28
    });
  }
}

// ─── COLLISION HELPER ──────────────────────────────────────────
function rectsOverlap(ax, ay, aw, ah, bx, by, bw, bh) {
  return ax < bx + bw && ax + aw > bx && ay < by + bh && ay + ah > by;
}

// ─── LIFE / GAME OVER ──────────────────────────────────────────
function loseLife(px, py) {
  lives--;
  spawnParticles(px, py, '#ff6b6b', 16);
  updateHUD();
  if (lives <= 0) endGame();
}

// ─── SCOREBOARD (localStorage) ────────────────────────────────
function getScores() {
  try { return JSON.parse(localStorage.getItem('irondome_scores') || '[]'); }
  catch(e) { return []; }
}
function saveScore(s) {
  const scores = getScores();
  scores.push(s);
  scores.sort((a, b) => b - a);
  localStorage.setItem('irondome_scores', JSON.stringify(scores.slice(0, 10)));
}
function renderScoreboard() {
  const scores = getScores();
  const el = document.getElementById('scoreboard');
  if (!scores.length) {
    el.innerHTML = '<h3>🏆 HIGH SCORES</h3><p class="no-scores">No scores yet!</p>';
    return;
  }
  const rows = scores.map((s, i) =>
    `<li><span><span class="rank">#${i+1}</span></span><span>${s}</span></li>`
  ).join('');
  el.innerHTML = `<h3>🏆 HIGH SCORES</h3><ol>${rows}</ol>`;
}

function showHome() {
  document.getElementById('overlayTitle').textContent = 'IRON DOME';
  document.getElementById('overlayTitle').style.color = '#7effd4';
  document.getElementById('overlayMsg').innerHTML =
    'Stop falling bombs and make sure that<br>Supply crates reach the city.';
  document.getElementById('startBtn').textContent = 'START MISSION';
  document.getElementById('startBtn').classList.remove('danger');
  document.getElementById('homeBtn').style.display = 'none';
  document.getElementById('scoreboard').style.display = '';
  renderScoreboard();
  document.getElementById('overlay').style.display = 'flex';
}

function endGame() {
  gameRunning = false;
  sfxGameOver();
  saveScore(score);
  document.getElementById('overlayTitle').textContent = 'MISSION FAILED';
  document.getElementById('overlayTitle').style.color = '#ff6b6b';
  document.getElementById('overlayMsg').innerHTML =
    `Final Score: <strong style="color:#7effd4">${score}</strong><br>` +
    `Supplies to city: <strong style="color:#7effd4">${caught}</strong>`;
  document.getElementById('startBtn').textContent = 'TRY AGAIN';
  document.getElementById('startBtn').classList.add('danger');
  document.getElementById('homeBtn').style.display = 'inline-block';
  document.getElementById('scoreboard').style.display = 'none';
  document.getElementById('overlay').style.display = 'flex';
}

function updateHUD() {
  document.getElementById('scoreDisplay').textContent = score;
  document.getElementById('livesDisplay').textContent = '❤️'.repeat(Math.max(0, lives));
  document.getElementById('caughtDisplay').textContent = caught;
  document.getElementById('supplyCounterVal').textContent = caught;
}

// ─── LOGIN ─────────────────────────────────────────────────────
const SHEET_ID = '1Ax05m4QXNbayllMVIurH-guI-deEDlaHcH6SEsDZKRk';
const SHEET_URL = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?tqx=out:json`;

let currentPlayer = null;

async function doLogin() {
  const nameEl = document.getElementById('loginName');
  const passEl = document.getElementById('loginPass');
  const errEl  = document.getElementById('loginError');
  const btn    = document.getElementById('loginBtn');
  const name   = nameEl.value.trim();
  const pass   = passEl.value.trim();

  if (!name || !pass) { errEl.textContent = 'Please enter name and password.'; return; }

  btn.disabled = true;
  btn.textContent = 'CHECKING...';
  errEl.textContent = '';

  try {
    const res = await fetch(SHEET_URL);
    const text = await res.text();
    // Google wraps the JSON in /*O_o*/google.visualization.Query.setResponse({...});
    const json = JSON.parse(text.match(/google\.visualization\.Query\.setResponse\(([\s\S]*)\)/)[1]);
    const rows = json.table.rows;

    // Column A = Name, Column B = Password
    const match = rows.find(row => {
      const rowName = (row.c[0]?.v || '').toString().trim().toLowerCase();
      const rowPass = (row.c[1]?.v || '').toString().trim();
      return rowName === name.toLowerCase() && rowPass === pass;
    });

    if (match) {
      currentPlayer = name;
      document.getElementById('loginScreen').style.display = 'none';
      document.getElementById('playerNameDisplay').textContent = name;
      renderScoreboard();
    } else {
      errEl.textContent = 'Incorrect name or password.';
      passEl.value = '';
    }
  } catch(e) {
    errEl.textContent = 'Could not reach login server. Is the sheet public?';
    console.error(e);
  }

  btn.disabled = false;
  btn.textContent = 'ENTER';
}

// Allow pressing Enter to submit
document.addEventListener('DOMContentLoaded', () => {
  ['loginName','loginPass'].forEach(id => {
    document.getElementById(id).addEventListener('keydown', e => {
      if (e.key === 'Enter') doLogin();
    });
  });
});

// Show scoreboard on first load (after login)
renderScoreboard();
</script>
</body>
</html>
