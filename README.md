# Runner-Mania
Hard work
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Subway Runner</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body { background: #0a0a1a; overflow: hidden; font-family: 'Segoe UI', sans-serif; }
  canvas { display: block; }

  .screen {
    position: fixed; top: 0; left: 0; width: 100%; height: 100%;
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    z-index: 100; pointer-events: auto;
    background: linear-gradient(135deg, #0a0a1a 0%, #1a0a2e 50%, #0a1a2e 100%);
  }

  #startScreen .game-title {
    font-size: clamp(48px, 8vw, 96px);
    font-weight: 900;
    letter-spacing: 4px;
    background: linear-gradient(90deg, #ff6b35, #f7c59f, #ffe66d, #4ecdc4, #a855f7);
    background-size: 300% 300%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    animation: gradientShift 3s ease infinite;
    text-shadow: none;
    margin-bottom: 8px;
  }

  @keyframes gradientShift {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }

  .creator-text {
    font-size: clamp(14px, 2.5vw, 20px);
    color: #a855f7;
    letter-spacing: 3px;
    text-transform: uppercase;
    margin-bottom: 50px;
    font-weight: 300;
  }

  .play-btn {
    padding: 18px 60px;
    font-size: 22px;
    font-weight: 700;
    letter-spacing: 3px;
    text-transform: uppercase;
    border: none;
    border-radius: 50px;
    cursor: pointer;
    background: linear-gradient(135deg, #ff6b35, #f7c59f);
    color: #0a0a1a;
    box-shadow: 0 0 40px rgba(255, 107, 53, 0.6), 0 0 80px rgba(255, 107, 53, 0.3);
    transition: all 0.3s ease;
    animation: pulse 2s ease-in-out infinite;
  }

  .play-btn:hover {
    transform: scale(1.08);
    box-shadow: 0 0 60px rgba(255, 107, 53, 0.9), 0 0 100px rgba(255, 107, 53, 0.5);
  }

  @keyframes pulse {
    0%, 100% { box-shadow: 0 0 40px rgba(255, 107, 53, 0.6); }
    50% { box-shadow: 0 0 70px rgba(255, 107, 53, 0.9), 0 0 120px rgba(255, 107, 53, 0.4); }
  }

  .controls-hint {
    margin-top: 40px;
    color: #555;
    font-size: 13px;
    letter-spacing: 1px;
    text-align: center;
    line-height: 1.8;
  }

  .controls-hint span { color: #a855f7; }

  /* HUD */
  #hud {
    display: none;
    position: fixed;
    top: 0; left: 0; width: 100%; z-index: 50;
    pointer-events: none;
  }

  .hud-bar {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    padding: 20px 30px;
  }

  .hud-item {
    background: rgba(0,0,0,0.5);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 12px;
    padding: 10px 20px;
    color: white;
  }

  .hud-label {
    font-size: 11px;
    letter-spacing: 2px;
    color: #aaa;
    text-transform: uppercase;
    margin-bottom: 2px;
  }

  .hud-value {
    font-size: 28px;
    font-weight: 700;
    color: #fff;
  }

  #scoreVal { color: #ffe66d; }
  #coinVal { color: #f7c59f; }
  #distVal { color: #4ecdc4; }

  .powerup-indicator {
    display: flex;
    gap: 10px;
    justify-content: center;
    padding-bottom: 10px;
  }

  .powerup-badge {
    display: none;
    background: rgba(168, 85, 247, 0.8);
    border: 1px solid #a855f7;
    border-radius: 20px;
    padding: 5px 16px;
    font-size: 13px;
    font-weight: 700;
    color: white;
    letter-spacing: 1px;
    animation: badgePulse 0.5s ease infinite alternate;
  }

  @keyframes badgePulse {
    from { opacity: 0.8; }
    to { opacity: 1; }
  }

  /* Game Over */
  #gameOverScreen {
    display: none;
  }

  #gameOverScreen .title {
    font-size: clamp(36px, 6vw, 72px);
    font-weight: 900;
    color: #ff4444;
    letter-spacing: 4px;
    text-transform: uppercase;
    margin-bottom: 20px;
    text-shadow: 0 0 40px rgba(255,68,68,0.6);
  }

  .score-card {
    background: rgba(255,255,255,0.05);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 20px;
    padding: 30px 60px;
    margin-bottom: 30px;
    text-align: center;
  }

  .score-row {
    display: flex;
    gap: 50px;
    justify-content: center;
  }

  .score-stat { text-align: center; }
  .score-stat-label { font-size: 12px; color: #888; letter-spacing: 2px; text-transform: uppercase; }
  .score-stat-val { font-size: 36px; font-weight: 700; margin-top: 4px; }

  .restart-btn {
    padding: 16px 50px;
    font-size: 18px;
    font-weight: 700;
    letter-spacing: 3px;
    text-transform: uppercase;
    border: none;
    border-radius: 50px;
    cursor: pointer;
    background: linear-gradient(135deg, #a855f7, #ec4899);
    color: white;
    box-shadow: 0 0 40px rgba(168, 85, 247, 0.5);
    transition: all 0.3s ease;
    margin-bottom: 20px;
  }

  .restart-btn:hover {
    transform: scale(1.05);
    box-shadow: 0 0 60px rgba(168, 85, 247, 0.8);
  }
</style>
</head>
<body>

<!-- Start Screen -->
<div id="startScreen" class="screen">
  <div class="game-title">SUBWAY RUNNER</div>
  <div class="creator-text">Created by Ebrahim</div>
  <button class="play-btn" id="playBtn">▶ Play Game</button>
  <div class="controls-hint">
    <span>← →</span> or <span>A D</span> — Change Lane &nbsp;|&nbsp;
    <span>↑</span> or <span>Space</span> — Jump &nbsp;|&nbsp;
    <span>↓</span> or <span>S</span> — Slide
  </div>
</div>

<!-- HUD -->
<div id="hud">
  <div class="hud-bar">
    <div class="hud-item">
      <div class="hud-label">Score</div>
      <div class="hud-value" id="scoreVal">0</div>
    </div>
    <div class="hud-item">
      <div class="hud-label">Coins</div>
      <div class="hud-value" id="coinVal">🪙 0</div>
    </div>
    <div class="hud-item">
      <div class="hud-label">Distance</div>
      <div class="hud-value" id="distVal">0m</div>
    </div>
  </div>
  <div class="powerup-indicator">
    <div class="powerup-badge" id="magnetBadge">🧲 MAGNET</div>
    <div class="powerup-badge" id="jetpackBadge">🚀 JETPACK</div>
  </div>
</div>

<!-- Game Over Screen -->
<div id="gameOverScreen" class="screen">
  <div class="title">Game Over</div>
  <div class="score-card">
    <div class="score-row">
      <div class="score-stat">
        <div class="score-stat-label">Score</div>
        <div class="score-stat-val" id="finalScore" style="color:#ffe66d;">0</div>
      </div>
      <div class="score-stat">
        <div class="score-stat-label">Coins</div>
        <div class="score-stat-val" id="finalCoins" style="color:#f7c59f;">0</div>
      </div>
      <div class="score-stat">
        <div class="score-stat-label">Distance</div>
        <div class="score-stat-val" id="finalDist" style="color:#4ecdc4;">0m</div>
      </div>
    </div>
  </div>
  <button class="restart-btn" id="restartBtn">↺ Restart</button>
  <div class="creator-text" style="margin-bottom:0;">Created by Ebrahim</div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
// =====================================================
//  AUDIO SYSTEM
// =====================================================
const AudioCtx = window.AudioContext || window.webkitAudioContext;
let audioCtx = null;
let bgmOscillators = [];
let bgmGain = null;

function initAudio() {
  if (audioCtx) return;
  audioCtx = new AudioCtx();
}

function playNote(freq, dur, type='sine', vol=0.15, delay=0) {
  if (!audioCtx) return;
  const o = audioCtx.createOscillator();
  const g = audioCtx.createGain();
  o.connect(g); g.connect(audioCtx.destination);
  o.type = type;
  o.frequency.setValueAtTime(freq, audioCtx.currentTime + delay);
  g.gain.setValueAtTime(0, audioCtx.currentTime + delay);
  g.gain.linearRampToValueAtTime(vol, audioCtx.currentTime + delay + 0.01);
  g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + delay + dur);
  o.start(audioCtx.currentTime + delay);
  o.stop(audioCtx.currentTime + delay + dur + 0.1);
}

function sfxCoin() {
  playNote(1046, 0.1, 'sine', 0.2);
  playNote(1318, 0.1, 'sine', 0.15, 0.08);
  playNote(1568, 0.12, 'sine', 0.1, 0.16);
}

function sfxJump() {
  if (!audioCtx) return;
  const o = audioCtx.createOscillator();
  const g = audioCtx.createGain();
  o.connect(g); g.connect(audioCtx.destination);
  o.type = 'sine';
  o.frequency.setValueAtTime(220, audioCtx.currentTime);
  o.frequency.exponentialRampToValueAtTime(660, audioCtx.currentTime + 0.15);
  g.gain.setValueAtTime(0.2, audioCtx.currentTime);
  g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.3);
  o.start(audioCtx.currentTime);
  o.stop(audioCtx.currentTime + 0.35);
}

function sfxSlide() {
  if (!audioCtx) return;
  const o = audioCtx.createOscillator();
  const g = audioCtx.createGain();
  o.connect(g); g.connect(audioCtx.destination);
  o.type = 'sawtooth';
  o.frequency.setValueAtTime(300, audioCtx.currentTime);
  o.frequency.exponentialRampToValueAtTime(80, audioCtx.currentTime + 0.25);
  g.gain.setValueAtTime(0.15, audioCtx.currentTime);
  g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.3);
  o.start(audioCtx.currentTime);
  o.stop(audioCtx.currentTime + 0.35);
}

function sfxCollision() {
  if (!audioCtx) return;
  // Noise burst
  for (let i = 0; i < 3; i++) {
    const o = audioCtx.createOscillator();
    const g = audioCtx.createGain();
    o.connect(g); g.connect(audioCtx.destination);
    o.type = 'sawtooth';
    o.frequency.setValueAtTime(150 - i * 30, audioCtx.currentTime + i * 0.08);
    o.frequency.exponentialRampToValueAtTime(30, audioCtx.currentTime + i * 0.08 + 0.3);
    g.gain.setValueAtTime(0.3, audioCtx.currentTime + i * 0.08);
    g.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + i * 0.08 + 0.4);
    o.start(audioCtx.currentTime + i * 0.08);
    o.stop(audioCtx.currentTime + i * 0.08 + 0.5);
  }
}

function sfxPowerup() {
  const notes = [523, 659, 784, 1047];
  notes.forEach((n, i) => playNote(n, 0.15, 'sine', 0.2, i * 0.07));
}

function startBGM() {
  if (!audioCtx) return;
  stopBGM();
  bgmGain = audioCtx.createGain();
  bgmGain.gain.value = 0.06;
  bgmGain.connect(audioCtx.destination);

  // Bass line
  const bassNotes = [55, 55, 69, 55, 55, 69, 73, 69];
  const melody = [262, 294, 330, 349, 392, 349, 330, 294];
  const bpm = 128;
  const beat = 60 / bpm;
  let t = audioCtx.currentTime;
  const lookahead = 4; // seconds per loop

  function scheduleLoop() {
    if (!audioCtx || !bgmGain) return;
    const now = audioCtx.currentTime;
    if (t < now + lookahead) {
      for (let i = 0; i < 16; i++) {
        const bassO = audioCtx.createOscillator();
        const bassG = audioCtx.createGain();
        bassO.connect(bassG); bassG.connect(bgmGain);
        bassO.type = 'sawtooth';
        bassO.frequency.setValueAtTime(bassNotes[i % 8], t + i * beat);
        bassG.gain.setValueAtTime(0.4, t + i * beat);
        bassG.gain.exponentialRampToValueAtTime(0.001, t + i * beat + beat * 0.8);
        bassO.start(t + i * beat);
        bassO.stop(t + i * beat + beat);
        bgmOscillators.push(bassO);

        // Hi-hat
        const hhO = audioCtx.createOscillator();
        const hhG = audioCtx.createGain();
        hhO.connect(hhG); hhG.connect(bgmGain);
        hhO.type = 'square';
        hhO.frequency.setValueAtTime(8000, t + i * beat * 0.5);
        hhG.gain.setValueAtTime(0.05, t + i * beat * 0.5);
        hhG.gain.exponentialRampToValueAtTime(0.001, t + i * beat * 0.5 + 0.05);
        hhO.start(t + i * beat * 0.5);
        hhO.stop(t + i * beat * 0.5 + 0.06);
        bgmOscillators.push(hhO);
      }

      // Melody
      for (let i = 0; i < 8; i++) {
        const melO = audioCtx.createOscillator();
        const melG = audioCtx.createGain();
        melO.connect(melG); melG.connect(bgmGain);
        melO.type = 'triangle';
        melO.frequency.setValueAtTime(melody[i % 8], t + i * beat * 2);
        melG.gain.setValueAtTime(0.35, t + i * beat * 2);
        melG.gain.exponentialRampToValueAtTime(0.001, t + i * beat * 2 + beat * 1.5);
        melO.start(t + i * beat * 2);
        melO.stop(t + i * beat * 2 + beat * 2);
        bgmOscillators.push(melO);
      }

      t += 16 * beat;
    }
    bgmTimer = requestAnimationFrame(scheduleLoop);
  }
  bgmTimer = requestAnimationFrame(scheduleLoop);
}

let bgmTimer = null;
function stopBGM() {
  if (bgmTimer) { cancelAnimationFrame(bgmTimer); bgmTimer = null; }
  bgmOscillators.forEach(o => { try { o.stop(); } catch(e) {} });
  bgmOscillators = [];
  if (bgmGain) { bgmGain.disconnect(); bgmGain = null; }
}

// =====================================================
//  THREE.JS SETUP
// =====================================================
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.setClearColor(0x0a0a1a);
document.body.appendChild(renderer.domElement);

const scene = new THREE.Scene();
scene.fog = new THREE.FogExp2(0x0a0a1a, 0.018);

const camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.1, 200);
camera.position.set(0, 5, 10);
camera.lookAt(0, 0, -10);

// Lighting
const ambientLight = new THREE.AmbientLight(0x334466, 1.2);
scene.add(ambientLight);

const dirLight = new THREE.DirectionalLight(0xffffff, 1.5);
dirLight.position.set(5, 15, 5);
dirLight.castShadow = true;
dirLight.shadow.mapSize.width = 1024;
dirLight.shadow.mapSize.height = 1024;
dirLight.shadow.camera.near = 0.1;
dirLight.shadow.camera.far = 100;
dirLight.shadow.camera.left = -20;
dirLight.shadow.camera.right = 20;
dirLight.shadow.camera.top = 20;
dirLight.shadow.camera.bottom = -20;
scene.add(dirLight);

const rimLight = new THREE.DirectionalLight(0xa855f7, 0.8);
rimLight.position.set(-5, 5, -10);
scene.add(rimLight);

const pointLight = new THREE.PointLight(0xff6b35, 2, 30);
pointLight.position.set(0, 3, 0);
scene.add(pointLight);

// =====================================================
//  GAME CONSTANTS
// =====================================================
const LANE_WIDTH = 2.2;
const LANES = [-LANE_WIDTH, 0, LANE_WIDTH];
const TRACK_SEGMENT_LENGTH = 30;
const NUM_TRACK_SEGMENTS = 10;
const SPAWN_Z = -80;
const DESPAWN_Z = 15;

// =====================================================
//  GAME STATE
// =====================================================
let gameState = 'start'; // start, playing, gameover
let score = 0;
let coins = 0;
let distance = 0;
let gameSpeed = 0.22;
let frameCount = 0;

// Player state
let playerLane = 1; // 0=left,1=center,2=right
let playerTargetX = LANES[1];
let playerX = LANES[1];
let playerY = 0;
let playerVY = 0;
let isJumping = false;
let isSliding = false;
let slideTimer = 0;
let playerRunAnim = 0;
let playerDead = false;

// Power-up state
let magnetActive = false;
let magnetTimer = 0;
let jetpackActive = false;
let jetpackTimer = 0;

// Input
const keys = {};

// Collections
let obstacles = [];
let coinObjects = [];
let powerups = [];
let trackSegments = [];
let envObjects = [];
let particles = [];

// =====================================================
//  TRACK CREATION
// =====================================================
const trackMat = new THREE.MeshLambertMaterial({ color: 0x222244 });
const trackLineMat = new THREE.MeshLambertMaterial({ color: 0x4444aa, emissive: 0x2222aa });
const groundMat = new THREE.MeshLambertMaterial({ color: 0x111122 });
const railMat = new THREE.MeshLambertMaterial({ color: 0x556699, emissive: 0x223366 });

function createTrackSegment(z) {
  const group = new THREE.Group();

  // Ground
  const ground = new THREE.Mesh(new THREE.BoxGeometry(9, 0.2, TRACK_SEGMENT_LENGTH), groundMat);
  ground.position.set(0, -0.1, z - TRACK_SEGMENT_LENGTH / 2);
  ground.receiveShadow = true;
  group.add(ground);

  // Lane markings
  for (let l = 0; l < 2; l++) {
    const stripe = new THREE.Mesh(new THREE.BoxGeometry(0.1, 0.12, TRACK_SEGMENT_LENGTH), trackLineMat);
    stripe.position.set(LANE_WIDTH * (l - 0.5), 0.01, z - TRACK_SEGMENT_LENGTH / 2);
    group.add(stripe);
  }

  // Side barriers
  [-1, 1].forEach(side => {
    const barrier = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.6, TRACK_SEGMENT_LENGTH), railMat);
    barrier.position.set(side * 4.5, 0.3, z - TRACK_SEGMENT_LENGTH / 2);
    barrier.castShadow = true;
    group.add(barrier);
    // Top glow
    const glow = new THREE.Mesh(new THREE.BoxGeometry(0.2, 0.15, TRACK_SEGMENT_LENGTH), new THREE.MeshBasicMaterial({ color: 0x4ecdc4 }));
    glow.position.set(side * 4.5, 0.65, z - TRACK_SEGMENT_LENGTH / 2);
    group.add(glow);
  });

  scene.add(group);
  return { mesh: group, z: z };
}

function initTrack() {
  trackSegments.forEach(t => scene.remove(t.mesh));
  trackSegments = [];
  for (let i = 0; i < NUM_TRACK_SEGMENTS; i++) {
    trackSegments.push(createTrackSegment(-i * TRACK_SEGMENT_LENGTH));
  }
}

// =====================================================
//  ENVIRONMENT (Buildings, Trees)
// =====================================================
const buildingColors = [0x1a1a3e, 0x2a1a4e, 0x1a2a3e, 0x3a1a2e, 0x1a3a2e];
const buildingEmissive = [0x0a0a2e, 0x1a0a2e, 0x0a1a2e];

function createBuilding(x, z) {
  const w = 3 + Math.random() * 4;
  const h = 8 + Math.random() * 20;
  const d = 3 + Math.random() * 4;
  const mat = new THREE.MeshLambertMaterial({
    color: buildingColors[Math.floor(Math.random() * buildingColors.length)],
    emissive: buildingEmissive[Math.floor(Math.random() * buildingEmissive.length)]
  });
  const mesh = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), mat);
  mesh.position.set(x, h / 2 - 0.1, z);
  mesh.castShadow = true;
  scene.add(mesh);

  // Windows
  const windowMat = new THREE.MeshBasicMaterial({ color: Math.random() > 0.5 ? 0xffe66d : 0x4ecdc4 });
  const numWinCols = Math.floor(w);
  const numWinRows = Math.floor(h / 2);
  for (let col = 0; col < numWinCols; col++) {
    for (let row = 0; row < numWinRows; row++) {
      if (Math.random() > 0.4) {
        const win = new THREE.Mesh(new THREE.BoxGeometry(0.35, 0.5, 0.15), windowMat);
        win.position.set(
          x - w / 2 + 0.5 + col * (w / numWinCols),
          (h / 2 - 0.1) - h + 1.5 + row * 2,
          z + d / 2 + 0.08
        );
        scene.add(win);
        mesh.userData.windows = mesh.userData.windows || [];
        mesh.userData.windows.push(win);
      }
    }
  }
  return { mesh, windows: mesh.userData.windows || [], z, removeZ: DESPAWN_Z + 15 };
}

function spawnEnvironment(z) {
  const side = Math.random() > 0.5 ? 1 : -1;
  const x = side * (6 + Math.random() * 5);
  envObjects.push(createBuilding(x, z));
  if (Math.random() > 0.4) {
    const x2 = -side * (6 + Math.random() * 5);
    envObjects.push(createBuilding(x2, z - Math.random() * 10));
  }
}

// =====================================================
//  PLAYER CHARACTER
// =====================================================
let player;
let playerParts = {};

function createPlayer() {
  if (player) scene.remove(player);
  player = new THREE.Group();

  const bodyMat = new THREE.MeshLambertMaterial({ color: 0x3366ff, emissive: 0x112244 });
  const headMat = new THREE.MeshLambertMaterial({ color: 0xff9966, emissive: 0x441100 });
  const limbMat = new THREE.MeshLambertMaterial({ color: 0xff4444, emissive: 0x220000 });
  const leg2Mat = new THREE.MeshLambertMaterial({ color: 0x2244cc, emissive: 0x001133 });
  const eyeMat = new THREE.MeshBasicMaterial({ color: 0x00ffff });
  const shaleMat = new THREE.MeshBasicMaterial({ color: 0xffe66d });

  // Torso
  const torso = new THREE.Mesh(new THREE.BoxGeometry(0.8, 0.9, 0.4), bodyMat);
  torso.position.y = 1.35;
  torso.castShadow = true;
  player.add(torso);
  playerParts.torso = torso;

  // Head
  const head = new THREE.Mesh(new THREE.BoxGeometry(0.65, 0.65, 0.55), headMat);
  head.position.y = 2.1;
  head.castShadow = true;
  player.add(head);
  playerParts.head = head;

  // Eyes
  const eye = new THREE.Mesh(new THREE.BoxGeometry(0.1, 0.1, 0.1), eyeMat);
  eye.position.set(0.15, 2.15, 0.28);
  player.add(eye);
  const eye2 = eye.clone();
  eye2.position.set(-0.15, 2.15, 0.28);
  player.add(eye2);

  // Visor
  const visor = new THREE.Mesh(new THREE.BoxGeometry(0.5, 0.08, 0.1), shaleMat);
  visor.position.set(0, 2.25, 0.28);
  player.add(visor);

  // Left Arm
  const lArm = new THREE.Group();
  const lArmMesh = new THREE.Mesh(new THREE.BoxGeometry(0.25, 0.7, 0.25), limbMat);
  lArmMesh.position.y = -0.35;
  lArmMesh.castShadow = true;
  lArm.add(lArmMesh);
  lArm.position.set(0.55, 1.7, 0);
  player.add(lArm);
  playerParts.lArm = lArm;

  // Right Arm
  const rArm = new THREE.Group();
  const rArmMesh = new THREE.Mesh(new THREE.BoxGeometry(0.25, 0.7, 0.25), limbMat);
  rArmMesh.position.y = -0.35;
  rArmMesh.castShadow = true;
  rArm.add(rArmMesh);
  rArm.position.set(-0.55, 1.7, 0);
  player.add(rArm);
  playerParts.rArm = rArm;

  // Left Leg
  const lLeg = new THREE.Group();
  const lLegMesh = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.85, 0.3), leg2Mat);
  lLegMesh.position.y = -0.42;
  lLegMesh.castShadow = true;
  lLeg.add(lLegMesh);
  lLeg.position.set(0.22, 0.9, 0);
  player.add(lLeg);
  playerParts.lLeg = lLeg;

  // Right Leg
  const rLeg = new THREE.Group();
  const rLegMesh = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.85, 0.3), leg2Mat);
  rLegMesh.position.y = -0.42;
  rLegMesh.castShadow = true;
  rLeg.add(rLegMesh);
  rLeg.position.set(-0.22, 0.9, 0);
  player.add(rLeg);
  playerParts.rLeg = rLeg;

  // Shoes
  [0.22, -0.22].forEach((xOff, i) => {
    const shoe = new THREE.Mesh(new THREE.BoxGeometry(0.35, 0.2, 0.45), new THREE.MeshLambertMaterial({ color: 0xff6b35, emissive: 0x220a00 }));
    shoe.position.set(xOff, 0.1, 0.05);
    shoe.castShadow = true;
    player.add(shoe);
  });

  scene.add(player);
  resetPlayerState();
}

function resetPlayerState() {
  playerLane = 1;
  playerTargetX = LANES[1];
  playerX = LANES[1];
  playerY = 0;
  playerVY = 0;
  isJumping = false;
  isSliding = false;
  slideTimer = 0;
  playerRunAnim = 0;
  playerDead = false;
  player.position.set(playerX, playerY, 0);
  player.rotation.set(0, 0, 0);
  player.scale.set(1, 1, 1);
  Object.values(playerParts).forEach(p => { if(p) { p.rotation.set(0,0,0); } });
}

function updatePlayerAnimation(dt) {
  if (playerDead) {
    player.rotation.z += dt * 3;
    player.position.y -= dt * 2;
    return;
  }

  playerRunAnim += dt * (isSliding ? 4 : 8) * (gameSpeed / 0.22);
  const runCycle = Math.sin(playerRunAnim);
  const runFast = Math.sin(playerRunAnim * 2);

  if (isSliding) {
    // Crouch
    player.scale.set(1, 0.55, 1);
    player.position.y = playerY - 0.55;
    playerParts.lArm.rotation.x = 0;
    playerParts.rArm.rotation.x = 0;
    playerParts.lLeg.rotation.x = 0.4;
    playerParts.rLeg.rotation.x = 0.4;
  } else if (isJumping) {
    player.scale.set(1, 1, 1);
    player.position.y = playerY;
    // Arms up during jump
    playerParts.lArm.rotation.x = -1.2;
    playerParts.rArm.rotation.x = -1.2;
    playerParts.lLeg.rotation.x = 0.5;
    playerParts.rLeg.rotation.x = -0.5;
  } else {
    player.scale.set(1, 1, 1);
    player.position.y = playerY;
    // Running animation
    playerParts.lArm.rotation.x = runCycle * 0.9;
    playerParts.rArm.rotation.x = -runCycle * 0.9;
    playerParts.lLeg.rotation.x = -runCycle * 1.1;
    playerParts.rLeg.rotation.x = runCycle * 1.1;
    // Torso bob
    playerParts.torso.position.y = 1.35 + Math.abs(runFast) * 0.04;
    playerParts.head.position.y = 2.1 + Math.abs(runFast) * 0.04;
  }

  // Lane switch
  playerX += (playerTargetX - playerX) * 0.18;
  player.position.x = playerX;

  // Lean during lane switch
  const leanDir = playerTargetX - playerX;
  player.rotation.z = -leanDir * 0.08;
}

// =====================================================
//  OBSTACLES
// =====================================================
const obstacleMat = new THREE.MeshLambertMaterial({ color: 0xff3333, emissive: 0x440000 });
const trainBodyMat = new THREE.MeshLambertMaterial({ color: 0x2244cc, emissive: 0x001144 });
const trainWindowMat = new THREE.MeshBasicMaterial({ color: 0xaaccff });
const hurdleMat = new THREE.MeshLambertMaterial({ color: 0xff9900, emissive: 0x331100 });
const barrierMat = new THREE.MeshLambertMaterial({ color: 0xff4444, emissive: 0x220000 });

function createTrain(lane) {
  const group = new THREE.Group();
  const body = new THREE.Mesh(new THREE.BoxGeometry(LANE_WIDTH * 0.9, 3.5, 6), trainBodyMat);
  body.position.y = 1.75;
  body.castShadow = true;
  group.add(body);

  // Windows
  for (let i = 0; i < 3; i++) {
    const win = new THREE.Mesh(new THREE.BoxGeometry(1.0, 0.7, 0.12), trainWindowMat);
    win.position.set(0, 2.0, -2.2 + i * 2.2);
    group.add(win);
  }
  // Front
  const front = new THREE.Mesh(new THREE.BoxGeometry(LANE_WIDTH * 0.9, 3.5, 0.2), new THREE.MeshBasicMaterial({ color: 0x4466ff }));
  front.position.set(0, 1.75, 3.1);
  group.add(front);

  group.position.set(LANES[lane], 0, SPAWN_Z);
  group.userData = { type: 'train', lane, hitW: LANE_WIDTH * 0.9 * 0.5, hitH: 3.5, hitD: 6, removable: false };
  scene.add(group);
  return group;
}

function createHurdle(lane) {
  const group = new THREE.Group();
  // Legs
  [-1, 1].forEach(side => {
    const leg = new THREE.Mesh(new THREE.BoxGeometry(0.15, 1.2, 0.15), hurdleMat);
    leg.position.set(side * 0.85, 0.6, 0);
    leg.castShadow = true;
    group.add(leg);
  });
  const bar = new THREE.Mesh(new THREE.BoxGeometry(2.0, 0.18, 0.15), hurdleMat);
  bar.position.set(0, 1.2, 0);
  group.add(bar);

  group.position.set(LANES[lane], 0, SPAWN_Z);
  group.userData = { type: 'hurdle', lane, hitW: 1.8 * 0.5, hitH: 1.2, hitD: 0.3, removable: false };
  scene.add(group);
  return group;
}

function createBarrier(lane) {
  const group = new THREE.Group();
  const body = new THREE.Mesh(new THREE.BoxGeometry(LANE_WIDTH * 0.8, 0.6, 1.2), barrierMat);
  body.position.y = 0.3;
  body.castShadow = true;
  group.add(body);
  const stripe1 = new THREE.Mesh(new THREE.BoxGeometry(LANE_WIDTH * 0.8, 0.15, 1.22), new THREE.MeshBasicMaterial({ color: 0xffffff }));
  stripe1.position.y = 0.5;
  group.add(stripe1);

  group.position.set(LANES[lane], 0, SPAWN_Z);
  group.userData = { type: 'barrier', lane, hitW: LANE_WIDTH * 0.8 * 0.5, hitH: 0.6, hitD: 1.2, removable: false };
  scene.add(group);
  return group;
}

let lastObstacleZ = -30;
function spawnObstacle() {
  const types = ['hurdle', 'barrier', 'train'];
  const weights = [0.4, 0.35, 0.25];
  const r = Math.random();
  let type = types[0];
  let cumulative = 0;
  for (let i = 0; i < types.length; i++) {
    cumulative += weights[i];
    if (r < cumulative) { type = types[i]; break; }
  }

  // Avoid blocking all 3 lanes
  const blockedLanes = Math.floor(Math.random() * 2) + 1; // 1 or 2 lanes blocked
  const lanesArr = [0, 1, 2];
  const shuffled = lanesArr.sort(() => Math.random() - 0.5);
  const toLock = shuffled.slice(0, blockedLanes);

  toLock.forEach(lane => {
    let obs;
    if (type === 'train') obs = createTrain(lane);
    else if (type === 'hurdle') obs = createHurdle(lane);
    else obs = createBarrier(lane);
    obstacles.push(obs);
  });
}

// =====================================================
//  COINS
// =====================================================
const coinGeo = new THREE.CylinderGeometry(0.25, 0.25, 0.1, 12);
const coinMat = new THREE.MeshLambertMaterial({ color: 0xffe66d, emissive: 0x554400 });

function createCoin(x, y, z) {
  const mesh = new THREE.Mesh(coinGeo, coinMat);
  mesh.rotation.x = Math.PI / 2;
  mesh.position.set(x, y, z);
  mesh.castShadow = true;
  scene.add(mesh);
  coinObjects.push(mesh);
  return mesh;
}

let lastCoinZ = -20;
function spawnCoins() {
  const lane = Math.floor(Math.random() * 3);
  const x = LANES[lane];
  const pattern = Math.floor(Math.random() * 3);

  if (pattern === 0) {
    // Line
    for (let i = 0; i < 6; i++) {
      createCoin(x, 0.7, SPAWN_Z - i * 1.5);
    }
  } else if (pattern === 1) {
    // Arc
    for (let i = 0; i < 5; i++) {
      const arc = Math.sin((i / 4) * Math.PI);
      createCoin(x, 0.7 + arc * 1.5, SPAWN_Z - i * 1.8);
    }
  } else {
    // All lanes
    LANES.forEach(lx => {
      for (let i = 0; i < 3; i++) {
        createCoin(lx, 0.7, SPAWN_Z - i * 2.2);
      }
    });
  }
}

// =====================================================
//  POWER-UPS
// =====================================================
function createPowerup(type, lane) {
  const group = new THREE.Group();
  const color = type === 'magnet' ? 0xff66ff : 0x00ffaa;
  const emissive = type === 'magnet' ? 0x550055 : 0x005533;
  const core = new THREE.Mesh(
    new THREE.OctahedronGeometry(0.45),
    new THREE.MeshLambertMaterial({ color, emissive })
  );
  group.add(core);

  const ring = new THREE.Mesh(
    new THREE.TorusGeometry(0.65, 0.07, 8, 20),
    new THREE.MeshBasicMaterial({ color })
  );
  ring.rotation.x = Math.PI / 2;
  group.add(ring);
  group.userData = { type, lane, core, ring };
  group.position.set(LANES[lane], 1.0, SPAWN_Z);
  scene.add(group);
  powerups.push(group);
}

let lastPowerupZ = -60;
function spawnPowerup() {
  const lane = Math.floor(Math.random() * 3);
  const type = Math.random() > 0.5 ? 'magnet' : 'jetpack';
  createPowerup(type, lane);
}

// =====================================================
//  PARTICLES
// =====================================================
const partGeo = new THREE.SphereGeometry(0.08, 4, 4);

function createParticles(x, y, z, color, count = 12) {
  for (let i = 0; i < count; i++) {
    const mesh = new THREE.Mesh(partGeo, new THREE.MeshBasicMaterial({ color }));
    mesh.position.set(x, y, z);
    mesh.userData = {
      vx: (Math.random() - 0.5) * 4,
      vy: Math.random() * 3 + 1,
      vz: (Math.random() - 0.5) * 4,
      life: 1.0,
      decay: 0.04 + Math.random() * 0.04
    };
    scene.add(mesh);
    particles.push(mesh);
  }
}

function updateParticles(dt) {
  for (let i = particles.length - 1; i >= 0; i--) {
    const p = particles[i];
    p.position.x += p.userData.vx * dt;
    p.position.y += p.userData.vy * dt;
    p.position.z += p.userData.vz * dt;
    p.userData.vy -= 5 * dt;
    p.userData.life -= p.userData.decay;
    p.material.opacity = p.userData.life;
    p.material.transparent = true;
    if (p.userData.life <= 0) {
      scene.remove(p);
      particles.splice(i, 1);
    }
  }
}

// =====================================================
//  STARS (background)
// =====================================================
function createStarField() {
  const starGeo = new THREE.BufferGeometry();
  const starCount = 2000;
  const positions = new Float32Array(starCount * 3);
  for (let i = 0; i < starCount; i++) {
    positions[i * 3] = (Math.random() - 0.5) * 300;
    positions[i * 3 + 1] = Math.random() * 80 + 5;
    positions[i * 3 + 2] = (Math.random() - 0.5) * 300;
  }
  starGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  const starMat = new THREE.PointsMaterial({ color: 0xffffff, size: 0.3, sizeAttenuation: true });
  const stars = new THREE.Points(starGeo, starMat);
  scene.add(stars);
}
createStarField();

// =====================================================
//  COLLISION DETECTION
// =====================================================
function checkCollisions() {
  if (playerDead) return;

  const px = player.position.x;
  const py = player.position.y;
  const pz = player.position.z;
  const pw = 0.38, ph = isSliding ? 0.55 : 1.1, pd = 0.35;

  // Obstacles
  for (const obs of obstacles) {
    const ox = obs.position.x;
    const oy = obs.position.y;
    const oz = obs.position.z;
    const ow = obs.userData.hitW;
    const oh = obs.userData.hitH;
    const od = obs.userData.hitD;

    if (
      Math.abs(px - ox) < pw + ow - 0.1 &&
      py < oy + oh - 0.1 &&
      py + ph > oy + 0.1 &&
      Math.abs(pz - oz) < pd + od * 0.4
    ) {
      if (obs.userData.type === 'hurdle' && isJumping && py > 0.8) continue;
      if (obs.userData.type === 'barrier' && isSliding) continue;
      if (jetpackActive) continue;
      triggerGameOver();
      return;
    }
  }

  // Coins
  for (let i = coinObjects.length - 1; i >= 0; i--) {
    const c = coinObjects[i];
    const cx = c.position.x;
    const cy = c.position.y;
    const cz = c.position.z;

    let collectDist = magnetActive ? 3.5 : 0.9;
    const dx = px - cx;
    const dy = py - cy + 0.5;
    const dz = pz - cz;
    const dist = Math.sqrt(dx*dx + dy*dy + dz*dz);

    if (magnetActive && dist < 3.5) {
      c.position.x += dx / dist * 6 * (1/60);
      c.position.y += dy / dist * 6 * (1/60);
      c.position.z += dz / dist * 6 * (1/60);
    }

    if (dist < 0.9) {
      scene.remove(c);
      coinObjects.splice(i, 1);
      coins++;
      score += 10;
      sfxCoin();
      createParticles(cx, cy, cz, 0xffe66d, 8);
    }
  }

  // Power-ups
  for (let i = powerups.length - 1; i >= 0; i--) {
    const p = powerups[i];
    const d = Math.sqrt(
      Math.pow(px - p.position.x, 2) +
      Math.pow(pz - p.position.z, 2)
    );
    if (d < 1.0) {
      scene.remove(p);
      powerups.splice(i, 1);
      sfxPowerup();
      createParticles(p.position.x, p.position.y, p.position.z, p.userData.type === 'magnet' ? 0xff66ff : 0x00ffaa, 15);
      if (p.userData.type === 'magnet') {
        magnetActive = true;
        magnetTimer = 8;
        document.getElementById('magnetBadge').style.display = 'inline-block';
      } else {
        jetpackActive = true;
        jetpackTimer = 6;
        document.getElementById('jetpackBadge').style.display = 'inline-block';
        // Launch up
        playerVY = 10;
        isJumping = true;
      }
    }
  }
}

// =====================================================
//  GAME OVER
// =====================================================
function triggerGameOver() {
  if (playerDead) return;
  playerDead = true;
  gameState = 'gameover';
  sfxCollision();
  createParticles(player.position.x, player.position.y + 1, player.position.z, 0xff4444, 20);

  setTimeout(() => {
    stopBGM();
    document.getElementById('hud').style.display = 'none';
    document.getElementById('finalScore').textContent = score;
    document.getElementById('finalCoins').textContent = coins;
    document.getElementById('finalDist').textContent = Math.floor(distance) + 'm';
    document.getElementById('gameOverScreen').style.display = 'flex';
  }, 1200);
}

// =====================================================
//  INPUT HANDLING
// =====================================================
let lastLaneChange = 0;

document.addEventListener('keydown', e => {
  if (!keys[e.code] && gameState === 'playing') {
    const now = Date.now();
    if ((e.code === 'ArrowLeft' || e.code === 'KeyA') && now - lastLaneChange > 200) {
      if (playerLane > 0) {
        playerLane--;
        playerTargetX = LANES[playerLane];
        lastLaneChange = now;
      }
    }
    if ((e.code === 'ArrowRight' || e.code === 'KeyD') && now - lastLaneChange > 200) {
      if (playerLane < 2) {
        playerLane++;
        playerTargetX = LANES[playerLane];
        lastLaneChange = now;
      }
    }
    if ((e.code === 'ArrowUp' || e.code === 'Space') && !isJumping && !isSliding) {
      isJumping = true;
      playerVY = jetpackActive ? 14 : 7;
      sfxJump();
    }
    if ((e.code === 'ArrowDown' || e.code === 'KeyS') && !isJumping) {
      if (!isSliding) {
        isSliding = true;
        slideTimer = 0.7;
        sfxSlide();
      }
    }
  }
  keys[e.code] = true;
});

document.addEventListener('keyup', e => {
  keys[e.code] = false;
});

// Touch / Swipe support
let touchStartX = 0, touchStartY = 0;
document.addEventListener('touchstart', e => {
  touchStartX = e.touches[0].clientX;
  touchStartY = e.touches[0].clientY;
}, { passive: true });

document.addEventListener('touchend', e => {
  if (gameState !== 'playing') return;
  const dx = e.changedTouches[0].clientX - touchStartX;
  const dy = e.changedTouches[0].clientY - touchStartY;
  const now = Date.now();
  if (Math.abs(dx) > Math.abs(dy)) {
    if (dx < -30 && playerLane > 0 && now - lastLaneChange > 200) {
      playerLane--; playerTargetX = LANES[playerLane]; lastLaneChange = now;
    } else if (dx > 30 && playerLane < 2 && now - lastLaneChange > 200) {
      playerLane++; playerTargetX = LANES[playerLane]; lastLaneChange = now;
    }
  } else {
    if (dy < -30 && !isJumping && !isSliding) {
      isJumping = true; playerVY = 7; sfxJump();
    } else if (dy > 30 && !isJumping && !isSliding) {
      isSliding = true; slideTimer = 0.7; sfxSlide();
    }
  }
}, { passive: true });

// =====================================================
//  HUD UPDATE
// =====================================================
function updateHUD() {
  document.getElementById('scoreVal').textContent = score;
  document.getElementById('coinVal').textContent = '🪙 ' + coins;
  document.getElementById('distVal').textContent = Math.floor(distance) + 'm';
}

// =====================================================
//  WORLD UPDATE
// =====================================================
let nextObstacleIn = 3;
let nextCoinIn = 1.5;
let nextEnvIn = 0;
let nextPowerupIn = 15;

function resetWorld() {
  // Remove all scene objects except camera, lights, player
  [...obstacles, ...coinObjects, ...powerups, ...trackSegments.map(t=>t.mesh), ...envObjects.map(e=>e.mesh)].forEach(m => scene.remove(m));
  obstacles = [];
  coinObjects = [];
  powerups = [];
  trackSegments = [];
  envObjects = [];
  particles.forEach(p => scene.remove(p));
  particles = [];

  score = 0; coins = 0; distance = 0;
  gameSpeed = 0.22;
  frameCount = 0;
  magnetActive = false; magnetTimer = 0;
  jetpackActive = false; jetpackTimer = 0;
  nextObstacleIn = 3;
  nextCoinIn = 1.5;
  nextEnvIn = 0;
  nextPowerupIn = 15;
  document.getElementById('magnetBadge').style.display = 'none';
  document.getElementById('jetpackBadge').style.display = 'none';

  initTrack();

  // Initial env
  for (let i = 0; i < 8; i++) {
    spawnEnvironment(-i * 20);
  }
}

function updateWorld(dt) {
  const moveZ = gameSpeed * 60 * dt;

  // Move track segments
  for (let i = trackSegments.length - 1; i >= 0; i--) {
    trackSegments[i].mesh.position.z += moveZ;
    if (trackSegments[i].mesh.position.z > TRACK_SEGMENT_LENGTH) {
      trackSegments[i].mesh.position.z -= NUM_TRACK_SEGMENTS * TRACK_SEGMENT_LENGTH;
    }
  }

  // Move obstacles
  for (let i = obstacles.length - 1; i >= 0; i--) {
    obstacles[i].position.z += moveZ;
    if (obstacles[i].position.z > DESPAWN_Z) {
      scene.remove(obstacles[i]);
      obstacles.splice(i, 1);
    }
  }

  // Move coins
  for (let i = coinObjects.length - 1; i >= 0; i--) {
    coinObjects[i].position.z += moveZ;
    coinObjects[i].rotation.z += dt * 3;
    if (coinObjects[i].position.z > DESPAWN_Z) {
      scene.remove(coinObjects[i]);
      coinObjects.splice(i, 1);
    }
  }

  // Move power-ups
  for (let i = powerups.length - 1; i >= 0; i--) {
    powerups[i].position.z += moveZ;
    powerups[i].rotation.y += dt * 2;
    powerups[i].userData.core.rotation.x += dt * 1.5;
    powerups[i].userData.ring.rotation.z += dt * 2;
    if (powerups[i].position.z > DESPAWN_Z) {
      scene.remove(powerups[i]);
      powerups.splice(i, 1);
    }
  }

  // Move env
  for (let i = envObjects.length - 1; i >= 0; i--) {
    const e = envObjects[i];
    e.mesh.position.z += moveZ;
    if (e.windows) e.windows.forEach(w => w.position.z += moveZ);
    if (e.mesh.position.z > 20) {
      scene.remove(e.mesh);
      if (e.windows) e.windows.forEach(w => scene.remove(w));
      envObjects.splice(i, 1);
    }
  }

  // Spawning
  nextObstacleIn -= dt;
  nextCoinIn -= dt;
  nextEnvIn -= dt;
  nextPowerupIn -= dt;

  const spawnInterval = Math.max(1.2, 2.5 - distance * 0.002);
  if (nextObstacleIn <= 0) {
    spawnObstacle();
    nextObstacleIn = spawnInterval + Math.random() * 0.8;
  }
  if (nextCoinIn <= 0) {
    spawnCoins();
    nextCoinIn = 2.0 + Math.random() * 1.5;
  }
  if (nextEnvIn <= 0) {
    spawnEnvironment(-90);
    nextEnvIn = 10 + Math.random() * 8;
  }
  if (nextPowerupIn <= 0) {
    spawnPowerup();
    nextPowerupIn = 12 + Math.random() * 10;
  }

  // Increase speed
  gameSpeed = Math.min(0.22 + distance * 0.0003, 0.55);
  distance += moveZ * 0.3;
  score += moveZ * 0.5;

  // Power-up timers
  if (magnetActive) {
    magnetTimer -= dt;
    if (magnetTimer <= 0) {
      magnetActive = false;
      document.getElementById('magnetBadge').style.display = 'none';
    }
  }
  if (jetpackActive) {
    jetpackTimer -= dt;
    if (jetpackTimer <= 0) {
      jetpackActive = false;
      document.getElementById('jetpackBadge').style.display = 'none';
    }
  }
}

// =====================================================
//  PHYSICS
// =====================================================
const GRAVITY = -18;

function updatePhysics(dt) {
  if (playerDead) return;

  if (isJumping) {
    playerVY += GRAVITY * dt;
    playerY += playerVY * dt;
    if (playerY <= 0) {
      playerY = 0;
      playerVY = 0;
      isJumping = false;
    }
  }

  if (isSliding) {
    slideTimer -= dt;
    if (slideTimer <= 0) {
      isSliding = false;
    }
  }
}

// =====================================================
//  CAMERA
// =====================================================
function updateCamera(dt) {
  const targetX = player.position.x * 0.3;
  const targetY = playerY + (jetpackActive ? 9 : 5.5);
  camera.position.x += (targetX - camera.position.x) * 0.08;
  camera.position.y += (targetY - camera.position.y) * 0.08;
  camera.position.z = 9;
  camera.lookAt(player.position.x * 0.2, player.position.y + 1.5, -8);
}

// =====================================================
//  MAIN GAME LOOP
// =====================================================
let lastTime = 0;
let animFrameId = null;

function gameLoop(timestamp) {
  animFrameId = requestAnimationFrame(gameLoop);
  const dt = Math.min((timestamp - lastTime) / 1000, 0.05);
  lastTime = timestamp;
  frameCount++;

  if (gameState === 'playing' || playerDead) {
    if (gameState === 'playing' && !playerDead) {
      updateWorld(dt);
      updatePhysics(dt);
      checkCollisions();
    }
    updatePlayerAnimation(dt);
    updateCamera(dt);
    updateParticles(dt);
    pointLight.position.set(player.position.x, player.position.y + 2, player.position.z);

    if (gameState === 'playing') {
      updateHUD();
    }
  }

  renderer.render(scene, camera);
}

// =====================================================
//  START / RESTART
// =====================================================
function startGame() {
  initAudio();
  gameState = 'playing';
  createPlayer();
  resetWorld();
  document.getElementById('startScreen').style.display = 'none';
  document.getElementById('gameOverScreen').style.display = 'none';
  document.getElementById('hud').style.display = 'block';
  startBGM();
}

function restartGame() {
  document.getElementById('gameOverScreen').style.display = 'none';
  document.getElementById('hud').style.display = 'block';
  stopBGM();
  gameState = 'playing';
  createPlayer();
  resetWorld();
  startBGM();
}

document.getElementById('playBtn').addEventListener('click', startGame);
document.getElementById('restartBtn').addEventListener('click', restartGame);

// =====================================================
//  RESIZE
// =====================================================
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});

// =====================================================
//  BOOT
// =====================================================
// Render start background
function renderIdle(timestamp) {
  if (gameState !== 'start') return;
  animFrameId = requestAnimationFrame(renderIdle);
  lastTime = timestamp;
  // Slowly rotate camera for a cinematic start screen
  const t = timestamp / 1000;
  camera.position.set(Math.sin(t * 0.2) * 8, 6, 12);
  camera.lookAt(0, 1, -5);
  renderer.render(scene, camera);
}

// Create a preview scene for start screen
const previewTrack = [];
for (let i = 0; i < 5; i++) {
  previewTrack.push(createTrackSegment(-i * TRACK_SEGMENT_LENGTH));
}
for (let i = 0; i < 5; i++) spawnEnvironment(-i * 20);
createPlayer();
resetPlayerState();
// Animate arms for idle
player.position.set(0, 0, -3);

animFrameId = requestAnimationFrame(t => {
  lastTime = t;
  renderIdle(t);
});

// Override renderIdle to also animate player arms
const _renderIdle = renderIdle;

// Kick off the game loop (will switch based on gameState)
cancelAnimationFrame(animFrameId);
lastTime = performance.now();
animFrameId = requestAnimationFrame(gameLoop);
</script>
</body>
</html>
