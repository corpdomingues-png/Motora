<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<meta name="theme-color" content="#00d4ff">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Motorista PRO">
<title>Motorista PRO</title>

<!-- PWA Manifest inline via JS -->
<script>
const manifestData = {
  name: "Motorista PRO",
  short_name: "Motorista",
  description: "Controle de corridas e lucro real",
  start_url: "./",
  display: "standalone",
  background_color: "#070b0f",
  theme_color: "#00d4ff",
  orientation: "portrait",
  icons: [
    { src: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 192 192'%3E%3Crect width='192' height='192' fill='%23070b0f'/%3E%3Ctext y='140' x='20' font-size='130' font-family='Arial'%3E⚡%3C/text%3E%3C/svg%3E", sizes: "192x192", type: "image/svg+xml" },
    { src: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Crect width='512' height='512' fill='%23070b0f'/%3E%3Ctext y='390' x='40' font-size='360' font-family='Arial'%3E⚡%3C/text%3E%3C/svg%3E", sizes: "512x512", type: "image/svg+xml" }
  ]
};
const blob = new Blob([JSON.stringify(manifestData)], {type: 'application/json'});
const manifestURL = URL.createObjectURL(blob);
document.addEventListener('DOMContentLoaded', () => {
  const link = document.createElement('link');
  link.rel = 'manifest';
  link.href = manifestURL;
  document.head.appendChild(link);
});
</script>

<!-- Service Worker Registration -->
<script>
if ('serviceWorker' in navigator) {
  const swCode = `
    const CACHE = 'motorista-pro-v1';
    self.addEventListener('install', e => {
      self.skipWaiting();
    });
    self.addEventListener('activate', e => {
      clients.claim();
    });
    self.addEventListener('fetch', e => {
      // Cache-first para o próprio app, network-first para APIs
      if (e.request.url.includes('anthropic.com') || e.request.url.includes('fonts.googleapis')) {
        e.respondWith(fetch(e.request).catch(() => new Response('')));
      } else {
        e.respondWith(caches.match(e.request).then(r => r || fetch(e.request)));
      }
    });
  `;
  const swBlob = new Blob([swCode], {type: 'application/javascript'});
  const swURL = URL.createObjectURL(swBlob);
  navigator.serviceWorker.register(swURL).catch(() => {});
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;500;600;700&family=Share+Tech+Mono&family=Exo+2:wght@300;400;600;800&display=swap');

:root {
  --bg: #070b0f;
  --s1: #0d1117;
  --s2: #131920;
  --s3: #1a2230;
  --border: #1e2d3d;
  --border2: #243447;
  --cyan: #00d4ff;
  --cyan2: #00a8cc;
  --green: #00ff88;
  --green2: #00cc6a;
  --red: #ff3355;
  --yellow: #ffcc00;
  --text: #e8f4f8;
  --muted: #4a6278;
}

* { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }

html, body {
  height: 100%;
  overscroll-behavior: none;
}

body {
  background: var(--bg);
  color: var(--text);
  font-family: 'Exo 2', sans-serif;
  min-height: 100vh;
  max-width: 480px;
  margin: 0 auto;
  overflow-x: hidden;
}

body::before {
  content: '';
  position: fixed;
  inset: 0;
  background-image: linear-gradient(var(--border) 1px, transparent 1px), linear-gradient(90deg, var(--border) 1px, transparent 1px);
  background-size: 40px 40px;
  opacity: 0.25;
  pointer-events: none;
  z-index: 0;
}

/* INSTALL BANNER */
.install-banner {
  background: linear-gradient(135deg, var(--s2), var(--s3));
  border: 1px solid var(--cyan2);
  border-radius: 12px;
  padding: 12px 14px;
  margin: 12px 14px 0;
  display: none;
  position: relative;
  z-index: 10;
}

.install-banner.show { display: flex; align-items: center; gap: 10px; }

.install-banner .ib-text { flex: 1; font-size: 0.78rem; color: var(--text); line-height: 1.4; }
.install-banner .ib-text strong { color: var(--cyan); font-family: 'Rajdhani', sans-serif; font-size: 0.9rem; letter-spacing: 1px; display: block; margin-bottom: 2px; }

.btn-install {
  background: var(--cyan);
  color: #000;
  border: none;
  border-radius: 8px;
  padding: 8px 14px;
  font-family: 'Rajdhani', sans-serif;
  font-size: 0.85rem;
  font-weight: 700;
  letter-spacing: 1px;
  cursor: pointer;
  white-space: nowrap;
  flex-shrink: 0;
}

.install-close {
  background: none;
  border: none;
  color: var(--muted);
  font-size: 1.1rem;
  cursor: pointer;
  padding: 4px;
  flex-shrink: 0;
}

/* iOS INSTALL HINT */
.ios-hint {
  background: var(--s2);
  border: 1px solid var(--border2);
  border-radius: 10px;
  padding: 12px 14px;
  margin: 12px 14px 0;
  display: none;
  font-size: 0.75rem;
  color: var(--muted);
  line-height: 1.6;
  position: relative;
  z-index: 10;
}

.ios-hint.show { display: block; }
.ios-hint strong { color: var(--cyan); }

/* NAV */
.nav {
  position: fixed;
  bottom: 0; left: 50%;
  transform: translateX(-50%);
  width: 100%;
  max-width: 480px;
  background: rgba(13,17,23,0.95);
  border-top: 1px solid var(--border2);
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  z-index: 100;
  backdrop-filter: blur(12px);
  padding-bottom: env(safe-area-inset-bottom);
}

.nav-btn {
  padding: 10px 4px 8px;
  background: none;
  border: none;
  color: var(--muted);
  font-size: 0.55rem;
  font-family: 'Rajdhani', sans-serif;
  font-weight: 600;
  letter-spacing: 1px;
  text-transform: uppercase;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 3px;
  transition: color 0.2s;
}

.nav-btn .icon { font-size: 1.3rem; }
.nav-btn.active { color: var(--cyan); }
.nav-btn.active .icon { filter: drop-shadow(0 0 6px var(--cyan)); }

/* PAGES */
.page { display: none; padding: 14px 14px 100px; position: relative; z-index: 1; }
.page.active { display: block; }

/* HEADER */
.page-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 16px;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border2);
}

.page-title {
  font-family: 'Rajdhani', sans-serif;
  font-size: 1.4rem;
  font-weight: 700;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--cyan);
  text-shadow: 0 0 20px var(--cyan2);
}

.hora-atual { font-family: 'Share Tech Mono', monospace; font-size: 0.8rem; color: var(--muted); }

/* CARDS */
.card {
  background: var(--s1);
  border: 1px solid var(--border2);
  border-radius: 12px;
  padding: 14px 16px;
  margin-bottom: 12px;
  position: relative;
  overflow: hidden;
}

.card::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: linear-gradient(90deg, transparent, var(--cyan2), transparent);
  opacity: 0.5;
}

.card.green::before { background: linear-gradient(90deg, transparent, var(--green2), transparent); }

/* META */
.meta-top { display: flex; justify-content: space-between; align-items: baseline; margin-bottom: 8px; }
.meta-label { font-size: 0.65rem; color: var(--muted); text-transform: uppercase; letter-spacing: 1.5px; font-family: 'Rajdhani', sans-serif; }
.meta-pct { font-family: 'Share Tech Mono', monospace; font-size: 0.85rem; color: var(--cyan); }
.progress-bar { height: 6px; background: var(--s3); border-radius: 3px; overflow: hidden; }
.progress-fill { height: 100%; border-radius: 3px; background: linear-gradient(90deg, var(--cyan2), var(--green)); transition: width 0.5s ease; box-shadow: 0 0 10px var(--cyan2); }

/* STATS */
.stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 12px; }
.stat-box { background: var(--s1); border: 1px solid var(--border2); border-radius: 10px; padding: 12px 14px; }
.stat-box.full { grid-column: 1 / -1; }
.stat-box .sl { font-size: 0.62rem; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; font-family: 'Rajdhani', sans-serif; margin-bottom: 4px; }
.stat-box .sv { font-family: 'Share Tech Mono', monospace; font-size: 1.4rem; color: var(--text); line-height: 1; }
.stat-box.hero .sv { font-size: 2rem; color: var(--green); text-shadow: 0 0 20px var(--green2); }
.stat-box .ss { font-size: 0.65rem; color: var(--muted); margin-top: 3px; }

/* VOZ */
.btn-voice {
  width: 100%;
  padding: 18px;
  background: linear-gradient(135deg, var(--s2), var(--s3));
  border: 2px solid var(--cyan2);
  border-radius: 14px;
  color: var(--cyan);
  font-family: 'Rajdhani', sans-serif;
  font-size: 1rem;
  font-weight: 700;
  letter-spacing: 2px;
  text-transform: uppercase;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  transition: all 0.2s;
}

.btn-voice.listening { border-color: var(--red); color: var(--red); animation: pulse-border 1s infinite; }

@keyframes pulse-border {
  0%, 100% { box-shadow: 0 0 0 0 rgba(255,51,85,0.4); }
  50% { box-shadow: 0 0 0 8px rgba(255,51,85,0); }
}

.voice-hint { font-size: 0.68rem; color: var(--muted); text-align: center; margin-top: 6px; font-family: 'Rajdhani', sans-serif; letter-spacing: 0.5px; }

/* FORM */
.form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }
.fg { display: flex; flex-direction: column; gap: 5px; }
.fg.full { grid-column: 1 / -1; }
.fg label { font-size: 0.62rem; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; font-family: 'Rajdhani', sans-serif; }
.fg input, .fg select { background: var(--s2); border: 1px solid var(--border2); border-radius: 8px; color: var(--text); font-family: 'Share Tech Mono', monospace; font-size: 1rem; padding: 10px 12px; outline: none; transition: border-color 0.2s; width: 100%; -webkit-appearance: none; }
.fg input:focus, .fg select:focus { border-color: var(--cyan); }
.fg select option { background: #0d1117; }

/* PREVIEW */
.preview-box { background: var(--s2); border: 1px solid var(--border2); border-radius: 10px; padding: 14px; margin: 10px 0; display: none; }
.preview-box.show { display: block; }
.pr { display: flex; justify-content: space-between; font-size: 0.8rem; padding: 3px 0; }
.pr .pk { color: var(--muted); font-family: 'Rajdhani', sans-serif; }
.pr .pv { font-family: 'Share Tech Mono', monospace; }
.pr.total { border-top: 1px solid var(--border2); margin-top: 8px; padding-top: 10px; }
.pr.total .pk { color: var(--text); font-weight: 700; font-size: 0.9rem; }
.pr.total .pv { font-size: 1.2rem; }
.verd { color: var(--green); } .verm { color: var(--red); } .amar { color: var(--yellow); }

.decision { display: flex; align-items: center; justify-content: center; gap: 8px; padding: 10px; border-radius: 8px; font-family: 'Rajdhani', sans-serif; font-size: 1rem; font-weight: 700; letter-spacing: 2px; margin-top: 8px; }
.decision.aceita { background: rgba(0,255,136,0.1); color: var(--green); border: 1px solid var(--green2); }
.decision.fraca  { background: rgba(255,204,0,0.1);  color: var(--yellow); border: 1px solid var(--yellow); }
.decision.recusa { background: rgba(255,51,85,0.1);  color: var(--red);    border: 1px solid var(--red); }

/* BUTTONS */
.btn { width: 100%; padding: 14px; border: none; border-radius: 10px; font-family: 'Rajdhani', sans-serif; font-size: 1rem; font-weight: 700; letter-spacing: 2px; cursor: pointer; transition: all 0.15s; text-transform: uppercase; }
.btn-primary { background: linear-gradient(135deg, var(--cyan2), var(--green2)); color: #000; margin-top: 8px; }
.btn-primary:active { transform: scale(0.98); }
.btn-danger { background: rgba(255,51,85,0.15); color: var(--red); border: 1px solid var(--red); }

/* HISTÓRICO */
.hist-item { background: var(--s1); border: 1px solid var(--border2); border-radius: 10px; padding: 12px 14px; margin-bottom: 8px; display: grid; grid-template-columns: 1fr auto; gap: 8px; animation: fadeSlide 0.2s ease; }
@keyframes fadeSlide { from { opacity: 0; transform: translateY(-6px); } to { opacity: 1; transform: translateY(0); } }
.hi-num { font-size: 0.6rem; color: var(--muted); font-family: 'Rajdhani', sans-serif; }
.hi-plat { font-size: 0.85rem; font-weight: 700; color: var(--text); }
.hi-det { font-size: 0.68rem; color: var(--muted); font-family: 'Share Tech Mono', monospace; margin-top: 2px; }
.badge { display: inline-block; padding: 2px 8px; border-radius: 20px; font-size: 0.58rem; font-weight: 700; font-family: 'Rajdhani', sans-serif; letter-spacing: 1px; text-transform: uppercase; margin-top: 4px; }
.b-ok { background: rgba(0,255,136,0.15); color: var(--green); }
.b-fr { background: rgba(255,204,0,0.15); color: var(--yellow); }
.b-ru { background: rgba(255,51,85,0.15); color: var(--red); }
.hi-val { text-align: right; }
.hi-bruto { font-size: 0.7rem; color: var(--muted); font-family: 'Share Tech Mono', monospace; }
.hi-liq { font-size: 1.1rem; font-weight: 700; font-family: 'Share Tech Mono', monospace; }
.vazio { text-align: center; color: var(--muted); padding: 30px; font-size: 0.85rem; }

/* SEMANA */
.week-bars { display: grid; grid-template-columns: repeat(7, 1fr); gap: 4px; align-items: flex-end; height: 80px; margin-top: 8px; }
.wbar-col { display: flex; flex-direction: column; align-items: center; gap: 3px; }
.wbar { width: 100%; border-radius: 4px 4px 0 0; background: var(--s3); min-height: 4px; transition: height 0.4s ease; }
.wbar.today { background: linear-gradient(var(--cyan2), var(--green2)); box-shadow: 0 0 10px var(--cyan2); }
.wbar.good  { background: var(--green2); }
.wbar.mid   { background: var(--yellow); }
.wbar.low   { background: var(--red); }
.wbar-label { font-size: 0.55rem; color: var(--muted); font-family: 'Rajdhani', sans-serif; text-transform: uppercase; }
.wbar-val   { font-size: 0.6rem; color: var(--muted); font-family: 'Share Tech Mono', monospace; }

/* CONFIG */
.config-item { background: var(--s1); border: 1px solid var(--border2); border-radius: 10px; padding: 14px 16px; margin-bottom: 10px; }
.config-item .ci-title { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; font-family: 'Rajdhani', sans-serif; margin-bottom: 8px; }
.config-item input { background: var(--s2); border: 1px solid var(--border2); border-radius: 8px; color: var(--text); font-family: 'Share Tech Mono', monospace; font-size: 1rem; padding: 10px 12px; outline: none; width: 100%; }
.config-item input:focus { border-color: var(--cyan); }
.config-row { display: flex; align-items: center; gap: 10px; }
.config-row .cr-label { flex: 1; font-size: 0.85rem; color: var(--text); }
.config-row .cr-val { font-family: 'Share Tech Mono', monospace; font-size: 0.9rem; color: var(--cyan); }

/* MELHOR HORÁRIO */
.hora-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; margin-top: 8px; }
.hora-cell { background: var(--s2); border: 1px solid var(--border2); border-radius: 6px; padding: 8px 4px; text-align: center; }
.hora-cell .hc-hora { font-size: 0.6rem; color: var(--muted); font-family: 'Rajdhani', sans-serif; }
.hora-cell .hc-val { font-size: 0.75rem; font-family: 'Share Tech Mono', monospace; color: var(--text); margin-top: 2px; }
.hora-cell.melhor { border-color: var(--green); background: rgba(0,255,136,0.08); }
.hora-cell.melhor .hc-val { color: var(--green); }

/* TRANSCRIPT */
.transcript { background: var(--s2); border: 1px solid var(--border2); border-radius: 8px; padding: 10px 12px; font-family: 'Share Tech Mono', monospace; font-size: 0.78rem; color: var(--muted); margin-top: 8px; min-height: 36px; display: none; }
.transcript.show { display: block; }

/* TOAST */
.toast { position: fixed; top: 16px; left: 50%; transform: translateX(-50%) translateY(-120px); background: var(--s2); border: 1px solid var(--cyan2); border-radius: 10px; padding: 12px 20px; font-family: 'Rajdhani', sans-serif; font-size: 0.9rem; font-weight: 600; letter-spacing: 1px; z-index: 999; transition: transform 0.3s ease; max-width: 320px; text-align: center; color: var(--cyan); box-shadow: 0 4px 20px rgba(0,212,255,0.2); }
.toast.show { transform: translateX(-50%) translateY(0); }
.toast.success { border-color: var(--green); color: var(--green); box-shadow: 0 4px 20px rgba(0,255,136,0.2); }
.toast.warning { border-color: var(--yellow); color: var(--yellow); }
.toast.error { border-color: var(--red); color: var(--red); }

.comb-status { font-size: 0.65rem; color: var(--muted); font-family: 'Rajdhani', sans-serif; display: flex; align-items: center; gap: 6px; margin-top: 4px; }
.comb-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--green); flex-shrink:0; }
</style>
</head>
<body>

<div class="toast" id="toast"></div>

<!-- INSTALL BANNER (Android/Chrome) -->
<div class="install-banner" id="installBanner">
  <div class="ib-text">
    <strong>📲 INSTALAR COMO APP</strong>
    Adicione na tela inicial e use sem abrir o navegador
  </div>
  <button class="btn-install" onclick="instalarApp()">INSTALAR</button>
  <button class="install-close" onclick="fecharBanner()">✕</button>
</div>

<!-- iOS HINT -->
<div class="ios-hint" id="iosHint">
  📱 <strong>iPhone/iPad:</strong> Toque em <strong>Compartilhar</strong> (□↑) → <strong>"Adicionar à Tela de Início"</strong> → o app fica no seu celular pra sempre, sem internet.
</div>

<!-- PAGE: DASHBOARD -->
<div class="page active" id="page-dash">
  <div class="page-header">
    <div class="page-title">⚡ Dashboard</div>
    <div class="hora-atual" id="horaAtual">--:--</div>
  </div>

  <div class="card green">
    <div class="meta-top">
      <span class="meta-label">Meta do dia</span>
      <span class="meta-pct" id="metaPct">0%</span>
    </div>
    <div class="progress-bar"><div class="progress-fill" id="progressFill" style="width:0%"></div></div>
    <div style="display:flex;justify-content:space-between;margin-top:6px;">
      <span style="font-size:0.7rem;color:var(--muted);font-family:'Share Tech Mono',monospace" id="metaAtual">R$ 0,00</span>
      <span style="font-size:0.7rem;color:var(--muted);font-family:'Share Tech Mono',monospace" id="metaAlvo">meta: R$ 200,00</span>
    </div>
  </div>

  <div class="stats-grid">
    <div class="stat-box hero full">
      <div class="sl">💰 Lucro Líquido Hoje</div>
      <div class="sv" id="dashLiquido">R$ 0,00</div>
      <div class="ss" id="dashMedia">— sem corridas registradas</div>
    </div>
    <div class="stat-box">
      <div class="sl">Bruto</div>
      <div class="sv" id="dashBruto">R$ 0,00</div>
    </div>
    <div class="stat-box">
      <div class="sl">Custo</div>
      <div class="sv" style="color:var(--red)" id="dashCusto">R$ 0,00</div>
    </div>
    <div class="stat-box">
      <div class="sl">Corridas</div>
      <div class="sv" id="dashN">0</div>
    </div>
    <div class="stat-box">
      <div class="sl">KM rodados</div>
      <div class="sv" id="dashKm">0</div>
    </div>
  </div>

  <div class="card" id="horaCard" style="display:none">
    <div class="meta-label" style="margin-bottom:8px">🕐 Seus melhores horários</div>
    <div class="hora-grid" id="horaGrid"></div>
  </div>

  <button class="btn btn-danger" style="margin-top:4px" onclick="zerarDia()">🗑 Zerar dia</button>
</div>

<!-- PAGE: REGISTRAR -->
<div class="page" id="page-reg">
  <div class="page-header">
    <div class="page-title">+ Corrida</div>
  </div>

  <div style="margin-bottom:12px">
    <button class="btn-voice" id="btnVoz" onclick="toggleVoz()">
      <span style="font-size:1.4rem">🎤</span>
      <span id="vozLabel">REGISTRAR POR VOZ</span>
    </button>
    <div class="voice-hint">Diga: "Uber, vinte reais, seis km, dois vazio"</div>
    <div class="transcript" id="transcript"></div>
  </div>

  <div style="text-align:center;font-size:0.65rem;color:var(--muted);margin:8px 0;font-family:'Rajdhani',sans-serif;letter-spacing:1px">— OU PREENCHA MANUALMENTE —</div>

  <div class="card">
    <div class="form-grid">
      <div class="fg">
        <label>Valor (R$)</label>
        <input type="number" id="fValor" placeholder="0,00" step="0.50" min="0" oninput="calcPreview()" inputmode="decimal">
      </div>
      <div class="fg">
        <label>Plataforma</label>
        <select id="fPlat">
          <option>Uber</option>
          <option>99</option>
          <option>InDriver</option>
          <option>Outra</option>
        </select>
      </div>
      <div class="fg">
        <label>KM passageiro</label>
        <input type="number" id="fKmC" placeholder="0" step="0.5" min="0" oninput="calcPreview()" inputmode="decimal">
      </div>
      <div class="fg">
        <label>KM vazio retorno</label>
        <input type="number" id="fKmV" placeholder="0" step="0.5" min="0" oninput="calcPreview()" inputmode="decimal">
      </div>
    </div>

    <div class="preview-box" id="preview">
      <div class="pr"><span class="pk">Valor bruto</span><span class="pv" id="pBruto">—</span></div>
      <div class="pr"><span class="pk">Custo combustível</span><span class="pv verm" id="pComb">—</span></div>
      <div class="pr"><span class="pk">Custo manutenção</span><span class="pv verm" id="pManut">—</span></div>
      <div class="pr"><span class="pk">R$/km (com passageiro)</span><span class="pv" id="pRkm">—</span></div>
      <div class="pr total">
        <span class="pk">LUCRO LÍQUIDO</span>
        <span class="pv" id="pLiq">—</span>
      </div>
      <div class="decision" id="decision"></div>
    </div>

    <button class="btn btn-primary" onclick="registrar()">✓ CONFIRMAR CORRIDA</button>
  </div>
</div>

<!-- PAGE: HISTÓRICO -->
<div class="page" id="page-hist">
  <div class="page-header">
    <div class="page-title">Histórico</div>
  </div>
  <div class="card">
    <div class="meta-label" style="margin-bottom:4px">📊 Últimos 7 dias</div>
    <div class="week-bars" id="weekBars"></div>
  </div>
  <div id="histLista"><div class="vazio">Nenhuma corrida ainda hoje.</div></div>
</div>

<!-- PAGE: CONFIG -->
<div class="page" id="page-cfg">
  <div class="page-header">
    <div class="page-title">Config</div>
  </div>

  <div class="config-item">
    <div class="ci-title">⛽ Preço do Álcool (R$/L)</div>
    <input type="number" id="cfgComb" value="4.20" step="0.05" min="2" max="10" oninput="salvarConfig()">
    <div class="comb-status"><div class="comb-dot"></div><span id="cfgStatus">Custo: R$0,31/km · 13,5 km/L</span></div>
  </div>

  <div class="config-item">
    <div class="ci-title">🎯 Meta Diária de Lucro (R$)</div>
    <input type="number" id="cfgMeta" value="200" step="10" min="50" oninput="salvarConfig()">
  </div>

  <div class="config-item">
    <div class="ci-title">📊 Limites de Qualidade</div>
    <div style="margin-top:4px">
      <div class="config-row" style="margin-bottom:6px">
        <span class="cr-label">✅ Boa (lucro acima de)</span>
        <span class="cr-val" id="cfgBoaVal">R$ 12</span>
      </div>
      <input type="range" id="cfgBoa" min="5" max="30" value="12" oninput="salvarConfig()" style="width:100%;accent-color:var(--green)">
      <div class="config-row" style="margin-top:8px;margin-bottom:6px">
        <span class="cr-label">⚠️ Fraca (acima de)</span>
        <span class="cr-val" id="cfgFracaVal">R$ 6</span>
      </div>
      <input type="range" id="cfgFraca" min="2" max="20" value="6" oninput="salvarConfig()" style="width:100%;accent-color:var(--yellow)">
    </div>
  </div>

  <div class="config-item">
    <div class="ci-title">ℹ️ Veículo</div>
    <div style="font-size:0.8rem;color:var(--muted);line-height:1.8">
      Gol 1.0 · 3 cilindros · Álcool<br>
      Consumo médio: 13,5 km/L<br>
      Manutenção estimada: R$ 0,12/km
    </div>
  </div>

  <div class="config-item">
    <div class="ci-title">📲 Instalar como App</div>
    <div style="font-size:0.8rem;color:var(--muted);line-height:1.8;margin-bottom:10px">
      Instale no celular para usar sem abrir o navegador. Funciona offline.
    </div>
    <button class="btn btn-primary" onclick="instalarApp()" id="btnInstalarCfg">INSTALAR NO CELULAR</button>
    <div class="ios-hint show" id="iosHintCfg" style="margin:10px 0 0;display:none">
      iPhone: Toque em <strong>Compartilhar</strong> (□↑) → <strong>"Adicionar à Tela de Início"</strong>
    </div>
  </div>
</div>

<!-- NAV -->
<nav class="nav">
  <button class="nav-btn active" onclick="goPage('dash',this)"><span class="icon">📊</span>Painel</button>
  <button class="nav-btn" onclick="goPage('reg',this)"><span class="icon">➕</span>Corrida</button>
  <button class="nav-btn" onclick="goPage('hist',this)"><span class="icon">📋</span>Histórico</button>
  <button class="nav-btn" onclick="goPage('cfg',this)"><span class="icon">⚙️</span>Config</button>
</nav>

<script>
// ═══════════════════ STATE ═══════════════════
const KM_L = 13.5;
const MANUT_KM = 0.12;

let cfg = JSON.parse(localStorage.getItem('cfg_v3') || JSON.stringify({comb:4.20,meta:200,boa:12,fraca:6}));
let corridas = JSON.parse(localStorage.getItem('corridas_v3') || '[]');

let deferredPrompt = null;
let isIOS = /iphone|ipad|ipod/i.test(navigator.userAgent);
let isInStandalone = window.matchMedia('(display-mode: standalone)').matches || window.navigator.standalone;

// ═══════════════════ PWA INSTALL ═══════════════════
window.addEventListener('beforeinstallprompt', e => {
  e.preventDefault();
  deferredPrompt = e;
  if (!isInStandalone && !localStorage.getItem('installDismissed')) {
    document.getElementById('installBanner').classList.add('show');
  }
  document.getElementById('btnInstalarCfg').style.display = 'block';
});

function instalarApp() {
  if (deferredPrompt) {
    deferredPrompt.prompt();
    deferredPrompt.userChoice.then(r => {
      if (r.outcome === 'accepted') showToast('✅ App instalado com sucesso!', 'success');
      deferredPrompt = null;
      document.getElementById('installBanner').classList.remove('show');
    });
  } else if (isIOS) {
    document.getElementById('iosHint').classList.add('show');
    document.getElementById('iosHintCfg').style.display = 'block';
  } else {
    showToast('Abra no Chrome para instalar', 'warning');
  }
}

function fecharBanner() {
  document.getElementById('installBanner').classList.remove('show');
  localStorage.setItem('installDismissed', '1');
}

// Mostra hint iOS se necessário
if (isIOS && !isInStandalone) {
  document.getElementById('iosHint').classList.add('show');
  document.getElementById('iosHintCfg').style.display = 'block';
}

if (isInStandalone) {
  document.getElementById('btnInstalarCfg').textContent = '✅ JÁ INSTALADO';
  document.getElementById('btnInstalarCfg').disabled = true;
}

// ═══════════════════ HELPERS ═══════════════════
function hojeStr() { return new Date().toDateString(); }
function corridasHoje() { return corridas.filter(c => c.dia === hojeStr()); }
function custoKm() { return cfg.comb / KM_L; }

function goPage(id, btn) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  btn.classList.add('active');
  if (id === 'dash') renderDash();
  if (id === 'hist') renderHist();
}

// Relógio
setInterval(() => {
  document.getElementById('horaAtual').textContent =
    new Date().toLocaleTimeString('pt-BR',{hour:'2-digit',minute:'2-digit',second:'2-digit'});
}, 1000);

// ═══════════════════ CONFIG ═══════════════════
function carregarConfig() {
  document.getElementById('cfgComb').value  = cfg.comb;
  document.getElementById('cfgMeta').value  = cfg.meta;
  document.getElementById('cfgBoa').value   = cfg.boa;
  document.getElementById('cfgFraca').value = cfg.fraca;
  atualizarConfigUI();
}

function salvarConfig() {
  cfg.comb  = parseFloat(document.getElementById('cfgComb').value)  || 4.20;
  cfg.meta  = parseFloat(document.getElementById('cfgMeta').value)  || 200;
  cfg.boa   = parseFloat(document.getElementById('cfgBoa').value)   || 12;
  cfg.fraca = parseFloat(document.getElementById('cfgFraca').value) || 6;
  localStorage.setItem('cfg_v3', JSON.stringify(cfg));
  atualizarConfigUI();
  renderDash();
}

function atualizarConfigUI() {
  document.getElementById('cfgStatus').textContent   = `Custo: R$${custoKm().toFixed(2)}/km · 13,5 km/L`;
  document.getElementById('cfgBoaVal').textContent   = `R$ ${cfg.boa}`;
  document.getElementById('cfgFracaVal').textContent = `R$ ${cfg.fraca}`;
  document.getElementById('metaAlvo').textContent    = `meta: R$ ${cfg.meta.toFixed(2)}`;
}

// ═══════════════════ CALC ═══════════════════
function calcCorrida(bruto, kmC, kmV) {
  const kmT = kmC + kmV;
  const custoComb  = kmT * custoKm();
  const custoManut = kmT * MANUT_KM;
  const custo   = custoComb + custoManut;
  const liquido = bruto - custo;
  const rKm     = kmC > 0 ? bruto / kmC : 0;
  return { custoComb, custoManut, custo, liquido, rKm, kmT };
}

function classificar(liq) {
  if (liq >= cfg.boa)   return ['✅ BOA CORRIDA',      'aceita', 'verd', 'b-ok', '✅ Boa'];
  if (liq >= cfg.fraca) return ['⚠️ CORRIDA FRACA',   'fraca',  'amar', 'b-fr', '⚠️ Fraca'];
  return                       ['❌ RECUSA — PREJUÍZO','recusa', 'verm', 'b-ru', '❌ Ruim'];
}

// ═══════════════════ PREVIEW ═══════════════════
function calcPreview() {
  const bruto = parseFloat(document.getElementById('fValor').value) || 0;
  const kmC   = parseFloat(document.getElementById('fKmC').value)   || 0;
  const kmV   = parseFloat(document.getElementById('fKmV').value)   || 0;

  if (!bruto && !kmC) { document.getElementById('preview').classList.remove('show'); return; }

  const r = calcCorrida(bruto, kmC, kmV);
  const [dlabel, dcls, lcls] = classificar(r.liquido);

  document.getElementById('pBruto').textContent = `R$ ${bruto.toFixed(2)}`;
  document.getElementById('pComb').textContent  = `- R$ ${r.custoComb.toFixed(2)}`;
  document.getElementById('pManut').textContent = `- R$ ${r.custoManut.toFixed(2)}`;
  document.getElementById('pRkm').textContent   = `R$ ${r.rKm.toFixed(2)}/km`;

  const el = document.getElementById('pLiq');
  el.textContent = `R$ ${r.liquido.toFixed(2)}`;
  el.className   = 'pv ' + lcls;

  const ed = document.getElementById('decision');
  ed.textContent = dlabel;
  ed.className   = 'decision ' + dcls;

  document.getElementById('preview').classList.add('show');
}

// ═══════════════════ REGISTRAR ═══════════════════
function registrar() {
  const bruto = parseFloat(document.getElementById('fValor').value) || 0;
  const kmC   = parseFloat(document.getElementById('fKmC').value)   || 0;
  const kmV   = parseFloat(document.getElementById('fKmV').value)   || 0;
  const plat  = document.getElementById('fPlat').value;

  if (bruto <= 0) { showToast('Informe o valor da corrida', 'error'); return; }

  const r = calcCorrida(bruto, kmC, kmV);
  const now = new Date();

  corridas.push({
    dia: hojeStr(),
    hora: now.toLocaleTimeString('pt-BR',{hour:'2-digit',minute:'2-digit'}),
    horaNum: now.getHours(),
    bruto, kmC, kmV, plat,
    custoComb: r.custoComb, custoManut: r.custoManut,
    custo: r.custo, liquido: r.liquido, rKm: r.rKm
  });

  localStorage.setItem('corridas_v3', JSON.stringify(corridas));
  limparForm();
  renderDash();
  showToast(`+R$ ${r.liquido.toFixed(2)} líquido ✓`, 'success');

  const totalHoje = corridasHoje().reduce((s,c) => s + c.liquido, 0);
  if (totalHoje >= cfg.meta) setTimeout(() => showToast('🎯 META DO DIA ATINGIDA!', 'success'), 1500);
}

function limparForm() {
  ['fValor','fKmC','fKmV'].forEach(id => document.getElementById(id).value = '');
  document.getElementById('preview').classList.remove('show');
  document.getElementById('transcript').classList.remove('show');
}

function zerarDia() {
  if (!confirm('Zerar todas as corridas de hoje?')) return;
  corridas = corridas.filter(c => c.dia !== hojeStr());
  localStorage.setItem('corridas_v3', JSON.stringify(corridas));
  renderDash(); renderHist();
  showToast('Dia zerado.', 'warning');
}

// ═══════════════════ RENDER DASH ═══════════════════
function renderDash() {
  const h = corridasHoje();
  const n = h.length;
  const tB = h.reduce((s,c) => s+c.bruto, 0);
  const tC = h.reduce((s,c) => s+c.custo, 0);
  const tL = h.reduce((s,c) => s+c.liquido, 0);
  const tK = h.reduce((s,c) => s+c.kmC+c.kmV, 0);
  const pct = Math.min(100, Math.round(tL/cfg.meta*100));

  document.getElementById('dashLiquido').textContent = `R$ ${tL.toFixed(2)}`;
  document.getElementById('dashBruto').textContent   = `R$ ${tB.toFixed(2)}`;
  document.getElementById('dashCusto').textContent   = `R$ ${tC.toFixed(2)}`;
  document.getElementById('dashN').textContent       = n;
  document.getElementById('dashKm').textContent      = tK.toFixed(1);
  document.getElementById('dashMedia').textContent   = n > 0 ? `Média R$ ${(tL/n).toFixed(2)} líquido/corrida` : '— sem corridas';
  document.getElementById('metaPct').textContent     = `${pct}%`;
  document.getElementById('progressFill').style.width = `${pct}%`;
  document.getElementById('metaAtual').textContent   = `R$ ${tL.toFixed(2)}`;

  // Melhores horários
  if (n >= 3) {
    const faixas = [{l:'6–10h',a:6,b:10},{l:'10–14h',a:10,b:14},{l:'14–18h',a:14,b:18},{l:'18–24h',a:18,b:24}];
    let maxM = -Infinity;
    const dados = faixas.map(f => {
      const cc = h.filter(c => c.horaNum >= f.a && c.horaNum < f.b);
      const m  = cc.length > 0 ? cc.reduce((s,c) => s+c.liquido, 0)/cc.length : 0;
      if (m > maxM) maxM = m;
      return {label:f.l, m, n:cc.length};
    });
    document.getElementById('horaCard').style.display = 'block';
    document.getElementById('horaGrid').innerHTML = dados.map(d => `
      <div class="hora-cell ${d.m===maxM&&d.n>0?'melhor':''}">
        <div class="hc-hora">${d.label}</div>
        <div class="hc-val">${d.n>0?'R$'+d.m.toFixed(0):'—'}</div>
      </div>`).join('');
  } else {
    document.getElementById('horaCard').style.display = 'none';
  }
}

// ═══════════════════ RENDER HIST ═══════════════════
function renderHist() {
  // Semana
  const dias = ['Dom','Seg','Ter','Qua','Qui','Sex','Sáb'];
  const hoje = new Date();
  const semana = Array.from({length:7}, (_,i) => {
    const d = new Date(hoje); d.setDate(hoje.getDate()-(6-i));
    const liq = corridas.filter(c => c.dia===d.toDateString()).reduce((s,c) => s+c.liquido, 0);
    return {label:dias[d.getDay()], liq, isHoje: i===6};
  });
  const maxL = Math.max(...semana.map(s=>s.liq), 1);
  document.getElementById('weekBars').innerHTML = semana.map(s => {
    const h = Math.max(4, Math.round(s.liq/maxL*70));
    const cls = s.isHoje ? 'today' : s.liq>=cfg.meta ? 'good' : s.liq>=cfg.meta*0.5 ? 'mid' : s.liq>0 ? 'low' : '';
    return `<div class="wbar-col">
      <div class="wbar-val">${s.liq>0?'R$'+s.liq.toFixed(0):''}</div>
      <div class="wbar ${cls}" style="height:${h}px"></div>
      <div class="wbar-label">${s.label}</div>
    </div>`;
  }).join('');

  // Lista
  const h = corridasHoje();
  const el = document.getElementById('histLista');
  if (!h.length) { el.innerHTML = '<div class="vazio">Nenhuma corrida hoje.</div>'; return; }
  el.innerHTML = [...h].reverse().map((c,i) => {
    const num = h.length-i;
    const [,,lcls,bcls,blabel] = classificar(c.liquido);
    return `<div class="hist-item">
      <div>
        <div class="hi-num">#${num} · ${c.hora}</div>
        <div class="hi-plat">${c.plat}</div>
        <div class="hi-det">${c.kmC}km pass + ${c.kmV}km vazio · R$${c.rKm.toFixed(2)}/km</div>
        <span class="badge ${bcls}">${blabel}</span>
      </div>
      <div class="hi-val">
        <div class="hi-bruto">R$ ${c.bruto.toFixed(2)}</div>
        <div class="hi-liq ${lcls}">R$ ${c.liquido.toFixed(2)}</div>
      </div>
    </div>`;
  }).join('');
}

// ═══════════════════ VOZ ═══════════════════
let recognition = null, isListening = false;

function setupVoz() {
  const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
  if (!SR) return;
  recognition = new SR();
  recognition.lang = 'pt-BR';
  recognition.interimResults = true;

  recognition.onresult = e => {
    const txt = Array.from(e.results).map(r=>r[0].transcript).join('');
    const el  = document.getElementById('transcript');
    el.textContent = '🎤 ' + txt;
    el.classList.add('show');
    if (e.results[e.results.length-1].isFinal) processarVoz(txt.toLowerCase());
  };

  recognition.onend = () => {
    isListening = false;
    document.getElementById('btnVoz').classList.remove('listening');
    document.getElementById('vozLabel').textContent = 'REGISTRAR POR VOZ';
  };

  recognition.onerror = e => {
    isListening = false;
    document.getElementById('btnVoz').classList.remove('listening');
    document.getElementById('vozLabel').textContent = 'REGISTRAR POR VOZ';
    if (e.error !== 'aborted') showToast('Erro no microfone: ' + e.error, 'error');
  };
}

function toggleVoz() {
  if (!recognition) { showToast('Voz não suportada neste navegador', 'error'); return; }
  if (isListening) { recognition.stop(); return; }
  isListening = true;
  document.getElementById('btnVoz').classList.add('listening');
  document.getElementById('vozLabel').textContent = 'OUVINDO... (toque para parar)';
  document.getElementById('transcript').textContent = '';
  document.getElementById('transcript').classList.add('show');
  recognition.start();
}

function processarVoz(texto) {
  let plat = 'Uber';
  if (/99|noventa/.test(texto)) plat = '99';
  else if (/indriver|in driver/.test(texto)) plat = 'InDriver';

  const ext = {zero:0,um:1,uma:1,dois:2,duas:2,'três':3,quatro:4,cinco:5,seis:6,sete:7,oito:8,nove:9,dez:10,
    onze:11,doze:12,treze:13,'quatorze':14,'catorze':14,'quinze':15,'dezesseis':16,'dezessete':17,'dezoito':18,'dezenove':19,
    vinte:20,trinta:30,'quarenta':40,'cinquenta':50,'sessenta':60};

  let t = texto;
  Object.entries(ext).forEach(([p,n]) => { t = t.replace(new RegExp(`\\b${p}\\b`,'g'), n); });
  t = t.replace(/(\d+)\s+e\s+(\d+)/g, (_,a,b) => String(+a + +b));

  const nums = [...t.matchAll(/\d+(?:[.,]\d+)?/g)].map(m => parseFloat(m[0].replace(',','.')));
  if (!nums.length) { showToast('Não entendi. Ex: "Uber, vinte reais, seis km, dois vazio"', 'warning'); return; }

  document.getElementById('fValor').value = nums[0] || '';
  document.getElementById('fKmC').value   = nums[1] || '';
  document.getElementById('fKmV').value   = nums[2] || '';
  document.getElementById('fPlat').value  = plat;
  calcPreview();
  showToast(`${plat} · R$${nums[0]} · ${nums[1]||0}km · ${nums[2]||0}km vazio`, 'success');
}

// ═══════════════════ TOAST ═══════════════════
function showToast(msg, type='') {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.className = 'toast show ' + type;
  setTimeout(() => t.classList.remove('show'), 2800);
}

// ═══════════════════ PREÇO AUTO ═══════════════════
async function buscarPreco() {
  const lastFetch = localStorage.getItem('lastCombFetch');
  if (lastFetch && Date.now() - +lastFetch < 3600000) return;
  try {
    const res = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {'Content-Type':'application/json'},
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 50,
        messages: [{role:'user', content:'Preço médio atual do etanol combustível no Brasil em 2026. Responda APENAS com o número decimal, ex: 4.35'}]
      })
    });
    const data = await res.json();
    const preco = parseFloat(data.content?.[0]?.text?.trim().replace(',','.'));
    if (preco > 2 && preco < 10) {
      cfg.comb = preco;
      localStorage.setItem('cfg_v3', JSON.stringify(cfg));
      localStorage.setItem('lastCombFetch', Date.now().toString());
      document.getElementById('cfgComb').value = preco;
      atualizarConfigUI();
      showToast(`⛽ Álcool atualizado: R$ ${preco.toFixed(2)}/L`, 'success');
    }
  } catch(e) {}
}

// ═══════════════════ INIT ═══════════════════
carregarConfig();
renderDash();
setupVoz();
buscarPreco();
</script>
</body>
</html>
