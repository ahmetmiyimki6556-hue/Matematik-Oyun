<!doctype html>
<html lang="tr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Matematik Oyunu — Tek Dosya</title>
<style>
  :root { --bg:#0f1724; --card:#0b1220; --accent:#06b6d4; --muted:#94a3b8; }
  body{font-family:Inter,Segoe UI,Roboto,Arial,sans-serif;margin:0;background:linear-gradient(180deg,#071021 0%, #071827 100%);color:#e6eef6;min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px;}
  .app{width:100%;max-width:920px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.02));border:1px solid rgba(255,255,255,0.04);border-radius:12px;padding:20px;box-shadow:0 6px 30px rgba(2,6,23,0.6);}
  header{display:flex;align-items:center;justify-content:space-between;gap:12px}
  h1{font-size:20px;margin:0}
  .meta{color:var(--muted);font-size:13px}
  .board{display:flex;gap:16px;margin-top:18px;align-items:flex-start}
  .left{flex:1}
  .right{width:320px}
  .target{background:linear-gradient(90deg,#052031,#0b2433);padding:18px;border-radius:10px;text-align:center}
  .target h2{margin:0;font-size:36px;letter-spacing:2px}
  .target p{margin:6px 0 0;color:var(--muted);font-size:13px}
  .numbers{display:flex;flex-wrap:wrap;gap:10px;margin-top:14px}
  .num{background:var(--card);padding:12px 16px;border-radius:10px;cursor:pointer;user-select:none;min-width:64px;text-align:center;font-weight:600;box-shadow:inset 0 -6px 18px rgba(0,0,0,0.25)}
  .num.disabled{opacity:.28;cursor:not-allowed}
  .controls{display:flex;gap:8px;margin-top:12px;flex-wrap:wrap}
  button{background:linear-gradient(180deg,#08182a,#072033);border:1px solid rgba(255,255,255,0.04);color:#dff7ff;padding:10px 12px;border-radius:8px;cursor:pointer;font-weight:600}
  button.secondary{background:transparent;border:1px solid rgba(255,255,255,0.04);color:var(--muted);font-weight:600}
  .expr{margin-top:16px;background:rgba(255,255,255,0.02);padding:12px;border-radius:10px;min-height:56px;display:flex;align-items:center;gap:8px;flex-wrap:wrap}
  .expr .pill{background:#02121b;padding:8px 10px;border-radius:8px;font-weight:700}
  .log{margin-top:12px;color:var(--muted);font-size:13px}
  .panel{background:linear-gradient(180deg,#061322,#071226);padding:14px;border-radius:10px}
  .right .panel + .panel{margin-top:12px}
  .hint{color:#ffd27a;font-weight:700}
  footer{margin-top:14px;color:var(--muted);font-size:13px;display:flex;justify-content:space-between;align-items:center}
  .score{font-weight:800;color:var(--accent)}
  .small{font-size:12px;color:var(--muted)}
  @media(max-width:800px){
    .board{flex-direction:column}
    .right{width:100%}
  }
</style>
</head>
<body>
<div class="app" role="application" aria-label="Matematik Oyunu">
  <header>
    <div>
      <h1>Matematik Oyunu</h1>
      <div class="meta">Hedefe en kısa adımda ulaş — ipuçları sınırlı. (Tek dosya demo)</div>
    </div>
    <div class="meta small">Mod: Klasik • Tek oyuncu</div>
  </header>

  <div class="board">
    <div class="left">
      <div class="target panel" id="targetCard" aria-live="polite">
        <h2 id="targetValue">--</h2>
        <p>Hedef Sayı</p>
      </div>

      <div class="panel" style="margin-top:12px">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div class="small">Kalan Hamle: <span id="movesLeft">--</span></div>
          <div class="small">İpuçları: <span id="hintsLeft">--</span></div>
        </div>

        <div class="numbers" id="numbersContainer" style="margin-top:10px"></div>

        <div class="controls" id="opsRow" style="margin-top:12px">
          <button id="opAdd">+</button>
          <button id="opSub">−</button>
          <button id="opMul">×</button>
          <button id="opDiv">÷</button>
          <button class="secondary" id="undoBtn">Geri Al</button>
          <button class="secondary" id="resetBtn">Sıfırla</button>
        </div>

        <div class="expr" id="exprArea" aria-live="polite">
          <!-- adım gösterimi -->
        </div>

        <div style="display:flex;gap:8px;margin-top:12px">
          <button id="combineBtn">Seçili İki Sayıyı Birleştir</button>
          <button id="checkBtn">Kontrol Et</button>
          <button id="hintBtn" class="secondary">İpucu Ver</button>
        </div>

        <div class="log" id="resultLog"></div>
      </div>

    </div>

    <div class="right">
      <div class="panel">
        <div style="display:flex;align-items:center;justify-content:space-between">
          <div>
            <div class="small">Puan</div>
            <div class="score" id="score">0</div>
          </div>
          <div style="text-align:right">
            <div class="small">Seviye</div>
            <div id="levelId">-</div>
          </div>
        </div>
        <hr style="border:none;border-top:1px solid rgba(255,255,255,0.03);margin:12px 0">
        <div class="small">İşlem Önerisi (İpucu için kullanılacak):</div>
        <div class="hint" id="hintArea" style="margin-top:8px">—</div>
      </div>

      <div class="panel">
        <div class="small">Hızlı Açıklama</div>
        <div style="margin-top:8px">
          - Kartlara tıkla (veya seç) → iki sayı seçtikten sonra bir işlem butonuna seç → "Seçili İki Sayıyı Birleştir" ile uygula.<br>
          - Her birleşme mevcut iki sayıyı çıkarır ve yerine ara sonucu ekler. Amaç son sayılardan birinin hedefe eşit olmasıdır.<br>
          - İpucu, çözücünün bulduğu çözümün ilk adımını açığa çıkarır.
        </div>
      </div>

    </div>
  </div>

  <footer>
    <div class="small">Bu demo tek dosya halinde: üretici + çözücü + oynanış.</div>
    <div>
      <button id="newBtn">Yeni Seviye</button>
    </div>
  </footer>
</div>

<script>
/*
  Tek dosya Matematik Oyunu
  - Seviye üretici: reverse-build + distractor ekleme
  - Çözücü: backtracking kombinasyon tabanlı (24-game tarzı) — aynı mantıkla hedef doğrulama ve ipucu çıkarma
  - Oynanış: iki sayı seçip işlem seç → birleştir
  - Dil: Türkçe
*/

/* ---------- Yardımcı Fonksiyonlar ---------- */
function randInt(min, max){ return Math.floor(Math.random()*(max-min+1))+min; }
function rndChoice(arr){ return arr[randInt(0, arr.length-1)]; }
const OPS = ['+','-','*','/'];
function applyOp(a, op, b){
  if(op === '+') return a + b;
  if(op === '-') return a - b;
  if(op === '*') return a * b;
  if(op === '/'){ if (Math.abs(b) < 1e-9) return null; return a / b; }
  return null;
}
function formatExpr(a, op, b){
  return '(' + a + op + b + ')';
}

/* ---------- Solüsyon Oluşturucu (reverse-build) ---------- */
function buildRandomSolution(config = {}) {
  const usedCount = randInt(2,4);
  const baseRange = config.baseRange || [1,25];
  const usedNumbers = [];
  for(let i=0;i<usedCount;i++) usedNumbers.push(randInt(baseRange[0], baseRange[1]));
  const ops = [];
  for(let i=0;i<usedCount-1;i++) ops.push(rndChoice(OPS));
  // Soldan sağa hesapla (basit)
  let val = usedNumbers[0];
  for(let i=0;i<ops.length;i++){
    const r = applyOp(val, ops[i], usedNumbers[i+1]);
    if(r === null || !isFinite(r)) return buildRandomSolution(config);
    val = r;
  }
  // Yuvarla makul hassasiyete
  const target = Math.round(val * 100) / 100;
  return {usedNumbers, ops, target};
}

function generateLevel(options = {}) {
  // Deneme/yanıta göre seviye üret
  const numbersCount = options.numbersCount || 6;
  const baseRange = options.baseRange || [1,25];
  for(let attempts=0; attempts<300; attempts++){
    const sol = buildRandomSolution({baseRange});
    // Ek distractor sayılar
    const all = sol.usedNumbers.slice();
    while(all.length < numbersCount){
      let n = randInt(baseRange[0], baseRange[1]);
      if(!all.includes(n)) all.push(n);
    }
    // karıştır
    for(let i=all.length-1;i>0;i--){ const j = randInt(0,i); [all[i],all[j]]=[all[j],all[i]]; }
    const candidate = {numbers: all, target: sol.target, solution: {numbers: sol.usedNumbers, ops: sol.ops}};
    // Doğrulama: çözücü ile bulunabildiğine emin ol
    const solverRes = solveForTarget(candidate.numbers, candidate.target, {findOne:true, timeoutMs:100});
    if(solverRes && solverRes.solutions && solverRes.solutions.length>0){
      // Seviye id
      candidate.id = 'lvl-' + Date.now().toString(36).slice(-6);
      candidate.maxMoves = numbersCount - 1;
      candidate.hintCount = 3;
      return candidate;
    }
  }
  // fallback: basit hazır seviye
  return { id:'lvl-000', numbers:[3,7,2,12,5,9], target:24, maxMoves:5, hintCount:3 };
}

/* ---------- Çözücü (Backtracking / pairwise combine) ----------
   - classic approach: take current list of numbers (with expr strings), pick ordered pair (i,j) i<j,
     try all ops (and both orders for non-commutative), combine to new list, recurse until single number.
   - returns all solutions (limite edilebilir).
*/
function solveForTarget(numbers, target, options = {}) {
  const tol = options.tolerance || 1e-6;
  const findOne = !!options.findOne;
  const timeoutMs = options.timeoutMs || 1000;
  const start = Date.now();
  const solutions = [];

  // initial state: numbers as objects {val, expr}
  const init = numbers.map(n => ({val: Number(n), expr: String(n)}));

  function backtrack(list){
    if(Date.now() - start > timeoutMs) return 'timeout';
    if(list.length === 0) return;
    if(list.length === 1){
      if(Math.abs(list[0].val - target) < tol){
        solutions.push(list[0].expr);
        if(findOne) return 'found';
      }
      return;
    }
    // choose ordered pair i, j
    for(let i=0;i<list.length;i++){
      for(let j=i+1;j<list.length;j++){
        const a = list[i], b = list[j];
        const rest = list.filter((_,idx)=>idx!==i && idx!==j);
        // try ops
        for(const op of OPS){
          // Try a op b
          const v1 = applyOp(a.val, op, b.val);
          if(v1 !== null && isFinite(v1)){
            const newExpr = formatExpr(a.expr,op,b.expr);
            const newList = rest.concat([{val:v1, expr:newExpr}]);
            const r = backtrack(newList);
            if(r === 'found') return 'found';
            if(r === 'timeout') return 'timeout';
          }
          // For non-commutative or to explore more, try b op a (skip symmetric for +,* to reduce duplicates)
          if(op === '-' || op === '/'){
            const v2 = applyOp(b.val, op, a.val);
            if(v2 !== null && isFinite(v2)){
              const newExpr2 = formatExpr(b.expr,op,a.expr);
              const newList2 = rest.concat([{val:v2, expr:newExpr2}]);
              const r2 = backtrack(newList2);
              if(r2 === 'found') return 'found';
              if(r2 === 'timeout') return 'timeout';
            }
          }
        }
      }
    }
    return;
  }

  const status = backtrack(init);
  return {solutions, status};
}

/* ---------- Oyun Mantığı (UI ile bağlama) ---------- */
const targetValueEl = document.getElementById('targetValue');
const numbersContainer = document.getElementById('numbersContainer');
const exprArea = document.getElementById('exprArea');
const resultLog = document.getElementById('resultLog');
const scoreEl = document.getElementById('score');
const levelIdEl = document.getElementById('levelId');
const movesLeftEl = document.getElementById('movesLeft');
const hintsLeftEl = document.getElementById('hintsLeft');
const hintArea = document.getElementById('hintArea');

let state = {
  level: null,
  numbers: [], // array of {val, expr, id}
  selected: [], // indices selected (max 2)
  selectedOp: null,
  movesLeft: 0,
  hintsLeft: 0,
  score: 0,
  history: [] // for undo snapshots
};

function renderNumbers(){
  numbersContainer.innerHTML = '';
  state.numbers.forEach((n, idx) => {
    const btn = document.createElement('div');
    btn.className = 'num' + (n.disabled ? ' disabled' : '');
    btn.tabIndex = n.disabled ? -1 : 0;
    btn.textContent = n.expr;
    if(state.selected.includes(idx)) btn.style.outline = '2px solid rgba(6,182,212,0.18)';
    btn.addEventListener('click', ()=> onNumberClick(idx));
    numbersContainer.appendChild(btn);
  });
  movesLeftEl.textContent = state.movesLeft;
  hintsLeftEl.textContent = state.hintsLeft;
}

function pushHistory(){
  state.history.push(JSON.parse(JSON.stringify({
    numbers: state.numbers,
    score: state.score,
    movesLeft: state.movesLeft,
    hintsLeft: state.hintsLeft
  })));
  if(state.history.length > 50) state.history.shift();
}

function onNumberClick(idx){
  if(state.numbers[idx].disabled) return;
  const pos = state.selected.indexOf(idx);
  if(pos >= 0) state.selected.splice(pos,1);
  else {
    if(state.selected.length >= 2) state.selected.shift();
    state.selected.push(idx);
  }
  renderExpr();
}

function renderExpr(){
  exprArea.innerHTML = '';
  state.selected.forEach((i,order)=> {
    const pill = document.createElement('div');
    pill.className = 'pill';
    pill.textContent = state.numbers[i].expr;
    exprArea.appendChild(pill);
    if(order === 0){
      const opP = document.createElement('div');
      opP.className = 'small';
      opP.style.opacity = .7;
      opP.style.marginLeft = '6px';
      opP.textContent = state.selected.length>1 ? '→ seçili iki sayı' : 'Birinci seçili';
      exprArea.appendChild(opP);
    }
  });
  if(state.selectedOp){
    const op = document.createElement('div');
    op.className = 'pill';
    op.textContent = state.selectedOp;
    exprArea.appendChild(op);
  }
}

document.getElementById('opAdd').addEventListener('click', ()=> setOp('+'));
document.getElementById('opSub').addEventListener('click', ()=> setOp('-'));
document.getElementById('opMul').addEventListener('click', ()=> setOp('*'));
document.getElementById('opDiv').addEventListener('click', ()=> setOp('/'));
function setOp(op){ state.selectedOp = op; renderExpr(); }

document.getElementById('combineBtn').addEventListener('click', combineSelected);
document.getElementById('checkBtn').addEventListener('click', manualCheck);
document.getElementById('hintBtn').addEventListener('click', giveHint);
document.getElementById('undoBtn').addEventListener('click', undo);
document.getElementById('resetBtn').addEventListener('click', resetLevel);
document.getElementById('newBtn').addEventListener('click', ()=> initNewLevel());

function combineSelected(){
  if(state.selected.length < 2) { resultLog.textContent = 'Lütfen iki sayı seçin.'; return; }
  if(!state.selectedOp) { resultLog.textContent = 'Lütfen bir işlem seçin.'; return; }
  if(state.movesLeft <= 0){ resultLog.textContent = 'Hamle hakkınız kalmadı.'; return; }
  const i = state.selected[0], j = state.selected[1];
  if(i === j){ resultLog.textContent = 'Aynı sayıyı iki kere seçemezsiniz.'; return; }

  // push history
  pushHistory();

  const a = state.numbers[i], b = state.numbers[j];
  const v = applyOp(a.val, state.selectedOp, b.val);
  if(v === null || !isFinite(v)){ resultLog.textContent = 'Geçersiz işlem (sıfıra bölme veya belirsiz).'; return; }
  const expr = formatExpr(a.expr, state.selectedOp, b.expr);

  // replace larger index first to preserve indices
  const newNumbers = state.numbers.slice();
  const maxIdx = Math.max(i,j), minIdx = Math.min(i,j);
  newNumbers.splice(maxIdx,1);
  newNumbers.splice(minIdx,1);
  newNumbers.push({val: Math.round(v*1000000)/1000000, expr});
  state.numbers = newNumbers;
  state.selected = [];
  state.selectedOp = null;
  state.movesLeft -= 1;
  resultLog.textContent = 'Birleştirildi: ' + expr;
  checkAutoWin();
  renderNumbers();
  renderExpr();
}

function manualCheck(){
  // Eğer listte herhangi bir sayı hedefe eşitse kazan
  const tol = 1e-6;
  for(const n of state.numbers){
    if(Math.abs(n.val - state.level.target) < tol){
      onWin(n.expr);
      return;
    }
  }
  resultLog.textContent = 'Henüz hedefe ulaşılamadı.';
}

function checkAutoWin(){
  const tol = 1e-6;
  for(const n of state.numbers){
    if(Math.abs(n.val - state.level.target) < tol){
      onWin(n.expr);
      return true;
    }
  }
  // eğer hamle bitti ve başka çözüm yoksa başarısız olabilir
  if(state.movesLeft <= 0){
    // hızlı kontrol: çözücüyle ara
    const solverRes = solveForTarget(state.numbers.map(x=>x.val), state.level.target, {findOne:true, timeoutMs:200});
    if(!solverRes || solverRes.solutions.length === 0){
      resultLog.textContent = 'Hamle bitti — çözüm yok.';
    }
  }
  return false;
}

function onWin(expr){
  resultLog.textContent = 'Tebrikler! Çözüldü: ' + expr;
  const gain = 100 + (state.movesLeft * 20) + (state.hintsLeft * 5);
  state.score += gain;
  scoreEl.textContent = state.score;
  // disable further interaction
  state.numbers.forEach(n=>n.disabled=true);
  renderNumbers();
}

function giveHint(){
  if(state.hintsLeft <= 0){ resultLog.textContent = 'İpuçların bitti.'; return; }
  // use solver to get a solution and show first step
  const solverRes = solveForTarget(state.numbers.map(x=>x.val), state.level.target, {findOne:true, timeoutMs:500});
  if(!solverRes || solverRes.solutions.length===0){ resultLog.textContent = 'İpucu bulunamadı.'; return; }
  const solExpr = solverRes.solutions[0];
  // solExpr is a fully parenthesized expression string like ((3+5)*(2+1))
  // Extract first binary operation: find outermost leftmost "(a op b)"
  const firstOp = extractFirstOperation(solExpr);
  hintArea.textContent = firstOp || solExpr;
  state.hintsLeft -= 1;
  hintsLeftEl.textContent = state.hintsLeft;
  resultLog.textContent = 'İpucu verildi: ilk adımı kullanabilirsiniz.';
}

function extractFirstOperation(expr){
  // find the first "(x op y)" pattern without nested parentheses inside x or y
  // simple parser: scan and when we see '(' track balance until we find a ')' where inside has exactly one top-level operator
  for(let i=0;i<expr.length;i++){
    if(expr[i] === '('){
      let bal = 0;
      for(let j=i+1;j<expr.length;j++){
        if(expr[j] === '(') bal++;
        else if(expr[j] === ')'){
          if(bal === 0){
            const inner = expr.slice(i+1,j);
            // if inner contains top-level operator + - * /
            // count parentheses inside
            let subBal=0, opPos=-1, opChar='';
            for(let k=0;k<inner.length;k++){
              const ch = inner[k];
              if(ch === '(') subBal++;
              else if(ch === ')') subBal--;
              else if(subBal===0 && (ch==='+'||ch==='-'||ch==='*'||ch==='/')){
                opPos = k; opChar = ch; break;
              }
            }
            if(opPos > 0){
              const left = inner.slice(0,opPos);
              const right = inner.slice(opPos+1);
              return '(' + left + opChar + right + ')';
            } else break;
          } else bal--;
        }
      }
    }
  }
  return expr;
}

function undo(){
  if(state.history.length === 0){ resultLog.textContent = 'Geri alınacak hamle yok.'; return; }
  const snap = state.history.pop();
  state.numbers = snap.numbers;
  state.score = snap.score;
  state.movesLeft = snap.movesLeft;
  state.hintsLeft = snap.hintsLeft;
  scoreEl.textContent = state.score;
  resultLog.textContent = 'Geri alındı.';
  renderNumbers();
  renderExpr();
}

function resetLevel(){
  initLevel(state.level);
}

/* ---------- Seviye Başlatma ---------- */
function initLevel(levelObj){
  state.level = levelObj;
  state.numbers = levelObj.numbers.map((n,idx)=>({val:Number(n), expr:String(n), id: idx}));
  state.selected = [];
  state.selectedOp = null;
  state.movesLeft = levelObj.maxMoves || (state.numbers.length-1);
  state.hintsLeft = levelObj.hintCount || 3;
  state.history = [];
  state.score = state.score || 0;
  targetValueEl.textContent = levelObj.target;
  levelIdEl.textContent = levelObj.id || '-';
  scoreEl.textContent = state.score;
  hintArea.textContent = '—';
  resultLog.textContent = 'Yeni seviye yüklendi.';
  renderNumbers();
  renderExpr();
}

/* ---------- Yeni Seviye Oluştur ---------- */
function initNewLevel(){
  resultLog.textContent = 'Seviye üretiliyor...';
  // generateLevel may be a bit heavy; show progress
  setTimeout(()=>{
    const lvl = generateLevel({numbersCount:6, baseRange:[1,25]});
    initLevel(lvl);
  }, 50);
}

/* ---------- Başlangıç ---------- */
initNewLevel();

</script>
</body>
</html>
