---
layout: null
---
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>E2D2 — Transformer con Difusión Discreta</title>
<style>
:root{
  --bg:#050810;--bg2:#090e1c;--card:#0d1530;
  --border:rgba(0,212,255,.13);--border2:rgba(139,92,246,.2);
  --cyan:#00d4ff;--purple:#8b5cf6;--green:#10b981;--orange:#f59e0b;
  --red:#ef4444;
  --t1:#f1f5f9;--t2:#94a3b8;--t3:#64748b;
  --grad:linear-gradient(135deg,#00d4ff,#8b5cf6);
  --gcard:linear-gradient(145deg,#0d1530,#090e1c);
  --glow:0 0 30px rgba(0,212,255,.12);
}
*{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
body{background:var(--bg);color:var(--t1);font-family:'Segoe UI',system-ui,sans-serif;line-height:1.7;overflow-x:hidden}
::-webkit-scrollbar{width:5px}
::-webkit-scrollbar-track{background:var(--bg)}
::-webkit-scrollbar-thumb{background:var(--cyan);border-radius:3px}
a{color:var(--cyan);text-decoration:none}

/* NAV */
nav{position:fixed;top:0;left:0;right:0;z-index:200;background:rgba(5,8,16,.94);backdrop-filter:blur(14px);border-bottom:1px solid var(--border);height:58px;display:flex;align-items:center;padding:0 2rem;justify-content:space-between}
.nav-logo{background:var(--grad);-webkit-background-clip:text;-webkit-text-fill-color:transparent;font-weight:800;font-size:1.15rem;letter-spacing:3px}
.nav-links{display:flex;gap:1.4rem;list-style:none}
.nav-links a{color:var(--t2);font-size:.82rem;letter-spacing:.5px;transition:color .2s}
.nav-links a:hover{color:var(--cyan)}
.nav-pill{background:rgba(0,212,255,.08);border:1px solid rgba(0,212,255,.25);border-radius:100px;padding:.25rem .8rem;font-size:.72rem;color:var(--cyan);letter-spacing:.5px}
@media(max-width:700px){.nav-links{display:none}}

/* HERO */
.hero{min-height:100vh;display:flex;align-items:center;justify-content:center;position:relative;overflow:hidden;padding:80px 2rem 3rem}
.hbg{position:absolute;inset:0;
  background:radial-gradient(ellipse 90% 70% at 50% -10%,rgba(0,212,255,.07) 0%,transparent 65%),
             radial-gradient(ellipse 60% 60% at 85% 85%,rgba(139,92,246,.07) 0%,transparent 65%)}
.hgrid{position:absolute;inset:0;
  background-image:linear-gradient(rgba(0,212,255,.04) 1px,transparent 1px),
                   linear-gradient(90deg,rgba(0,212,255,.04) 1px,transparent 1px);
  background-size:52px 52px;
  -webkit-mask-image:radial-gradient(ellipse 90% 80% at 50% 50%,black 30%,transparent 100%);
  mask-image:radial-gradient(ellipse 90% 80% at 50% 50%,black 30%,transparent 100%)}
.hero-content{position:relative;z-index:1;text-align:center;max-width:920px}
.badge{display:inline-flex;align-items:center;gap:.45rem;background:rgba(0,212,255,.08);border:1px solid rgba(0,212,255,.28);border-radius:100px;padding:.35rem 1rem;font-size:.75rem;color:var(--cyan);letter-spacing:1.2px;text-transform:uppercase;margin-bottom:2rem}
.badge-dot{width:7px;height:7px;border-radius:50%;background:var(--cyan);animation:pulse 2s infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.5;transform:scale(.85)}}
h1.hero-h{font-size:clamp(2.8rem,7vw,5.5rem);font-weight:900;line-height:1.05;margin-bottom:1.1rem}
.hl{background:var(--grad);-webkit-background-clip:text;-webkit-text-fill-color:transparent}
.hero-sub{font-size:clamp(1rem,2.2vw,1.25rem);color:var(--t2);max-width:580px;margin:0 auto 2.5rem}
.hero-sub strong{color:var(--t1)}
.cta-row{display:flex;gap:1rem;justify-content:center;flex-wrap:wrap;margin-bottom:3.5rem}
.btn{display:inline-flex;align-items:center;gap:.45rem;padding:.7rem 1.8rem;border-radius:9px;font-size:.88rem;font-weight:600;transition:all .2s;cursor:pointer;border:none;text-decoration:none}
.btn-p{background:var(--grad);color:#000}
.btn-p:hover{transform:translateY(-2px);box-shadow:0 8px 22px rgba(0,212,255,.28);color:#000}
.btn-s{background:transparent;border:1px solid var(--border);color:var(--t1)}
.btn-s:hover{border-color:var(--cyan);color:var(--cyan)}
.hero-stats{display:flex;gap:3rem;justify-content:center;flex-wrap:wrap}
.stat-v{font-size:2.1rem;font-weight:800;background:var(--grad);-webkit-background-clip:text;-webkit-text-fill-color:transparent}
.stat-l{font-size:.73rem;color:var(--t3);text-transform:uppercase;letter-spacing:1.2px;margin-top:.1rem}

/* SECTIONS */
.sec{padding:5rem 2rem;max-width:1180px;margin:0 auto}
.sec-label{font-size:.72rem;text-transform:uppercase;letter-spacing:3px;color:var(--cyan);margin-bottom:.4rem}
.sec-title{font-size:clamp(1.8rem,4vw,2.7rem);font-weight:800;margin-bottom:.9rem}
.sec-desc{color:var(--t2);max-width:620px;margin-bottom:2.8rem;font-size:.95rem}
.divider{height:1px;background:linear-gradient(to right,transparent,rgba(0,212,255,.12),transparent);margin:0}

/* CARDS */
.card{background:var(--gcard);border:1px solid var(--border);border-radius:16px;padding:1.5rem;transition:all .3s}
.card:hover{border-color:rgba(0,212,255,.35);box-shadow:var(--glow);transform:translateY(-3px)}
.ci{font-size:1.8rem;margin-bottom:.8rem}
.ct{font-size:1rem;font-weight:700;margin-bottom:.45rem}
.cd{color:var(--t2);font-size:.88rem}
.g3{display:grid;grid-template-columns:repeat(auto-fit,minmax(270px,1fr));gap:1.4rem}
.g2{display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:1.4rem}
.g4{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:1.3rem}
code{background:rgba(0,212,255,.1);border:1px solid rgba(0,212,255,.2);border-radius:4px;padding:.1rem .4rem;font-family:'Courier New',monospace;font-size:.84em;color:var(--cyan)}

/* TEAM */
.team-card{background:var(--gcard);border:1px solid var(--border2);border-radius:16px;padding:1.5rem;text-align:center;transition:all .3s}
.team-card:hover{border-color:rgba(139,92,246,.45);box-shadow:0 0 28px rgba(139,92,246,.13);transform:translateY(-3px)}
.av{width:60px;height:60px;border-radius:50%;background:var(--grad);display:flex;align-items:center;justify-content:center;font-size:1.2rem;font-weight:800;margin:0 auto .9rem;color:#000}
.tn{font-weight:700;margin-bottom:.2rem;font-size:.95rem}
.tr{font-size:.72rem;color:var(--cyan);text-transform:uppercase;letter-spacing:1px;margin-bottom:.3rem}
.te{font-size:.75rem;color:var(--t3)}

/* I/O FLOW */
.io-scroll{overflow-x:auto;padding:1.5rem 0 1rem}
.io-row{display:flex;align-items:stretch;gap:.8rem;min-width:max-content}
.io-step{background:var(--gcard);border:1px solid var(--border);border-radius:12px;padding:1rem 1.2rem;text-align:center;min-width:130px;display:flex;flex-direction:column;align-items:center;gap:.4rem}
.io-step.active-step{border-color:rgba(0,212,255,.4);background:rgba(0,212,255,.04)}
.io-icon{font-size:1.4rem}
.io-lbl{font-size:.65rem;color:var(--t3);text-transform:uppercase;letter-spacing:1px}
.io-val{font-size:.8rem;color:var(--cyan);font-family:'Courier New',monospace;word-break:break-all;text-align:center}
.io-val.text-val{color:var(--t2);font-family:inherit;font-size:.78rem}
.io-arr{color:var(--cyan);font-size:1.3rem;align-self:center;flex-shrink:0}

/* TOKENS */
.tok-row{display:flex;gap:.35rem;flex-wrap:wrap;margin:.7rem 0}
.tok{background:rgba(0,212,255,.09);border:1px solid rgba(0,212,255,.28);border-radius:5px;padding:.25rem .55rem;font-family:'Courier New',monospace;font-size:.78rem;color:var(--cyan)}
.tok.sp{background:rgba(139,92,246,.09);border-color:rgba(139,92,246,.3);color:var(--purple)}
.tok.msk{background:rgba(245,158,11,.09);border-color:rgba(245,158,11,.3);color:var(--orange)}
.tok.out{background:rgba(16,185,129,.09);border-color:rgba(16,185,129,.3);color:var(--green)}

/* QKV */
.qkv-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:1.5rem;margin-bottom:1.8rem}
.qkv{border-radius:18px;padding:2rem;transition:all .35s;cursor:default;position:relative;overflow:hidden}
.qkv::before{content:'';position:absolute;top:-40%;right:-40%;width:80%;height:80%;border-radius:50%;opacity:.06;transition:opacity .3s}
.qkv:hover::before{opacity:.1}
.qkv.Q{background:linear-gradient(145deg,rgba(245,158,11,.1),rgba(245,158,11,.02));border:1px solid rgba(245,158,11,.3)}
.qkv.K{background:linear-gradient(145deg,rgba(0,212,255,.1),rgba(0,212,255,.02));border:1px solid rgba(0,212,255,.3)}
.qkv.V{background:linear-gradient(145deg,rgba(139,92,246,.1),rgba(139,92,246,.02));border:1px solid rgba(139,92,246,.3)}
.qkv.Q::before{background:var(--orange)}
.qkv.K::before{background:var(--cyan)}
.qkv.V::before{background:var(--purple)}
.qkv.Q:hover{box-shadow:0 10px 35px rgba(245,158,11,.2);transform:translateY(-4px)}
.qkv.K:hover{box-shadow:0 10px 35px rgba(0,212,255,.2);transform:translateY(-4px)}
.qkv.V:hover{box-shadow:0 10px 35px rgba(139,92,246,.2);transform:translateY(-4px)}
.ql{font-size:3.2rem;font-weight:900;line-height:1;margin-bottom:.4rem}
.Q .ql{color:var(--orange)}.K .ql{color:var(--cyan)}.V .ql{color:var(--purple)}
.qn{font-size:1rem;font-weight:700;margin-bottom:1rem;color:var(--t1)}
.qf{font-family:'Courier New',monospace;font-size:.83rem;background:rgba(0,0,0,.35);padding:.7rem 1rem;border-radius:8px;margin-bottom:.9rem;color:var(--t2)}
.Q .qf .fh{color:var(--orange)}.K .qf .fh{color:var(--cyan)}.V .qf .fh{color:var(--purple)}
.qshape{display:inline-flex;align-items:center;gap:.3rem;background:rgba(0,0,0,.3);padding:.25rem .7rem;border-radius:100px;font-size:.76rem;font-family:'Courier New',monospace;color:var(--t3);margin-bottom:.9rem}
.qd{font-size:.87rem;color:var(--t2);line-height:1.65}
.qd .em{font-weight:600}
.Q .qd .em{color:var(--orange)}.K .qd .em{color:var(--cyan)}.V .qd .em{color:var(--purple)}

/* FORMULA BOX */
.fbox{background:var(--gcard);border:1px solid var(--border);border-radius:16px;padding:2rem;text-align:center;margin:1.8rem 0}
.fbox-lbl{font-size:.7rem;text-transform:uppercase;letter-spacing:2.5px;color:var(--t3);margin-bottom:1rem}
.fmain{font-family:'Courier New',monospace;font-size:clamp(.82rem,2.2vw,1.1rem);background:rgba(0,0,0,.45);padding:1rem 2rem;border-radius:11px;display:inline-block;margin-bottom:1rem;line-height:1.8}
.fQ{color:var(--orange)}.fK{color:var(--cyan)}.fV{color:var(--purple)}
.fbox-note{font-size:.85rem;color:var(--t2);max-width:580px;margin:0 auto}

/* COMPARISON TABLE */
.ctbl{width:100%;border-collapse:collapse;font-size:.87rem;margin:1.8rem 0;overflow:hidden;border-radius:12px;border:1px solid var(--border)}
.ctbl th{background:rgba(0,212,255,.08);padding:.75rem 1rem;text-align:left;font-weight:700;color:var(--cyan);border-bottom:1px solid var(--border);font-size:.78rem;text-transform:uppercase;letter-spacing:.5px}
.ctbl td{padding:.7rem 1rem;border-bottom:1px solid rgba(30,58,95,.2);color:var(--t2)}
.ctbl tr:last-child td{border-bottom:none}
.ctbl tr:hover td{background:rgba(0,212,255,.03)}
.ctbl td:first-child{color:var(--t1);font-weight:600}

/* DIFFUSION DEMO */
.ddemo{background:var(--gcard);border:1px solid var(--border);border-radius:18px;padding:2rem;margin:1.8rem 0}
.ddemo-head{display:flex;align-items:center;justify-content:space-between;margin-bottom:1.5rem;flex-wrap:wrap;gap:.8rem}
.ddemo-title{font-size:.95rem;font-weight:700}
.step-ctr{font-size:.82rem;color:var(--t3);background:rgba(0,0,0,.3);padding:.3rem .8rem;border-radius:100px}
.ctx-row{display:flex;gap:.4rem;justify-content:center;flex-wrap:wrap;margin-bottom:.5rem}
.ctx-lbl{font-size:.65rem;color:var(--t3);text-transform:uppercase;letter-spacing:1px;text-align:center;margin-bottom:.4rem}
.block-row{display:flex;gap:1rem;justify-content:center;flex-wrap:wrap;margin:1.2rem 0}
.btoken{width:82px;height:82px;border-radius:12px;display:flex;align-items:center;justify-content:center;font-family:'Courier New',monospace;font-size:.82rem;font-weight:700;transition:all .55s ease;border:2px solid;text-align:center;padding:.2rem}
.btoken.masked{background:rgba(245,158,11,.08);border-color:rgba(245,158,11,.4);color:var(--orange)}
.btoken.revealed{background:rgba(16,185,129,.1);border-color:rgba(16,185,129,.45);color:var(--green)}
.step-desc{text-align:center;color:var(--t2);font-size:.87rem;min-height:2.2rem;margin:.6rem 0}
.step-btns{display:flex;gap:.65rem;justify-content:center;flex-wrap:wrap;margin-top:1rem}
.sbtn{padding:.45rem 1.15rem;border-radius:8px;border:1px solid var(--border);background:rgba(0,212,255,.03);color:var(--t2);cursor:pointer;font-size:.82rem;transition:all .2s;font-family:inherit}
.sbtn:hover{border-color:var(--cyan);color:var(--cyan)}
.sbtn.on{background:rgba(0,212,255,.12);border-color:var(--cyan);color:var(--cyan)}

/* RESULTS */
.rtbl{width:100%;border-collapse:collapse;font-size:.87rem;margin:1.2rem 0;border:1px solid var(--border);border-radius:12px;overflow:hidden}
.rtbl th{background:rgba(0,0,0,.35);padding:.7rem 1rem;font-size:.75rem;text-transform:uppercase;letter-spacing:.5px;color:var(--t2);font-weight:600;border-bottom:1px solid var(--border)}
.rtbl th:first-child{text-align:left}
.rtbl td{padding:.7rem 1rem;border-bottom:1px solid rgba(30,58,95,.18);color:var(--t2);text-align:center}
.rtbl td:first-child{text-align:left;color:var(--t1);font-weight:600}
.rtbl tr.hr td{background:rgba(0,212,255,.05)}
.rtbl tr:last-child td{border-bottom:none}

/* HBOX */
.hbox{border-radius:12px;padding:1.1rem 1.4rem;margin:1.3rem 0;font-size:.87rem;line-height:1.7}
.hbox.info{background:rgba(0,212,255,.05);border:1px solid rgba(0,212,255,.2)}
.hbox.warn{background:rgba(245,158,11,.05);border:1px solid rgba(245,158,11,.2)}
.hbox.ok{background:rgba(16,185,129,.05);border:1px solid rgba(16,185,129,.2)}
.hbox strong{color:var(--cyan)}
.hbox.warn strong{color:var(--orange)}
.hbox.ok strong{color:var(--green)}
.hbox p{color:var(--t2);margin-top:.25rem}

/* TABS */
.tabs{display:flex;gap:.4rem;margin-bottom:1.8rem;border-bottom:1px solid var(--border);flex-wrap:wrap}
.tbtn{padding:.55rem 1.2rem;border:none;background:none;color:var(--t3);cursor:pointer;font-size:.87rem;border-bottom:2px solid transparent;transition:all .2s;margin-bottom:-1px;font-family:inherit}
.tbtn.on{color:var(--cyan);border-bottom-color:var(--cyan)}
.tbtn:hover{color:var(--t1)}
.tpanel{display:none}.tpanel.on{display:block}

/* TIMELINE */
.tl{position:relative;padding-left:2rem}
.tl::before{content:'';position:absolute;left:0;top:0;bottom:0;width:2px;background:linear-gradient(to bottom,var(--cyan),var(--purple))}
.tli{position:relative;margin-bottom:2.2rem;padding-left:1.5rem}
.tldot{position:absolute;left:-2.4rem;top:.35rem;width:11px;height:11px;border-radius:50%;border:2px solid var(--cyan);background:var(--bg)}
.tldot.p{border-color:var(--purple)}
.tlt{font-size:.72rem;color:var(--cyan);text-transform:uppercase;letter-spacing:1.2px;margin-bottom:.2rem}
.tlt.p{color:var(--purple)}
.tls{font-size:1rem;font-weight:700;margin-bottom:.6rem}
.tlc{background:var(--gcard);border:1px solid var(--border);border-radius:12px;padding:1rem 1.2rem}
.tltp{font-size:.88rem;font-weight:600;margin-bottom:.5rem}
.tlul{list-style:none;font-size:.82rem;color:var(--t2)}
.tlul li::before{content:'→ ';color:var(--cyan)}
.tlul li{margin-bottom:.2rem}

/* FAQ */
.faq-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:1rem;margin-top:1.8rem}
.faq{background:var(--gcard);border:1px solid var(--border);border-radius:12px;overflow:hidden}
.faq-h{padding:.9rem 1.1rem;cursor:pointer;display:flex;justify-content:space-between;align-items:center;gap:1rem;transition:background .2s;font-size:.87rem;font-weight:600}
.faq-h:hover{background:rgba(0,212,255,.04)}
.faq-toggle{color:var(--cyan);flex-shrink:0;font-size:1.2rem;transition:transform .3s;line-height:1}
.faq.open .faq-toggle{transform:rotate(45deg)}
.faq-body{display:none;padding:.9rem 1.1rem;font-size:.83rem;color:var(--t2);border-top:1px solid var(--border);line-height:1.7}
.faq.open .faq-body{display:block}

/* FOOTER */
footer{background:var(--bg2);border-top:1px solid var(--border);padding:2.5rem 2rem;text-align:center;color:var(--t3);font-size:.83rem}
.ft{font-size:.95rem;font-weight:600;color:var(--t2);margin-bottom:.4rem}
.links-row{display:flex;gap:1.5rem;justify-content:center;flex-wrap:wrap;margin:.8rem 0}
.links-row a{color:var(--cyan);font-size:.8rem}

/* ANIMATIONS */
@keyframes fadeUp{from{opacity:0;transform:translateY(18px)}to{opacity:1;transform:translateY(0)}}
.reveal{opacity:0;transform:translateY(18px);transition:opacity .55s ease,transform .55s ease}
.reveal.in{opacity:1;transform:translateY(0)}

/* MASK GRID */
.mask-grid{display:grid;gap:3px;margin:1.2rem auto;max-width:320px}
.mg-cell{width:28px;height:28px;border-radius:4px;display:flex;align-items:center;justify-content:center;font-size:.6rem;font-weight:700;transition:background .5s,color .5s}
.mg-cell.attend{background:rgba(0,212,255,.2);color:var(--cyan);border:1px solid rgba(0,212,255,.4)}
.mg-cell.self{background:rgba(139,92,246,.2);color:var(--purple);border:1px solid rgba(139,92,246,.4)}
.mg-cell.block{background:rgba(245,158,11,.15);color:var(--orange);border:1px solid rgba(245,158,11,.3)}
.mg-cell.empty{background:rgba(255,255,255,.03);color:transparent;border:1px solid rgba(255,255,255,.04)}
.mg-label{font-size:.65rem;color:var(--t3);text-align:center;margin-bottom:.3rem}
</style>
</head>
<body>

<!-- NAV -->
<nav>
  <div class="nav-logo">E2D2</div>
  <ul class="nav-links">
    <li><a href="#proyecto">Proyecto</a></li>
    <li><a href="#io">Entradas/Salidas</a></li>
    <li><a href="#qkv">Q · K · V</a></li>
    <li><a href="#difusion">Difusión</a></li>
    <li><a href="#resultados">Resultados</a></li>
    <li><a href="#guion">Guión</a></li>
  </ul>
  <span class="nav-pill">WMT14 De→En</span>
</nav>

<!-- HERO -->
<div class="hero" style="max-width:none;padding:80px 2rem 3rem">
  <div class="hbg"></div>
  <div class="hgrid"></div>
  <div class="hero-content">
    <div class="badge">
      <span class="badge-dot"></span>
      Proyecto Final · Especialización en Ciencia de Datos 2026-01
    </div>
    <h1 class="hero-h">
      <span class="hl">E2D2</span><br>
      Transformer con<br>Difusión Discreta
    </h1>
    <p class="hero-sub">
      Un encoder-decoder que no genera texto <strong>palabra por palabra</strong> —
      parte de bloques de ruido total y los refina iterativamente
      hasta obtener una <strong>traducción coherente</strong>.
    </p>
    <div class="cta-row">
      <a href="#qkv" class="btn btn-p">Explorar Q · K · V</a>
      <a href="#difusion" class="btn btn-s">Ver Difusión en Acción</a>
    </div>
    <div class="hero-stats">
      <div class="stat">
        <div class="stat-v">28</div>
        <div class="stat-l">Capas Encoder</div>
      </div>
      <div class="stat">
        <div class="stat-v">4</div>
        <div class="stat-l">Capas Decoder</div>
      </div>
      <div class="stat">
        <div class="stat-v">block_size=4</div>
        <div class="stat-l">Tokens por bloque</div>
      </div>
      <div class="stat">
        <div class="stat-v">16.53</div>
        <div class="stat-l">BLEU (150 ejemplos)</div>
      </div>
    </div>
  </div>
</div>

<div class="divider"></div>

<!-- PROYECTO + EQUIPO -->
<section class="sec" id="proyecto">
  <div class="sec-label">El Proyecto</div>
  <h2 class="sec-title">¿Qué hace E2D2 diferente?</h2>
  <p class="sec-desc">
    Mientras OPUS-MT genera un token a la vez de izquierda a derecha, E2D2 genera bloques completos
    en paralelo mediante <strong>absorbing diffusion</strong> — el equivalente discreto del ruido gaussiano.
  </p>

  <div class="g3" style="margin-bottom:3.5rem">
    <div class="card reveal">
      <div class="ci">🔀</div>
      <div class="ct">Generación en Bloques</div>
      <div class="cd">Genera <code>block_size=4</code> tokens simultáneamente. Cada bloque comienza como <code>[MASK][MASK][MASK][MASK]</code> y se refina en múltiples pasos — no token por token.</div>
    </div>
    <div class="card reveal">
      <div class="ci">🌫️</div>
      <div class="ct">Difusión Discreta</div>
      <div class="cd">Adapta los modelos de difusión de imágenes al lenguaje. El "ruido" no es gaussiano: es el token especial <code>[MASK]</code>. Denoising = predecir qué token real reemplaza cada máscara.</div>
    </div>
    <div class="card reveal">
      <div class="ci">⚖️</div>
      <div class="ct">Asimetría Estratégica</div>
      <div class="cd">28 capas en el encoder vs 4 en el decoder. La mayor capacidad se concentra en <em>entender</em> el alemán, no en refinar el inglés — decisión arquitectural deliberada del paper.</div>
    </div>
  </div>

  <div class="sec-label">Equipo</div>
  <h2 class="sec-title">Integrantes</h2>

  <div class="g4">
    <div class="team-card reveal" style="border-top:3px solid var(--orange)">
      <div class="av" style="background:linear-gradient(135deg,#f59e0b,#ef4444)">MG</div>
      <div class="tn">Mateo González Ruiz</div>
      <div class="tr">Entradas &amp; Salidas</div>
      <div class="te">mateo.gonzalez@uao.edu.co</div>
    </div>
    <div class="team-card reveal" style="border-top:3px solid var(--cyan)">
      <div class="av">JF</div>
      <div class="tn">Jorge Luis Fong Gutierrez</div>
      <div class="tr">Cross-Attention Q·K·V</div>
      <div class="te">jorge.fong@uao.edu.co</div>
    </div>
    <div class="team-card reveal" style="border-top:3px solid var(--purple)">
      <div class="av" style="background:linear-gradient(135deg,#8b5cf6,#06b6d4)">JR</div>
      <div class="tn">Jhonatan Rengifo Bermeo</div>
      <div class="tr">Difusión Discreta</div>
      <div class="te">jhonatan.rengifo@uao.edu.co</div>
    </div>
    <div class="team-card reveal" style="border-top:3px solid var(--green)">
      <div class="av" style="background:linear-gradient(135deg,#10b981,#0ea5e9)">NV</div>
      <div class="tn">Nicolás Vásquez Renjifo</div>
      <div class="tr">Resultados &amp; Cierre</div>
      <div class="te">nicolas.vasquez@uao.edu.co</div>
    </div>
  </div>
</section>

<div class="divider"></div>

<!-- ENTRADAS Y SALIDAS -->
<section class="sec" id="io">
  <div class="sec-label">Flujo de Datos</div>
  <h2 class="sec-title">Entradas y Salidas del Sistema</h2>
  <p class="sec-desc">
    Desde texto alemán crudo hasta traducción en inglés. El pipeline completo pasa por tokenización,
    codificación tensorial y generación por difusión bloque a bloque.
  </p>

  <div class="io-scroll">
    <div class="io-row">
      <div class="io-step">
        <div class="io-icon">🇩🇪</div>
        <div class="io-lbl">Entrada</div>
        <div class="io-val text-val">"Guten Morgen, wie geht es Ihnen heute?"</div>
      </div>
      <div class="io-arr">→</div>
      <div class="io-step">
        <div class="io-icon">🔤</div>
        <div class="io-lbl">Tokenización</div>
        <div class="io-val">[BOS, 4922, 12834, 16, ...]</div>
      </div>
      <div class="io-arr">→</div>
      <div class="io-step">
        <div class="io-icon">📐</div>
        <div class="io-lbl">Tensor</div>
        <div class="io-val">[1, L_src]</div>
      </div>
      <div class="io-arr">→</div>
      <div class="io-step active-step">
        <div class="io-icon">🤖</div>
        <div class="io-lbl">E2D2 Model</div>
        <div class="io-val">Encoder (28L) + Decoder (4L) × bloques</div>
      </div>
      <div class="io-arr">→</div>
      <div class="io-step">
        <div class="io-icon">🔢</div>
        <div class="io-lbl">output_ids</div>
        <div class="io-val">[src_tokens + generated_tokens]</div>
      </div>
      <div class="io-arr">→</div>
      <div class="io-step">
        <div class="io-icon">🔤</div>
        <div class="io-lbl">Decodificación</div>
        <div class="io-val">extraer solo tokens nuevos, strip especiales</div>
      </div>
      <div class="io-arr">→</div>
      <div class="io-step" style="border-color:rgba(16,185,129,.4);background:rgba(16,185,129,.04)">
        <div class="io-icon">🇬🇧</div>
        <div class="io-lbl">Traducción</div>
        <div class="io-val text-val" style="color:#10b981">"Good morning, how are you today?"</div>
      </div>
    </div>
  </div>

  <div class="g2" style="margin-top:1.8rem">
    <div class="card reveal">
      <div class="ct" style="margin-bottom:.8rem">Tokens de Entrada</div>
      <div class="tok-row">
        <span class="tok sp">[BOS]</span>
        <span class="tok">Guten</span>
        <span class="tok">Morgen</span>
        <span class="tok">,</span>
        <span class="tok">wie</span>
        <span class="tok">geht</span>
        <span class="tok">es</span>
        <span class="tok">Ihnen</span>
        <span class="tok">heute</span>
        <span class="tok">?</span>
        <span class="tok sp">[EOS]</span>
      </div>
      <div style="font-size:.78rem;color:var(--t3);margin-top:.6rem">
        Tensor shape: <code>[1, L_src]</code> · L_src = longitud con tokens especiales
      </div>
    </div>
    <div class="card reveal">
      <div class="ct" style="margin-bottom:.8rem">Parámetros de Control</div>
      <table style="width:100%;font-size:.85rem;border-collapse:collapse">
        <tr>
          <td style="padding:.45rem 0;color:var(--t3);border-bottom:1px solid rgba(30,58,95,.3)"><code>block_size</code></td>
          <td style="padding:.45rem 0;color:var(--cyan);font-family:monospace;font-weight:700;border-bottom:1px solid rgba(30,58,95,.3)">4</td>
          <td style="padding:.45rem 0;color:var(--t2);font-size:.8rem;border-bottom:1px solid rgba(30,58,95,.3)">Tokens generados en paralelo por bloque</td>
        </tr>
        <tr>
          <td style="padding:.45rem 0;color:var(--t3);border-bottom:1px solid rgba(30,58,95,.3)"><code>num_steps</code></td>
          <td style="padding:.45rem 0;color:var(--cyan);font-family:monospace;font-weight:700;border-bottom:1px solid rgba(30,58,95,.3)">4</td>
          <td style="padding:.45rem 0;color:var(--t2);font-size:.8rem;border-bottom:1px solid rgba(30,58,95,.3)">Iteraciones de denoising por bloque</td>
        </tr>
        <tr>
          <td style="padding:.45rem 0;color:var(--t3)"><code>max_new_tokens</code></td>
          <td style="padding:.45rem 0;color:var(--cyan);font-family:monospace;font-weight:700">64–96</td>
          <td style="padding:.45rem 0;color:var(--t2);font-size:.8rem">Límite superior de la traducción</td>
        </tr>
      </table>
    </div>
  </div>

  <div class="card reveal" style="margin-top:1.3rem">
    <div class="ct" style="margin-bottom:.9rem">Salida del Modelo + Métricas</div>
    <div class="g2" style="gap:1rem">
      <div>
        <div style="font-size:.75rem;color:var(--t3);margin-bottom:.45rem">Tokens generados (nuevos):</div>
        <div class="tok-row">
          <span class="tok out">Good</span>
          <span class="tok out">morning</span>
          <span class="tok out">,</span>
          <span class="tok out">how</span>
          <span class="tok out">are</span>
          <span class="tok out">you</span>
          <span class="tok out">today</span>
          <span class="tok out">?</span>
        </div>
      </div>
      <div>
        <div style="font-size:.75rem;color:var(--t3);margin-bottom:.45rem">Métricas de inferencia (config oficial, 150 ej.):</div>
        <div style="font-size:.85rem;color:var(--t2);line-height:2">
          ⏱ Tiempo promedio: <strong style="color:var(--t1)">0.706 s</strong><br>
          ⚡ Throughput: <strong style="color:var(--t1)">50.32 tokens/s</strong><br>
          📊 BLEU: <strong style="color:var(--cyan)">16.53</strong> &nbsp;·&nbsp; chrF: <strong style="color:var(--cyan)">49.66</strong>
        </div>
      </div>
    </div>
  </div>
</section>

<div class="divider"></div>

<!-- Q K V -->
<section class="sec" id="qkv">
  <div class="sec-label">Mecanismo Central</div>
  <h2 class="sec-title">Query · Key · Value en E2D2</h2>
  <p class="sec-desc">
    El cross-attention es el <em>puente</em> entre lo que el encoder entendió del alemán
    y lo que el decoder está construyendo en inglés. Cada componente tiene un rol preciso
    — y en E2D2 hay diferencias fundamentales respecto a un Transformer clásico.
  </p>

  <div class="qkv-grid">
    <!-- QUERY -->
    <div class="qkv Q reveal">
      <div class="ql">Q</div>
      <div class="qn">Query &mdash; "¿Qué necesito?"</div>
      <div class="qf">
        <span class="fh">Q</span> = z_bloque_activo &times; W<sub>Q</sub>
      </div>
      <div class="qshape">📐 [batch, block_size, d_model] = [1, 4, d]</div>
      <div class="qd">
        Proviene del <span class="em">decoder</span>. Son las representaciones de los
        <code>block_size=4</code> tokens que están siendo refinados ahora mismo.
        Estos tokens están en estado <span class="em">ruidoso / parcialmente enmascarado</span>
        — no son tokens definitivos aún.<br><br>
        <span style="font-size:.8rem;color:var(--orange)">⚡ Diferencia clave con OPUS-MT:</span>
        <span style="font-size:.82rem;color:var(--t2)"> en OPUS-MT Q es el último token
        definitivo (certeza total). En E2D2, Q es <em>incertidumbre que busca certeza</em>
        y se usa <code>num_steps</code> veces por bloque.</span>
      </div>
    </div>

    <!-- KEY -->
    <div class="qkv K reveal">
      <div class="ql">K</div>
      <div class="qn">Key &mdash; "¿De qué hablo?"</div>
      <div class="qf">
        <span class="fh">K</span> = H_encoder &times; W<sub>K</sub>
      </div>
      <div class="qshape">📐 [batch, L_src, d_model]</div>
      <div class="qd">
        Proviene del <span class="em">encoder</span> (28 capas Transformer).
        Representa la "firma" de cada token alemán: qué ofrece como contexto,
        qué tan relevante es para cada posición del decoder.<br><br>
        El encoder es <span class="em">bidireccional</span> — cada Key contiene
        contexto de toda la oración alemana, no solo del prefijo. Esto contrasta
        con el decoder, que es causal.
      </div>
    </div>

    <!-- VALUE -->
    <div class="qkv V reveal">
      <div class="ql">V</div>
      <div class="qn">Value &mdash; "¿Qué aporto?"</div>
      <div class="qf">
        <span class="fh">V</span> = H_encoder &times; W<sub>V</sub>
      </div>
      <div class="qshape">📐 [batch, L_src, d_v]</div>
      <div class="qd">
        También del <span class="em">encoder</span>.
        Es la información semántica real que se transfiere al decoder cuando la
        similitud Query·Key es alta.<br><br>
        Las Keys miden <em>compatibilidad</em>. Los Values entregan <em>contenido</em>.
        Son proyecciones lineales independientes del mismo H_encoder:<br>
        <code style="font-size:.76rem">tie_encoder_decoder_weights = False</code>
        (confirmado celda 29 del notebook).
      </div>
    </div>
  </div>

  <!-- FORMULA -->
  <div class="fbox reveal">
    <div class="fbox-lbl">Fórmula Scaled Dot-Product Cross-Attention</div>
    <div class="fmain">
      CrossAttention(<span class="fQ">Q</span>, <span class="fK">K</span>, <span class="fV">V</span>)
      &nbsp;=&nbsp;
      softmax&nbsp;(&nbsp;<span class="fQ">Q</span> &times; <span class="fK">K</span><sup>T</sup>
      &nbsp;/&nbsp; &radic;d<sub>k</sub>&nbsp;)
      &nbsp;&times;&nbsp; <span class="fV">V</span>
    </div>
    <div class="fbox-note">
      Resultado: <code>[batch, block_size, d_v]</code> &mdash;
      un vector enriquecido con contexto alemán para cada uno de los
      <strong>4 tokens del bloque activo</strong>.
      Esto ocurre <code>num_steps = 4</code> veces antes de que el bloque quede fijo.
    </div>
  </div>

  <!-- MASK VISUALIZATION -->
  <div class="card reveal" style="margin-bottom:1.8rem">
    <div class="ct" style="margin-bottom:.5rem">Máscara de Atención Block-Causal</div>
    <div class="cd" style="margin-bottom:1rem">
      Visualización simplificada: columnas <code>h*</code> = tokens del encoder (K,V),
      columnas <code>z*</code> = tokens del bloque activo (self-attention).
      El token activo atiende a todos los <code>h*</code> (cross-attention) y
      a todos los <code>z*</code> del mismo bloque.
    </div>
    <div style="overflow-x:auto">
      <div style="display:inline-block;min-width:280px">
        <div class="mg-label">← h* encoder (K,V) → &nbsp; ← z* bloque activo →</div>
        <div id="mask-grid" style="display:grid;grid-template-columns:repeat(8,28px);gap:3px;margin:0 auto;width:fit-content">
        </div>
        <div style="display:flex;gap:1.2rem;justify-content:center;margin-top:.8rem;flex-wrap:wrap">
          <span style="font-size:.72rem;color:var(--cyan)">■ Cross-attention (Q→K,V)</span>
          <span style="font-size:.72rem;color:var(--purple)">■ Self-attention intra-bloque</span>
          <span style="font-size:.72rem;color:var(--t3)">□ No atención (futuro)</span>
        </div>
      </div>
    </div>
  </div>

  <!-- COMPARISON TABLE -->
  <h3 style="margin-bottom:1rem;font-size:1.05rem;font-weight:700">E2D2 vs. Transformer Autoregresivo Clásico</h3>
  <table class="ctbl reveal">
    <thead>
      <tr>
        <th>Aspecto</th>
        <th>OPUS-MT (Autoregresivo)</th>
        <th>E2D2 (Difusión Discreta)</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Origen de Q</td>
        <td>Último token generado (definitivo)</td>
        <td>Bloque de <code>block_size</code> tokens ruidosos</td>
      </tr>
      <tr>
        <td>Estado de Q</td>
        <td>Certeza total — no cambia</td>
        <td>Incertidumbre → se refina <code>num_steps</code> veces</td>
      </tr>
      <tr>
        <td>Origen de K, V</td>
        <td>Encoder (representaciones limpias)</td>
        <td>Encoder 28 capas bidireccional (representaciones más ricas)</td>
      </tr>
      <tr>
        <td>Veces que se ejecuta cross-attn</td>
        <td>1 vez por token</td>
        <td><code>num_steps × n_bloques</code> veces</td>
      </tr>
      <tr>
        <td>Máscara de atención</td>
        <td>Triangular inferior (causal)</td>
        <td>Block-causal (bloques anteriores + intra-bloque libre)</td>
      </tr>
      <tr>
        <td>Pesos encoder ↔ decoder compartidos</td>
        <td>Depende del modelo</td>
        <td>No — <code>tie_encoder_decoder_weights=False</code></td>
      </tr>
    </tbody>
  </table>

  <div class="hbox info reveal">
    <strong>Con <code>use_cache=True</code>:</strong>
    <p>El encoder se ejecuta <strong>una sola vez</strong> para toda la oración alemana.
    Las representaciones H_encoder (y por tanto K y V) se reutilizan en cada bloque
    y en cada paso de denoising — esencial para la eficiencia computacional.</p>
  </div>
</section>

<div class="divider"></div>

<!-- BLOCK DIFFUSION -->
<section class="sec" id="difusion">
  <div class="sec-label">Proceso Generativo</div>
  <h2 class="sec-title">Block Diffusion en Acción</h2>
  <p class="sec-desc">
    Cada bloque de <code>block_size=4</code> tokens comienza completamente enmascarado.
    El decoder aplica <code>num_steps=4</code> iteraciones de cross-attention + self-attention
    hasta que los 4 tokens quedan fijados. Explora el proceso paso a paso:
  </p>

  <div class="ddemo reveal">
    <div class="ddemo-head">
      <div class="ddemo-title">Demo: generando "Good morning , how" desde [MASK][MASK][MASK][MASK]</div>
      <div class="step-ctr" id="sctr">Inicio · Paso 0 de 4</div>
    </div>

    <div class="ctx-lbl">Contexto fijo (bloques anteriores ya generados):</div>
    <div class="ctx-row">
      <span class="tok sp" style="font-size:.75rem">[BOS]</span>
      <span class="tok" style="font-size:.75rem">▸Guten</span>
      <span class="tok" style="font-size:.75rem">Morgen</span>
      <span class="tok" style="font-size:.75rem">,</span>
      <span class="tok" style="font-size:.75rem">wie</span>
      <span class="tok sp" style="font-size:.75rem">[SEP]</span>
    </div>

    <div class="ctx-lbl" style="margin-top:1rem">Bloque activo (siendo refinado ahora):</div>
    <div class="block-row">
      <div class="btoken masked" id="bt0">[MASK]</div>
      <div class="btoken masked" id="bt1">[MASK]</div>
      <div class="btoken masked" id="bt2">[MASK]</div>
      <div class="btoken masked" id="bt3">[MASK]</div>
    </div>

    <div class="step-desc" id="sdesc">
      Estado inicial: los 4 tokens del bloque están completamente enmascarados.
      El decoder va a ejecutar <strong>num_steps=4</strong> iteraciones de refinamiento.
    </div>

    <div class="step-btns">
      <button class="sbtn on" onclick="setStep(0)">◼ Inicio</button>
      <button class="sbtn" onclick="setStep(1)">Paso 1</button>
      <button class="sbtn" onclick="setStep(2)">Paso 2</button>
      <button class="sbtn" onclick="setStep(3)">Paso 3</button>
      <button class="sbtn" onclick="setStep(4)">✓ Final</button>
    </div>
  </div>

  <div class="g3">
    <div class="card reveal">
      <div class="ci">🎭</div>
      <div class="ct">De Imágenes al Lenguaje</div>
      <div class="cd">
        Stable Diffusion y DALL-E 2 usan ruido gaussiano continuo. Los tokens
        son discretos — no existe un valor entre "perro" y "gato". La solución:
        usar <code>[MASK]</code> como ruido discreto. Esto se llama
        <em>absorbing diffusion</em>.
      </div>
    </div>
    <div class="card reveal">
      <div class="ci">🔄</div>
      <div class="ct">4 Diferencias vs. GPT de Clase</div>
      <div class="cd">
        <strong style="color:var(--cyan)">1)</strong> [MASK] como estado generativo inicial.<br>
        <strong style="color:var(--cyan)">2)</strong> Máscara block-causal, no triangular.<br>
        <strong style="color:var(--cyan)">3)</strong> Decisiones revisables (num_steps veces).<br>
        <strong style="color:var(--cyan)">4)</strong> Encoder 7× más profundo que el decoder.
      </div>
    </div>
    <div class="card reveal">
      <div class="ci">⚡</div>
      <div class="ct">Entrenamiento</div>
      <div class="cd">
        Cross-entropy sobre todos los tokens enmascarados en todos los pasos de difusión.
        Sin vanishing gradient: el error se propaga desde posiciones <code>[MASK]</code>
        específicas, no a través de toda la secuencia.
      </div>
    </div>
  </div>
</section>

<div class="divider"></div>

<!-- RESULTADOS -->
<section class="sec" id="resultados">
  <div class="sec-label">Evaluación Experimental</div>
  <h2 class="sec-title">Resultados</h2>
  <p class="sec-desc">
    Evaluación sobre WMT14 de-en. Ablation study en 30 ejemplos para sensibilidad de parámetros;
    evaluación extendida en 150 ejemplos para comparación principal.
  </p>

  <div class="tabs">
    <button class="tbtn on" onclick="tab(event,'abl')">Ablation Study (30 ej.)</button>
    <button class="tbtn" onclick="tab(event,'ext')">Evaluación Extendida (150 ej.)</button>
  </div>

  <div class="tpanel on" id="abl">
    <table class="rtbl reveal">
      <thead>
        <tr>
          <th style="text-align:left">Configuración E2D2</th>
          <th>BLEU ↑</th>
          <th>chrF ↑</th>
          <th>Tiempo prom.</th>
          <th>Tokens/s ↑</th>
          <th>Rep. bigrama ↓</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>b2/s2 con cache</td>
          <td>0.00</td>
          <td>0.31</td>
          <td>0.140 s</td>
          <td>15.75</td>
          <td>0.033</td>
        </tr>
        <tr class="hr">
          <td><strong>b4/s4 con cache ✓ oficial</strong></td>
          <td><strong style="color:var(--cyan)">17.85</strong></td>
          <td><strong style="color:var(--cyan)">50.00</strong></td>
          <td>1.066 s</td>
          <td>37.24</td>
          <td>0.092</td>
        </tr>
        <tr>
          <td>b8/s8 con cache</td>
          <td>10.13</td>
          <td>45.65</td>
          <td>0.950 s</td>
          <td>71.03</td>
          <td><strong style="color:var(--orange)">0.240</strong></td>
        </tr>
      </tbody>
    </table>
    <div class="hbox warn">
      <strong>Limitación técnica:</strong>
      <p>La configuración <code>use_cache=False</code> falló en todos los ejemplos:
      <code>RuntimeError: The size of tensor a (4) must match the size of tensor b (0)</code>.
      El cache no es solo optimización — es parte integral del diseño arquitectural del checkpoint.</p>
    </div>
  </div>

  <div class="tpanel" id="ext">
    <table class="rtbl reveal">
      <thead>
        <tr>
          <th style="text-align:left">Modelo / Configuración</th>
          <th>BLEU ↑</th>
          <th>chrF ↑</th>
          <th>Tiempo prom.</th>
          <th>Tokens/s ↑</th>
          <th>Rep. bigrama ↓</th>
        </tr>
      </thead>
      <tbody>
        <tr class="hr">
          <td><strong>E2D2 b4/s4 cache (oficial)</strong></td>
          <td><strong style="color:var(--cyan)">16.53</strong></td>
          <td><strong style="color:var(--cyan)">49.66</strong></td>
          <td>0.706 s</td>
          <td>50.32</td>
          <td>0.077</td>
        </tr>
        <tr>
          <td>OPUS-MT autoregresivo</td>
          <td>25.02</td>
          <td>54.09</td>
          <td>0.189 s</td>
          <td>144.68</td>
          <td>0.005</td>
        </tr>
      </tbody>
    </table>
    <div class="hbox info">
      <strong>Contexto importante:</strong>
      <p>OPUS-MT supera a E2D2 en BLEU, chrF y velocidad. Esto es <strong>esperado y no invalida el proyecto</strong>.
      El objetivo fue implementar y analizar el <em>paradigma</em> de difusión discreta, no competir en benchmarks.
      OPUS-MT es un modelo especializado en De→En; E2D2 fue entrenado en condiciones distintas.
      La comparación es <strong>ilustrativa</strong>: muestra las diferencias de paradigma, no de calidad absoluta.</p>
    </div>
  </div>

  <h3 style="margin:2.2rem 0 1rem;font-size:1.05rem;font-weight:700">Patrones de Error en E2D2</h3>
  <div class="g2">
    <div class="card reveal" style="border-left:3px solid var(--orange)">
      <div class="ci">🔁</div>
      <div class="ct">Repetición de contenido</div>
      <div class="cd">
        E2D2 genera correctamente el inicio del bloque y luego repite contenido ajeno.
        Más frecuente con <code>block_size=8</code> (repetición de bigramas: <strong style="color:var(--orange)">24%</strong>).
        Causa: insuficientes pasos de refinamiento para bloques grandes.
      </div>
    </div>
    <div class="card reveal" style="border-left:3px solid var(--red)">
      <div class="ci">📉</div>
      <div class="ct">Salidas cortas o vacías</div>
      <div class="cd">
        Con <code>block_size=2</code>, los bloques no tienen suficiente ventana temporal
        para establecer coherencia intra-bloque (<strong style="color:var(--red)">BLEU ≈ 0</strong>).
        Ambos errores son consistentes con los límites teóricos de difusión discreta con pocos pasos.
      </div>
    </div>
  </div>
</section>

<div class="divider"></div>

<!-- GUION -->
<section class="sec" id="guion">
  <div class="sec-label">Sustentación</div>
  <h2 class="sec-title">Guión · 15 Minutos</h2>
  <p class="sec-desc">
    Distribución de tiempo entre los 4 integrantes. Orden aleatorio:
    <strong>Mateo → Fong → Jhonatan → Nicolás</strong>.
    Cada sección incluye puntos clave y frases de transición.
  </p>

  <div class="tl">
    <div class="tli">
      <div class="tldot"></div>
      <div class="tlt">0:00 – 3:30 min</div>
      <div class="tls">Mateo González — Apertura + Entradas y Salidas</div>
      <div class="tlc">
        <div class="tltp">Presentar el equipo e introducir el proyecto. Explicar el flujo completo de datos.</div>
        <ul class="tlul">
          <li>E2D2 no genera token por token — usa difusión discreta en bloques paralelos</li>
          <li>Tokenización: texto alemán → IDs con BOS/EOS → tensor [1, L_src]</li>
          <li>Parámetros de control: block_size=4, num_steps=4, max_new_tokens=64–96</li>
          <li>Salida: output_ids decodificados → texto en inglés + métricas (tiempo, tokens/s)</li>
          <li>Diferencia con OPUS-MT: tokens uno a uno vs. bloques completos en paralelo</li>
        </ul>
        <div style="margin-top:.8rem;font-size:.8rem;background:rgba(0,212,255,.07);border-radius:8px;padding:.6rem .9rem;color:var(--cyan)">
          → Transición: <em>"Le doy la palabra a Fong para explicar cómo ocurre esa comunicación interna."</em>
        </div>
      </div>
    </div>

    <div class="tli">
      <div class="tldot"></div>
      <div class="tlt">3:30 – 6:30 min</div>
      <div class="tls">Jorge Luis Fong — Q, K y V en Cross-Attention</div>
      <div class="tlc">
        <div class="tltp">De la analogía YouTube a la implementación en E2D2.</div>
        <ul class="tlul">
          <li>Analogía: Q = tu búsqueda, K = títulos de videos, V = contenido real</li>
          <li>Q del decoder: tokens ruidosos [batch, block_size, d_model] — incertidumbre buscando certeza</li>
          <li>K del encoder: "de qué habla este token alemán" — 28 capas bidireccionales</li>
          <li>V del encoder: la información semántica real que se transfiere</li>
          <li>Fórmula: CrossAttn(Q,K,V) = softmax(QKᵀ/√d_k) × V</li>
          <li>tie_encoder_decoder_weights=False (confirmado celda 29 del notebook)</li>
        </ul>
        <div style="margin-top:.8rem;font-size:.8rem;background:rgba(0,212,255,.07);border-radius:8px;padding:.6rem .9rem;color:var(--cyan)">
          → Transición: <em>"Le paso la palabra a Jhonatan para explicar el proceso de difusión."</em>
        </div>
      </div>
    </div>

    <div class="tli">
      <div class="tldot"></div>
      <div class="tlt">6:30 – 9:30 min</div>
      <div class="tls">Jhonatan Rengifo — Difusión Discreta + Block Diffusion</div>
      <div class="tlc">
        <div class="tltp">Del ruido gaussiano en imágenes al enmascaramiento en lenguaje.</div>
        <ul class="tlul">
          <li>Stable Diffusion / DALL-E 2: ruido gaussiano → imagen. Los tokens son discretos → [MASK]</li>
          <li>Absorbing diffusion: token "destruido" = [MASK]. Denoising = recuperar el token real</li>
          <li>Block Diffusion: [MASK]×4 → cross-attention + self-attention × num_steps → tokens fijos</li>
          <li>4 diferencias vs. GPT: token [MASK], máscara block-causal, revisión de decisiones, asimetría encoder/decoder</li>
          <li>use_cache=True: encoder se ejecuta una sola vez; K y V se reutilizan en todos los bloques</li>
        </ul>
        <div style="margin-top:.8rem;font-size:.8rem;background:rgba(0,212,255,.07);border-radius:8px;padding:.6rem .9rem;color:var(--cyan)">
          → Transición: <em>"Le doy la palabra a Nicolás para cerrar con los resultados experimentales."</em>
        </div>
      </div>
    </div>

    <div class="tli">
      <div class="tldot p"></div>
      <div class="tlt p">9:30 – 15:00 min</div>
      <div class="tls">Nicolás Vásquez — Resultados, Comparación y Cierre</div>
      <div class="tlc">
        <div class="tltp">Ablation study, comparación con OPUS-MT, errores cualitativos y reflexión final.</div>
        <ul class="tlul">
          <li>Config oficial b4/s4 cache: BLEU 17.85, chrF 50.00 en 30 ejemplos</li>
          <li>b2 colapsa (BLEU ≈ 0), b8 degrada calidad (rep. bigramas 24%)</li>
          <li>OPUS-MT supera en BLEU y velocidad — esperado, comparación ilustrativa no competitiva</li>
          <li>Errores cualitativos: repetición de contenido y salidas cortas — límites teóricos del paradigma</li>
          <li>Cierre: E2D2 combina encoder bidireccional + cross-attention + difusión discreta en un trade-off configurable</li>
        </ul>
        <div style="margin-top:.8rem;font-size:.8rem;background:rgba(139,92,246,.07);border-radius:8px;padding:.6rem .9rem;color:var(--purple)">
          → Cierre: <em>"Quedamos disponibles para preguntas. Gracias."</em>
        </div>
      </div>
    </div>
  </div>

  <h3 style="margin:2.5rem 0 .5rem;font-size:1.05rem;font-weight:700">Posibles Preguntas del Profesor</h3>
  <div class="faq-grid">
    <div class="faq">
      <div class="faq-h" onclick="toggleFaq(this)">
        <span>¿Por qué 28 capas encoder y solo 4 decoder?</span>
        <span class="faq-toggle">+</span>
      </div>
      <div class="faq-body">
        El paper argumenta que la mayor capacidad debe concentrarse en <em>entender</em> la entrada.
        El decoder solo necesita refinar, no comprender. Y como el decoder se ejecuta
        <code>num_steps × n_bloques</code> veces, un decoder liviano es esencial
        para que la inferencia sea computacionalmente viable.
      </div>
    </div>
    <div class="faq">
      <div class="faq-h" onclick="toggleFaq(this)">
        <span>¿Por qué falló use_cache=False?</span>
        <span class="faq-toggle">+</span>
      </div>
      <div class="faq-body">
        El cache del encoder no es solo optimización. Las dimensiones que espera el decoder
        asumen que las representaciones del encoder están pre-computadas en una forma específica.
        Sin cache, el modelo falla con <code>RuntimeError: tensor a (4) != tensor b (0)</code>
        — es un error estructural del checkpoint, no de configuración.
      </div>
    </div>
    <div class="faq">
      <div class="faq-h" onclick="toggleFaq(this)">
        <span>¿Cómo se entrena E2D2?</span>
        <span class="faq-toggle">+</span>
      </div>
      <div class="faq-body">
        Con cross-entropy sobre todos los tokens enmascarados en todos los pasos de difusión.
        No hay vanishing gradient porque el error se propaga desde posiciones [MASK] específicas.
        El loss promedia sobre todos los pasos del proceso de denoising, no solo el último.
      </div>
    </div>
    <div class="faq">
      <div class="faq-h" onclick="toggleFaq(this)">
        <span>¿Por qué OPUS-MT gana en BLEU?</span>
        <span class="faq-toggle">+</span>
      </div>
      <div class="faq-body">
        OPUS-MT está especializado en De→En y es más liviano. El checkpoint E2D2 fue entrenado
        en condiciones diferentes. La comparación es <strong>ilustrativa, no competitiva</strong>:
        el objetivo fue analizar el paradigma de difusión discreta, no ganar un benchmark.
      </div>
    </div>
    <div class="faq">
      <div class="faq-h" onclick="toggleFaq(this)">
        <span>¿Qué es absorbing diffusion?</span>
        <span class="faq-toggle">+</span>
      </div>
      <div class="faq-body">
        El mecanismo de corrupción durante el entrenamiento. Algunos tokens se reemplazan por [MASK].
        El modelo aprende a recuperarlos. Es la versión discreta del ruido gaussiano de los modelos
        de difusión de imágenes — el token queda "absorbido" en el estado [MASK].
      </div>
    </div>
    <div class="faq">
      <div class="faq-h" onclick="toggleFaq(this)">
        <span>¿Qué significa tie_encoder_decoder_weights=False?</span>
        <span class="faq-toggle">+</span>
      </div>
      <div class="faq-body">
        Los pesos del encoder y del decoder son completamente independientes.
        No se comparten parámetros entre las dos mitades del modelo.
        Confirmado por inspección directa del checkpoint en la <strong>celda 29</strong> del notebook.
      </div>
    </div>
  </div>
</section>

<div class="divider"></div>

<!-- FOOTER -->
<footer>
  <div class="ft">E2D2 — Encoder-Decoder Diffusion Language Models</div>
  <div style="margin-bottom:.4rem;color:var(--t2)">Especialización en Ciencia de Datos · 2026-01 · Computación en la Nube</div>
  <div style="margin-bottom:1rem">Mateo González · Jorge Luis Fong · Jhonatan Rengifo · Nicolás Vásquez</div>
  <div class="links-row">
    <a href="https://arxiv.org/abs/2510.22852" target="_blank">📄 Paper arXiv:2510.22852</a>
    <a href="https://github.com/kuleshov-group/e2d2" target="_blank">💻 Código Oficial</a>
    <a href="https://huggingface.co/kuleshov-group/e2d2-wmt" target="_blank">🤗 Checkpoint HuggingFace</a>
  </div>
  <div style="font-size:.75rem;color:var(--t3)">
    Checkpoint: <code>kuleshov-group/e2d2-wmt @ 7c65309</code> · Dataset: WMT14 de-en
  </div>
</footer>

<script>
// ---- TABS ----
function tab(e, id) {
  document.querySelectorAll('.tbtn').forEach(b => b.classList.remove('on'));
  document.querySelectorAll('.tpanel').forEach(p => p.classList.remove('on'));
  e.target.classList.add('on');
  document.getElementById(id).classList.add('on');
}

// ---- DIFFUSION STEPPER ----
var steps = [
  {
    toks: ['[MASK]','[MASK]','[MASK]','[MASK]'],
    st:   ['masked','masked','masked','masked'],
    desc: 'Estado inicial: los 4 tokens del bloque están completamente enmascarados. El decoder aplica cross-attention con el encoder en cada iteración.',
    ctr:  'Inicio · Paso 0 de 4'
  },
  {
    toks: ['Good','[MASK]','[MASK]','[MASK]'],
    st:   ['revealed','masked','masked','masked'],
    desc: 'Paso 1 — Cross-attention con el encoder: el token más probable emerge. "Good" tiene la mayor probabilidad dada la Query del primer [MASK].',
    ctr:  'Paso 1 de 4'
  },
  {
    toks: ['Good','morning','[MASK]','[MASK]'],
    st:   ['revealed','revealed','masked','masked'],
    desc: 'Paso 2 — Con "Good" como contexto intra-bloque (self-attention), el segundo token se refina: "morning" es altamente probable.',
    ctr:  'Paso 2 de 4'
  },
  {
    toks: ['Good','morning',',','[MASK]'],
    st:   ['revealed','revealed','revealed','masked'],
    desc: 'Paso 3 — La coma se confirma. El self-attention intra-bloque refuerza la coherencia entre los 4 tokens del bloque.',
    ctr:  'Paso 3 de 4'
  },
  {
    toks: ['Good','morning',',','how'],
    st:   ['revealed','revealed','revealed','revealed'],
    desc: '✓ Bloque completo — los 4 tokens quedan fijados. El decoder avanza al siguiente bloque, que comienza de nuevo con [MASK][MASK][MASK][MASK].',
    ctr:  'Paso 4 de 4 — ¡Bloque fijado!'
  }
];

function setStep(i) {
  var s = steps[i];
  ['bt0','bt1','bt2','bt3'].forEach(function(id,j) {
    var el = document.getElementById(id);
    el.textContent = s.toks[j];
    el.className = 'btoken ' + s.st[j];
  });
  document.getElementById('sdesc').innerHTML = s.desc;
  document.getElementById('sctr').textContent = s.ctr;
  document.querySelectorAll('.sbtn').forEach(function(b,j) {
    b.classList.toggle('on', j === i);
  });
}

// ---- FAQ TOGGLE ----
function toggleFaq(h) {
  h.parentElement.classList.toggle('open');
}

// ---- MASK GRID ----
(function buildMaskGrid() {
  var g = document.getElementById('mask-grid');
  if (!g) return;
  // 4 rows (decoder positions in block), 8 cols (4 encoder h* + 4 decoder z*)
  var labels_col = ['h1','h2','h3','h4','z1','z2','z3','z4'];
  var rows = 4;
  for (var r = 0; r < rows; r++) {
    for (var c = 0; c < 8; c++) {
      var cell = document.createElement('div');
      cell.className = 'mg-cell';
      if (c < 4) {
        // cross-attention: all decoder rows attend to all encoder columns
        cell.classList.add('attend');
        cell.textContent = '✓';
      } else {
        // self-attention within block: all positions attend to all (non-causal within block)
        cell.classList.add('self');
        cell.textContent = '✓';
      }
      g.appendChild(cell);
    }
  }
})();

// ---- SCROLL REVEAL ----
if ('IntersectionObserver' in window) {
  var obs = new IntersectionObserver(function(entries) {
    entries.forEach(function(e) {
      if (e.isIntersecting) { e.target.classList.add('in'); }
    });
  }, { threshold: 0.08 });
  document.querySelectorAll('.reveal').forEach(function(el) { obs.observe(el); });
}
</script>
</body>
</html>
