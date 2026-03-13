<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>WorthIt — AI Verdict in Seconds</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=DM+Sans:wght@300;400;500&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #080808;
    --surface: #111111;
    --surface2: #161616;
    --border: rgba(255,255,255,0.07);
    --gold: #c9a84c;
    --gold2: #e8c97a;
    --white: #f0ede8;
    --muted: #6b6860;
    --green: #4caf7d;
    --red: #e05252;
    --amber: #e8a94c;
    --glow: rgba(201,168,76,0.15);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--white);
    font-family: 'DM Sans', sans-serif;
    font-weight: 300;
    min-height: 100vh;
    overflow-x: hidden;
    cursor: none;
  }

  /* ── CUSTOM CURSOR ── */
  .cursor {
    width: 8px; height: 8px;
    background: var(--gold);
    border-radius: 50%;
    position: fixed;
    pointer-events: none;
    z-index: 9999;
    transition: transform 0.15s ease;
    mix-blend-mode: difference;
  }
  .cursor-ring {
    width: 36px; height: 36px;
    border: 1px solid rgba(201,168,76,0.4);
    border-radius: 50%;
    position: fixed;
    pointer-events: none;
    z-index: 9998;
    transition: all 0.12s ease;
    transform: translate(-14px, -14px);
  }

  /* ── BACKGROUND ── */
  .bg-grid {
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(255,255,255,0.02) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255,255,255,0.02) 1px, transparent 1px);
    background-size: 60px 60px;
    pointer-events: none;
    z-index: 0;
  }

  .bg-orb {
    position: fixed;
    border-radius: 50%;
    filter: blur(120px);
    pointer-events: none;
    z-index: 0;
    animation: orbFloat 8s ease-in-out infinite;
  }
  .bg-orb-1 {
    width: 600px; height: 600px;
    background: radial-gradient(circle, rgba(201,168,76,0.06) 0%, transparent 70%);
    top: -200px; right: -100px;
    animation-delay: 0s;
  }
  .bg-orb-2 {
    width: 400px; height: 400px;
    background: radial-gradient(circle, rgba(76,175,125,0.04) 0%, transparent 70%);
    bottom: 100px; left: -100px;
    animation-delay: 4s;
  }

  @keyframes orbFloat {
    0%, 100% { transform: translateY(0px) scale(1); }
    50% { transform: translateY(-30px) scale(1.05); }
  }

  /* ── LAYOUT ── */
  .container {
    max-width: 860px;
    margin: 0 auto;
    padding: 0 24px;
    position: relative;
    z-index: 1;
  }

  /* ── NAV ── */
  nav {
    padding: 28px 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
    z-index: 1;
  }

  .logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 22px;
    font-weight: 600;
    letter-spacing: 0.05em;
    color: var(--white);
  }
  .logo span { color: var(--gold); }

  .nav-badge {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.12em;
    color: var(--muted);
    border: 1px solid var(--border);
    padding: 5px 12px;
    border-radius: 20px;
    text-transform: uppercase;
  }

  /* ── HERO ── */
  .hero {
    padding: 80px 0 60px;
    text-align: center;
    opacity: 0;
    transform: translateY(30px);
    animation: fadeUp 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.2s forwards;
  }

  @keyframes fadeUp {
    to { opacity: 1; transform: translateY(0); }
  }

  .hero-label {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--gold);
    text-transform: uppercase;
    margin-bottom: 24px;
    display: inline-flex;
    align-items: center;
    gap: 8px;
  }
  .hero-label::before, .hero-label::after {
    content: '';
    width: 24px;
    height: 1px;
    background: var(--gold);
    opacity: 0.5;
  }

  .hero-title {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(52px, 8vw, 88px);
    font-weight: 300;
    line-height: 1.0;
    letter-spacing: -0.02em;
    margin-bottom: 20px;
    color: var(--white);
  }
  .hero-title em {
    font-style: italic;
    color: var(--gold);
  }

  .hero-sub {
    font-size: 16px;
    color: var(--muted);
    font-weight: 300;
    max-width: 420px;
    margin: 0 auto 48px;
    line-height: 1.7;
  }

  /* ── SEARCH BOX ── */
  .search-wrap {
    position: relative;
    max-width: 620px;
    margin: 0 auto;
    opacity: 0;
    transform: translateY(20px);
    animation: fadeUp 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.5s forwards;
  }

  .search-box {
    width: 100%;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 20px 160px 20px 24px;
    font-family: 'DM Sans', sans-serif;
    font-size: 16px;
    font-weight: 300;
    color: var(--white);
    outline: none;
    transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
    caret-color: var(--gold);
  }

  .search-box::placeholder { color: var(--muted); }

  .search-box:focus {
    border-color: rgba(201,168,76,0.4);
    background: var(--surface2);
    box-shadow: 0 0 0 4px var(--glow), 0 20px 60px rgba(0,0,0,0.4);
  }

  .search-btn {
    position: absolute;
    right: 8px;
    top: 50%;
    transform: translateY(-50%);
    background: var(--gold);
    color: #080808;
    border: none;
    border-radius: 10px;
    padding: 12px 24px;
    font-family: 'DM Sans', sans-serif;
    font-size: 13px;
    font-weight: 500;
    letter-spacing: 0.04em;
    cursor: none;
    transition: all 0.25s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .search-btn:hover {
    background: var(--gold2);
    transform: translateY(-50%) scale(1.03);
    box-shadow: 0 8px 24px rgba(201,168,76,0.3);
  }

  .search-btn:active { transform: translateY(-50%) scale(0.98); }

  /* ── EXAMPLES ── */
  .examples {
    display: flex;
    gap: 8px;
    justify-content: center;
    flex-wrap: wrap;
    margin-top: 16px;
    opacity: 0;
    animation: fadeUp 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.7s forwards;
  }

  .example-pill {
    font-size: 12px;
    color: var(--muted);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 6px 14px;
    cursor: none;
    transition: all 0.2s ease;
    font-weight: 300;
  }

  .example-pill:hover {
    color: var(--gold);
    border-color: rgba(201,168,76,0.3);
    background: var(--glow);
  }

  /* ── DIVIDER ── */
  .divider {
    height: 1px;
    background: var(--border);
    margin: 60px 0;
    position: relative;
  }
  .divider::after {
    content: '◆';
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    background: var(--bg);
    color: var(--gold);
    font-size: 10px;
    padding: 0 16px;
  }

  /* ── VERDICT CARD ── */
  .verdict-section {
    display: none;
    opacity: 0;
    transform: translateY(24px);
    transition: all 0.6s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .verdict-section.visible {
    display: block;
    opacity: 1;
    transform: translateY(0);
  }

  .verdict-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    overflow: hidden;
    position: relative;
  }

  .verdict-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 1px;
    background: linear-gradient(90deg, transparent, var(--gold), transparent);
    opacity: 0.5;
  }

  .verdict-header {
    padding: 32px 36px 24px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 20px;
  }

  .verdict-query {
    font-family: 'Cormorant Garamond', serif;
    font-size: 26px;
    font-weight: 400;
    font-style: italic;
    color: var(--white);
    line-height: 1.3;
  }

  .verdict-badge {
    flex-shrink: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
  }

  .verdict-score {
    font-family: 'Cormorant Garamond', serif;
    font-size: 48px;
    font-weight: 600;
    line-height: 1;
  }
  .verdict-score.high { color: var(--green); }
  .verdict-score.mid { color: var(--amber); }
  .verdict-score.low { color: var(--red); }

  .verdict-score-label {
    font-family: 'DM Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.1em;
    color: var(--muted);
    text-transform: uppercase;
  }

  .verdict-body { padding: 28px 36px; }

  .verdict-summary {
    font-size: 15px;
    color: rgba(240,237,232,0.75);
    line-height: 1.75;
    margin-bottom: 28px;
    font-weight: 300;
  }

  .verdict-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;
    margin-bottom: 28px;
  }

  .verdict-item {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 16px 18px;
    transition: border-color 0.2s ease;
  }

  .verdict-item:hover { border-color: rgba(201,168,76,0.2); }

  .verdict-item-label {
    font-family: 'DM Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.12em;
    color: var(--muted);
    text-transform: uppercase;
    margin-bottom: 6px;
  }

  .verdict-item-value {
    font-size: 14px;
    color: var(--white);
    font-weight: 400;
    line-height: 1.5;
  }

  .alternatives-section { margin-bottom: 28px; }

  .alternatives-title {
    font-family: 'DM Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.12em;
    color: var(--muted);
    text-transform: uppercase;
    margin-bottom: 12px;
  }

  .alt-list { display: flex; flex-direction: column; gap: 8px; }

  .alt-item {
    display: flex;
    align-items: center;
    gap: 12px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 12px 16px;
    transition: all 0.2s ease;
    cursor: none;
  }

  .alt-item:hover {
    border-color: rgba(201,168,76,0.25);
    transform: translateX(4px);
  }

  .alt-dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--gold);
    flex-shrink: 0;
    opacity: 0.6;
  }

  .alt-name { font-size: 13px; color: var(--white); flex: 1; }
  .alt-price { font-family: 'DM Mono', monospace; font-size: 11px; color: var(--gold); }

  .verdict-footer {
    padding: 20px 36px;
    border-top: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
  }

  .verdict-timing {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 0.08em;
  }

  .buy-btn {
    background: transparent;
    border: 1px solid var(--gold);
    color: var(--gold);
    border-radius: 8px;
    padding: 10px 20px;
    font-family: 'DM Sans', sans-serif;
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.05em;
    cursor: none;
    transition: all 0.25s ease;
    text-decoration: none;
    display: inline-block;
  }

  .buy-btn:hover {
    background: var(--gold);
    color: #080808;
    box-shadow: 0 4px 20px rgba(201,168,76,0.25);
  }

  /* ── LOADING STATE ── */
  .loading-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 48px;
    text-align: center;
    display: none;
  }

  .loading-card.visible { display: block; }

  .loading-orb {
    width: 56px; height: 56px;
    border: 1px solid rgba(201,168,76,0.3);
    border-top-color: var(--gold);
    border-radius: 50%;
    margin: 0 auto 20px;
    animation: spin 1s linear infinite;
  }

  @keyframes spin { to { transform: rotate(360deg); } }

  .loading-text {
    font-family: 'Cormorant Garamond', serif;
    font-size: 20px;
    font-style: italic;
    color: var(--muted);
  }

  .loading-steps {
    margin-top: 16px;
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .loading-step {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.08em;
    color: var(--muted);
    opacity: 0;
    animation: stepFade 0.4s ease forwards;
  }

  @keyframes stepFade { to { opacity: 0.6; } }

  /* ── HOW IT WORKS ── */
  .how-section {
    padding: 20px 0 80px;
    opacity: 0;
    transform: translateY(30px);
    animation: fadeUp 0.9s cubic-bezier(0.16, 1, 0.3, 1) 0.9s forwards;
  }

  .section-label {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--gold);
    text-transform: uppercase;
    text-align: center;
    margin-bottom: 40px;
  }

  .steps-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
  }

  .step-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 28px 24px;
    transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
    position: relative;
    overflow: hidden;
  }

  .step-card::after {
    content: '';
    position: absolute;
    inset: 0;
    background: radial-gradient(circle at 50% 0%, var(--glow), transparent 70%);
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  .step-card:hover {
    border-color: rgba(201,168,76,0.2);
    transform: translateY(-4px);
    box-shadow: 0 20px 40px rgba(0,0,0,0.3);
  }

  .step-card:hover::after { opacity: 1; }

  .step-num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 40px;
    font-weight: 300;
    color: rgba(201,168,76,0.15);
    line-height: 1;
    margin-bottom: 12px;
  }

  .step-title {
    font-size: 14px;
    font-weight: 500;
    color: var(--white);
    margin-bottom: 8px;
    letter-spacing: 0.02em;
  }

  .step-desc {
    font-size: 13px;
    color: var(--muted);
    line-height: 1.65;
    font-weight: 300;
  }

  /* ── STATS ── */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 16px;
    overflow: hidden;
    margin-bottom: 80px;
  }

  .stat-item {
    background: var(--surface);
    padding: 28px 24px;
    text-align: center;
    transition: background 0.2s ease;
  }

  .stat-item:hover { background: var(--surface2); }

  .stat-num {
    font-family: 'Cormorant Garamond', serif;
    font-size: 36px;
    font-weight: 600;
    color: var(--gold);
    line-height: 1;
    margin-bottom: 6px;
  }

  .stat-label {
    font-size: 12px;
    color: var(--muted);
    font-weight: 300;
    letter-spacing: 0.03em;
  }

  /* ── FOOTER ── */
  footer {
    border-top: 1px solid var(--border);
    padding: 28px 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
    z-index: 1;
  }

  .footer-logo {
    font-family: 'Cormorant Garamond', serif;
    font-size: 16px;
    font-weight: 600;
    color: var(--muted);
  }
  .footer-logo span { color: var(--gold); }

  .footer-note {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    letter-spacing: 0.06em;
  }

  /* ── AD SLOTS ── */
  .ad-slot {
    border: 1px dashed rgba(255,255,255,0.06);
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: rgba(255,255,255,0.1);
    font-family: 'DM Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin: 24px 0;
  }
  .ad-banner { height: 80px; }

  /* ── RESPONSIVE ── */
  @media (max-width: 600px) {
    .verdict-grid { grid-template-columns: 1fr; }
    .steps-grid { grid-template-columns: 1fr; }
    .stats-row { grid-template-columns: 1fr; }
    .verdict-header { flex-direction: column; }
    .hero-title { font-size: 44px; }
    .search-box { padding-right: 20px; padding-bottom: 60px; }
    .search-btn { right: 8px; bottom: 8px; top: auto; transform: none; width: calc(100% - 16px); }
  }
</style>
</head>
<body>

<div class="cursor" id="cursor"></div>
<div class="cursor-ring" id="cursorRing"></div>
<div class="bg-grid"></div>
<div class="bg-orb bg-orb-1"></div>
<div class="bg-orb bg-orb-2"></div>

<div class="container">

  <!-- NAV -->
  <nav>
    <div class="logo">Worth<span>It</span></div>
    <div class="nav-badge">AI · Free Forever</div>
  </nav>

  <!-- HERO -->
  <div class="hero">
    <div class="hero-label">Powered by AI</div>
    <h1 class="hero-title">Is it actually<br><em>worth it?</em></h1>
    <p class="hero-sub">Type anything you're about to buy or subscribe to. Get an honest AI verdict in seconds. Free, always.</p>
  </div>

  <!-- SEARCH -->
  <div class="search-wrap">
    <input
      type="text"
      class="search-box"
      id="searchInput"
      placeholder="Is Netflix worth $22/month?"
      maxlength="200"
    />
    <button class="search-btn" id="searchBtn" onclick="getVerdict()">Get Verdict →</button>
  </div>

  <div class="examples" id="examples">
    <span class="example-pill" onclick="setExample(this)">iPhone 16 Pro</span>
    <span class="example-pill" onclick="setExample(this)">Spotify Premium</span>
    <span class="example-pill" onclick="setExample(this)">Standing desk $400</span>
    <span class="example-pill" onclick="setExample(this)">MasterClass subscription</span>
    <span class="example-pill" onclick="setExample(this)">MacBook Air M3</span>
  </div>

  <!-- AD SLOT -->
  <div class="ad-slot ad-banner" style="margin-top:40px;">Advertisement</div>

  <!-- LOADING -->
  <div class="loading-card" id="loadingCard">
    <div class="loading-orb"></div>
    <div class="loading-text">Analyzing your query...</div>
    <div class="loading-steps" id="loadingSteps"></div>
  </div>

  <!-- VERDICT -->
  <div class="verdict-section" id="verdictSection">
    <div class="verdict-card">
      <div class="verdict-header">
        <div class="verdict-query" id="verdictQuery"></div>
        <div class="verdict-badge">
          <div class="verdict-score" id="verdictScore"></div>
          <div class="verdict-score-label">/ 10</div>
        </div>
      </div>
      <div class="verdict-body">
        <p class="verdict-summary" id="verdictSummary"></p>
        <div class="verdict-grid" id="verdictGrid"></div>
        <div class="alternatives-section">
          <div class="alternatives-title">Cheaper Alternatives</div>
          <div class="alt-list" id="altList"></div>
        </div>
      </div>
      <div class="verdict-footer">
        <div class="verdict-timing" id="verdictTiming"></div>
        <a href="#" class="buy-btn" id="buyBtn">Best Price →</a>
      </div>
    </div>

    <!-- AD SLOT AFTER VERDICT -->
    <div class="ad-slot ad-banner" style="margin-top:16px;">Advertisement</div>
  </div>

  <div class="divider"></div>

  <!-- STATS -->
  <div class="stats-row">
    <div class="stat-item">
      <div class="stat-num">10s</div>
      <div class="stat-label">Average verdict time</div>
    </div>
    <div class="stat-item">
      <div class="stat-num">Free</div>
      <div class="stat-label">Always, no signup needed</div>
    </div>
    <div class="stat-item">
      <div class="stat-num">AI</div>
      <div class="stat-label">Honest, unbiased analysis</div>
    </div>
  </div>

  <!-- HOW IT WORKS -->
  <div class="how-section">
    <div class="section-label">How It Works</div>
    <div class="steps-grid">
      <div class="step-card">
        <div class="step-num">01</div>
        <div class="step-title">Type anything</div>
        <div class="step-desc">A product, subscription, course, gadget — anything you're considering spending money on.</div>
      </div>
      <div class="step-card">
        <div class="step-num">02</div>
        <div class="step-title">AI analyzes it</div>
        <div class="step-desc">Our AI researches real value, pricing, alternatives, and user sentiment instantly.</div>
      </div>
      <div class="step-card">
        <div class="step-num">03</div>
        <div class="step-title">Get your verdict</div>
        <div class="step-desc">A clear score, honest summary, and cheaper alternatives — so you decide with confidence.</div>
      </div>
    </div>
  </div>

</div>

<!-- FOOTER -->
<div class="container">
  <footer>
    <div class="footer-logo">Worth<span>It</span></div>
    <div class="footer-note">Free · No signup · Powered by AI</div>
  </footer>
</div>

<script>
  // ── CURSOR ──
  const cursor = document.getElementById('cursor');
  const ring = document.getElementById('cursorRing');
  let mx = 0, my = 0, rx = 0, ry = 0;

  document.addEventListener('mousemove', e => {
    mx = e.clientX; my = e.clientY;
    cursor.style.left = mx - 4 + 'px';
    cursor.style.top = my - 4 + 'px';
  });

  function animRing() {
    rx += (mx - rx - 18) * 0.12;
    ry += (my - ry - 18) * 0.12;
    ring.style.left = rx + 'px';
    ring.style.top = ry + 'px';
    requestAnimationFrame(animRing);
  }
  animRing();

  document.querySelectorAll('button, .example-pill, .buy-btn').forEach(el => {
    el.addEventListener('mouseenter', () => {
      cursor.style.transform = 'scale(2.5)';
      ring.style.transform = 'translate(-14px,-14px) scale(1.5)';
      ring.style.borderColor = 'rgba(201,168,76,0.7)';
    });
    el.addEventListener('mouseleave', () => {
      cursor.style.transform = 'scale(1)';
      ring.style.transform = 'translate(-14px,-14px) scale(1)';
      ring.style.borderColor = 'rgba(201,168,76,0.4)';
    });
  });

  // ── EXAMPLES ──
  function setExample(el) {
    document.getElementById('searchInput').value = 'Is ' + el.textContent + ' worth it?';
    document.getElementById('searchInput').focus();
  }

  // ── ENTER KEY ──
  document.getElementById('searchInput').addEventListener('keydown', e => {
    if (e.key === 'Enter') getVerdict();
  });

  // ── VERDICT ──
  async function getVerdict() {
    const query = document.getElementById('searchInput').value.trim();
    if (!query) return;

    // Hide examples, show loading
    document.getElementById('examples').style.opacity = '0.3';
    document.getElementById('verdictSection').classList.remove('visible');
    document.getElementById('verdictSection').style.display = 'none';

    const loadingCard = document.getElementById('loadingCard');
    loadingCard.classList.add('visible');

    // Animate loading steps
    const steps = ['Researching pricing data...', 'Analyzing user sentiment...', 'Finding alternatives...', 'Generating verdict...'];
    const stepsEl = document.getElementById('loadingSteps');
    stepsEl.innerHTML = '';
    steps.forEach((s, i) => {
      const el = document.createElement('div');
      el.className = 'loading-step';
      el.textContent = s;
      el.style.animationDelay = (i * 0.5) + 's';
      stepsEl.appendChild(el);
    });

    try {
      const response = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514',
          max_tokens: 1000,
          messages: [{
            role: 'user',
            content: `You are WorthIt, an honest AI that tells people if something is worth buying or subscribing to.

The user asked: "${query}"

Respond ONLY with a valid JSON object, no markdown, no backticks, no extra text:
{
  "query": "cleaned up version of what they asked about",
  "score": 7,
  "scoreClass": "high",
  "summary": "2-3 sentence honest verdict explaining if it's worth it and why",
  "trueCost": "monthly or yearly cost breakdown",
  "buyTiming": "Buy now / Wait for sale / Avoid",
  "userSentiment": "one sentence about what real users think",
  "valueRating": "Great value / Fair value / Overpriced",
  "alternatives": [
    {"name": "Alternative 1", "price": "$X/mo"},
    {"name": "Alternative 2", "price": "$X/mo"},
    {"name": "Alternative 3", "price": "Free"}
  ],
  "buyLink": "https://www.google.com/search?q=best+price+${encodeURIComponent(query)}"
}

scoreClass must be "high" (score 7-10), "mid" (score 4-6), or "low" (score 1-3).
Be honest and direct. Don't be overly positive.`
          }]
        })
      });

      const data = await response.json();
      const text = data.content[0].text;
      const parsed = JSON.parse(text);

      loadingCard.classList.remove('visible');
      renderVerdict(parsed);

    } catch (err) {
      loadingCard.classList.remove('visible');
      renderFallback(query);
    }
  }

  function renderVerdict(d) {
    document.getElementById('verdictQuery').textContent = '"' + d.query + '"';

    const scoreEl = document.getElementById('verdictScore');
    scoreEl.textContent = d.score;
    scoreEl.className = 'verdict-score ' + d.scoreClass;

    document.getElementById('verdictSummary').textContent = d.summary;

    document.getElementById('verdictGrid').innerHTML = `
      <div class="verdict-item">
        <div class="verdict-item-label">True Cost</div>
        <div class="verdict-item-value">${d.trueCost}</div>
      </div>
      <div class="verdict-item">
        <div class="verdict-item-label">Best Time to Buy</div>
        <div class="verdict-item-value">${d.buyTiming}</div>
      </div>
      <div class="verdict-item">
        <div class="verdict-item-label">User Sentiment</div>
        <div class="verdict-item-value">${d.userSentiment}</div>
      </div>
      <div class="verdict-item">
        <div class="verdict-item-label">Value Rating</div>
        <div class="verdict-item-value">${d.valueRating}</div>
      </div>
    `;

    document.getElementById('altList').innerHTML = d.alternatives.map(a => `
      <div class="alt-item">
        <div class="alt-dot"></div>
        <div class="alt-name">${a.name}</div>
        <div class="alt-price">${a.price}</div>
      </div>
    `).join('');

    const now = new Date();
    document.getElementById('verdictTiming').textContent =
      'Verdict generated ' + now.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});

    document.getElementById('buyBtn').href = d.buyLink || '#';

    const section = document.getElementById('verdictSection');
    section.style.display = 'block';
    requestAnimationFrame(() => {
      requestAnimationFrame(() => {
        section.classList.add('visible');
        section.scrollIntoView({ behavior: 'smooth', block: 'start' });
      });
    });
  }

  function renderFallback(query) {
    renderVerdict({
      query: query,
      score: 6,
      scoreClass: 'mid',
      summary: "We couldn't fully analyze this right now, but our general advice is to compare at least 3 alternatives before buying, check recent reviews, and look for seasonal sales.",
      trueCost: "Check retailer for current pricing",
      buyTiming: "Compare first",
      userSentiment: "Mixed reviews — research before committing",
      valueRating: "Needs more research",
      alternatives: [
        { name: "Search Google for alternatives", price: "Free" },
        { name: "Check Reddit reviews", price: "Free" },
        { name: "Compare on Wirecutter", price: "Free" }
      ],
      buyLink: 'https://www.google.com/search?q=is+' + encodeURIComponent(query) + '+worth+it'
    });
  }
</script>
</body>
</html>

