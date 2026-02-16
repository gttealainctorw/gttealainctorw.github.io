# gttealainctorw.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Game of Life</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=DM+Mono:wght@300;400;500&family=Syne:wght@400;700;800&display=swap');

*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}

:root{
  --bg:#f5f2ed;--fg:#1a1a18;--muted:#9a9690;--border:#d8d4ce;
  --accent:#2d5a27;--accent-bg:#e8f0e6;--panel:#eeebe5;
  --mono:'DM Mono',monospace;--sans:'Syne',sans-serif;
}

body{background:var(--bg);color:var(--fg);font-family:var(--mono);min-height:100vh;overflow:hidden}

/* ── SCREENS ── */
.screen{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;transition:opacity .5s ease,transform .5s ease}
.screen.out{opacity:0;pointer-events:none;transform:translateY(16px)}

/* ── WELCOME ── */
#welcome{background:var(--bg)}
#bgCanvas{position:absolute;inset:0;opacity:.06;pointer-events:none}
.wc{position:relative;z-index:1;text-align:center;display:flex;flex-direction:column;align-items:center;gap:28px}
.tag{font-size:11px;letter-spacing:.2em;color:var(--muted);text-transform:uppercase}
.big{font-family:var(--sans);font-size:clamp(56px,9vw,104px);font-weight:800;line-height:.9;letter-spacing:-.03em}
.big em{font-style:normal;color:var(--accent)}
.sub{font-size:13px;color:var(--muted);max-width:300px;line-height:1.6;letter-spacing:.03em}

/* ── INTRO ── */
#intro{background:var(--bg);padding:40px 24px}
.intro-wrap{max-width:540px;width:100%;display:flex;flex-direction:column;gap:36px}
.back-btn{font-size:11px;color:var(--muted);letter-spacing:.1em;cursor:pointer;text-transform:uppercase;background:none;border:none;text-align:left;font-family:var(--mono);transition:color .15s}
.back-btn:hover{color:var(--fg)}
.ih{display:flex;flex-direction:column;gap:6px}
.ih small{font-size:11px;color:var(--muted);letter-spacing:.12em}
.ih h2{font-family:var(--sans);font-size:30px;font-weight:700;line-height:1.1}
.rules{border-top:1px solid var(--border)}
.rule{display:flex;gap:18px;padding:14px 0;border-bottom:1px solid var(--border)}
.rule-n{font-size:11px;color:var(--muted);min-width:18px;padding-top:1px}
.rule-t{font-size:13px;line-height:1.5;color:var(--fg)}
.rule-t b{color:var(--accent);font-weight:500}
.intro-ft{display:flex;align-items:center;justify-content:space-between}
.intro-note{font-size:11px;color:var(--muted)}

/* ── BUTTONS ── */
.btn{background:var(--fg);color:var(--bg);border:none;padding:13px 32px;font-family:var(--mono);font-size:12px;letter-spacing:.14em;text-transform:uppercase;cursor:pointer;border-radius:2px;transition:background .15s,transform .1s}
.btn:hover{background:var(--accent);transform:translateY(-1px)}
.btn:active{transform:none}

/* ── GAME ── */
#game{flex-direction:column;background:var(--bg)}

.topbar{width:100%;display:flex;align-items:center;justify-content:space-between;padding:0 20px;border-bottom:1px solid var(--border);height:52px;flex-shrink:0}
.tl{display:flex;align-items:center;gap:18px}
.logo{font-family:var(--sans);font-size:15px;font-weight:700;letter-spacing:-.02em}
.pill{font-size:11px;color:var(--muted);letter-spacing:.04em}
.pill b{color:var(--fg);font-weight:500}
.tr{display:flex;align-items:center;gap:8px}

.ibtn{width:30px;height:30px;background:transparent;border:1px solid var(--border);border-radius:2px;cursor:pointer;display:flex;align-items:center;justify-content:center;font-size:13px;color:var(--fg);transition:all .12s;font-family:var(--mono)}
.ibtn:hover{background:var(--panel);border-color:var(--fg)}
.ibtn.on{background:var(--fg);color:var(--bg);border-color:var(--fg)}

.body{display:flex;flex:1;overflow:hidden;min-height:0}
.canvas-area{flex:1;display:flex;align-items:stretch;justify-content:stretch;padding:0;min-width:0;overflow:hidden}
#c{cursor:crosshair;display:block;width:100%;height:100%}

/* ── SIDEBAR ── */
.sb{width:260px;flex-shrink:0;border-left:1px solid var(--border);display:flex;flex-direction:column;overflow:hidden}
.s{padding:14px;border-bottom:1px solid var(--border)}
.sl{font-size:10px;color:var(--muted);letter-spacing:.14em;text-transform:uppercase;margin-bottom:10px}

.speed-row{display:flex;align-items:center;gap:8px}
.speed-lbl{font-size:10px;color:var(--muted);min-width:40px}
input[type=range]{flex:1;height:2px;background:var(--border);border-radius:1px;-webkit-appearance:none;outline:none}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;appearance:none;width:11px;height:11px;background:var(--fg);border-radius:50%;cursor:pointer}
input[type=range]::-webkit-slider-thumb:hover{background:var(--accent)}

.sz-row{display:flex;align-items:center;gap:7px;margin-top:9px}
.sz-val{font-size:11px;color:var(--fg);min-width:44px;text-align:center}
.szb{width:22px;height:22px;background:transparent;border:1px solid var(--border);border-radius:2px;font-size:13px;cursor:pointer;color:var(--fg);display:flex;align-items:center;justify-content:center;transition:all .1s}
.szb:hover{border-color:var(--fg);background:var(--panel)}

.sg{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.si{display:flex;flex-direction:column;gap:2px}
.sv{font-family:var(--sans);font-size:20px;font-weight:700;line-height:1}
.sk{font-size:10px;color:var(--muted);letter-spacing:.08em;text-transform:uppercase}

.spark{display:flex;align-items:flex-end;gap:2px;height:22px;margin-top:9px}
.spk{flex:1;background:var(--border);border-radius:1px;min-height:2px;transition:height .2s}
.spk.hot{background:var(--accent)}

.pg{display:grid;grid-template-columns:1fr 1fr;gap:5px}
.pb{padding:6px 4px;background:transparent;border:1px solid var(--border);border-radius:2px;font-family:var(--mono);font-size:10px;color:var(--muted);cursor:pointer;text-align:center;letter-spacing:.04em;transition:all .12s}
.pb:hover{color:var(--fg);border-color:var(--fg);background:var(--panel)}

.tf-row{display:flex;gap:5px}
.tf{flex:1;background:transparent;border:1px solid var(--border);border-radius:2px;font-family:var(--mono);font-size:11px;color:var(--fg);padding:7px 8px;outline:none;text-transform:uppercase;letter-spacing:.07em;transition:border-color .15s;min-width:0}
.tf::placeholder{color:var(--muted)}
.tf:focus{border-color:var(--fg)}
.go-btn{padding:7px 10px;background:var(--fg);color:var(--bg);border:none;border-radius:2px;font-family:var(--mono);font-size:11px;cursor:pointer;letter-spacing:.05em;transition:background .12s;flex-shrink:0}
.go-btn:hover{background:var(--accent)}

/* AI panel */
.ai-s{flex:1;display:flex;flex-direction:column;overflow:hidden;padding:14px;min-height:0}
.ai-h{display:flex;align-items:center;justify-content:space-between;margin-bottom:10px;flex-shrink:0}
.ai-badge{font-size:9px;background:var(--accent-bg);color:var(--accent);padding:3px 7px;border-radius:10px;letter-spacing:.08em;text-transform:uppercase;font-weight:500}
.toggle{width:26px;height:15px;background:var(--border);border-radius:7px;border:none;cursor:pointer;position:relative;transition:background .2s;flex-shrink:0}
.toggle::after{content:'';position:absolute;top:2px;left:2px;width:11px;height:11px;background:#fff;border-radius:50%;transition:transform .2s;box-shadow:0 1px 2px rgba(0,0,0,.2)}
.toggle.on{background:var(--accent)}
.toggle.on::after{transform:translateX(11px)}

.ai-log{flex:1;overflow-y:auto;display:flex;flex-direction:column;gap:8px;min-height:0;scrollbar-width:none}
.ai-log::-webkit-scrollbar{display:none}
.ai-msg{font-size:12px;line-height:1.6;color:var(--fg);padding:9px;background:var(--panel);border-radius:2px;border-left:2px solid var(--accent);animation:up .3s ease}
.ai-gen{font-size:10px;color:var(--muted);margin-bottom:3px;letter-spacing:.04em}
.ai-load{font-size:12px;color:var(--muted);padding:6px;letter-spacing:.04em;animation:pulse 1.2s ease infinite}
.ai-empty{font-size:12px;color:var(--muted);line-height:1.6}

@keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}
@keyframes up{from{opacity:0;transform:translateY(5px)}to{opacity:1;transform:none}}

.bar{width:100%;border-top:1px solid var(--border);padding:7px 20px;display:flex;align-items:center;gap:18px;flex-shrink:0;height:38px}
.hint{font-size:10px;color:var(--muted);letter-spacing:.05em}
.hint kbd{background:var(--panel);border:1px solid var(--border);border-radius:2px;padding:1px 4px;font-family:var(--mono);font-size:9px;color:var(--fg)}

@media(max-width:680px){.sb{display:none}}

/* API KEY MODAL */
.modal-overlay{position:fixed;inset:0;background:rgba(245,242,237,.93);backdrop-filter:blur(4px);z-index:999;display:flex;align-items:center;justify-content:center;opacity:0;pointer-events:none;transition:opacity .3s}
.modal-overlay.show{opacity:1;pointer-events:all}
.modal{background:var(--bg);border:1px solid var(--border);border-radius:4px;padding:32px;max-width:420px;width:90%;display:flex;flex-direction:column;gap:18px;box-shadow:0 8px 40px rgba(0,0,0,.08)}
.modal h3{font-family:var(--sans);font-size:18px;font-weight:700}
.modal p{font-size:12px;color:var(--muted);line-height:1.65}
.modal a{color:var(--accent)}
.api-input{width:100%;background:transparent;border:1px solid var(--border);border-radius:2px;font-family:var(--mono);font-size:12px;color:var(--fg);padding:10px 12px;outline:none;letter-spacing:.04em;transition:border-color .15s}
.api-input:focus{border-color:var(--fg)}
.api-input::placeholder{color:var(--muted)}
.modal-row{display:flex;gap:8px;align-items:center;justify-content:flex-end}
.btn-sm{background:var(--fg);color:var(--bg);border:none;padding:10px 20px;font-family:var(--mono);font-size:11px;letter-spacing:.12em;text-transform:uppercase;cursor:pointer;border-radius:2px;transition:background .15s}
.btn-sm:hover{background:var(--accent)}
.btn-ghost{background:transparent;color:var(--muted);border:1px solid var(--border);padding:10px 16px;font-family:var(--mono);font-size:11px;cursor:pointer;border-radius:2px;transition:all .15s}
.btn-ghost:hover{border-color:var(--fg);color:var(--fg)}
.key-tag{font-size:9px;padding:2px 7px;border-radius:10px;letter-spacing:.07em;text-transform:uppercase}
.key-tag.set{background:var(--accent-bg);color:var(--accent)}
.key-tag.unset{background:var(--panel);color:var(--muted)}
.ibtn{width:36px;height:36px;background:transparent;border:1px solid var(--border);border-radius:3px;cursor:pointer;display:flex;align-items:center;justify-content:center;font-size:15px;color:var(--fg);transition:all .12s;font-family:var(--mono)}
.ibtn:hover{background:var(--panel);border-color:var(--fg)}
.ibtn.on{background:var(--fg);color:var(--bg);border-color:var(--fg)}
</style>
</head>
<body>

<!-- WELCOME -->
<div class="screen" id="welcome">
  <canvas id="bgCanvas"></canvas>
  <div class="wc">
    <span class="tag">Conway · 1970</span>
    <h1 class="big">Game<br>of <em>Life</em></h1>
    <p class="sub">A zero-player simulation where four rules produce infinite complexity.</p>
    <button class="btn" onclick="nav('intro')">Start →</button>
  </div>
</div>

<!-- INTRO -->
<div class="screen out" id="intro">
  <div class="intro-wrap">
    <button class="back-btn" onclick="nav('welcome')">← Back</button>
    <div class="ih">
      <small>01 / 04 — The Rules</small>
      <h2>Four rules.<br>Infinite complexity.</h2>
    </div>
    <div class="rules">
      <div class="rule"><span class="rule-n">01</span><span class="rule-t"><b>Underpopulation</b> — A live cell with fewer than 2 neighbors dies.</span></div>
      <div class="rule"><span class="rule-n">02</span><span class="rule-t"><b>Survival</b> — A live cell with 2–3 neighbors lives on.</span></div>
      <div class="rule"><span class="rule-n">03</span><span class="rule-t"><b>Overpopulation</b> — A live cell with more than 3 neighbors dies.</span></div>
      <div class="rule"><span class="rule-n">04</span><span class="rule-t"><b>Reproduction</b> — A dead cell with exactly 3 neighbors is born.</span></div>
    </div>
    <div class="intro-ft">
      <span class="intro-note">AI commentary available in the sidebar</span>
      <button class="btn" onclick="nav('game')">Open simulation →</button>
    </div>
  </div>
</div>

<!-- GAME -->
<div class="screen out" id="game">
  <div class="topbar">
    <div class="tl">
      <span class="logo">Life</span>
      <span class="pill">Gen <b id="tg">0</b></span>
      <span class="pill">Pop <b id="tp">0</b></span>
      <span class="pill" id="ts" style="color:var(--muted)">paused</span>
    </div>
    <div class="tr">
      <button class="ibtn" onclick="step1()" title="Step [→]">▷</button>
      <button class="ibtn" id="playBtn" onclick="toggle()" title="Play [Space]">▶</button>
      <button class="ibtn" onclick="clear_()">✕</button>
      <span id="keyTag" class="key-tag unset" onclick="openModal()" title="Set API Key" style="cursor:pointer;margin-left:4px">no key</span>
    </div>
  </div>

  <div class="body">
    <div class="canvas-area"><canvas id="c"></canvas></div>

    <div class="sb">
      <!-- speed + grid -->
      <div class="s">
        <div class="sl">Playback</div>
        <div class="speed-row">
          <span class="speed-lbl" id="fpsLbl">10 fps</span>
          <input type="range" id="spd" min="1" max="30" value="10" oninput="setFps(+this.value)">
        </div>
        <div class="sz-row">
          <span class="speed-lbl">Grid</span>
          <button class="szb" onclick="resz(-5)">−</button>
          <span class="sz-val" id="szVal">50×34</span>
          <button class="szb" onclick="resz(5)">+</button>
        </div>
      </div>

      <!-- stats -->
      <div class="s">
        <div class="sl">Statistics</div>
        <div class="sg">
          <div class="si"><span class="sv" id="sg">0</span><span class="sk">Generation</span></div>
          <div class="si"><span class="sv" id="sp">0</span><span class="sk">Population</span></div>
          <div class="si"><span class="sv" id="sb">0</span><span class="sk">Born</span></div>
          <div class="si"><span class="sv" id="sd">0</span><span class="sk">Died</span></div>
        </div>
        <div class="spark" id="spark"></div>
      </div>

      <!-- patterns -->
      <div class="s">
        <div class="sl">Patterns</div>
        <div class="pg">
          <button class="pb" onclick="pat('glider')">Glider</button>
          <button class="pb" onclick="pat('blinker')">Blinker</button>
          <button class="pb" onclick="pat('pulsar')">Pulsar</button>
          <button class="pb" onclick="pat('lwss')">LWSS</button>
          <button class="pb" onclick="pat('rpento')">R-Pento</button>
          <button class="pb" onclick="pat('random')">Random</button>
        </div>
      </div>

      <!-- word -->
      <div class="s">
        <div class="sl">Word → Cells</div>
        <div class="tf-row">
          <input class="tf" id="wi" placeholder="HELLO" maxlength="6">
          <button class="go-btn" onclick="stamp()">→</button>
        </div>
      </div>

      <!-- AI -->
      <div class="ai-s">
        <div class="ai-h">
          <div style="display:flex;align-items:center;gap:7px">
            <span class="sl" style="margin:0">AI Commentary</span>
            <span class="ai-badge">Claude</span>
          </div>
          <button class="toggle" id="aiTgl" onclick="toggleAI()"></button>
        </div>
        <div class="ai-log" id="alog">
          <span class="ai-empty">Enable the toggle to get real-time AI analysis of the simulation.</span>
        </div>
      </div>
    </div>
  </div>

  <div class="bar">
    <span class="hint"><kbd>Space</kbd> play</span>
    <span class="hint"><kbd>→</kbd> step</span>
    <span class="hint"><kbd>C</kbd> clear</span>
    <span class="hint"><kbd>R</kbd> random</span>
    <span class="hint">click+drag to draw</span>
  </div>
</div>


<!-- API KEY MODAL -->
<div class="modal-overlay" id="apiModal">
  <div class="modal">
    <div style="display:flex;align-items:flex-start;justify-content:space-between;gap:12px">
      <div>
        <h3>AI Commentary</h3>
        <p style="margin-top:6px;font-size:11px;color:var(--muted)">Powered by Claude · key stored locally only</p>
      </div>
      <button onclick="cancelModal()" style="background:none;border:none;cursor:pointer;font-size:18px;color:var(--muted);line-height:1;padding:0;margin-top:2px">×</button>
    </div>
    <p>Paste your Anthropic API key below. Get one free at <a href="https://console.anthropic.com" target="_blank">console.anthropic.com</a> → API Keys → Create Key.</p>
    <div style="position:relative">
      <input class="api-input" id="apiKeyInput" type="password" placeholder="sk-ant-api03-..." style="padding-right:44px">
      <button onclick="toggleKeyVis()" id="visBtn" style="position:absolute;right:8px;top:50%;transform:translateY(-50%);background:none;border:none;cursor:pointer;font-size:12px;color:var(--muted);font-family:var(--mono)">show</button>
    </div>
    <div class="modal-row">
      <button class="btn-ghost" onclick="cancelModal()">Cancel — disable AI</button>
      <button class="btn-sm" onclick="saveKey()">Save →</button>
    </div>
  </div>
</div>

<script>
// ── NAV ──────────────────────────────────────
const $ = id => document.getElementById(id);
function nav(to) {
  document.querySelectorAll('.screen').forEach(s => s.classList.add('out'));
  $(to).classList.remove('out');
  if (to === 'welcome') bgStart();
  if (to === 'game') { if(bgRaf){cancelAnimationFrame(bgRaf);bgRaf=null;} if(!inited) boot(); }
}

// ── BACKGROUND ANIMATION ─────────────────────
let bgRaf = null;
function bgStart() {
  const cv = $('bgCanvas'); cv.width = innerWidth; cv.height = innerHeight;
  const cx = cv.getContext('2d'), sz = 18;
  const W = Math.ceil(cv.width/sz), H = Math.ceil(cv.height/sz);
  let g = Array.from({length:H}, () => Uint8Array.from({length:W}, () => Math.random()<.22?1:0));
  let ng = Array.from({length:H}, () => new Uint8Array(W));
  const nb = (r,c) => {let n=0;for(let dr=-1;dr<=1;dr++)for(let dc=-1;dc<=1;dc++){if(!dr&&!dc)continue;n+=g[(r+dr+H)%H][(c+dc+W)%W];}return n;};
  const tick = () => {
    cx.clearRect(0,0,cv.width,cv.height);
    for(let r=0;r<H;r++)for(let c=0;c<W;c++){const n=nb(r,c);ng[r][c]=g[r][c]?(n===2||n===3?1:0):(n===3?1:0);if(g[r][c]){cx.fillStyle='#1a1a18';cx.fillRect(c*sz+2,r*sz+2,sz-4,sz-4);}}
    [g,ng]=[ng,g]; bgRaf=requestAnimationFrame(tick);
  };
  if(bgRaf) cancelAnimationFrame(bgRaf);
  bgRaf = requestAnimationFrame(tick);
}

// ── GAME CORE ────────────────────────────────
const cv = $('c'), ctx = cv.getContext('2d');
let C=50, R=34, SZ=16;
let grid, next;
let running=false, timer=null, fps=10;
let gen=0, pop=0, brn=0, ded=0;
let hist = new Array(20).fill(0);
let drawing=false, dv=1;
let inited=false;
let aiOn=false, aiPending=false;
const AI_EVERY=5;

function boot() {
  inited=true;
  fit(); mkGrid();
  pat('glider');
  bindEvents();
}

function mkGrid() {
  grid = Array.from({length:R}, () => new Uint8Array(C));
  next = Array.from({length:R}, () => new Uint8Array(C));
}

function fit() {
  const area = document.querySelector('.canvas-area');
  const mw = area.clientWidth;
  const mh = area.clientHeight;
  // fill the area maximally, keeping integer cell size
  SZ = Math.max(8, Math.floor(Math.min(mw / C, mh / R)));
  cv.width  = C * SZ;
  cv.height = R * SZ;
  // stretch canvas CSS to fill parent
  cv.style.width  = mw + 'px';
  cv.style.height = mh + 'px';
}

// render — minimal, fast
function draw() {
  ctx.fillStyle='#f5f2ed'; ctx.fillRect(0,0,cv.width,cv.height);
  ctx.strokeStyle='rgba(0,0,0,0.06)'; ctx.lineWidth=.5;
  for(let x=0;x<=C;x++){ctx.beginPath();ctx.moveTo(x*SZ+.5,0);ctx.lineTo(x*SZ+.5,cv.height);ctx.stroke();}
  for(let y=0;y<=R;y++){ctx.beginPath();ctx.moveTo(0,y*SZ+.5);ctx.lineTo(cv.width,y*SZ+.5);ctx.stroke();}
  ctx.fillStyle='#1a1a18';
  for(let r=0;r<R;r++) for(let c=0;c<C;c++) {
    if(!grid[r][c]) continue;
    const x=c*SZ+1, y=r*SZ+1, s=SZ-2;
    if(SZ>=12){ctx.beginPath();ctx.roundRect(x,y,s,s,2);ctx.fill();}
    else ctx.fillRect(x,y,s,s);
  }
}

let rafQ=false;
const qDraw = () => { if(!rafQ){rafQ=true;requestAnimationFrame(()=>{draw();rafQ=false;});} };

function nb(r,c) {
  let n=0;
  for(let dr=-1;dr<=1;dr++) for(let dc=-1;dc<=1;dc++) {
    if(!dr&&!dc) continue;
    n+=grid[(r+dr+R)%R][(c+dc+C)%C];
  }
  return n;
}

function tick() {
  let b=0,d=0,p=0;
  for(let r=0;r<R;r++) for(let c=0;c<C;c++) {
    const n=nb(r,c), a=grid[r][c];
    if(a){if(n===2||n===3){next[r][c]=1;p++;}else{next[r][c]=0;d++;}}
    else{if(n===3){next[r][c]=1;p++;b++;}else next[r][c]=0;}
  }
  [grid,next]=[next,grid];
  gen++;pop=p;brn=b;ded=d;
  hist.push(pop); if(hist.length>20) hist.shift();
  updateStats(); qDraw();
  if(aiOn&&!aiPending&&gen%AI_EVERY===0) aiComment();
}

function updateStats() {
  $('tg').textContent=$('sg').textContent=gen;
  $('tp').textContent=$('sp').textContent=pop;
  $('sb').textContent=brn; $('sd').textContent=ded;
  const el=$('spark'), mx=Math.max(...hist,1);
  el.innerHTML='';
  hist.forEach((v,i)=>{
    const b=document.createElement('div');
    b.className='spk'+(i===hist.length-1?' hot':'');
    b.style.height=`${Math.max(2,(v/mx)*22)}px`;
    el.appendChild(b);
  });
}

// ── GAME LOOP ─────────────────────────────────
function start() {
  if(running)return; running=true;
  $('playBtn').textContent='⏸'; $('playBtn').classList.add('on');
  $('ts').textContent='running'; $('ts').style.color='var(--accent)';
  const loop=()=>{tick();timer=setTimeout(loop,1000/fps);};
  timer=setTimeout(loop,1000/fps);
}
function stop() {
  running=false; clearTimeout(timer);
  $('playBtn').textContent='▶'; $('playBtn').classList.remove('on');
  $('ts').textContent='paused'; $('ts').style.color='var(--muted)';
}
function toggle(){running?stop():start();}
function step1(){if(running)stop();tick();}
function clear_(){stop();mkGrid();gen=pop=brn=ded=0;hist=new Array(20).fill(0);updateStats();qDraw();}
function setFps(v){fps=v;$('fpsLbl').textContent=`${v} fps`;if(running){stop();start();}}

// ── GRID RESIZE ───────────────────────────────
function resz(d) {
  const was=running; if(was)stop();
  C=Math.max(10,Math.min(80,C+d)); R=Math.max(8,Math.min(60,Math.round(C*.7)));
  fit(); mkGrid(); gen=pop=brn=ded=0; hist=new Array(20).fill(0);
  updateStats(); qDraw(); $('szVal').textContent=`${C}×${R}`;
  if(was)start();
}

// ── PATTERNS ─────────────────────────────────
const PATS = {
  glider: [[0,1],[1,2],[2,0],[2,1],[2,2]],
  blinker:[[0,0],[0,1],[0,2]],
  lwss:   [[0,1],[0,4],[1,0],[2,0],[2,4],[3,0],[3,1],[3,2],[3,3]],
  rpento: [[0,1],[0,2],[1,0],[1,1],[2,1]],
  pulsar: [[0,2],[0,3],[0,4],[0,8],[0,9],[0,10],[2,0],[2,5],[2,7],[2,12],[3,0],[3,5],[3,7],[3,12],[4,0],[4,5],[4,7],[4,12],[5,2],[5,3],[5,4],[5,8],[5,9],[5,10],[7,2],[7,3],[7,4],[7,8],[7,9],[7,10],[8,0],[8,5],[8,7],[8,12],[9,0],[9,5],[9,7],[9,12],[10,0],[10,5],[10,7],[10,12],[12,2],[12,3],[12,4],[12,8],[12,9],[12,10]],
};
function pat(name) {
  clear_();
  if(name==='random'){for(let r=0;r<R;r++)for(let c=0;c<C;c++)grid[r][c]=Math.random()<.3?1:0;pop=grid.reduce((s,row)=>s+row.reduce((a,b)=>a+b,0),0);updateStats();qDraw();return;}
  const p=PATS[name]; if(!p)return;
  const mr=Math.max(...p.map(x=>x[0])), mc=Math.max(...p.map(x=>x[1]));
  const sr=Math.floor((R-mr)/2), sc=Math.floor((C-mc)/2);
  p.forEach(([dr,dc])=>{const r=sr+dr,c=sc+dc;if(r>=0&&r<R&&c>=0&&c<C)grid[r][c]=1;});
  pop=grid.reduce((s,row)=>s+row.reduce((a,b)=>a+b,0),0);
  updateStats(); qDraw();
}

// ── PIXEL FONT (5×5) ─────────────────────────
const FONT={
  'A':['01110','10001','11111','10001','10001'],'B':['11110','10001','11110','10001','11110'],
  'C':['01111','10000','10000','10000','01111'],'D':['11110','10001','10001','10001','11110'],
  'E':['11111','10000','11110','10000','11111'],'F':['11111','10000','11110','10000','10000'],
  'G':['01111','10000','10111','10001','01111'],'H':['10001','10001','11111','10001','10001'],
  'I':['11111','00100','00100','00100','11111'],'J':['00011','00001','00001','10001','01110'],
  'K':['10001','10010','11100','10010','10001'],'L':['10000','10000','10000','10000','11111'],
  'M':['10001','11011','10101','10001','10001'],'N':['10001','11001','10101','10011','10001'],
  'O':['01110','10001','10001','10001','01110'],'P':['11110','10001','11110','10000','10000'],
  'Q':['01110','10001','10101','10010','01101'],'R':['11110','10001','11110','10010','10001'],
  'S':['01111','10000','01110','00001','11110'],'T':['11111','00100','00100','00100','00100'],
  'U':['10001','10001','10001','10001','01110'],'V':['10001','10001','10001','01010','00100'],
  'W':['10001','10001','10101','11011','10001'],'X':['10001','01010','00100','01010','10001'],
  'Y':['10001','01010','00100','00100','00100'],'Z':['11111','00010','00100','01000','11111'],
  '0':['01110','10011','10101','11001','01110'],'1':['00100','01100','00100','00100','01110'],
  '2':['01110','10001','00110','01000','11111'],'3':['11110','00001','01110','00001','11110'],
  '4':['00010','00110','01010','11111','00010'],'5':['11111','10000','11110','00001','11110'],
  '6':['01110','10000','11110','10001','01110'],'7':['11111','00001','00010','00100','01000'],
  '8':['01110','10001','01110','10001','01110'],'9':['01110','10001','01111','00001','01110'],
  '!':['00100','00100','00100','00000','00100'],'?':['01110','10001','00110','00000','00100'],
  ' ':['00000','00000','00000','00000','00000'],
};

let stampTimer=null;
function stamp() {
  const chars=$('wi').value.toUpperCase().trim().split('').filter(ch=>FONT[ch]);
  if(!chars.length)return;
  const CW=5,GAP=1,CH=5;
  const tw=chars.length*(CW+GAP)-GAP;
  const sr=Math.floor((R-CH)/2), sc=Math.floor((C-tw)/2);
  const tgt=Array.from({length:R},()=>new Uint8Array(C));
  chars.forEach((ch,i)=>FONT[ch].forEach((row,dr)=>row.split('').forEach((b,dc)=>{if(b==='1'){const r=sr+dr,c=sc+i*(CW+GAP)+dc;if(r>=0&&r<R&&c>=0&&c<C)tgt[r][c]=1;}})));
  stop(); mkGrid();
  const on=tgt.reduce((s,row)=>s+row.reduce((a,b)=>a+b,0),0);
  const all=[]; for(let r=0;r<R;r++)for(let c=0;c<C;c++)all.push([r,c]);
  for(let i=all.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[all[i],all[j]]=[all[j],all[i]];}
  for(let k=0;k<on;k++)grid[all[k][0]][all[k][1]]=1;
  qDraw();
  if(stampTimer)clearInterval(stampTimer);
  stampTimer=setInterval(()=>{
    const off=[],on2=[];
    for(let r=0;r<R;r++)for(let c=0;c<C;c++){if(grid[r][c]&&!tgt[r][c])off.push([r,c]);if(!grid[r][c]&&tgt[r][c])on2.push([r,c]);}
    if(!off.length&&!on2.length){clearInterval(stampTimer);stampTimer=null;return;}
    const sh=a=>{for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];}};
    sh(off);sh(on2);
    const n=Math.max(1,Math.ceil(Math.max(off.length,on2.length)*.12));
    const f=Math.min(n,off.length,on2.length);
    for(let i=0;i<f;i++){grid[off[i][0]][off[i][1]]=0;grid[on2[i][0]][on2[i][1]]=1;}
    for(let i=f;i<Math.min(n,on2.length);i++)grid[on2[i][0]][on2[i][1]]=1;
    for(let i=f;i<Math.min(n,off.length);i++)grid[off[i][0]][off[i][1]]=0;
    qDraw();
  },40);
}

// ── DRAW INPUT ───────────────────────────────
function cell(e) {
  const rect=cv.getBoundingClientRect(), sx=cv.width/rect.width, sy=cv.height/rect.height;
  return{r:Math.floor((e.clientY-rect.top)*sy/SZ),c:Math.floor((e.clientX-rect.left)*sx/SZ)};
}
function bindEvents() {
  cv.addEventListener('mousedown',e=>{const{r,c}=cell(e);if(r<0||r>=R||c<0||c>=C)return;dv=grid[r][c]?0:1;grid[r][c]=dv;drawing=true;qDraw();e.preventDefault();});
  cv.addEventListener('mousemove',e=>{if(!drawing)return;const{r,c}=cell(e);if(r<0||r>=R||c<0||c>=C)return;grid[r][c]=dv;qDraw();});
  cv.addEventListener('mouseup',()=>drawing=false);
  cv.addEventListener('mouseleave',()=>drawing=false);
  cv.addEventListener('touchstart',e=>{e.preventDefault();const{r,c}=cell(e.touches[0]);if(r<0||r>=R||c<0||c>=C)return;dv=grid[r][c]?0:1;grid[r][c]=dv;drawing=true;qDraw();},{passive:false});
  cv.addEventListener('touchmove',e=>{e.preventDefault();if(!drawing)return;const{r,c}=cell(e.touches[0]);if(r<0||r>=R||c<0||c>=C)return;grid[r][c]=dv;qDraw();},{passive:false});
  cv.addEventListener('touchend',()=>drawing=false);
  $('wi').addEventListener('keydown',e=>{if(e.key==='Enter')stamp();});
  document.addEventListener('keydown',e=>{
    if(['INPUT','TEXTAREA'].includes(document.activeElement.tagName))return;
    if(e.code==='Space'){e.preventDefault();toggle();}
    if(e.code==='ArrowRight')step1();
    if(e.code==='KeyC')clear_();
    if(e.code==='KeyR')pat('random');
  });
  window.addEventListener('resize',()=>{fit();qDraw();});
}

// ── AI COMMENTARY ────────────────────────────
function toggleAI() {
  aiOn=!aiOn;
  $('aiTgl').classList.toggle('on',aiOn);
  if(aiOn&&$('alog').children.length<=1) {
    $('alog').innerHTML='<span class="ai-load">Watching the simulation…</span>';
  }
}

async function aiComment() {
  if(!aiOn||aiPending)return;
  if(!API_KEY){openModal();return;}
  aiPending=true;
  const snap={gen,pop,brn,ded,density:((pop/(R*C))*100).toFixed(1)};
  const log=$('alog');
  const loader=document.createElement('div');
  loader.className='ai-load';
  loader.textContent=`Analyzing gen ${snap.gen}…`;
  log.appendChild(loader); log.scrollTop=log.scrollHeight;
  try {
    const res=await fetch('https://api.anthropic.com/v1/messages',{
      method:'POST',
      headers:{'Content-Type':'application/json','x-api-key':API_KEY,'anthropic-version':'2023-06-01','anthropic-dangerous-direct-browser-access':'true'},
      body:JSON.stringify({
        model:'claude-sonnet-4-20250514',
        max_tokens:100,
        system:'You are a sharp, concise narrator for Conway\'s Game of Life. Analyze each generation in 1-2 sentences. Be specific and insightful. No filler.',
        messages:[{role:'user',content:`Gen ${snap.gen}. Pop: ${snap.pop} (${snap.density}% density). Born: ${snap.brn}. Died: ${snap.ded}. Grid ${C}×${R}. What's happening?`}]
      })
    });
    const data=await res.json();
    const text=data?.content?.[0]?.text||'—';
    log.removeChild(loader);
    const msg=document.createElement('div'); msg.className='ai-msg';
    msg.innerHTML=`<div class="ai-gen">Gen ${snap.gen}</div>${text}`;
    log.appendChild(msg);
    while(log.children.length>5)log.removeChild(log.firstChild);
    log.scrollTop=log.scrollHeight;
  } catch{ log.removeChild(loader); }
  aiPending=false;
}

// ── API KEY ──────────────────────────────────
let API_KEY = localStorage.getItem('gol_api_key') || '';

function openModal() {
  $('apiKeyInput').value = API_KEY;
  $('apiModal').classList.add('show');
  setTimeout(() => $('apiKeyInput').focus(), 100);
}
function closeModal() { $('apiModal').classList.remove('show'); }
function cancelModal() {
  // turn off AI if no key is set so user isn't stuck
  if (!API_KEY) {
    aiOn = false;
    $('aiTgl').classList.remove('on');
    $('alog').innerHTML = '<span class="ai-empty">Enable the toggle to get real-time AI analysis of the simulation.</span>';
  }
  closeModal();
}
function toggleKeyVis() {
  const inp = $('apiKeyInput');
  const btn = $('visBtn');
  if (inp.type === 'password') { inp.type = 'text'; btn.textContent = 'hide'; }
  else { inp.type = 'password'; btn.textContent = 'show'; }
}
function saveKey() {
  const val = $('apiKeyInput').value.trim();
  API_KEY = val;
  if (val) localStorage.setItem('gol_api_key', val);
  else localStorage.removeItem('gol_api_key');
  updateKeyTag();
  closeModal();
}
function updateKeyTag() {
  const tag = $('keyTag');
  if (API_KEY) { tag.textContent = 'key ✓'; tag.className = 'key-tag set'; }
  else { tag.textContent = 'no key'; tag.className = 'key-tag unset'; }
}
$('apiModal').addEventListener('click', e => { if(e.target===$('apiModal')) cancelModal(); });
document.addEventListener('keydown', e => { if(e.key==='Escape' && $('apiModal').classList.contains('show')) cancelModal(); });

// ── BOOT ──────────────────────────────────────
updateKeyTag();
bgStart();           // run background on load
nav('welcome');
</script>
</body>
</html>
