<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Damas Lineales PRO</title>
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  :root {
    --bg:#0a0a0f; --surface:#111118; --border:#1e1e2e;
    --blue:#3b82f6; --blue-glow:#60a5fa;
    --red:#ef4444; --red-glow:#f87171;
    --gold:#fbbf24; --text:#e2e8f0; --muted:#475569;
    --cell:52px;
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  body{font-family:'Share Tech Mono',monospace;background:var(--bg);color:var(--text);min-height:100vh;display:flex;flex-direction:column;align-items:center;padding:16px;gap:12px;}
  body::before{content:'';position:fixed;inset:0;background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(59,130,246,.015) 2px,rgba(59,130,246,.015) 4px),repeating-linear-gradient(90deg,transparent,transparent 2px,rgba(59,130,246,.015) 2px,rgba(59,130,246,.015) 4px);pointer-events:none;z-index:0;}
  h1{font-family:'Orbitron',monospace;font-weight:900;font-size:clamp(18px,4vw,30px);letter-spacing:4px;background:linear-gradient(135deg,var(--blue-glow),var(--gold),var(--blue-glow));background-size:200% auto;-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;animation:shimmer 3s linear infinite;text-align:center;}
  .subtitle{font-size:9px;letter-spacing:5px;color:var(--muted);text-align:center;}
  @keyframes shimmer{to{background-position:200% center;}}

  /* ── MAIN LAYOUT ── */
  .main{display:flex;gap:12px;align-items:flex-start;position:relative;z-index:1;}

  /* ── PRISON COLUMNS ── */
  .prison-col{display:flex;flex-direction:column;gap:10px;width:90px;}
  .prison{background:var(--surface);border:1px solid var(--border);border-radius:8px;padding:8px 10px;display:flex;flex-direction:column;gap:6px;}
  .prison.blue-p{border-color:rgba(59,130,246,.35);}
  .prison.red-p {border-color:rgba(239,68,68,.35);}
  .prison-title{font-family:'Orbitron',monospace;font-size:7px;letter-spacing:2px;color:var(--muted);text-align:center;}
  .prison-pips{display:flex;flex-wrap:wrap;gap:3px;min-height:14px;}
  .prison-pip{width:14px;height:14px;border-radius:50%;}
  .prison-pip::before{content:'';display:block;width:100%;height:100%;border-radius:50%;background:rgba(255,255,255,.12);}
  .prison-pip.blue{background:radial-gradient(circle at 35% 35%,#60a5fa,#1d4ed8);box-shadow:0 0 4px rgba(59,130,246,.5);}
  .prison-pip.red {background:radial-gradient(circle at 35% 35%,#f87171,#b91c1c);box-shadow:0 0 4px rgba(239,68,68,.5);}
  .prison-empty{font-size:8px;color:var(--muted);}

  /* ── CENTER COLUMN ── */
  .center{display:flex;flex-direction:column;align-items:center;gap:10px;}

  /* ── PLAYER PANELS ── */
  .players-row{display:flex;gap:8px;width:100%;}
  .panel{flex:1;background:var(--surface);border:1px solid var(--border);border-radius:8px;padding:10px 12px;display:flex;flex-direction:column;gap:5px;transition:border-color .3s,box-shadow .3s;}
  .panel.ab{border-color:var(--blue);box-shadow:0 0 14px rgba(59,130,246,.2);}
  .panel.ar{border-color:var(--red); box-shadow:0 0 14px rgba(239,68,68,.2);}
  .pname{font-family:'Orbitron',monospace;font-size:10px;font-weight:700;letter-spacing:2px;display:flex;align-items:center;gap:6px;}
  .pdot{width:8px;height:8px;border-radius:50%;}
  .pdot.blue{background:var(--blue);box-shadow:0 0 6px var(--blue);}
  .pdot.red {background:var(--red); box-shadow:0 0 6px var(--red);}
  .pstats{display:flex;gap:10px;}
  .pstat .lbl{font-size:7px;color:var(--muted);letter-spacing:1px;}
  .pstat .val{font-family:'Orbitron',monospace;font-size:12px;color:var(--gold);}
  .badge{font-size:8px;letter-spacing:2px;padding:2px 7px;border-radius:3px;align-self:flex-start;}
  .badge.blue{background:rgba(59,130,246,.15);color:var(--blue-glow);border:1px solid rgba(59,130,246,.3);}
  .badge.red {background:rgba(239,68,68,.15); color:var(--red-glow); border:1px solid rgba(239,68,68,.3);}
  .badge.wait{background:transparent;color:var(--muted);border:1px solid var(--border);}

  /* ── BOARD ── */
  .board-wrap{background:var(--surface);border:1px solid var(--border);border-radius:10px;padding:14px;box-shadow:0 0 30px rgba(59,130,246,.06);}
  #board{display:grid;grid-template-columns:repeat(5, var(--cell));grid-template-rows:repeat(10, var(--cell));gap:3px;}

  .cell{width:var(--cell);height:var(--cell);background:#16161f;border:1px solid #1e1e30;border-radius:4px;cursor:pointer;position:relative;display:flex;align-items:center;justify-content:center;transition:background .12s,border-color .12s,box-shadow .12s;}
  .cell:hover{background:#1c1c2a;border-color:#2e2e48;}
  .cell.valid{background:rgba(59,130,246,.09);border-color:rgba(59,130,246,.4);}
  .cell.valid:hover{background:rgba(59,130,246,.15);box-shadow:0 0 8px rgba(59,130,246,.25);}
  .cell.valid::after{content:'';width:10px;height:10px;border-radius:50%;background:rgba(59,130,246,.6);position:absolute;}
  .cell.capture{background:rgba(239,68,68,.09);border-color:rgba(239,68,68,.45);}
  .cell.capture:hover{background:rgba(239,68,68,.15);box-shadow:0 0 8px rgba(239,68,68,.25);}
  .cell.capture::after{content:'✕';font-size:13px;color:rgba(239,68,68,.9);position:absolute;}
  .cell.selected{background:rgba(251,191,36,.1);border-color:var(--gold);box-shadow:0 0 12px rgba(251,191,36,.22);}

  /* Pieces */
  .piece{width:38px;height:38px;border-radius:50%;position:relative;cursor:pointer;transition:transform .12s;}
  .piece::before{content:'';position:absolute;top:4px;left:4px;right:4px;bottom:4px;border-radius:50%;background:rgba(255,255,255,.15);}
  .piece::after{content:'';position:absolute;top:5px;left:8px;width:10px;height:5px;border-radius:50%;background:rgba(255,255,255,.28);}
  .piece.blue{background:radial-gradient(circle at 35% 35%,#60a5fa,#1d4ed8);box-shadow:0 3px 10px rgba(59,130,246,.55),0 0 18px rgba(59,130,246,.18);}
  .piece.red {background:radial-gradient(circle at 35% 35%,#f87171,#b91c1c);box-shadow:0 3px 10px rgba(239,68,68,.55), 0 0 18px rgba(239,68,68,.18);}
  .piece:hover{transform:scale(1.1);}

  @keyframes land{0%{transform:scale(.4);opacity:0;}70%{transform:scale(1.18);}100%{transform:scale(1);opacity:1;}}
  .land{animation:land .28s ease-out forwards;}
  @keyframes cap{0%{transform:scale(1);opacity:1;}50%{transform:scale(1.3) rotate(20deg);opacity:.4;}100%{transform:scale(0);opacity:0;}}
  .cap{animation:cap .35s ease-out forwards;}
  @keyframes rescue{0%{transform:scale(.3) rotate(-20deg);opacity:0;}65%{transform:scale(1.2);}100%{transform:scale(1);opacity:1;}}
  .rescue{animation:rescue .4s ease-out forwards;}

  /* Message */
  #msg{font-family:'Orbitron',monospace;font-size:15px;font-weight:700;letter-spacing:3px;min-height:22px;text-align:center;}
  #msg.wb{color:var(--blue-glow);text-shadow:0 0 18px var(--blue);animation:wf .6s ease infinite alternate;}
  #msg.wr{color:var(--red-glow); text-shadow:0 0 18px var(--red); animation:wf .6s ease infinite alternate;}
  @keyframes wf{from{opacity:1;}to{opacity:.5;}}

  /* Legend */
  .legend{display:flex;gap:14px;justify-content:center;flex-wrap:wrap;font-size:9px;color:var(--muted);}
  .legend span{display:flex;align-items:center;gap:4px;}
  .lsq{width:9px;height:9px;border-radius:2px;border:1px solid;}
  .lsq.m{background:rgba(59,130,246,.2);border-color:rgba(59,130,246,.5);}
  .lsq.c{background:rgba(239,68,68,.2);border-color:rgba(239,68,68,.5);}

  .btn{font-family:'Orbitron',monospace;font-size:10px;font-weight:700;letter-spacing:2px;padding:8px 18px;border:1px solid var(--blue);border-radius:4px;cursor:pointer;background:transparent;color:var(--blue-glow);transition:all .2s;}
  .btn:hover{background:rgba(59,130,246,.12);box-shadow:0 0 14px rgba(59,130,246,.3);}

  /* Ranking */
  .ranking{width:100%;max-width:700px;background:var(--surface);border:1px solid var(--border);border-radius:8px;padding:12px 18px;position:relative;z-index:1;}
  .rtitle{font-family:'Orbitron',monospace;font-size:9px;letter-spacing:4px;color:var(--muted);margin-bottom:8px;display:flex;align-items:center;gap:8px;}
  .rtitle::after{content:'';flex:1;height:1px;background:var(--border);}
  .rrow{display:flex;align-items:center;justify-content:space-between;padding:5px 10px;background:#0d0d14;border-radius:4px;border:1px solid var(--border);margin-bottom:5px;}
  .rplayer{display:flex;align-items:center;gap:7px;font-size:11px;}
  .rdot{width:7px;height:7px;border-radius:50%;}
  .rdot.blue{background:var(--blue);box-shadow:0 0 5px var(--blue);}
  .rdot.red {background:var(--red); box-shadow:0 0 5px var(--red);}
  .rwins{font-family:'Orbitron',monospace;font-size:13px;color:var(--gold);font-weight:700;}
  .rempty{color:var(--muted);font-size:11px;text-align:center;padding:6px;}
</style>
</head>
<body>

<h1>DAMAS LINEALES</h1>
<div class="subtitle">5×10 · TORRE + PEÓN · CAPTURA & RESCATE</div>

<!-- Player panels -->
<div class="players-row" style="max-width:700px;width:100%;position:relative;z-index:1;">
  <div class="panel ab" id="panelBlue">
    <div class="pname"><div class="pdot blue"></div>AZUL</div>
    <div class="pstats">
      <div class="pstat"><div class="lbl">EN META</div><div class="val" id="scoreBlue">0/10</div></div>
      <div class="pstat"><div class="lbl">PRESOS</div><div class="val" id="prisonerBlue">0</div></div>
    </div>
    <div class="badge blue" id="badgeBlue">▶ TU TURNO</div>
  </div>
  <div class="panel" id="panelRed">
    <div class="pname"><div class="pdot red"></div>ROJO</div>
    <div class="pstats">
      <div class="pstat"><div class="lbl">EN META</div><div class="val" id="scoreRed">0/10</div></div>
      <div class="pstat"><div class="lbl">PRESOS</div><div class="val" id="prisonerRed">0</div></div>
    </div>
    <div class="badge wait" id="badgeRed">ESPERA</div>
  </div>
</div>

<!-- Main layout -->
<div class="main">

  <!-- Left prisons -->
  <div class="prison-col">
    <div class="prison blue-p">
      <div class="prison-title">🔒 PRESOS AZUL</div>
      <div class="prison-pips" id="ppBlueL"></div>
    </div>
    <div class="prison red-p">
      <div class="prison-title">🔒 PRESOS ROJO</div>
      <div class="prison-pips" id="ppRedL"></div>
    </div>
  </div>

  <!-- Board + controls -->
  <div class="center">
    <div class="board-wrap">
      <div id="board"></div>
    </div>
    <div class="legend">
      <span><div class="lsq m"></div>MOVER (torre)</span>
      <span><div class="lsq c"></div>CAPTURAR (diagonal+salto)</span>
    </div>
    <div id="msg"></div>
    <button class="btn" onclick="resetGame()">↺ NUEVA PARTIDA</button>
  </div>

  <!-- Right prisons -->
  <div class="prison-col">
    <div class="prison blue-p">
      <div class="prison-title">🔒 PRESOS AZUL</div>
      <div class="prison-pips" id="ppBlueR"></div>
    </div>
    <div class="prison red-p">
      <div class="prison-title">🔒 PRESOS ROJO</div>
      <div class="prison-pips" id="ppRedR"></div>
    </div>
  </div>

</div>

<!-- Ranking -->
<div class="ranking">
  <div class="rtitle">RANKING LOCAL</div>
  <div id="ranking"></div>
</div>

<script>
  const COLS = 5, ROWS = 10;
  const TOTAL = 10; // pieces per player (two rows each)

  // Blue starts at row 0 (top), Red at row 9 (bottom)
  // Blue home zone: row 0, Red home zone: row 9
  // Win: fill the enemy's home row entirely

  function idx(r,c){ return r*COLS+c; }
  function rc(i){ return {r:Math.floor(i/COLS), c:i%COLS}; }
  function inBoard(r,c){ return r>=0&&r<ROWS&&c>=0&&c<COLS; }
  function opp(p){ return p==="blue"?"red":"blue"; }
  function getCell(r,c){ return document.querySelector(`.cell[data-r='${r}'][data-c='${c}']`); }

  let state, selected, currentPlayer, gameOver, prisoners;

  // ── MOVES ──────────────────────────────────────────────────────────────
  function getMoves(r, c) {
    const color = state[r][c];
    if (!color) return [];
    const moves = [];

    // TORRE: slide straight in 4 dirs, stop before any piece
    for (const [dr,dc] of [[0,1],[0,-1],[1,0],[-1,0]]) {
      let tr=r+dr, tc=c+dc;
      while (inBoard(tr,tc)) {
        if (state[tr][tc] !== null) break;
        moves.push({tr,tc,type:"move",cr:null,cc:null});
        tr+=dr; tc+=dc;
      }
    }

    // PEÓN CAPTURE: diagonal jump over enemy in ALL 4 diagonal dirs
    for (const dr of [-1,1]) {
      for (const dc of [-1,1]) {
        const er=r+dr, ec=c+dc;
        const lr=r+2*dr, lc=c+2*dc;
        if (inBoard(er,ec) && state[er][ec]===opp(color) &&
            inBoard(lr,lc) && state[lr][lc]===null) {
          moves.push({tr:lr,tc:lc,type:"capture",cr:er,cc:ec});
        }
      }
    }

    return moves;
  }

  // ── BOARD SETUP ─────────────────────────────────────────────────────────
  function createBoard() {
    const boardEl = document.getElementById("board");
    boardEl.innerHTML = "";
    state = Array.from({length:ROWS}, ()=>Array(COLS).fill(null));
    document.getElementById("msg").textContent = "";
    document.getElementById("msg").className = "";
    gameOver = false; selected = null; currentPlayer = "blue";
    prisoners = {blue:0, red:0};

    for (let r=0; r<ROWS; r++) {
      for (let c=0; c<COLS; c++) {
        const cell = document.createElement("div");
        cell.classList.add("cell");
        cell.dataset.r = r; cell.dataset.c = c;
        cell.onclick = handleClick;
        boardEl.appendChild(cell);
      }
    }

    // Blue top 2 rows, Red bottom 2 rows
    for (let c=0; c<COLS; c++) placePiece(0, c, "blue");
    for (let c=0; c<COLS; c++) placePiece(1, c, "blue");
    for (let c=0; c<COLS; c++) placePiece(ROWS-1, c, "red");
    for (let c=0; c<COLS; c++) placePiece(ROWS-2, c, "red");

    updateUI();
  }

  function placePiece(r, c, color, anim="") {
    state[r][c] = color;
    const cell = getCell(r,c); if (!cell) return;
    cell.innerHTML = "";
    const p = document.createElement("div");
    p.classList.add("piece", color);
    if (anim) p.classList.add(anim);
    cell.appendChild(p);
  }

  // ── INTERACTION ──────────────────────────────────────────────────────────
  function clearHL() {
    document.querySelectorAll('.cell.valid,.cell.selected,.cell.capture')
      .forEach(c=>c.classList.remove('valid','selected','capture'));
  }

  function highlightMoves(r,c) {
    clearHL();
    const cell = getCell(r,c); if(cell) cell.classList.add('selected');
    getMoves(r,c).forEach(m => {
      const t = getCell(m.tr,m.tc);
      if (t) t.classList.add(m.type==="capture"?"capture":"valid");
    });
  }

  function handleClick(e) {
    if (gameOver) return;
    const cell = e.target.closest('.cell'); if (!cell) return;
    const r=+cell.dataset.r, c=+cell.dataset.c;

    if (selected) {
      const m = getMoves(selected.r, selected.c).find(mv=>mv.tr===r&&mv.tc===c);
      if (m) { doMove(selected.r, selected.c, m); clearHL(); selected=null; return; }
      if (state[r][c]===currentPlayer) { selected={r,c}; highlightMoves(r,c); return; }
      selected=null; clearHL();
    } else if (state[r][c]===currentPlayer) {
      selected={r,c}; highlightMoves(r,c);
    }
  }

  function doMove(fr, fc, m) {
    const color = state[fr][fc];
    state[fr][fc] = null;
    const fromCell = getCell(fr,fc); if(fromCell) fromCell.innerHTML="";

    if (m.type==="capture") {
      const capturedColor = state[m.cr][m.cc];
      const ec = getCell(m.cr,m.cc);
      if (ec) {
        const ep = ec.querySelector('.piece');
        if (ep) ep.classList.add('cap');
        setTimeout(()=>{ ec.innerHTML=""; }, 350);
      }
      state[m.cr][m.cc] = null;
      prisoners[capturedColor]++;
    }

    placePiece(m.tr, m.tc, color, "land");

    // Rescue: if mover reaches enemy's home row, free one of their own prisoners
    const enemyHome = color==="blue" ? [ROWS-1, ROWS-2] : [0, 1];
    if (enemyHome.includes(m.tr) && prisoners[color]>0) {
      prisoners[color]--;
      setTimeout(()=>spawnRescued(color), 420);
    }

    updateUI();
    if (!checkVictory()) switchTurn();
  }

  function spawnRescued(color) {
    const homeRows = color==="blue" ? [0,1] : [ROWS-1,ROWS-2];
    for (const row of homeRows) {
      for (let c=0; c<COLS; c++) {
        if (state[row][c]===null) {
          placePiece(row, c, color, "rescue");
          updateUI();
          return;
        }
      }
    }
  }

  // ── VICTORY ──────────────────────────────────────────────────────────────
  function checkVictory() {
    // Blue wins if it fills rows ROWS-1 and ROWS-2 entirely
    const blueWin = [ROWS-1,ROWS-2].every(row=>Array.from({length:COLS},(_,c)=>state[row][c]).every(v=>v==="blue"));
    // Red wins if it fills rows 0 and 1 entirely
    const redWin  = [0,1].every(row=>Array.from({length:COLS},(_,c)=>state[row][c]).every(v=>v==="red"));
    if (blueWin) { showWin("AZUL","blue"); return true; }
    if (redWin)  { showWin("ROJO","red");  return true; }
    return false;
  }

  function showWin(label, color) {
    gameOver = true;
    const el = document.getElementById("msg");
    el.textContent = `★ ${label} GANA ★`; el.className = color==="blue"?"wb":"wr";
    saveWin(label); updateUI();
  }

  function switchTurn() { currentPlayer=opp(currentPlayer); updateUI(); }

  // ── UI ────────────────────────────────────────────────────────────────────
  function updateUI() {
    const bInR = [ROWS-1,ROWS-2].flatMap(row=>Array.from({length:COLS},(_,c)=>state[row][c])).filter(v=>v==="blue").length;
    const rInB = [0,1].flatMap(row=>Array.from({length:COLS},(_,c)=>state[row][c])).filter(v=>v==="red").length;
    document.getElementById("scoreBlue").textContent = `${bInR}/10`;
    document.getElementById("scoreRed").textContent  = `${rInB}/10`;
    document.getElementById("prisonerBlue").textContent = prisoners.blue;
    document.getElementById("prisonerRed").textContent  = prisoners.red;

    renderPrison("ppBlueL","blue",prisoners.blue);
    renderPrison("ppBlueR","blue",prisoners.blue);
    renderPrison("ppRedL","red",prisoners.red);
    renderPrison("ppRedR","red",prisoners.red);

    const pb=document.getElementById("panelBlue"), pr=document.getElementById("panelRed");
    const bb=document.getElementById("badgeBlue"), br=document.getElementById("badgeRed");
    if (!gameOver) {
      pb.className = "panel"+(currentPlayer==="blue"?" ab":"");
      pr.className = "panel"+(currentPlayer==="red"?" ar":"");
      if (currentPlayer==="blue") {
        bb.textContent="▶ TU TURNO"; bb.className="badge blue";
        br.textContent="ESPERA";     br.className="badge wait";
      } else {
        br.textContent="▶ TU TURNO"; br.className="badge red";
        bb.textContent="ESPERA";     bb.className="badge wait";
      }
    } else {
      pb.className="panel"; pr.className="panel";
      bb.textContent=""; br.textContent="";
    }
  }

  function renderPrison(elId, color, count) {
    const el = document.getElementById(elId); el.innerHTML="";
    if (count===0) { el.innerHTML=`<span class="prison-empty">—</span>`; return; }
    for (let i=0;i<count;i++) {
      const pip=document.createElement("div");
      pip.classList.add("prison-pip",color);
      el.appendChild(pip);
    }
  }

  // ── RANKING ───────────────────────────────────────────────────────────────
  function saveWin(label) {
    let r={}; try{r=JSON.parse(localStorage.getItem("rnk_5x10"))||{};}catch(e){}
    r[label]=(r[label]||0)+1;
    try{localStorage.setItem("rnk_5x10",JSON.stringify(r));}catch(e){}
    loadRanking();
  }
  function loadRanking() {
    const div=document.getElementById("ranking");
    let r={}; try{r=JSON.parse(localStorage.getItem("rnk_5x10"))||{};}catch(e){}
    const players=[{key:"AZUL",label:"JUGADOR AZUL",cls:"blue"},{key:"ROJO",label:"JUGADOR ROJO",cls:"red"}];
    div.innerHTML=""; let has=false;
    players.forEach(p=>{
      if(r[p.key]){has=true;
        div.innerHTML+=`<div class="rrow"><div class="rplayer"><div class="rdot ${p.cls}"></div><span>${p.label}</span></div><div class="rwins">${r[p.key
