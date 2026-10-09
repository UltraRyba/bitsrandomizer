<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bits Giveaway Wheel | Chat Listener</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.9.2/dist/confetti.browser.min.js"></script>

<style>
  :root{
    --bg-main:#0e0e10; --bg-panel:#18181b; --bg-card:#1f1f23; --bg-input:#26262c;
    --border-subtle:#2f2f36; --twitch-purple:#9146FF; --twitch-purple-light:#a970ff;
    --neon-cyan:#00f5d4; --text-primary:#f4f4f5; --text-secondary:#a1a1aa; --danger:#ff4d6d;
    --radius-lg:18px; --radius-md:12px; --radius-sm:8px;
    --shadow-card:0 10px 30px -10px rgba(0,0,0,0.6);
    --transition:all 0.25s cubic-bezier(.4,0,.2,1);
  }
  *{box-sizing:border-box;margin:0;padding:0;}
  body{font-family:'Inter',system-ui,sans-serif;background:radial-gradient(circle at 20% 0%,#1a1025 0%,#0e0e10 45%) fixed;color:var(--text-primary);min-height:100vh;-webkit-font-smoothing:antialiased;}
  .app-shell{max-width:1280px;margin:0 auto;padding:24px 20px 60px;}
  header.top-header{display:flex;flex-wrap:wrap;align-items:center;justify-content:space-between;gap:20px;padding:20px 26px;background:linear-gradient(135deg,rgba(145,70,255,0.12),rgba(0,245,212,0.05));border:1px solid var(--border-subtle);border-radius:var(--radius-lg);margin-bottom:24px;box-shadow:var(--shadow-card);}
  .brand{display:flex;align-items:center;gap:14px;}
  .brand-icon{width:48px;height:48px;border-radius:14px;background:linear-gradient(135deg,var(--twitch-purple),#6a2ec7);display:flex;align-items:center;justify-content:center;box-shadow:0 4px 18px rgba(145,70,255,0.45);flex-shrink:0;}
  .brand-text h1{font-size:1.3rem;font-weight:800;letter-spacing:-0.3px;}
  .brand-text p{font-size:0.78rem;color:var(--text-secondary);font-weight:500;margin-top:2px;}
  .header-stats{display:flex;gap:14px;flex-wrap:wrap;}
  .stat-pill{background:var(--bg-card);border:1px solid var(--border-subtle);border-radius:var(--radius-md);padding:10px 18px;text-align:center;min-width:110px;}
  .stat-pill .stat-value{font-size:1.25rem;font-weight:800;color:var(--neon-cyan);}
  .stat-pill .stat-value.purple{color:var(--twitch-purple-light);}
  .stat-pill .stat-label{font-size:0.65rem;text-transform:uppercase;letter-spacing:0.6px;color:var(--text-secondary);margin-top:4px;font-weight:600;}

  .twitch-connect{display:flex;flex-wrap:wrap;gap:18px;align-items:center;margin-bottom:24px;padding:18px 22px;background:var(--bg-panel);border:1px solid var(--border-subtle);border-radius:var(--radius-lg);box-shadow:var(--shadow-card);}
  .twitch-connect .tc-info{flex:1;min-width:220px;}
  .twitch-connect h3{font-size:0.95rem;margin-bottom:4px;display:flex;align-items:center;}
  .twitch-connect p{font-size:0.78rem;color:var(--text-secondary);}
  .status-dot{width:9px;height:9px;border-radius:50%;background:var(--danger);display:inline-block;margin-right:8px;box-shadow:0 0 8px var(--danger);flex-shrink:0;}
  .status-dot.live{background:var(--neon-cyan);box-shadow:0 0 8px var(--neon-cyan);animation:pulse 1.4s infinite;}
  @keyframes pulse{0%,100%{opacity:1;}50%{opacity:0.4;}}
  .tc-controls{display:flex;gap:8px;flex-wrap:wrap;align-items:center;}
  .tc-controls .input-prefix{position:relative;}
  .tc-controls .input-prefix span{position:absolute;left:12px;top:50%;transform:translateY(-50%);color:var(--text-secondary);font-weight:600;font-size:0.85rem;}
  .tc-controls input{padding:10px 12px 10px 24px;background:var(--bg-input);border:1px solid var(--border-subtle);border-radius:var(--radius-sm);color:var(--text-primary);font-size:0.82rem;width:200px;}
  .tc-controls input:focus{outline:none;border-color:var(--twitch-purple);}

  main.grid-layout{display:grid;grid-template-columns:1.2fr 1fr;gap:24px;align-items:start;}
  @media (max-width:920px){main.grid-layout{grid-template-columns:1fr;}}
  .card{background:var(--bg-panel);border:1px solid var(--border-subtle);border-radius:var(--radius-lg);padding:24px;box-shadow:var(--shadow-card);}
  .card-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:20px;gap:10px;flex-wrap:wrap;}
  .card-header h2{font-size:1.02rem;font-weight:700;display:flex;align-items:center;gap:8px;}
  .card-header h2 .dot{width:8px;height:8px;border-radius:50%;background:var(--neon-cyan);box-shadow:0 0 10px var(--neon-cyan);}

  .mode-toggle{display:flex;background:var(--bg-input);border-radius:999px;padding:4px;border:1px solid var(--border-subtle);position:relative;}
  .mode-toggle button{flex:1;padding:9px 14px;border:none;background:transparent;color:var(--text-secondary);font-weight:600;font-size:0.78rem;border-radius:999px;cursor:pointer;transition:var(--transition);position:relative;z-index:2;white-space:nowrap;}
  .mode-toggle button.active{color:#0e0e10;}
  .mode-toggle .slider{position:absolute;top:4px;bottom:4px;left:4px;width:calc(50% - 4px);background:linear-gradient(135deg,var(--neon-cyan),#00c2a8);border-radius:999px;transition:transform 0.3s cubic-bezier(.4,0,.2,1);z-index:1;}
  .mode-toggle.weighted .slider{transform:translateX(100%);}

  .wheel-zone{display:flex;flex-direction:column;align-items:center;margin-top:10px;}
  .wheel-wrap{position:relative;width:min(320px,80vw);aspect-ratio:1/1;margin:10px auto 6px;}
  .wheel-wrap canvas{width:100%;height:100%;border-radius:50%;box-shadow:0 0 0 6px var(--bg-card),0 0 0 8px var(--border-subtle),0 20px 50px -15px rgba(145,70,255,0.5);}
  .wheel-pointer{position:absolute;top:-6px;left:50%;transform:translateX(-50%);width:0;height:0;border-left:14px solid transparent;border-right:14px solid transparent;border-top:24px solid var(--neon-cyan);filter:drop-shadow(0 2px 4px rgba(0,0,0,0.5));z-index:5;}
  .wheel-hub{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);width:46px;height:46px;border-radius:50%;background:linear-gradient(135deg,var(--twitch-purple),#6a2ec7);border:3px solid var(--bg-panel);box-shadow:0 4px 14px rgba(0,0,0,0.5);z-index:4;}
  #wheelCanvas{display:block;transition:transform 5s cubic-bezier(.17,.67,.21,1);}

  .winner-banner{margin-top:16px;text-align:center;min-height:50px;}
  #winnerName{font-size:clamp(1.3rem,4vw,1.9rem);font-weight:900;letter-spacing:-0.5px;background:linear-gradient(135deg,#fff,#c7b6ff);-webkit-background-clip:text;background-clip:text;color:transparent;}
  #winnerName.locked{animation:winnerPop 0.5s cubic-bezier(.34,1.56,.64,1);}
  @keyframes winnerPop{0%{transform:scale(0.7);opacity:0;}60%{transform:scale(1.1);opacity:1;}100%{transform:scale(1);}}
  .winner-sub{margin-top:6px;font-size:0.85rem;color:var(--text-secondary);font-weight:500;}
  .winner-sub span{color:var(--neon-cyan);font-weight:700;}

  .roll-btn{margin-top:18px;padding:15px 38px;border:none;border-radius:999px;background:linear-gradient(135deg,var(--twitch-purple),#7a2fe0);color:#fff;font-weight:800;font-size:0.92rem;cursor:pointer;box-shadow:0 10px 25px -8px rgba(145,70,255,0.7);transition:var(--transition);display:flex;align-items:center;gap:10px;}
  .roll-btn:hover:not(:disabled){transform:translateY(-2px);box-shadow:0 14px 30px -8px rgba(145,70,255,0.9);}
  .roll-btn:disabled{opacity:0.6;cursor:not-allowed;filter:grayscale(0.3);}
  .roll-btn svg{width:18px;height:18px;}
  .empty-hint{font-size:0.82rem;color:var(--text-secondary);margin-top:10px;text-align:center;}

  .form-group{margin-bottom:16px;}
  .form-group label{display:block;font-size:0.72rem;font-weight:700;text-transform:uppercase;letter-spacing:0.5px;color:var(--text-secondary);margin-bottom:7px;}
  .input-wrap{position:relative;}
  .input-wrap .prefix{position:absolute;left:14px;top:50%;transform:translateY(-50%);color:var(--text-secondary);font-weight:600;}
  input[type="text"],input[type="number"]{width:100%;padding:12px 14px;background:var(--bg-input);border:1px solid var(--border-subtle);border-radius:var(--radius-sm);color:var(--text-primary);font-size:0.88rem;font-weight:500;transition:var(--transition);font-family:inherit;}
  input#usernameInput{padding-left:30px;}
  input:focus{outline:none;border-color:var(--twitch-purple);box-shadow:0 0 0 3px rgba(145,70,255,0.25);}
  input.invalid{border-color:var(--danger);box-shadow:0 0 0 3px rgba(255,77,109,0.18);}
  .error-msg{color:var(--danger);font-size:0.74rem;margin-top:6px;font-weight:600;min-height:16px;display:block;}
  .form-actions{display:flex;gap:10px;margin-top:6px;}
  .btn{border:none;border-radius:var(--radius-sm);padding:12px 18px;font-weight:700;font-size:0.84rem;cursor:pointer;transition:var(--transition);display:flex;align-items:center;justify-content:center;gap:8px;}
  .btn-primary{background:var(--neon-cyan);color:#06211d;flex:1;}
  .btn-primary:hover{filter:brightness(1.1);transform:translateY(-1px);}
  .btn-ghost{background:transparent;border:1px solid var(--border-subtle);color:var(--text-secondary);}
  .btn-ghost:hover{border-color:var(--danger);color:var(--danger);}
  .btn-twitch{background:var(--twitch-purple);color:#fff;padding:10px 16px;font-size:0.8rem;white-space:nowrap;}
  .btn-twitch:hover{background:#7f36ec;}

  .leaderboard-card{margin-top:24px;}
  .leaderboard-list{display:flex;flex-direction:column;gap:10px;max-height:380px;overflow-y:auto;padding-right:4px;}
  .leaderboard-list::-webkit-scrollbar{width:6px;}
  .leaderboard-list::-webkit-scrollbar-thumb{background:var(--border-subtle);border-radius:10px;}
  .user-row{display:flex;align-items:center;gap:12px;background:var(--bg-card);border:1px solid var(--border-subtle);padding:12px 14px;border-radius:var(--radius-md);transition:var(--transition);animation:rowIn 0.35s ease;}
  @keyframes rowIn{from{opacity:0;transform:translateY(6px);}to{opacity:1;transform:translateY(0);}}
  .user-row:hover{border-color:var(--twitch-purple);transform:translateX(2px);}
  .user-rank{width:30px;height:30px;border-radius:8px;background:var(--bg-input);display:flex;align-items:center;justify-content:center;font-weight:800;font-size:0.78rem;color:var(--text-secondary);flex-shrink:0;}
  .user-row:nth-child(1) .user-rank{background:linear-gradient(135deg,#ffd76a,#e8a800);color:#2b1c00;}
  .user-row:nth-child(2) .user-rank{background:linear-gradient(135deg,#dfe4ea,#a9b0bb);color:#20242b;}
  .user-row:nth-child(3) .user-rank{background:linear-gradient(135deg,#e2a06b,#a9633a);color:#2b1600;}
  .user-info{flex:1;min-width:0;}
  .user-name{font-weight:700;font-size:0.9rem;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
  .user-bits{font-size:0.76rem;color:var(--text-secondary);font-weight:600;margin-top:2px;}
  .delete-btn{background:transparent;border:none;color:var(--text-secondary);cursor:pointer;padding:6px;border-radius:8px;transition:var(--transition);flex-shrink:0;display:flex;}
  .delete-btn:hover{background:rgba(255,77,109,0.15);color:var(--danger);}
  .delete-btn svg{width:17px;height:17px;}
  .empty-state{text-align:center;padding:30px 10px;color:var(--text-secondary);font-size:0.85rem;}

  .modal-overlay{position:fixed;inset:0;background:rgba(5,5,7,0.7);backdrop-filter:blur(4px);display:flex;align-items:center;justify-content:center;z-index:999;opacity:0;pointer-events:none;transition:var(--transition);}
  .modal-overlay.show{opacity:1;pointer-events:all;}
  .modal-box{background:var(--bg-panel);border:1px solid var(--border-subtle);border-radius:var(--radius-lg);padding:28px;width:92%;max-width:380px;text-align:center;transform:scale(0.9) translateY(10px);transition:var(--transition);box-shadow:var(--shadow-card);}
  .modal-overlay.show .modal-box{transform:scale(1) translateY(0);}
  .modal-box h3{font-size:1.05rem;margin-bottom:8px;}
  .modal-box p{font-size:0.84rem;color:var(--text-secondary);margin-bottom:20px;}
  .modal-actions{display:flex;gap:10px;}
  .modal-actions .btn{flex:1;}

  .toast{position:fixed;bottom:24px;left:50%;transform:translateX(-50%) translateY(20px);background:var(--bg-card);border:1px solid var(--border-subtle);border-left:4px solid var(--neon-cyan);padding:14px 20px;border-radius:var(--radius-sm);font-size:0.84rem;font-weight:600;opacity:0;pointer-events:none;transition:var(--transition);z-index:1000;box-shadow:var(--shadow-card);max-width:90vw;}
  .toast.show{opacity:1;transform:translateX(-50%) translateY(0);}
  footer.app-footer{text-align:center;color:var(--text-secondary);font-size:0.76rem;margin-top:40px;}
  footer.app-footer strong{color:var(--twitch-purple-light);}
</style>
</head>
<body>

<div class="app-shell">

  <header class="top-header">
    <div class="brand">
      <div class="brand-icon">
        <svg width="26" height="26" viewBox="0 0 24 24" fill="none">
          <path d="M11.571 4.714h1.715v5.143H11.57zm4.715 0H18v5.143h-1.714zM6 0 2 4v16h5.714V24l4-4h3.429L22 13.143V0zm14.286 12.286-3.429 3.428h-3.428l-3 3v-3H6.857V1.714h13.429z" fill="#fff"/>
        </svg>
      </div>
      <div class="brand-text">
        <h1>Bits Giveaway Wheel</h1>
        <p>Live chat listener + spin-to-win engine</p>
      </div>
    </div>
    <div class="header-stats">
      <div class="stat-pill"><div class="stat-value" id="statParticipants">0</div><div class="stat-label">Participants</div></div>
      <div class="stat-pill"><div class="stat-value purple" id="statBits">0</div><div class="stat-label">Total Bits</div></div>
    </div>
  </header>

  <!-- CHAT CONNECTION (no API keys, anonymous read-only) -->
  <section class="twitch-connect">
    <div class="tc-info">
      <h3><span class="status-dot" id="statusDot"></span><span id="statusText">Not connected to any chat</span></h3>
      <p>Enter any Twitch channel name below — we'll quietly watch chat as an anonymous viewer and auto-add anyone who cheers Bits.</p>
    </div>
    <div class="tc-controls">
      <div class="input-prefix">
        <span>#</span>
        <input type="text" id="channelInput" placeholder="channel name">
      </div>
      <button class="btn btn-twitch" id="connectBtn">Connect to Chat</button>
      <button class="btn btn-ghost" id="disconnectBtn" style="display:none;">Disconnect</button>
      <button class="btn btn-ghost" id="simulateBtn" title="Simulate a test cheer for demo purposes">Simulate Cheer</button>
    </div>
  </section>

  <main class="grid-layout">

    <section class="card randomizer-card">
      <div class="card-header">
        <h2><span class="dot"></span> Giveaway Wheel</h2>
        <div class="mode-toggle" id="modeToggle">
          <div class="slider"></div>
          <button type="button" class="active" data-mode="equal">Equal Chance</button>
          <button type="button" data-mode="weighted">Weighted (Bits)</button>
        </div>
      </div>

      <div class="wheel-zone">
        <div class="wheel-wrap">
          <div class="wheel-pointer"></div>
          <canvas id="wheelCanvas" width="640" height="640"></canvas>
          <div class="wheel-hub"></div>
        </div>
        <div class="winner-banner">
          <div id="winnerName">—</div>
          <div class="winner-sub" id="winnerSub">Add participants and hit spin</div>
        </div>
        <button class="roll-btn" id="rollBtn">
          <svg viewBox="0 0 24 24" fill="currentColor"><path d="M12 2a10 10 0 1 0 10 10A10 10 0 0 0 12 2zm0 18a8 8 0 1 1 8-8 8 8 0 0 1-8 8zm1-13h-2v6l5.25 3.15 1-1.64L13 11.5z"/></svg>
          Spin the Wheel
        </button>
        <p class="empty-hint" id="rollHint"></p>
      </div>
    </section>

    <section>
      <div class="card">
        <div class="card-header"><h2><span class="dot"></span> Add Manually</h2></div>
        <form id="addForm" novalidate>
          <div class="form-group">
            <label for="usernameInput">Twitch Username</label>
            <div class="input-wrap">
              <span class="prefix">@</span>
              <input type="text" id="usernameInput" placeholder="e.g. ShadowStrike99" autocomplete="off">
            </div>
            <span class="error-msg" id="usernameError"></span>
          </div>
          <div class="form-group">
            <label for="bitsInput">Number of Bits</label>
            <input type="number" id="bitsInput" placeholder="e.g. 500" min="1">
            <span class="error-msg" id="bitsError"></span>
          </div>
          <div class="form-actions">
            <button type="submit" class="btn btn-primary">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor"><path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6z"/></svg>
              Add to List
            </button>
            <button type="button" class="btn btn-ghost" id="clearAllBtn">Clear All</button>
          </div>
        </form>
      </div>

      <div class="card leaderboard-card">
        <div class="card-header">
          <h2><span class="dot"></span> Bits Leaderboard</h2>
          <span style="font-size:0.75rem;color:var(--text-secondary);font-weight:600;" id="listCount">0 users</span>
        </div>
        <div class="leaderboard-list" id="leaderboardList"></div>
      </div>
    </section>

  </main>

  <footer class="app-footer">Crafted for <strong>streamers</strong> — reads public chat anonymously, no API keys required.</footer>
</div>

<div class="modal-overlay" id="confirmModal">
  <div class="modal-box">
    <h3>Reset the entire list?</h3>
    <p>This will permanently remove all participants from your giveaway list.</p>
    <div class="modal-actions">
      <button class="btn btn-ghost" id="cancelClear">Cancel</button>
      <button class="btn" id="confirmClear" style="background:var(--danger);color:#fff;">Yes, Reset</button>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
/* =====================================================================
   STATE
===================================================================== */
const STORAGE_KEY = 'twitchBitsList';
let participants = [];
let drawMode = 'equal';
let isRolling = false;
let currentWheelRotation = 0;
let chatSocket = null;
let connectedChannel = null;

const DEMO_DATA = [
  { id: crypto.randomUUID(), username: 'ShadowStrike99', bits: 15200 },
  { id: crypto.randomUUID(), username: 'PixelQueen',      bits: 8400  },
  { id: crypto.randomUUID(), username: 'NoobMaster69',    bits: 450   }
];

const WHEEL_COLORS = ['#9146FF','#00f5d4','#ff4d6d','#ffd76a','#4f9dff','#ff8f6b','#c77dff','#5cf08a'];

/* =====================================================================
   DOM REFS
===================================================================== */
const leaderboardList  = document.getElementById('leaderboardList');
const listCountEl      = document.getElementById('listCount');
const statParticipants = document.getElementById('statParticipants');
const statBits          = document.getElementById('statBits');
const addForm           = document.getElementById('addForm');
const usernameInput     = document.getElementById('usernameInput');
const bitsInput         = document.getElementById('bitsInput');
const usernameError     = document.getElementById('usernameError');
const bitsError         = document.getElementById('bitsError');
const clearAllBtn       = document.getElementById('clearAllBtn');
const confirmModal      = document.getElementById('confirmModal');
const cancelClearBtn    = document.getElementById('cancelClear');
const confirmClearBtn   = document.getElementById('confirmClear');
const toastEl           = document.getElementById('toast');
const modeToggle        = document.getElementById('modeToggle');
const rollBtn           = document.getElementById('rollBtn');
const rollHint          = document.getElementById('rollHint');
const winnerNameEl      = document.getElementById('winnerName');
const winnerSubEl       = document.getElementById('winnerSub');
const wheelCanvas       = document.getElementById('wheelCanvas');
const wheelCtx          = wheelCanvas.getContext('2d');
const channelInput      = document.getElementById('channelInput');
const connectBtn        = document.getElementById('connectBtn');
const disconnectBtn     = document.getElementById('disconnectBtn');
const simulateBtn       = document.getElementById('simulateBtn');
const statusDot         = document.getElementById('statusDot');
const statusText        = document.getElementById('statusText');

/* =====================================================================
   PERSISTENCE
===================================================================== */
function saveToStorage(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(participants)); }
function loadFromStorage(){
  const raw = localStorage.getItem(STORAGE_KEY);
  if (raw) { try { participants = JSON.parse(raw); } catch(e){ participants = [...DEMO_DATA]; } }
  else { participants = [...DEMO_DATA]; saveToStorage(); }
}

/* =====================================================================
   UTILITIES
===================================================================== */
function formatNumber(n){ return n.toLocaleString('en-US'); }
function generateId(){ return crypto.randomUUID ? crypto.randomUUID() : 'id-'+Date.now()+Math.random(); }
function escapeHtml(str){ const d=document.createElement('div'); d.textContent=str; return d.innerHTML; }
function showToast(msg){
  toastEl.textContent = msg;
  toastEl.classList.add('show');
  clearTimeout(showToast._t);
  showToast._t = setTimeout(()=>toastEl.classList.remove('show'), 2600);
}

/* =====================================================================
   RENDERING
===================================================================== */
function renderStats(){
  const totalBits = participants.reduce((s,p)=>s+p.bits,0);
  statParticipants.textContent = formatNumber(participants.length);
  statBits.textContent = formatNumber(totalBits);
}

function renderList(){
  const sorted = [...participants].sort((a,b)=>b.bits-a.bits);
  leaderboardList.innerHTML = '';
  listCountEl.textContent = `${participants.length} user${participants.length!==1?'s':''}`;

  if (sorted.length === 0){
    leaderboardList.innerHTML = `<div class="empty-state">No participants yet.<br>Connect to chat or add manually 🎉</div>`;
  } else {
    sorted.forEach((user,i)=>{
      const row = document.createElement('div');
      row.className = 'user-row';
      row.innerHTML = `
        <div class="user-rank">#${i+1}</div>
        <div class="user-info">
          <div class="user-name">${escapeHtml(user.username)}</div>
          <div class="user-bits">${formatNumber(user.bits)} Bits</div>
        </div>
        <button class="delete-btn" data-id="${user.id}" title="Remove">
          <svg viewBox="0 0 24 24" fill="currentColor"><path d="M9 3v1H4v2h1v13a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V6h1V4h-5V3zm2 5h2v9h-2zm-4 0h2v9H7zm8 0h2v9h-2z"/></svg>
        </button>`;
      leaderboardList.appendChild(row);
    });
    document.querySelectorAll('.delete-btn').forEach(btn=>{
      btn.addEventListener('click', ()=>deleteUser(btn.dataset.id));
    });
  }

  renderStats();
  drawWheel();
  updateRollAvailability();
}

function updateRollAvailability(){
  if (participants.length < 2){
    rollBtn.disabled = true;
    rollHint.textContent = 'Add at least 2 participants to spin.';
  } else {
    rollBtn.disabled = isRolling;
    rollHint.textContent = '';
  }
}

/* =====================================================================
   CRUD
===================================================================== */
function addOrUpdateUser(username, bits){
  const existing = participants.find(p => p.username.toLowerCase() === username.toLowerCase());
  if (existing){
    existing.bits += bits;
    showToast(`💜 ${username} cheered ${formatNumber(bits)} more Bits! (Total: ${formatNumber(existing.bits)})`);
  } else {
    participants.push({ id: generateId(), username, bits });
    showToast(`✅ ${username} joined with ${formatNumber(bits)} Bits!`);
  }
  saveToStorage();
  renderList();
}

function deleteUser(id){
  const user = participants.find(p=>p.id===id);
  participants = participants.filter(p=>p.id!==id);
  saveToStorage();
  renderList();
  if (user) showToast(`🗑️ ${user.username} removed.`);
}

function clearAllUsers(){
  participants = [];
  saveToStorage();
  renderList();
  resetWinnerDisplay();
  showToast('♻️ List has been reset.');
}

/* =====================================================================
   FORM VALIDATION (manual add)
===================================================================== */
function validateUsername(value){
  if (!value.trim()) return 'Username is required.';
  if (value.trim().length < 3) return 'Must be at least 3 characters.';
  if (!/^[a-zA-Z0-9_]+$/.test(value.trim())) return 'Only letters, numbers & underscores allowed.';
  return '';
}
function validateBits(value){
  if (!value) return 'Bits amount is required.';
  const num = Number(value);
  if (!Number.isInteger(num) || num <= 0) return 'Enter a valid positive whole number.';
  return '';
}
function toggleFieldError(input, errorEl, message){
  if (message){ input.classList.add('invalid'); errorEl.textContent = message; }
  else { input.classList.remove('invalid'); errorEl.textContent = ''; }
}
usernameInput.addEventListener('input', ()=> toggleFieldError(usernameInput, usernameError, validateUsername(usernameInput.value)));
bitsInput.addEventListener('input', ()=> toggleFieldError(bitsInput, bitsError, validateBits(bitsInput.value)));

addForm.addEventListener('submit', (e)=>{
  e.preventDefault();
  const uErr = validateUsername(usernameInput.value);
  const bErr = validateBits(bitsInput.value);
  toggleFieldError(usernameInput, usernameError, uErr);
  toggleFieldError(bitsInput, bitsError, bErr);
  if (uErr || bErr) return;
  addOrUpdateUser(usernameInput.value.trim(), parseInt(bitsInput.value,10));
  addForm.reset();
  usernameInput.focus();
});

clearAllBtn.addEventListener('click', ()=>{
  if (participants.length === 0){ showToast('List is already empty.'); return; }
  confirmModal.classList.add('show');
});
cancelClearBtn.addEventListener('click', ()=>confirmModal.classList.remove('show'));
confirmModal.addEventListener('click', (e)=>{ if (e.target===confirmModal) confirmModal.classList.remove('show'); });
confirmClearBtn.addEventListener('click', ()=>{ clearAllUsers(); confirmModal.classList.remove('show'); });

/* =====================================================================
   DRAW MODE TOGGLE
===================================================================== */
modeToggle.addEventListener('click', (e)=>{
  const btn = e.target.closest('button[data-mode]');
  if (!btn) return;
  drawMode = btn.dataset.mode;
  modeToggle.querySelectorAll('button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  modeToggle.classList.toggle('weighted', drawMode==='weighted');
  drawWheel();
});

/* =====================================================================
   WHEEL SEGMENTS + DRAWING
===================================================================== */
function getWheelSegments(){
  const total = drawMode === 'weighted'
    ? participants.reduce((s,p)=>s+p.bits,0)
    : participants.length;

  let angle = 0;
  return participants.map((p,i)=>{
    const weight = drawMode === 'weighted' ? p.bits : 1;
    const sweep = (weight/total) * 360;
    const seg = { user:p, start:angle, end:angle+sweep, color: WHEEL_COLORS[i % WHEEL_COLORS.length] };
    angle += sweep;
    return seg;
  });
}

function drawWheel(){
  const size = wheelCanvas.width;
  const cx = size/2, cy = size/2, r = size/2 - 6;
  wheelCtx.clearRect(0,0,size,size);

  if (participants.length === 0){
    wheelCtx.fillStyle = '#26262c';
    wheelCtx.beginPath();
    wheelCtx.arc(cx,cy,r,0,Math.PI*2);
    wheelCtx.fill();
    wheelCtx.fillStyle = '#a1a1aa';
    wheelCtx.font = 'bold 26px Inter';
    wheelCtx.textAlign = 'center';
    wheelCtx.fillText('No Entries', cx, cy);
    return;
  }

  const segments = getWheelSegments();

  segments.forEach(seg=>{
    const startRad = (seg.start - 90) * Math.PI/180;
    const endRad = (seg.end - 90) * Math.PI/180;

    wheelCtx.beginPath();
    wheelCtx.moveTo(cx,cy);
    wheelCtx.arc(cx,cy,r,startRad,endRad);
    wheelCtx.closePath();
    wheelCtx.fillStyle = seg.color;
    wheelCtx.fill();
    wheelCtx.strokeStyle = 'rgba(0,0,0,0.25)';
    wheelCtx.lineWidth = 2;
    wheelCtx.stroke();

    const midRad = (startRad + endRad) / 2;
    wheelCtx.save();
    wheelCtx.translate(cx + Math.cos(midRad)*(r*0.62), cy + Math.sin(midRad)*(r*0.62));
    wheelCtx.rotate(midRad + Math.PI/2);
    wheelCtx.fillStyle = '#0e0e10';
    wheelCtx.font = 'bold 20px Inter';
    wheelCtx.textAlign = 'center';
    wheelCtx.textBaseline = 'middle';
    const label = seg.user.username.length > 10 ? seg.user.username.slice(0,9)+'…' : seg.user.username;
    wheelCtx.fillText(label, 0, 0);
    wheelCtx.restore();
  });
}

/* =====================================================================
   WINNER SELECTION LOGIC
===================================================================== */
function calculateEqualWinner(list){ return list[Math.floor(Math.random()*list.length)]; }
function calculateWeightedWinner(list){
  const total = list.reduce((s,p)=>s+p.bits,0);
  let r = Math.random()*total;
  for (const u of list){ r -= u.bits; if (r<=0) return u; }
  return list[list.length-1];
}
function pickWinner(){
  return drawMode === 'weighted' ? calculateWeightedWinner(participants) : calculateEqualWinner(participants);
}

function resetWinnerDisplay(){
  winnerNameEl.textContent = '—';
  winnerNameEl.classList.remove('locked');
  winnerSubEl.textContent = 'Add participants and hit spin';
}

/* =====================================================================
   SPIN ANIMATION
===================================================================== */
function spinWheelTo(winner, onComplete){
  const segments = getWheelSegments();
  const seg = segments.find(s => s.user.id === winner.id);
  const midAngle = (seg.start + seg.end) / 2;
  const jitter = (Math.random() - 0.5) * (seg.end - seg.start) * 0.6;
  const targetMid = midAngle + jitter;

  const extraSpins = 6;
  const normalizedCurrent = currentWheelRotation % 360;
  const delta = ((360 - targetMid) - normalizedCurrent + 360) % 360;
  const finalRotation = currentWheelRotation + extraSpins*360 + delta;

  currentWheelRotation = finalRotation;
  wheelCanvas.style.transform = `rotate(${finalRotation}deg)`;

  const handleEnd = () => {
    wheelCanvas.removeEventListener('transitionend', handleEnd);
    onComplete();
  };
  wheelCanvas.addEventListener('transitionend', handleEnd);
}

function celebrateWinner(){
  confetti({ particleCount:140, spread:100, startVelocity:38, origin:{x:0.5,y:0.4}, colors:['#9146FF','#00f5d4','#ffffff'] });
  setTimeout(()=>{
    confetti({ particleCount:60, angle:60, spread:70, origin:{x:0,y:0.8}, colors:['#9146FF','#00f5d4'] });
    confetti({ particleCount:60, angle:120, spread:70, origin:{x:1,y:0.8}, colors:['#9146FF','#00f5d4'] });
  }, 200);
}

rollBtn.addEventListener('click', ()=>{
  if (participants.length < 2 || isRolling) return;
  isRolling = true;
  rollBtn.disabled = true;
  rollBtn.innerHTML = `<svg viewBox="0 0 24 24" fill="currentColor" style="animation:spin 0.8s linear infinite;"><path d="M12 2a10 10 0 1 0 10 10A10 10 0 0 0 12 2zm0 18a8 8 0 1 1 8-8 8 8 0 0 1-8 8z"/></svg> Spinning...`;
  winnerSubEl.textContent = 'Spinning the wheel...';
  winnerNameEl.classList.remove('locked');
  winnerNameEl.textContent = '🎰';

  const winner = pickWinner();

  spinWheelTo(winner, ()=>{
    winnerNameEl.textContent = winner.username;
    winnerNameEl.classList.add('locked');

    if (drawMode === 'weighted'){
      const total = participants.reduce((s,p)=>s+p.bits,0);
      const odds = ((winner.bits/total)*100).toFixed(1);
      winnerSubEl.innerHTML = `Won with <span>${formatNumber(winner.bits)} Bits</span> (${odds}% odds)`;
    } else {
      const odds = (100/participants.length).toFixed(1);
      winnerSubEl.innerHTML = `Equal chance — <span>${odds}% odds</span> among ${participants.length} entries`;
    }

    celebrateWinner();
    showToast(`🏆 ${winner.username} wins the giveaway!`);

    isRolling = false;
    rollBtn.disabled = false;
    rollBtn.innerHTML = `<svg viewBox="0 0 24 24" fill="currentColor"><path d="M12 2a10 10 0 1 0 10 10A10 10 0 0 0 12 2zm0 18a8 8 0 1 1 8-8 8 8 0 0 1-8 8zm1-13h-2v6l5.25 3.15 1-1.64L13 11.5z"/></svg> Spin Again`;
  });
});

const spinKeyframes = document.createElement('style');
spinKeyframes.textContent = `@keyframes spin{from{transform:rotate(0deg);}to{transform:rotate(360deg);}}`;
document.head.appendChild(spinKeyframes);

/* =====================================================================
   ANONYMOUS TWITCH CHAT LISTENER (NO API KEYS, NO OAUTH)
   ---------------------------------------------------------------------
   Twitch chat runs on IRC. Anyone can connect as a read-only "ghost"
   viewer using a random "justinfanXXXXX" username — this is the same
   trick browser extensions and chat overlays use. We watch every
   message for the IRCv3 `bits=` tag, which Twitch automatically
   attaches to any chat message that included a Cheer.
===================================================================== */
function connectToChat(channel){
  channel = channel.toLowerCase().replace('#','').trim();
  if (!channel){ showToast('⚠️ Enter a channel name first.'); return; }

  if (chatSocket) chatSocket.close();

  const ws = new WebSocket('wss://irc-ws.chat.twitch.tv:443');
  chatSocket = ws;
  connectedChannel = channel;

  ws.onopen = () => {
    ws.send('CAP REQ :twitch.tv/tags twitch.tv/commands');
    ws.send('PASS SCHMOOPIIE'); // dummy password, ignored for anonymous logins
    ws.send('NICK justinfan' + Math.floor(Math.random() * 90000 + 10000));
    ws.send('JOIN #' + channel);
  };

  ws.onmessage = (event) => {
    const lines = event.data.split('\r\n').filter(Boolean);
    lines.forEach(handleIRCLine);
  };

  ws.onclose = () => {
    updateConnectionUI(false);
  };

  ws.onerror = () => {
    showToast('⚠️ Could not connect to chat.');
    updateConnectionUI(false);
  };
}

function handleIRCLine(line){
  // Twitch sends periodic keep-alive pings — we must respond or get disconnected
  if (line.startsWith('PING')){
    chatSocket.send('PONG :tmi.twitch.tv');
    return;
  }

  const msg = parseIRCMessage(line);
  if (!msg) return;

  // Numeric 001 = successful connection welcome message
  if (msg.command === '001'){
    updateConnectionUI(true);
    showToast(`🎉 Connected to #${connectedChannel} chat! Watching for cheers...`);
    return;
  }

  // Any chat message carrying a "bits" tag is a Cheer
  if (msg.command === 'PRIVMSG' && msg.tags.bits){
    const bits = parseInt(msg.tags.bits, 10);
    const username = msg.tags['display-name'] || (msg.prefix ? msg.prefix.split('!')[0] : 'Anonymous');
    if (bits > 0){
      addOrUpdateUser(username, bits);
    }
  }
}

// Minimal IRCv3 message parser: handles @tags, :prefix, COMMAND, params
function parseIRCMessage(line){
  let rest = line;
  let tags = {};
  let prefix = '';

  if (rest.startsWith('@')){
    const spaceIdx = rest.indexOf(' ');
    if (spaceIdx === -1) return null;
    const tagStr = rest.slice(1, spaceIdx);
    rest = rest.slice(spaceIdx + 1);
    tagStr.split(';').forEach(pair => {
      const eqIdx = pair.indexOf('=');
      if (eqIdx === -1) return;
      tags[pair.slice(0, eqIdx)] = pair.slice(eqIdx + 1);
    });
  }

  if (rest.startsWith(':')){
    const spaceIdx = rest.indexOf(' ');
    if (spaceIdx === -1) return null;
    prefix = rest.slice(1, spaceIdx);
    rest = rest.slice(spaceIdx + 1);
  }

  const spaceIdx = rest.indexOf(' ');
  const command = spaceIdx === -1 ? rest : rest.slice(0, spaceIdx);

  return { tags, prefix, command };
}

function updateConnectionUI(connected){
  statusDot.classList.toggle('live', connected);
  statusText.textContent = connected
    ? `Connected to #${connectedChannel} — listening for cheers`
    : 'Not connected to any chat';
  connectBtn.style.display = connected ? 'none' : 'inline-flex';
  disconnectBtn.style.display = connected ? 'inline-flex' : 'none';
  channelInput.disabled = connected;
}

connectBtn.addEventListener('click', () => connectToChat(channelInput.value));
channelInput.addEventListener('keydown', (e) => { if (e.key === 'Enter') connectToChat(channelInput.value); });

disconnectBtn.addEventListener('click', () => {
  if (chatSocket) chatSocket.close();
  chatSocket = null;
  connectedChannel = null;
  updateConnectionUI(false);
  showToast('Disconnected from chat.');
});

// For demos/testing without a live stream running
simulateBtn.addEventListener('click', () => {
  const names = ['HypeViewer', 'PurpleRain22', 'BitsGoblin', 'StreamSniper', 'CozyCatLady'];
  const randomName = names[Math.floor(Math.random() * names.length)] + Math.floor(Math.random()*99);
  const randomBits = [100, 250, 500, 1000, 5000][Math.floor(Math.random() * 5)];
  addOrUpdateUser(randomName, randomBits);
});

/* =====================================================================
   INIT
===================================================================== */
function init(){
  loadFromStorage();
  renderList();
}
init();
</script>
</body>
</html>
