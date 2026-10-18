<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>PI Tracker Pro</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/exceljs/4.4.0/exceljs.min.js"></script>
<style>
:root{
  --navy:#0f172a; --navy2:#1e293b; --brand:#2563eb; --brand-d:#1d4ed8; --teal:#0d9488;
  --bg:#f1f5f9; --card:#ffffff; --line:#e2e8f0; --text:#0f172a; --muted:#64748b;
  --green:#16a34a; --amber:#d97706; --red:#dc2626; --purple:#7c3aed;
  --radius:14px; --shadow:0 1px 3px rgba(15,23,42,.08),0 6px 18px rgba(15,23,42,.06);
}
*{box-sizing:border-box;margin:0;padding:0}
html,body{height:100%}
body{font-family:"Segoe UI",system-ui,-apple-system,Roboto,Arial,sans-serif;background:var(--bg);color:var(--text);font-size:14.5px;line-height:1.45}
button{font-family:inherit;cursor:pointer}
input,select{font-family:inherit;font-size:14px}
.muted{color:var(--muted)} .small{font-size:12.5px}
.screen{display:none;min-height:100vh}
.screen.active{display:block}
#appScreen.active{display:flex}

.btn{border:1px solid transparent;border-radius:9px;padding:9px 16px;font-weight:600;font-size:14px;transition:.15s;background:#e2e8f0;color:var(--text)}
.btn:hover{filter:brightness(.96);transform:translateY(-1px)}
.btn.primary{background:var(--brand);color:#fff}
.btn.primary:hover{background:var(--brand-d)}
.btn.success{background:var(--green);color:#fff}
.btn.warn{background:#fef3c7;color:#92400e;border-color:#fcd34d}
.btn.ghost{background:transparent;border-color:var(--line);color:var(--text)}
.btn.danger-ghost{background:transparent;border-color:#fecaca;color:var(--red)}
.btn.sm{padding:6px 12px;font-size:12.5px}
.btn.block{display:block;width:100%}
input[type=text],input[type=password],input[type=number],input[type=date],select{
  width:100%;padding:10px 12px;border:1px solid #cbd5e1;border-radius:9px;background:#fff;color:var(--text);outline:none;transition:.15s}
input:focus,select:focus{border-color:var(--brand);box-shadow:0 0 0 3px rgba(37,99,235,.15)}
input[readonly]{background:#f8fafc;color:var(--muted)}
label{display:block;font-size:13px;font-weight:600;color:#334155;margin-bottom:14px}
label input,label select{margin-top:6px;font-weight:400}
.error{color:var(--red);font-size:13px;min-height:18px;margin-bottom:8px}

#loginScreen{background:linear-gradient(135deg,#e0e7ff,#f1f5f9)}
#loginScreen.active{display:flex}
.login-wrap{margin:auto;display:grid;grid-template-columns:1.1fr 1fr;width:min(940px,94vw);border-radius:20px;overflow:hidden;box-shadow:0 20px 60px rgba(15,23,42,.25);background:#fff}
.login-brand{background:linear-gradient(145deg,#0f172a,#1d4ed8);color:#fff;padding:48px 40px}
.login-brand h1{font-size:30px;margin:18px 0 10px}
.login-brand p{opacity:.85;margin-bottom:22px}
.login-brand ul{list-style:none}
.login-brand li{padding:6px 0;opacity:.9}
.login-brand li:before{content:"✔ ";color:#5eead4}
.logo{display:inline-grid;place-items:center;width:56px;height:56px;border-radius:14px;background:#fff;color:var(--brand);font-weight:800;font-size:22px}
.logo.sm{width:34px;height:34px;font-size:14px;border-radius:9px;background:var(--brand);color:#fff}
.login-card{padding:48px 40px;display:flex;flex-direction:column;justify-content:center}
.login-card h2{font-size:24px;margin-bottom:4px}
.login-card .muted{margin-bottom:24px}

.topbar{display:flex;align-items:center;gap:14px;background:#fff;border-bottom:1px solid var(--line);padding:12px 24px;position:sticky;top:0;z-index:20}
.topbar h2{font-size:19px;flex:1}
.topbar.simple{justify-content:space-between}
.brand{display:flex;align-items:center;gap:10px;font-weight:800;font-size:16px}
.top-right{display:flex;align-items:center;gap:12px}
.year-pill{background:#dbeafe;color:var(--brand-d);font-weight:700;padding:6px 14px;border-radius:99px;font-size:13px}
.icon-btn{position:relative;background:#f1f5f9;border:1px solid var(--line);border-radius:10px;width:40px;height:40px;font-size:17px}
.menu-btn{display:none}
.badge{font-style:normal;background:#334155;color:#fff;border-radius:99px;padding:1px 8px;font-size:11.5px;font-weight:700;margin-left:auto}
.badge.red{background:var(--red);position:absolute;top:-6px;right:-6px;padding:1px 6px}

.container{max-width:980px;margin:0 auto;padding:36px 20px}
.page-h{font-size:28px;margin-bottom:4px}
.year-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(250px,1fr));gap:18px;margin:26px 0}
.year-card{background:#fff;border-radius:var(--radius);padding:22px;box-shadow:var(--shadow);border:1px solid var(--line)}
.year-card h3{font-size:24px;color:var(--brand-d)}
.year-card .stats{display:flex;gap:14px;margin:12px 0 16px;color:var(--muted);font-size:13px}
.year-card .stats b{display:block;font-size:18px;color:var(--text)}
.year-card .acts{display:flex;gap:8px}
.year-card .acts .btn:first-child{flex:1}
.year-add .row{display:flex;gap:12px;align-items:center;flex-wrap:wrap}
.year-add input{max-width:230px}
.year-preview{font-weight:700;color:var(--brand-d);min-width:110px}

.sidebar{width:250px;background:var(--navy);color:#cbd5e1;padding:20px 14px;display:flex;flex-direction:column;position:sticky;top:0;height:100vh;flex-shrink:0}
.sidebar .brand{color:#fff;padding:4px 8px 16px}
.side-year{background:var(--navy2);border-radius:10px;padding:10px 14px;margin:0 4px 16px;font-size:13px}
.side-year strong{float:right;color:#fff}
.sidebar nav{display:flex;flex-direction:column;gap:4px;flex:1}
.nav-item{display:flex;align-items:center;gap:10px;background:none;border:0;color:#cbd5e1;padding:11px 12px;border-radius:10px;text-align:left;font-size:14.5px;font-weight:500}
.nav-item:hover{background:var(--navy2)}
.nav-item.active{background:var(--brand);color:#fff}
.side-foot{display:grid;gap:8px}
.side-foot .btn.ghost{color:#cbd5e1;border-color:#334155}
.main{flex:1;min-width:0}
.content{padding:24px;max-width:1280px}
.side-backdrop{display:none}

.card{background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);border:1px solid var(--line);padding:20px;margin-bottom:20px}
.card h3{font-size:16px;margin-bottom:14px}
.kpis{display:grid;grid-template-columns:repeat(auto-fit,minmax(170px,1fr));gap:16px;margin-bottom:20px}
.kpi{background:#fff;border-radius:var(--radius);padding:18px;box-shadow:var(--shadow);border:1px solid var(--line);border-left:5px solid var(--brand)}
.kpi span{color:var(--muted);font-size:13px}
.kpi b{display:block;font-size:30px;margin-top:2px}
.kpi.amber{border-left-color:var(--amber)} .kpi.green{border-left-color:var(--green)} .kpi.purple{border-left-color:var(--purple)}
.kpi.red{border-left-color:var(--red)} .kpi.teal{border-left-color:var(--teal)}
.grid-2{display:grid;grid-template-columns:1fr 1.4fr;gap:20px}
.chart-box{position:relative;height:290px}
.banner{padding:12px 16px;border-radius:10px;margin-bottom:18px;font-weight:600}
.banner.danger{background:#fee2e2;color:#991b1b;border:1px solid #fecaca}
.link{background:none;border:0;color:inherit;text-decoration:underline;font-weight:700}

.new-grid{display:grid;grid-template-columns:1.5fr 1fr;gap:20px;align-items:start}
.two{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.team-add{display:flex;gap:10px;margin-top:6px}
.chips{display:flex;flex-wrap:wrap;gap:8px;margin:12px 0 16px;min-height:34px}
.chip{display:inline-flex;align-items:center;gap:8px;background:#dbeafe;color:var(--brand-d);padding:6px 8px 6px 12px;border-radius:99px;font-weight:600;font-size:13px}
.chip button{background:rgba(37,99,235,.15);border:0;color:var(--brand-d);width:20px;height:20px;border-radius:50%;line-height:1}
.chip.gray{background:#f1f5f9;color:#334155}
.chip.gray button{background:#e2e8f0;color:var(--red)}
.check{display:flex;align-items:center;gap:8px;font-weight:600;margin-bottom:18px}
.check input{width:auto}

.toolbar{display:flex;gap:12px;flex-wrap:wrap;margin-bottom:18px;align-items:center}
.toolbar .search{flex:1;min-width:220px}
.toolbar select{max-width:220px}
.team-cards{display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:12px;margin-bottom:20px}
.tcard{background:#fff;border:1px solid var(--line);border-radius:12px;padding:12px 14px;cursor:pointer;box-shadow:var(--shadow);transition:.15s}
.tcard:hover{border-color:var(--brand)}
.tcard.active{border-color:var(--brand);background:#eff6ff}
.tcard b{font-size:24px;display:block;color:var(--amber)}
.tcard span{font-size:12.5px;color:var(--muted);font-weight:600}

.pi-list{display:grid;grid-template-columns:repeat(auto-fill,minmax(360px,1fr));gap:18px}
.pi-card{background:#fff;border-radius:var(--radius);border:1px solid var(--line);box-shadow:var(--shadow);padding:18px;border-top:4px solid var(--amber)}
.pi-card.is-priority{border-top-color:var(--purple);background:linear-gradient(#faf5ff,#fff 80px)}
.pi-card.is-overdue{border-top-color:var(--red)}
.pi-head{display:flex;justify-content:space-between;gap:10px;margin-bottom:12px}
.pi-no{font-weight:800;font-size:17px;margin-right:8px}
.pi-company{color:#334155;margin-top:2px;font-weight:500}
.pi-side{text-align:right}
.tag{display:inline-block;padding:2px 9px;border-radius:99px;font-size:11.5px;font-weight:700;margin-right:4px}
.tag.prio{background:#ede9fe;color:var(--purple)}
.tag.late{background:#fee2e2;color:var(--red)}
.progress{height:7px;background:#e2e8f0;border-radius:99px;overflow:hidden;margin:6px 0 4px}
.progress span{display:block;height:100%;background:linear-gradient(90deg,var(--teal),var(--green))}
.team-list{list-style:none;margin:12px 0}
.team-list li{display:flex;justify-content:space-between;align-items:center;gap:10px;padding:8px 10px;border-radius:9px;background:#f8fafc;margin-bottom:6px}
.team-list li.done{background:#f0fdf4;color:#166534}
.tname{font-weight:600}
.pi-actions{display:flex;gap:8px;justify-content:flex-end;border-top:1px dashed var(--line);padding-top:12px}
.empty{text-align:center;padding:50px 20px;color:var(--muted);background:#fff;border:1px dashed #cbd5e1;border-radius:var(--radius)}

.table-wrap{overflow:auto;border-radius:var(--radius);border:1px solid var(--line);background:#fff;box-shadow:var(--shadow)}
table{width:100%;border-collapse:collapse;min-width:760px}
th{background:#f8fafc;text-align:left;font-size:12.5px;text-transform:uppercase;letter-spacing:.04em;color:var(--muted);padding:12px 14px;border-bottom:1px solid var(--line)}
td{padding:12px 14px;border-bottom:1px solid #f1f5f9;vertical-align:middle}
tr:hover td{background:#fafcff}
.inv-cell{display:flex;gap:8px;min-width:260px}
.pill{display:inline-block;padding:2px 10px;border-radius:99px;font-size:12px;font-weight:700}
.pill.pending{background:#fef3c7;color:#92400e} .pill.completed{background:#dcfce7;color:#166534} .pill.dispatched{background:#dbeafe;color:#1e40af}

.modal{position:fixed;inset:0;background:rgba(15,23,42,.55);display:none;align-items:center;justify-content:center;z-index:100;padding:16px}
.modal.show{display:flex}
.modal-card{background:#fff;border-radius:16px;padding:26px;width:min(400px,100%);box-shadow:0 20px 50px rgba(0,0,0,.3);animation:pop .18s ease}
.modal-card.wide{width:min(620px,100%);max-height:85vh;overflow:auto}
.modal-card h3{margin-bottom:6px;font-size:19px}
#pinInput{margin:14px 0 4px;text-align:center;letter-spacing:6px;font-size:18px}
.modal-actions{display:flex;justify-content:flex-end;gap:10px;margin-top:14px}
.alert-list{margin-top:12px}
.alert-item{border:1px solid #fecaca;background:#fef2f2;border-radius:10px;padding:10px 14px;margin-bottom:8px}
.alert-item b{color:#991b1b}
.shake{animation:shake .3s}
@keyframes pop{from{transform:scale(.94);opacity:0}to{transform:none;opacity:1}}
@keyframes shake{25%{transform:translateX(-6px)}75%{transform:translateX(6px)}}

#toasts{position:fixed;top:16px;right:16px;z-index:200;display:grid;gap:10px}
.toast{background:#0f172a;color:#fff;padding:12px 18px;border-radius:10px;box-shadow:0 8px 24px rgba(0,0,0,.25);animation:pop .2s;max-width:340px}
.toast.ok{background:#166534} .toast.err{background:#991b1b} .toast.warn{background:#92400e}
.btn-row{display:flex;gap:10px;flex-wrap:wrap;margin-top:6px}

@media(max-width:980px){
  .grid-2,.new-grid{grid-template-columns:1fr}
  .sidebar{position:fixed;left:-270px;z-index:60;transition:.25s}
  .sidebar.open{left:0}
  .side-backdrop.show{display:block;position:fixed;inset:0;background:rgba(15,23,42,.5);z-index:50}
  .menu-btn{display:block}
}
@media(max-width:720px){
  .login-wrap{grid-template-columns:1fr}
  .login-brand{padding:28px}
  .two{grid-template-columns:1fr}
  .pi-list{grid-template-columns:1fr}
  .content{padding:14px}
  .topbar{padding:10px 14px}
}
</style>
</head>
<body>
<div id="toasts"></div>

<!-- LOGIN -->
<section id="loginScreen" class="screen active">
  <div class="login-wrap">
    <div class="login-brand">
      <div class="logo">PI</div>
      <h1>PI Tracker Pro</h1>
      <p>Proforma Invoice tracking, team workflow &amp; dispatch management in one place.</p>
      <ul>
        <li>Team-wise PI progress</li>
        <li>3-day pending alerts</li>
        <li>Excel reports with charts</li>
      </ul>
    </div>
    <form id="loginForm" class="login-card" autocomplete="off">
      <h2>Welcome back</h2>
      <p class="muted">Sign in to continue</p>
      <label>Username
        <input id="lUser" type="text" placeholder="Enter username" required autofocus>
      </label>
      <label>Password
        <input id="lPass" type="password" placeholder="Enter password" required>
      </label>
      <div class="error" id="loginErr"></div>
      <button class="btn primary block" type="submit">Login</button>
    </form>
  </div>
</section>

<!-- YEAR SELECT -->
<section id="yearScreen" class="screen">
  <header class="topbar simple">
    <div class="brand"><span class="logo sm">PI</span> PI Tracker Pro</div>
    <button class="btn ghost sm" data-action="logout">Logout</button>
  </header>
  <main class="container">
    <h1 class="page-h">Select Year</h1>
    <p class="muted">Choose a financial year to open its PI records.</p>
    <div id="yearGrid" class="year-grid"></div>
    <form id="yearForm" class="card year-add">
      <h3>＋ Add New Year</h3>
      <div class="row">
        <input id="newYearStart" type="number" min="2000" max="2099" placeholder="Start year (e.g. 2027)" required>
        <span id="yearPreview" class="year-preview">—</span>
        <button class="btn primary" type="submit">Add Year</button>
      </div>
    </form>
  </main>
</section>

<!-- MAIN APP -->
<section id="appScreen" class="screen">
  <aside class="sidebar" id="sidebar">
    <div class="brand"><span class="logo sm">PI</span> PI Tracker Pro</div>
    <div class="side-year">Year <strong id="sideYear">—</strong></div>
    <nav>
      <button class="nav-item" data-action="nav" data-view="dashboard"><span>📊</span> Dashboard</button>
      <button class="nav-item" data-action="nav" data-view="new"><span>➕</span> New Add PI</button>
      <button class="nav-item" data-action="nav" data-view="pending"><span>⏳</span> Pending PI <em class="badge" id="bPending">0</em></button>
      <button class="nav-item" data-action="nav" data-view="completed"><span>✅</span> Completed PI <em class="badge" id="bCompleted">0</em></button>
      <button class="nav-item" data-action="nav" data-view="dispatched"><span>🚚</span> Dispatched PI <em class="badge" id="bDispatched">0</em></button>
      <button class="nav-item" data-action="nav" data-view="reports"><span>📥</span> Reports / Export</button>
    </nav>
    <div class="side-foot">
      <button class="btn ghost block sm" data-action="change-year">⇄ Change Year</button>
      <button class="btn ghost block sm" data-action="logout">Logout</button>
    </div>
  </aside>
  <div class="side-backdrop" data-action="close-sidebar"></div>

  <div class="main">
    <header class="topbar">
      <button class="icon-btn menu-btn" data-action="open-sidebar" aria-label="Menu">☰</button>
      <h2 id="pageTitle">Dashboard</h2>
      <div class="top-right">
        <span class="year-pill" id="topYear">—</span>
        <button class="icon-btn bell" data-action="show-alerts" title="Overdue pending PIs (3+ days)">🔔<em class="badge red" id="bBell">0</em></button>
      </div>
    </header>
    <div id="viewRoot" class="content"></div>
  </div>
</section>

<!-- MODALS -->
<div class="modal" id="pinModal">
  <form class="modal-card" id="pinForm" autocomplete="off">
    <h3>🔒 Admin PIN Required</h3>
    <p id="pinMsg" class="muted">Enter admin PIN to continue.</p>
    <input id="pinInput" type="password" inputmode="numeric" maxlength="6" placeholder="6-digit PIN">
    <div class="error" id="pinErr"></div>
    <div class="modal-actions">
      <button type="button" class="btn ghost" data-action="pin-cancel">Cancel</button>
      <button type="submit" class="btn primary">Verify</button>
    </div>
  </form>
</div>

<div class="modal" id="alertModal">
  <div class="modal-card wide">
    <h3>⚠ Pending PI Alert</h3>
    <p class="muted">These PIs have been pending for 3 days or more.</p>
    <div id="alertBody" class="alert-list"></div>
    <div class="modal-actions">
      <button class="btn ghost" data-action="alert-close">Close</button>
      <button class="btn primary" data-action="alert-go">Open Pending PI</button>
    </div>
  </div>
</div>

<script>
'use strict';
/* PI Tracker Pro - data is stored in the browser (localStorage). */
const CFG = { user: 'admin', pass: 'Daniel', pin: '123456', alertDays: 3, key: 'pi_tracker_pro_v1' };

const $  = (s, r = document) => r.querySelector(s);
const $$ = (s, r = document) => [...r.querySelectorAll(s)];
const esc = s => String(s == null ? '' : s).replace(/[&<>"']/g, c => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[c]));
const uid = () => Date.now().toString(36) + Math.random().toString(36).slice(2, 7);
const DAY = 864e5;
const hasChart = () => typeof Chart !== 'undefined';

function fmt(iso) {
  if (!iso) return '—';
  return new Date(iso).toLocaleString('en-GB', { day: '2-digit', month: 'short', year: 'numeric', hour: '2-digit', minute: '2-digit', hour12: true });
}
function toInputDate(d) {
  const z = n => String(n).padStart(2, '0');
  return d.getFullYear() + '-' + z(d.getMonth() + 1) + '-' + z(d.getDate());
}
function daysBetween(a, b) { return Math.floor((new Date(b) - new Date(a)) / DAY); }
function ageText(p) {
  const ms = Date.now() - new Date(p.createdAt);
  const d = Math.floor(ms / DAY), h = Math.floor((ms % DAY) / 36e5);
  return d > 0 ? d + 'd ' + h + 'h' : h + 'h';
}
function toast(msg, type) {
  const t = document.createElement('div');
  t.className = 'toast ' + (type || 'ok'); t.textContent = msg;
  $('#toasts').appendChild(t);
  setTimeout(() => t.remove(), 3500);
}

/* ---------- data ---------- */
function load() {
  try { const d = JSON.parse(localStorage.getItem(CFG.key)); if (d && d.years) return d; } catch (e) {}
  return { years: { '2026-2027': { pis: [] } }, teams: ['Production', 'Quality', 'Packing', 'Stores', 'Accounts'] };
}
let db = load();
function save() { try { localStorage.setItem(CFG.key, JSON.stringify(db)); } catch (e) { toast('Browser storage blocked - data will not be saved', 'err'); } }

const ui = { year: null, view: 'dashboard', draftTeams: [], draftPriority: false, pf: { q: '', team: '' }, cf: { q: '' }, df: { q: '' }, charts: [], clock: null };

const pis = () => db.years[ui.year].pis;
const byStatus = s => pis().filter(p => p.status === s);
const isOverdue = p => p.status === 'pending' && Date.now() - new Date(p.createdAt) >= CFG.alertDays * DAY;
const sortPIs = list => [...list].sort((a, b) => ((b.priority ? 1 : 0) - (a.priority ? 1 : 0)) || (new Date(a.createdAt) - new Date(b.createdAt)));
const yearLabel = y => y.replace('-', ' – ');
function allTeamNames() {
  const set = new Set(db.teams);
  pis().forEach(p => p.teams.forEach(t => set.add(t.name)));
  return [...set];
}
function pendingByTeam() {
  const m = {}; allTeamNames().forEach(n => m[n] = 0);
  byStatus('pending').forEach(p => p.teams.forEach(t => { if (!t.done) m[t.name] = (m[t.name] || 0) + 1; }));
  return m;
}

/* ---------- screens ---------- */
function showScreen(id) {
  $$('.screen').forEach(s => s.classList.remove('active'));
  $('#' + id).classList.add('active');
  window.scrollTo(0, 0);
}

/* ---------- login ---------- */
$('#loginForm').addEventListener('submit', e => {
  e.preventDefault();
  if ($('#lUser').value.trim() === CFG.user && $('#lPass').value === CFG.pass) {
    try { sessionStorage.setItem('pi_auth', '1'); } catch (x) {}
    $('#loginErr').textContent = ''; $('#loginForm').reset();
    renderYears(); showScreen('yearScreen');
  } else {
    $('#loginErr').textContent = 'Invalid username or password.';
    $('#loginForm').classList.add('shake'); setTimeout(() => $('#loginForm').classList.remove('shake'), 400);
  }
});
function logout() {
  try { sessionStorage.removeItem('pi_auth'); } catch (x) {}
  ui.year = null; showScreen('loginScreen');
}

/* ---------- admin PIN modal ---------- */
let pinResolve = null;
function askPin(msg) {
  return new Promise(res => {
    pinResolve = res;
    $('#pinMsg').textContent = msg || 'Enter admin PIN to continue.';
    $('#pinInput').value = ''; $('#pinErr').textContent = '';
    $('#pinModal').classList.add('show'); setTimeout(() => $('#pinInput').focus(), 50);
  });
}
function closePin(ok) { $('#pinModal').classList.remove('show'); if (pinResolve) pinResolve(ok); pinResolve = null; }
$('#pinForm').addEventListener('submit', e => {
  e.preventDefault();
  if ($('#pinInput').value === CFG.pin) closePin(true);
  else {
    $('#pinErr').textContent = 'Wrong PIN. Try again.'; $('#pinInput').value = '';
    $('#pinForm').classList.add('shake'); setTimeout(() => $('#pinForm').classList.remove('shake'), 400);
  }
});

/* ---------- years ---------- */
function renderYears() {
  const keys = Object.keys(db.years).sort();
  $('#yearGrid').innerHTML = keys.map(y => {
    const list = db.years[y].pis;
    const pend = list.filter(p => p.status === 'pending').length;
    const over = list.filter(p => p.status === 'pending' && Date.now() - new Date(p.createdAt) >= CFG.alertDays * DAY).length;
    return '<div class="year-card">' +
      '<h3>' + esc(yearLabel(y)) + '</h3>' +
      '<div class="stats"><div><b>' + list.length + '</b>Total PI</div><div><b>' + pend + '</b>Pending</div><div><b style="color:' + (over ? 'var(--red)' : 'inherit') + '">' + over + '</b>Overdue</div></div>' +
      '<div class="acts"><button class="btn primary" data-action="open-year" data-y="' + esc(y) + '">Open</button>' +
      '<button class="btn danger-ghost" data-action="delete-year" data-y="' + esc(y) + '" title="Delete year (PIN)">🗑</button></div></div>';
  }).join('');
}
$('#newYearStart').addEventListener('input', e => {
  const v = parseInt(e.target.value, 10);
  $('#yearPreview').textContent = v ? v + ' – ' + (v + 1) : '—';
});
$('#yearForm').addEventListener('submit', e => {
  e.preventDefault();
  const s = parseInt($('#newYearStart').value, 10);
  if (!s || s < 2000 || s > 2099) return toast('Enter a valid start year', 'err');
  const key = s + '-' + (s + 1);
  if (db.years[key]) return toast('This year already exists', 'warn');
  db.years[key] = { pis: [] }; save(); renderYears();
  $('#yearForm').reset(); $('#yearPreview').textContent = '—';
  toast('Year ' + s + ' – ' + (s + 1) + ' added');
});
function openYear(y) {
  ui.year = y; ui.pf = { q: '', team: '' }; ui.cf = { q: '' }; ui.df = { q: '' };
  $('#sideYear').textContent = $('#topYear').textContent = yearLabel(y);
  showScreen('appScreen'); setView('dashboard');
  if (byStatus('pending').some(isOverdue)) showAlerts();
}

/* ---------- navigation ---------- */
const TITLES = { dashboard: 'Dashboard', new: 'New Add PI', pending: 'Pending PI', completed: 'Completed PI', dispatched: 'Dispatched PI', reports: 'Reports / Export' };
function setView(v) {
  ui.view = v; clearInterval(ui.clock); destroyCharts();
  $$('.nav-item').forEach(b => b.classList.toggle('active', b.dataset.view === v));
  $('#pageTitle').textContent = TITLES[v];
  ({ dashboard: renderDashboard, new: renderNew, pending: renderPending, completed: renderCompleted, dispatched: renderDispatched, reports: renderReports })[v]();
  updateBadges(); closeSidebar(); window.scrollTo(0, 0);
}
function updateBadges() {
  if (!ui.year) return;
  $('#bPending').textContent = byStatus('pending').length;
  $('#bCompleted').textContent = byStatus('completed').length;
  $('#bDispatched').textContent = byStatus('dispatched').length;
  const over = byStatus('pending').filter(isOverdue).length;
  const b = $('#bBell'); b.textContent = over; b.style.display = over ? 'inline-block' : 'none';
}
function openSidebar() { $('#sidebar').classList.add('open'); $('.side-backdrop').classList.add('show'); }
function closeSidebar() { $('#sidebar').classList.remove('open'); $('.side-backdrop').classList.remove('show'); }
function destroyCharts() { ui.charts.forEach(c => c.destroy()); ui.charts = []; }

/* ---------- alerts ---------- */
function showAlerts() {
  const list = sortPIs(byStatus('pending').filter(isOverdue));
  $('#alertBody').innerHTML = list.length ? list.map(p => {
    const waiting = p.teams.filter(t => !t.done).map(t => t.name).join(', ');
    return '<div class="alert-item"><b>' + esc(p.piNo) + '</b> — ' + esc(p.company) + (p.priority ? ' ★' : '') + '<br>' +
      '<span class="small muted">Pending ' + ageText(p) + ' · Waiting: ' + esc(waiting) + '</span></div>';
  }).join('') : '<div class="empty">No overdue pending PIs. 🎉</div>';
  $('#alertModal').classList.add('show');
}

/* ================= DASHBOARD ================= */
function kpi(label, val, cls) { return '<div class="kpi ' + (cls || '') + '"><span>' + label + '</span><b>' + val + '</b></div>'; }

function renderDashboard() {
  const all = pis(), p = byStatus('pending'), c = byStatus('completed'), d = byStatus('dispatched');
  const over = p.filter(isOverdue), pr = p.filter(x => x.priority);
  const recent = [...all].sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt)).slice(0, 6);
  $('#viewRoot').innerHTML =
    (over.length ? '<div class="banner danger">⚠ ' + over.length + ' pending PI(s) are older than ' + CFG.alertDays + ' days. <button class="link" data-action="show-alerts">View list</button></div>' : '') +
    '<div class="kpis">' + kpi('Total PI', all.length) + kpi('Pending', p.length, 'amber') + kpi('Completed', c.length, 'green') +
      kpi('Dispatched', d.length, 'teal') + kpi('Priority', pr.length, 'purple') + kpi('Overdue (3d+)', over.length, 'red') + '</div>' +
    '<div class="grid-2">' +
      '<div class="card"><h3>PI Status</h3><div class="chart-box"><canvas id="chStatus"></canvas></div></div>' +
      '<div class="card"><h3>Team-wise Pending PI</h3><div class="chart-box"><canvas id="chTeam"></canvas></div></div>' +
    '</div>' +
    '<div class="card"><h3>Recently Added PI</h3>' +
      (recent.length ? '<div class="table-wrap"><table><thead><tr><th>PI No</th><th>Company</th><th>Added</th><th>Status</th></tr></thead><tbody>' +
      recent.map(x => '<tr><td><b>' + esc(x.piNo) + '</b></td><td>' + esc(x.company) + '</td><td>' + fmt(x.createdAt) + '</td><td><span class="pill ' + x.status + '">' + x.status + '</span></td></tr>').join('') +
      '</tbody></table></div>' : '<div class="empty">No PI added yet. Use “New Add PI” to start.</div>') +
    '</div>';
  if (!hasChart()) {
    $$('.chart-box').forEach(b => b.innerHTML = '<div class="empty">Chart library not loaded (check internet).</div>');
    return;
  }
  const ptm = pendingByTeam(), names = Object.keys(ptm);
  ui.charts.push(new Chart($('#chStatus'), {
    type: 'doughnut',
    data: { labels: ['Pending', 'Completed', 'Dispatched'], datasets: [{ data: [p.length, c.length, d.length], backgroundColor: ['#f59e0b', '#16a34a', '#2563eb'] }] },
    options: { maintainAspectRatio: false, plugins: { legend: { position: 'bottom' } } }
  }));
  ui.charts.push(new Chart($('#chTeam'), {
    type: 'bar',
    data: { labels: names, datasets: [{ label: 'Pending PI', data: names.map(n => ptm[n]), backgroundColor: '#2563eb', borderRadius: 6 }] },
    options: { maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true, ticks: { precision: 0 } } } }
  }));
}

/* ================= NEW PI ================= */
function renderNew() {
  ui.draftTeams = []; ui.draftPriority = false;
  $('#viewRoot').innerHTML =
  '<div class="new-grid">' +
    '<form class="card" id="piForm" autocomplete="off">' +
      '<h3>Add New PI</h3>' +
      '<div class="two">' +
        '<label>PI Number *<input type="text" id="fPi" placeholder="Enter PI number manually" required></label>' +
        '<label>Date &amp; Time (auto)<input type="text" id="fTime" readonly></label>' +
      '</div>' +
      '<label>Company Name *<input type="text" id="fCompany" placeholder="Enter company name" required></label>' +
      '<label>Following Team(s) *' +
        '<div class="team-add"><select id="fTeamSel"></select><button type="button" class="btn primary" data-action="add-draft-team">Add</button></div>' +
      '</label>' +
      '<div class="chips" id="draftChips"></div>' +
      '<label class="check"><input type="checkbox" id="fPrio"> ★ Mark as priority PI</label>' +
      '<button class="btn primary" type="submit">Save PI</button>' +
    '</form>' +
    '<div class="card">' +
      '<h3>Team Names</h3>' +
      '<p class="muted small" style="margin-bottom:12px">Add or remove team names used in the dropdown. Removing a team needs the admin PIN.</p>' +
      '<form id="teamForm" class="team-add" autocomplete="off"><input type="text" id="newTeam" placeholder="New team name"><button class="btn primary" type="submit">Add</button></form>' +
      '<div class="chips" id="masterChips" style="margin-top:14px"></div>' +
    '</div>' +
  '</div>';
  const tick = () => { const el = $('#fTime'); if (el) el.value = fmt(new Date().toISOString()); };
  tick(); ui.clock = setInterval(tick, 1000);
  drawTeamSelect(); drawDraftChips(); drawMasterChips();
  $('#fPrio').addEventListener('change', e => ui.draftPriority = e.target.checked);
  $('#piForm').addEventListener('submit', submitPI);
  $('#teamForm').addEventListener('submit', e => {
    e.preventDefault();
    const n = $('#newTeam').value.trim();
    if (!n) return;
    if (db.teams.some(t => t.toLowerCase() === n.toLowerCase())) return toast('Team already exists', 'warn');
    db.teams.push(n); save(); $('#newTeam').value = ''; drawTeamSelect(); drawMasterChips(); toast('Team added');
  });
}
function drawTeamSelect() {
  const sel = $('#fTeamSel'); if (!sel) return;
  const opts = db.teams.filter(t => !ui.draftTeams.includes(t));
  sel.innerHTML = opts.length ? opts.map(t => '<option>' + esc(t) + '</option>').join('') : '<option value="">— all teams added —</option>';
}
function drawDraftChips() {
  const el = $('#draftChips'); if (!el) return;
  el.innerHTML = ui.draftTeams.length
    ? ui.draftTeams.map((t, i) => '<span class="chip">' + esc(t) + '<button type="button" data-action="remove-draft-team" data-i="' + i + '" title="Remove">×</button></span>').join('')
    : '<span class="muted small">No team added yet.</span>';
}
function drawMasterChips() {
  const el = $('#masterChips'); if (!el) return;
  el.innerHTML = db.teams.length
    ? db.teams.map((t, i) => '<span class="chip gray">' + esc(t) + '<button type="button" data-action="remove-master-team" data-i="' + i + '" title="Remove (PIN)">×</button></span>').join('')
    : '<span class="muted small">No teams yet. Add one above.</span>';
}
function submitPI(e) {
  e.preventDefault();
  const piNo = $('#fPi').value.trim(), company = $('#fCompany').value.trim();
  if (!piNo || !company) return;
  if (!ui.draftTeams.length) return toast('Add at least one team', 'err');
  if (pis().some(p => p.piNo.toLowerCase() === piNo.toLowerCase())) return toast('This PI number already exists in this year', 'err');
  pis().push({
    id: uid(), piNo: piNo, company: company, createdAt: new Date().toISOString(), priority: ui.draftPriority, status: 'pending',
    teams: ui.draftTeams.map(n => ({ name: n, done: false, doneAt: null })),
    completedAt: null, invoiceNo: null, dispatchedAt: null
  });
  save(); toast('PI ' + piNo + ' added'); updateBadges(); renderNew();
}

/* ================= PENDING PI ================= */
function renderPending() {
  $('#viewRoot').innerHTML =
    '<div id="teamCards" class="team-cards"></div>' +
    '<div class="toolbar">' +
      '<input type="text" class="search" id="pSearch" placeholder="🔍 Search PI number, company or team…" value="' + esc(ui.pf.q) + '">' +
      '<select id="pTeam"></select>' +
    '</div>' +
    '<div id="pendingList" class="pi-list"></div>';
  $('#pSearch').addEventListener('input', e => { ui.pf.q = e.target.value; updatePendingList(); });
  $('#pTeam').addEventListener('change', e => { ui.pf.team = e.target.value; updatePendingList(); });
  updatePendingList();
}
function piCard(p) {
  const over = isOverdue(p), done = p.teams.filter(t => t.done).length;
  return '<article class="pi-card ' + (p.priority ? 'is-priority' : '') + ' ' + (over ? 'is-overdue' : '') + '">' +
    '<div class="pi-head">' +
      '<div><span class="pi-no">' + esc(p.piNo) + '</span>' + (p.priority ? '<span class="tag prio">★ Priority</span>' : '') + (over ? '<span class="tag late">Overdue</span>' : '') +
        '<div class="pi-company">' + esc(p.company) + '</div></div>' +
      '<div class="pi-side"><div class="muted small">Added ' + fmt(p.createdAt) + '</div><div class="muted small">Pending ' + ageText(p) + '</div></div>' +
    '</div>' +
    '<div class="progress"><span style="width:' + ((done / p.teams.length) * 100) + '%"></span></div>' +
    '<div class="small muted">' + done + '/' + p.teams.length + ' teams completed</div>' +
    '<ul class="team-list">' + p.teams.map((t, i) => '<li class="' + (t.done ? 'done' : '') + '">' +
      '<span class="tname">' + (t.done ? '✔' : '⏳') + ' ' + esc(t.name) + '</span>' +
      (t.done ? '<span class="small">' + fmt(t.doneAt) + '</span>' : '<button class="btn sm success" data-action="mark-team" data-id="' + p.id + '" data-i="' + i + '">Mark Completed</button>') + '</li>').join('') + '</ul>' +
    '<div class="pi-actions">' +
      '<button class="btn sm ' + (p.priority ? 'warn' : 'ghost') + '" data-action="toggle-priority" data-id="' + p.id + '">' + (p.priority ? '★ Remove Priority' : '☆ Set Priority') + '</button>' +
      '<button class="btn sm danger-ghost" data-action="delete-pi" data-id="' + p.id + '">Delete</button>' +
    '</div></article>';
}
function updatePendingList() {
  const all = byStatus('pending'), ptm = pendingByTeam(), names = Object.keys(ptm);
  $('#teamCards').innerHTML = '<div class="tcard ' + (ui.pf.team === '' ? 'active' : '') + '" data-action="filter-team" data-team=""><b>' + all.length + '</b><span>All Teams</span></div>' +
    names.map(n => '<div class="tcard ' + (ui.pf.team === n ? 'active' : '') + '" data-action="filter-team" data-team="' + esc(n) + '"><b>' + ptm[n] + '</b><span>' + esc(n) + ' pending</span></div>').join('');
  $('#pTeam').innerHTML = '<option value="">All teams</option>' + names.map(n => '<option ' + (ui.pf.team === n ? 'selected' : '') + '>' + esc(n) + '</option>').join('');
  const q = ui.pf.q.toLowerCase().trim();
  let list = all;
  if (q) list = list.filter(p => (p.piNo + ' ' + p.company + ' ' + p.teams.map(t => t.name).join(' ')).toLowerCase().includes(q));
  if (ui.pf.team) list = list.filter(p => p.teams.some(t => t.name === ui.pf.team && !t.done));
  list = sortPIs(list);
  $('#pendingList').innerHTML = list.length ? list.map(piCard).join('') : '<div class="empty" style="grid-column:1/-1">No pending PI found.</div>';
  updateBadges();
}

/* ================= COMPLETED PI ================= */
function renderCompleted() {
  $('#viewRoot').innerHTML =
    '<div class="toolbar"><input type="text" class="search" id="cSearch" placeholder="🔍 Search PI number or company…" value="' + esc(ui.cf.q) + '"></div>' +
    '<p class="muted small" style="margin-bottom:12px">Enter the invoice number manually and press Dispatch to move the PI to the Dispatched list.</p>' +
    '<div id="completedList"></div>';
  $('#cSearch').addEventListener('input', e => { ui.cf.q = e.target.value; updateCompletedList(); });
  updateCompletedList();
}
function updateCompletedList() {
  const q = ui.cf.q.toLowerCase().trim();
  let list = byStatus('completed');
  if (q) list = list.filter(p => (p.piNo + ' ' + p.company).toLowerCase().includes(q));
  list = sortPIs(list);
  $('#completedList').innerHTML = list.length ? '<div class="table-wrap"><table><thead><tr>' +
    '<th>PI No</th><th>Company</th><th>Teams</th><th>Added</th><th>Completed</th><th>Invoice No → Dispatch</th></tr></thead><tbody>' +
    list.map(p => '<tr>' +
      '<td><b>' + esc(p.piNo) + '</b>' + (p.priority ? ' <span class="tag prio">★</span>' : '') + '</td><td>' + esc(p.company) + '</td>' +
      '<td>' + p.teams.map(t => esc(t.name)).join(', ') + '</td><td>' + fmt(p.createdAt) + '</td><td>' + fmt(p.completedAt) + '</td>' +
      '<td><div class="inv-cell"><input type="text" id="inv-' + p.id + '" placeholder="Invoice number" data-enter="' + p.id + '">' +
      '<button class="btn sm primary" data-action="dispatch" data-id="' + p.id + '">Dispatch</button></div></td></tr>').join('') +
    '</tbody></table></div>' : '<div class="empty">No completed PI waiting for dispatch.</div>';
  updateBadges();
}

/* ================= DISPATCHED PI ================= */
function renderDispatched() {
  $('#viewRoot').innerHTML =
    '<div class="toolbar"><input type="text" class="search" id="dSearch" placeholder="🔍 Search PI, company or invoice number…" value="' + esc(ui.df.q) + '"></div>' +
    '<div id="dispatchedList"></div>';
  $('#dSearch').addEventListener('input', e => { ui.df.q = e.target.value; updateDispatchedList(); });
  updateDispatchedList();
}
function updateDispatchedList() {
  const q = ui.df.q.toLowerCase().trim();
  let list = byStatus('dispatched');
  if (q) list = list.filter(p => (p.piNo + ' ' + p.company + ' ' + p.invoiceNo).toLowerCase().includes(q));
  list.sort((a, b) => new Date(b.dispatchedAt) - new Date(a.dispatchedAt));
  $('#dispatchedList').innerHTML = list.length ? '<div class="table-wrap"><table><thead><tr>' +
    '<th>PI No</th><th>Company</th><th>Invoice No</th><th>Added</th><th>Completed</th><th>Dispatched</th><th>Days Taken</th></tr></thead><tbody>' +
    list.map(p => '<tr><td><b>' + esc(p.piNo) + '</b></td><td>' + esc(p.company) + '</td><td><span class="pill dispatched">' + esc(p.invoiceNo) + '</span></td>' +
      '<td>' + fmt(p.createdAt) + '</td><td>' + fmt(p.completedAt) + '</td><td>' + fmt(p.dispatchedAt) + '</td><td>' + daysBetween(p.createdAt, p.dispatchedAt) + '</td></tr>').join('') +
    '</tbody></table></div>' : '<div class="empty">No dispatched PI yet.</div>';
  updateBadges();
}

/* ================= REPORTS + EXCEL EXPORT ================= */
function renderReports() {
  const now = new Date(), first = new Date(now.getFullYear(), now.getMonth(), 1);
  $('#viewRoot').innerHTML =
    '<div class="card">' +
      '<h3>Date-to-Date Report</h3>' +
      '<p class="muted small" style="margin-bottom:14px">Filtered by PI added date. The Excel file contains summary charts, all PI data and team-wise report.</p>' +
      '<div class="two" style="max-width:520px">' +
        '<label>From Date<input type="date" id="rFrom" value="' + toInputDate(first) + '"></label>' +
        '<label>To Date<input type="date" id="rTo" value="' + toInputDate(now) + '"></label>' +
      '</div>' +
      '<div class="btn-row">' +
        '<button class="btn primary" data-action="export-range">📥 Export Excel (Date Range)</button>' +
        '<button class="btn ghost" data-action="export-all">📥 Export All Tracking</button>' +
      '</div>' +
    '</div>' +
    '<div id="reportPreview"></div>';
  $('#rFrom').addEventListener('change', drawReportPreview);
  $('#rTo').addEventListener('change', drawReportPreview);
  drawReportPreview();
}
function rangeData(from, to) {
  if (!from && !to) return [...pis()];
  const f = from ? new Date(from + 'T00:00:00') : new Date(0);
  const t = to ? new Date(to + 'T23:59:59.999') : new Date(8.64e15);
  return pis().filter(p => { const d = new Date(p.createdAt); return d >= f && d <= t; });
}
function teamStats(list) {
  const m = {};
  list.forEach(p => p.teams.forEach(t => {
    m[t.name] = m[t.name] || { assigned: 0, done: 0 };
    m[t.name].assigned++; if (t.done) m[t.name].done++;
  }));
  return m;
}
function drawReportPreview() {
  const list = rangeData($('#rFrom').value, $('#rTo').value), ts = teamStats(list);
  const cnt = s => list.filter(p => p.status === s).length;
  $('#reportPreview').innerHTML =
    '<div class="kpis">' + kpi('PI in range', list.length) + kpi('Pending', cnt('pending'), 'amber') + kpi('Completed', cnt('completed'), 'green') + kpi('Dispatched', cnt('dispatched'), 'teal') + '</div>' +
    '<div class="card"><h3>Team-wise Summary</h3>' +
    (Object.keys(ts).length ? '<div class="table-wrap"><table><thead><tr><th>Team</th><th>Assigned</th><th>Completed</th><th>Pending</th></tr></thead><tbody>' +
      Object.keys(ts).map(n => '<tr><td><b>' + esc(n) + '</b></td><td>' + ts[n].assigned + '</td><td>' + ts[n].done + '</td><td>' + (ts[n].assigned - ts[n].done) + '</td></tr>').join('') +
    '</tbody></table></div>' : '<div class="empty">No data in this date range.</div>') + '</div>';
}

function chartImage(cfg, w, h) {
  const c = document.createElement('canvas'); c.width = w || 560; c.height = h || 300;
  cfg.options = Object.assign({ responsive: false, animation: false, devicePixelRatio: 1 }, cfg.options || {});
  cfg.plugins = [{ id: 'bg', beforeDraw(ch) { const x = ch.ctx; x.save(); x.fillStyle = '#fff'; x.fillRect(0, 0, ch.width, ch.height); x.restore(); } }];
  const ch = new Chart(c, cfg), url = c.toDataURL('image/png'); ch.destroy(); return url;
}
function styleHeader(row, color) {
  row.eachCell(c => {
    c.font = { bold: true, color: { argb: 'FFFFFFFF' } };
    c.fill = { type: 'pattern', pattern: 'solid', fgColor: { argb: color || 'FF1E3A8A' } };
    c.alignment = { vertical: 'middle', horizontal: 'center', wrapText: true };
    c.border = { top: { style: 'thin' }, bottom: { style: 'thin' }, left: { style: 'thin' }, right: { style: 'thin' } };
  });
  row.height = 22;
}
function addSheet(wb, name, cols, rows) {
  const ws = wb.addWorksheet(name, { views: [{ state: 'frozen', ySplit: 1 }] });
  ws.columns = cols.map(c => ({ header: c.h, key: c.k, width: c.w }));
  rows.forEach(r => ws.addRow(r));
  styleHeader(ws.getRow(1));
  ws.eachRow((row, n) => { if (n > 1) row.eachCell(c => { c.alignment = { vertical: 'middle', wrapText: true }; c.border = { bottom: { style: 'hair' } }; }); });
  if (ws.rowCount > 1) ws.autoFilter = { from: { row: 1, column: 1 }, to: { row: 1, column: cols.length } };
  return ws;
}
function piRow(p) {
  const end = p.status === 'dispatched' ? p.dispatchedAt : p.status === 'completed' ? p.completedAt : new Date().toISOString();
  return {
    pi: p.piNo, company: p.company, added: fmt(p.createdAt),
    teams: p.teams.map(t => t.name).join(', '),
    tstatus: p.teams.map(t => t.name + ': ' + (t.done ? 'Done' : 'Pending')).join(' | '),
    prio: p.priority ? 'Yes' : 'No', status: p.status.charAt(0).toUpperCase() + p.status.slice(1),
    completed: fmt(p.completedAt), invoice: p.invoiceNo || '', dispatched: fmt(p.dispatchedAt), days: daysBetween(p.createdAt, end)
  };
}
const PI_COLS = [
  { h: 'PI Number', k: 'pi', w: 16 }, { h: 'Company', k: 'company', w: 28 }, { h: 'Added Date & Time', k: 'added', w: 22 },
  { h: 'Teams', k: 'teams', w: 26 }, { h: 'Team Status', k: 'tstatus', w: 40 }, { h: 'Priority', k: 'prio', w: 10 },
  { h: 'Status', k: 'status', w: 13 }, { h: 'Completed On', k: 'completed', w: 22 }, { h: 'Invoice No', k: 'invoice', w: 18 },
  { h: 'Dispatched On', k: 'dispatched', w: 22 }, { h: 'Age / Days Taken', k: 'days', w: 14 }
];

async function exportExcel(from, to) {
  if (typeof ExcelJS === 'undefined' || !hasChart()) return toast('Excel/Chart library failed to load. Check internet connection.', 'err');
  const list = rangeData(from, to);
  if (!list.length) return toast('No PI data in the selected range', 'warn');
  toast('Preparing Excel report…', 'ok');
  try {
    const cnt = s => list.filter(p => p.status === s).length;
    const ts = teamStats(list), tnames = Object.keys(ts);
    const wb = new ExcelJS.Workbook(); wb.creator = 'PI Tracker Pro'; wb.created = new Date();

    const ws = wb.addWorksheet('Summary', { views: [{ showGridLines: false }] });
    ws.columns = [{ width: 30 }, { width: 14 }, { width: 14 }, { width: 14 }, { width: 3 }];
    ws.mergeCells('A1:D1'); ws.getCell('A1').value = 'PI TRACKING REPORT — ' + yearLabel(ui.year);
    ws.getCell('A1').font = { bold: true, size: 16, color: { argb: 'FF1E3A8A' } }; ws.getRow(1).height = 28;
    ws.getCell('A2').value = 'Period: ' + (from || 'Start') + '  to  ' + (to || 'Today');
    ws.getCell('A3').value = 'Generated: ' + fmt(new Date().toISOString());

    ws.getRow(5).values = ['Metric', 'Count']; styleHeader(ws.getRow(5));
    [['Total PI', list.length], ['Pending', cnt('pending')], ['Completed (awaiting dispatch)', cnt('completed')], ['Dispatched', cnt('dispatched')],
     ['Priority PI', list.filter(p => p.priority).length], ['Overdue (3d+)', list.filter(isOverdue).length]]
      .forEach((r, i) => { const row = ws.getRow(6 + i); row.values = r; row.getCell(2).alignment = { horizontal: 'center' }; });
    ws.getRow(13).values = ['Team', 'Assigned', 'Completed', 'Pending']; styleHeader(ws.getRow(13));
    tnames.forEach((n, i) => { const row = ws.getRow(14 + i); row.values = [n, ts[n].assigned, ts[n].done, ts[n].assigned - ts[n].done]; for (let c = 2; c <= 4; c++) row.getCell(c).alignment = { horizontal: 'center' }; });

    const daily = {};
    list.forEach(p => { const k = toInputDate(new Date(p.createdAt)); daily[k] = (daily[k] || 0) + 1; });
    const dkeys = Object.keys(daily).sort();

    const img1 = wb.addImage({ base64: chartImage({ type: 'doughnut', data: { labels: ['Pending', 'Completed', 'Dispatched'], datasets: [{ data: [cnt('pending'), cnt('completed'), cnt('dispatched')], backgroundColor: ['#f59e0b', '#16a34a', '#2563eb'] }] }, options: { plugins: { title: { display: true, text: 'PI Status' }, legend: { position: 'bottom' } } } }), extension: 'png' });
    const img2 = wb.addImage({ base64: chartImage({ type: 'bar', data: { labels: tnames, datasets: [{ label: 'Pending', data: tnames.map(n => ts[n].assigned - ts[n].done), backgroundColor: '#f59e0b' }, { label: 'Completed', data: tnames.map(n => ts[n].done), backgroundColor: '#16a34a' }] }, options: { plugins: { title: { display: true, text: 'Team-wise PI Work' } }, scales: { y: { beginAtZero: true, ticks: { precision: 0 } } } } }), extension: 'png' });
    const img3 = wb.addImage({ base64: chartImage({ type: 'line', data: { labels: dkeys, datasets: [{ label: 'PI added per day', data: dkeys.map(k => daily[k]), borderColor: '#2563eb', backgroundColor: 'rgba(37,99,235,.15)', fill: true, tension: 0.3 }] }, options: { plugins: { title: { display: true, text: 'PI Added (Date-wise)' } }, scales: { y: { beginAtZero: true, ticks: { precision: 0 } } } } }), extension: 'png' });
    ws.addImage(img1, { tl: { col: 5, row: 1 }, ext: { width: 540, height: 290 } });
    ws.addImage(img2, { tl: { col: 5, row: 17 }, ext: { width: 540, height: 290 } });
    ws.addImage(img3, { tl: { col: 5, row: 33 }, ext: { width: 540, height: 290 } });

    const sorted = [...list].sort((a, b) => new Date(a.createdAt) - new Date(b.createdAt));
    addSheet(wb, 'All PI', PI_COLS, sorted.map(piRow));
    addSheet(wb, 'Pending PI', PI_COLS, sorted.filter(p => p.status === 'pending').map(piRow));
    addSheet(wb, 'Completed PI', PI_COLS, sorted.filter(p => p.status === 'completed').map(piRow));
    addSheet(wb, 'Dispatched PI', PI_COLS, sorted.filter(p => p.status === 'dispatched').map(piRow));
    addSheet(wb, 'Team Wise', [{ h: 'Team', k: 't', w: 22 }, { h: 'Assigned', k: 'a', w: 12 }, { h: 'Completed', k: 'c', w: 12 }, { h: 'Pending', k: 'p', w: 12 }],
      tnames.map(n => ({ t: n, a: ts[n].assigned, c: ts[n].done, p: ts[n].assigned - ts[n].done })));

    const buf = await wb.xlsx.writeBuffer();
    const blob = new Blob([buf], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' });
    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob);
    a.download = 'PI_Report_' + ui.year + '_' + (from || 'start') + '_to_' + (to || 'today') + '.xlsx';
    document.body.appendChild(a); a.click(); a.remove();
    setTimeout(() => URL.revokeObjectURL(a.href), 4000);
    toast('Excel report downloaded');
  } catch (err) {
    console.error(err); toast('Export failed: ' + err.message, 'err');
  }
}

/* ================= GLOBAL CLICK HANDLER ================= */
document.addEventListener('click', async e => {
  const b = e.target.closest('[data-action]'); if (!b) return;
  const a = b.dataset.action, id = b.dataset.id;
  const find = () => pis().find(p => p.id === id);

  switch (a) {
    case 'logout': logout(); break;
    case 'change-year': ui.year = null; clearInterval(ui.clock); renderYears(); showScreen('yearScreen'); break;
    case 'open-year': openYear(b.dataset.y); break;
    case 'delete-year': {
      const y = b.dataset.y;
      if (!(await askPin('Enter admin PIN to delete year ' + yearLabel(y) + ' and all its PI.'))) break;
      if (!confirm('Delete ' + yearLabel(y) + ' permanently?')) break;
      delete db.years[y]; if (!Object.keys(db.years).length) db.years['2026-2027'] = { pis: [] };
      save(); renderYears(); toast('Year deleted', 'warn'); break;
    }
    case 'nav': setView(b.dataset.view); break;
    case 'open-sidebar': openSidebar(); break;
    case 'close-sidebar': closeSidebar(); break;
    case 'pin-cancel': closePin(false); break;
    case 'show-alerts': if (ui.year) showAlerts(); break;
    case 'alert-close': $('#alertModal').classList.remove('show'); break;
    case 'alert-go': $('#alertModal').classList.remove('show'); setView('pending'); break;

    case 'add-draft-team': {
      const v = $('#fTeamSel').value; if (!v) return toast('No team to add', 'warn');
      ui.draftTeams.push(v); drawTeamSelect(); drawDraftChips(); break;
    }
    case 'remove-draft-team': ui.draftTeams.splice(+b.dataset.i, 1); drawTeamSelect(); drawDraftChips(); break;
    case 'remove-master-team': {
      const name = db.teams[+b.dataset.i];
      if (!(await askPin('Enter admin PIN to remove team "' + name + '".'))) break;
      db.teams.splice(+b.dataset.i, 1); ui.draftTeams = ui.draftTeams.filter(t => t !== name);
      save(); drawTeamSelect(); drawDraftChips(); drawMasterChips(); toast('Team removed', 'warn'); break;
    }

    case 'filter-team': ui.pf.team = b.dataset.team; updatePendingList(); break;
    case 'mark-team': {
      const p = find(); if (!p) break;
      const t = p.teams[+b.dataset.i]; if (!t || t.done) break;
      t.done = true; t.doneAt = new Date().toISOString();
      if (p.teams.every(x => x.done)) {
        p.status = 'completed'; p.completedAt = new Date().toISOString();
        toast('PI ' + p.piNo + ' fully completed → moved to Completed PI');
      } else toast(t.name + ' marked completed for PI ' + p.piNo);
      save(); updatePendingList(); break;
    }
    case 'toggle-priority': {
      const p = find(); if (!p) break;
      if (p.priority) {
        if (!(await askPin('Enter admin PIN to remove priority.'))) break;
        p.priority = false; toast('Priority removed', 'warn');
      } else { p.priority = true; toast('Marked as priority'); }
      save(); updatePendingList(); break;
    }
    case 'delete-pi': {
      const p = find(); if (!p) break;
      if (!(await askPin('Enter admin PIN to delete PI ' + p.piNo + '.'))) break;
      if (!confirm('Delete PI ' + p.piNo + '?')) break;
      db.years[ui.year].pis = pis().filter(x => x.id !== id); save(); updatePendingList(); toast('PI deleted', 'warn'); break;
    }
    case 'dispatch': dispatchPI(id); break;
    case 'export-range': {
      const f = $('#rFrom').value, t = $('#rTo').value;
      if (f && t && f > t) return toast('From date cannot be after To date', 'err');
      exportExcel(f, t); break;
    }
    case 'export-all': exportExcel('', ''); break;
  }
});
function dispatchPI(id) {
  const p = pis().find(x => x.id === id); if (!p) return;
  const inp = $('#inv-' + id), inv = inp ? inp.value.trim() : '';
  if (!inv) { toast('Enter invoice number first', 'err'); if (inp) inp.focus(); return; }
  p.invoiceNo = inv; p.status = 'dispatched'; p.dispatchedAt = new Date().toISOString();
  save(); updateCompletedList(); toast('PI ' + p.piNo + ' dispatched with invoice ' + inv);
}
document.addEventListener('keydown', e => {
  if (e.key === 'Enter' && e.target.dataset && e.target.dataset.enter) { e.preventDefault(); dispatchPI(e.target.dataset.enter); }
  if (e.key === 'Escape') { if ($('#pinModal').classList.contains('show')) closePin(false); $('#alertModal').classList.remove('show'); }
});

/* refresh overdue badge every minute */
setInterval(() => {
  if (!ui.year) return;
  updateBadges();
  if (ui.view === 'dashboard') { destroyCharts(); renderDashboard(); }
}, 60000);

/* init */
try { if (sessionStorage.getItem('pi_auth')) { renderYears(); showScreen('yearScreen'); } } catch (e) {}
</script>
</body>
</html>
