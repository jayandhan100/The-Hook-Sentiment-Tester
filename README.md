# The-Hook-Sentiment-Tester

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>ScrollStop · AI Headline Optimizer</title>
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Mono:wght@400;500&family=Space+Grotesk:wght@400;600;700;900&display=swap" rel="stylesheet" />
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  ::selection { background: #FFE500; color: #000; }
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: #111; }
  ::-webkit-scrollbar-thumb { background: #FFE500; border-radius: 2px; }

  :root {
    --yellow: #FFE500;
    --green: #00FF87;
    --red: #FF3B3B;
    --orange: #FF8C00;
    --bg: #0a0a0a;
    --card: #111;
    --border: #1e1e1e;
    --muted: #444;
    --text: #f0f0f0;
  }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Mono', 'Courier New', monospace;
    min-height: 100vh;
    overflow-x: hidden;
    padding-bottom: 80px;
  }

  /* Noise overlay */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    pointer-events: none;
    opacity: .03;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
    z-index: 999;
  }

  /* Header */
  header {
    border-bottom: 1px solid #1a1a1a;
    padding: 20px 40px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .logo-wrap { display: flex; align-items: baseline; gap: 14px; }
  .logo { font-family: 'Bebas Neue', sans-serif; font-size: 32px; color: var(--yellow); letter-spacing: .1em; }
  .logo-sub { font-size: 10px; color: #444; letter-spacing: .2em; }
  .version { font-size: 10px; color: #333; letter-spacing: .15em; }

  /* Main container */
  .container { max-width: 760px; margin: 0 auto; padding: 40px 24px 0; }

  /* Hero */
  .hero { margin-bottom: 40px; }
  .hero h1 {
    font-family: 'Bebas Neue', sans-serif;
    font-size: clamp(40px, 8vw, 76px);
    line-height: .93;
    letter-spacing: .02em;
    color: var(--text);
  }
  .hero h1 span { color: var(--yellow); }
  .hero-sub { margin-top: 16px; font-size: 11px; color: #444; letter-spacing: .12em; }

  /* Platform */
  .section-label { font-size: 10px; color: var(--muted); letter-spacing: .18em; margin-bottom: 10px; text-transform: uppercase; }
  .platforms { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 20px; }
  .platform-btn {
    background: transparent;
    border: 1px solid #222;
    color: #555;
    cursor: pointer;
    padding: 8px 14px;
    font-family: 'DM Mono', monospace;
    font-size: 11px;
    letter-spacing: .1em;
    transition: all .2s;
    border-radius: 3px;
  }
  .platform-btn:hover { border-color: var(--yellow); color: var(--yellow); }
  .platform-btn.active { background: var(--yellow); color: #000; border-color: var(--yellow); font-weight: 700; }

  /* Textarea */
  .input-wrap { position: relative; margin-bottom: 20px; }
  .input-bar { position: absolute; top: 0; left: 0; right: 0; height: 2px; background: #222; transition: background .3s; border-radius: 2px 2px 0 0; }
  .input-bar.active { background: var(--yellow); }
  textarea {
    width: 100%;
    background: var(--card);
    border: 1px solid var(--border);
    border-top: none;
    color: var(--text);
    padding: 18px;
    font-size: 15px;
    line-height: 1.6;
    resize: vertical;
    font-family: 'Space Grotesk', sans-serif;
    border-radius: 0 0 3px 3px;
    min-height: 120px;
  }
  textarea:focus { outline: none; }
  textarea::placeholder { color: #333; }
  .char-count { position: absolute; bottom: 12px; right: 14px; font-size: 10px; color: #333; transition: color .2s; }
  .char-count.warn { color: var(--red); }

  /* CTA row */
  .cta-row { display: flex; align-items: center; gap: 20px; margin-bottom: 50px; flex-wrap: wrap; }
  .analyze-btn {
    background: var(--yellow);
    color: #000;
    border: none;
    cursor: pointer;
    padding: 14px 36px;
    font-family: 'Bebas Neue', sans-serif;
    font-size: 22px;
    letter-spacing: .15em;
    transition: all .15s;
    clip-path: polygon(0 0, calc(100% - 10px) 0, 100% 10px, 100% 100%, 10px 100%, 0 calc(100% - 10px));
  }
  .analyze-btn:hover:not(:disabled) { background: #fff; transform: translateY(-2px); box-shadow: 0 8px 30px rgba(255,229,0,.3); }
  .analyze-btn:disabled { opacity: .4; cursor: not-allowed; transform: none; }

  /* Pulse dots */
  .pulse-dots { display: flex; gap: 6px; align-items: center; }
  .dot {
    width: 8px; height: 8px; border-radius: 50%; background: var(--yellow);
    animation: pulse 1.2s ease-in-out infinite;
  }
  .dot:nth-child(2) { animation-delay: .2s; }
  .dot:nth-child(3) { animation-delay: .4s; }
  @keyframes pulse { 0%,100%{opacity:.2;transform:scale(.8)} 50%{opacity:1;transform:scale(1.2)} }

  .error-msg { font-size: 12px; color: var(--red); }

  /* Results */
  #results { display: none; }
  #results.visible { display: block; animation: slideUp .4s ease forwards; }
  @keyframes slideUp { from{opacity:0;transform:translateY(20px)} to{opacity:1;transform:translateY(0)} }

  /* Score banner */
  .score-banner {
    padding: 24px 28px;
    margin-bottom: 24px;
    border-radius: 0 3px 3px 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 20px;
    background: var(--card);
    border: 1px solid transparent;
    border-left-width: 4px;
  }
  .score-label-small { font-size: 10px; color: var(--muted); letter-spacing: .2em; margin-bottom: 6px; }
  .score-big { font-family: 'Bebas Neue', sans-serif; font-size: 72px; line-height: 1; }
  .score-big span { font-size: 32px; }
  .score-verdict-label { font-family: 'Bebas Neue', sans-serif; font-size: 20px; letter-spacing: .1em; margin-top: 4px; }
  .verdict-box {
    background: var(--bg);
    padding: 12px 20px;
    border-radius: 3px;
    border: 1px solid #1a1a1a;
  }
  .verdict-title { font-size: 10px; color: var(--muted); letter-spacing: .1em; margin-bottom: 4px; }
  .verdict-text { font-family: 'Bebas Neue', sans-serif; font-size: 24px; letter-spacing: .05em; }

  /* Rings */
  .rings-row {
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
    gap: 16px;
    background: var(--card);
    border: 1px solid #1a1a1a;
    border-radius: 3px;
    padding: 28px 20px;
    margin-bottom: 24px;
  }
  .ring-wrap { display: flex; flex-direction: column; align-items: center; gap: 8px; }
  .ring-label { font-size: 10px; color: #666; letter-spacing: .1em; text-transform: uppercase; text-align: center; max-width: 90px; }
  .ring-text { font-family: 'DM Mono', monospace; font-size: 16px; font-weight: 900; }

  /* Two col */
  .two-col { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 24px; }
  @media(max-width:480px) { .two-col { grid-template-columns: 1fr; } }
  .feedback-card { border-radius: 3px; padding: 16px 18px; }
  .feedback-card.good { background: #00FF8708; border: 1px solid #00FF8722; }
  .feedback-card.bad { background: #FF3B3B08; border: 1px solid #FF3B3B22; }
  .feedback-card-label { font-size: 10px; letter-spacing: .15em; margin-bottom: 8px; }
  .feedback-card.good .feedback-card-label { color: var(--green); }
  .feedback-card.bad .feedback-card-label { color: #FF6B6B; }
  .feedback-card p { font-size: 13px; color: #aaa; line-height: 1.6; }

  /* Tags */
  .tags-row { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 24px; }
  @media(max-width:480px) { .tags-row { grid-template-columns: 1fr; } }
  .tag {
    display: inline-block;
    background: #1a1a1a;
    border: 1px solid #333;
    padding: 3px 10px;
    font-size: 10px;
    letter-spacing: .1em;
    margin: 3px;
    border-radius: 2px;
    color: #888;
  }
  .tag.trigger { border-color: rgba(0,255,135,.25); color: var(--green); background: rgba(0,255,135,.06); }
  .tag.missing { border-color: rgba(255,59,59,.25); color: #FF6B6B; background: rgba(255,59,59,.06); }

  /* Rewrites */
  .rewrite-card {
    background: var(--card);
    border: 1px solid #222;
    border-left: 3px solid var(--yellow);
    padding: 12px 16px;
    margin-bottom: 8px;
    border-radius: 3px;
    font-size: 13px;
    line-height: 1.6;
    color: #ccc;
    cursor: pointer;
    transition: all .2s;
    font-family: 'Space Grotesk', sans-serif;
  }
  .rewrite-card:hover { border-color: var(--yellow); color: #fff; background: #161616; }
  .rewrite-num { color: var(--yellow); font-weight: 900; margin-right: 8px; font-family: 'DM Mono', monospace; }

  /* Footer note */
  .footer-note {
    margin-top: 32px;
    padding-top: 20px;
    border-top: 1px solid #1a1a1a;
    font-size: 10px;
    color: #333;
    letter-spacing: .1em;
    text-align: center;
  }

  /* Empty state */
  #empty-state { text-align: center; padding: 40px 0; opacity: .3; }
  #empty-state h2 { font-family: 'Bebas Neue', sans-serif; font-size: 48px; letter-spacing: .1em; color: #444; }
  #empty-state p { font-size: 11px; color: #333; letter-spacing: .15em; margin-top: 8px; }
</style>
</head>
<body>

<header>
  <div class="logo-wrap">
    <span class="logo">SCROLLSTOP</span>
    <span class="logo-sub">AI HEADLINE OPTIMIZER</span>
  </div>
  <span class="version">v2.0 · BETA</span>
</header>

<div class="container">

  <!-- Hero -->
  <div class="hero">
    <h1>WILL THEY<br><span>STOP SCROLLING</span><br>OR KEEP GOING?</h1>
    <p class="hero-sub">PASTE YOUR HOOK · GET A BRUTAL SCORE · STEAL THE REWRITES</p>
  </div>

  <!-- Platform -->
  <div class="section-label">TARGET PLATFORM</div>
  <div class="platforms" id="platforms">
    <button class="platform-btn active" data-id="tiktok">♪ TikTok</button>
    <button class="platform-btn" data-id="instagram">◈ Instagram</button>
    <button class="platform-btn" data-id="twitter">✦ X / Twitter</button>
    <button class="platform-btn" data-id="linkedin">in LinkedIn</button>
    <button class="platform-btn" data-id="youtube">▶ YouTube</button>
  </div>

  <!-- Input -->
  <div class="input-wrap">
    <div class="input-bar" id="inputBar"></div>
    <textarea id="hookInput" placeholder="Drop your hook here... 'I quit my 6-figure job and here's what nobody tells you'" oninput="handleInput(this)"></textarea>
    <span class="char-count" id="charCount">0 chars</span>
  </div>

  <!-- CTA -->
  <div class="cta-row">
    <button class="analyze-btn" id="analyzeBtn" onclick="analyze()" disabled>RATE MY HOOK →</button>
    <div class="pulse-dots" id="pulseDots" style="display:none">
      <div class="dot"></div><div class="dot"></div><div class="dot"></div>
    </div>
    <span class="error-msg" id="errorMsg"></span>
  </div>

  <!-- Empty state -->
  <div id="empty-state">
    <h2>WAITING FOR YOUR HOOK</h2>
    <p>THE ALGORITHM DOESN'T CARE HOW HARD YOU WORKED</p>
  </div>

  <!-- Results -->
  <div id="results">

    <!-- Score banner -->
    <div class="score-banner" id="scoreBanner">
      <div>
        <div class="score-label-small">OVERALL ENGAGEMENT SCORE</div>
        <div class="score-big" id="scoreBig">0<span>/100</span></div>
        <div class="score-verdict-label" id="scoreVerdictLabel"></div>
      </div>
      <div class="verdict-box">
        <div class="verdict-title">VERDICT</div>
        <div class="verdict-text" id="verdictText"></div>
      </div>
    </div>

    <!-- Rings -->
    <div class="rings-row" id="ringsRow">
      <div class="ring-wrap">
        <svg width="90" height="90"><circle cx="45" cy="45" r="36" fill="none" stroke="#1a1a1a" stroke-width="6"/><circle id="ring-hookStrength" cx="45" cy="45" r="36" fill="none" stroke="#FFE500" stroke-width="6" stroke-linecap="round" stroke-dasharray="0 226.19" transform="rotate(-90 45 45)" style="transition:stroke-dasharray .05s linear"/><text x="45" y="45" text-anchor="middle" dominant-baseline="central" fill="#FFE500" font-size="16" font-weight="900" font-family="'DM Mono',monospace" id="ring-hookStrength-text">0</text></svg>
        <span class="ring-label">Hook Strength</span>
      </div>
      <div class="ring-wrap">
        <svg width="90" height="90"><circle cx="45" cy="45" r="36" fill="none" stroke="#1a1a1a" stroke-width="6"/><circle id="ring-curiosityGap" cx="45" cy="45" r="36" fill="none" stroke="#FFE500" stroke-width="6" stroke-linecap="round" stroke-dasharray="0 226.19" transform="rotate(-90 45 45)" style="transition:stroke-dasharray .05s linear"/><text x="45" y="45" text-anchor="middle" dominant-baseline="central" fill="#FFE500" font-size="16" font-weight="900" font-family="'DM Mono',monospace" id="ring-curiosityGap-text">0</text></svg>
        <span class="ring-label">Curiosity Gap</span>
      </div>
      <div class="ring-wrap">
        <svg width="90" height="90"><circle cx="45" cy="45" r="36" fill="none" stroke="#1a1a1a" stroke-width="6"/><circle id="ring-emotionalPull" cx="45" cy="45" r="36" fill="none" stroke="#FFE500" stroke-width="6" stroke-linecap="round" stroke-dasharray="0 226.19" transform="rotate(-90 45 45)" style="transition:stroke-dasharray .05s linear"/><text x="45" y="45" text-anchor="middle" dominant-baseline="central" fill="#FFE500" font-size="16" font-weight="900" font-family="'DM Mono',monospace" id="ring-emotionalPull-text">0</text></svg>
        <span class="ring-label">Emotional Pull</span>
      </div>
      <div class="ring-wrap">
        <svg width="90" height="90"><circle cx="45" cy="45" r="36" fill="none" stroke="#1a1a1a" stroke-width="6"/><circle id="ring-ctr" cx="45" cy="45" r="36" fill="none" stroke="#FFE500" stroke-width="6" stroke-linecap="round" stroke-dasharray="0 226.19" transform="rotate(-90 45 45)" style="transition:stroke-dasharray .05s linear"/><text x="45" y="45" text-anchor="middle" dominant-baseline="central" fill="#FFE500" font-size="16" font-weight="900" font-family="'DM Mono',monospace" id="ring-ctr-text">0</text></svg>
        <span class="ring-label">CTR Potential</span>
      </div>
    </div>

    <!-- What works / fails -->
    <div class="two-col">
      <div class="feedback-card good">
        <div class="feedback-card-label">✓ WHAT WORKS</div>
        <p id="whatWorks"></p>
      </div>
      <div class="feedback-card bad">
        <div class="feedback-card-label">✗ WHAT FAILS</div>
        <p id="whatFails"></p>
      </div>
    </div>

    <!-- Tags -->
    <div class="tags-row">
      <div>
        <div class="section-label">POWER WORDS DETECTED</div>
        <div id="triggerTags"></div>
      </div>
      <div>
        <div class="section-label">MISSING ELEMENTS</div>
        <div id="missingTags"></div>
      </div>
    </div>

    <!-- Rewrites -->
    <div>
      <div class="section-label">AI REWRITES · CLICK TO USE</div>
      <div id="rewritesList"></div>
    </div>

    <div class="footer-note">CLICK ANY REWRITE TO LOAD IT → ANALYZE AGAIN → KEEP ITERATING</div>
  </div><!-- /results -->

</div><!-- /container -->

<script>
  let currentPlatform = 'tiktok';

  // Platform buttons
  document.getElementById('platforms').addEventListener('click', e => {
    const btn = e.target.closest('.platform-btn');
    if (!btn) return;
    document.querySelectorAll('.platform-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentPlatform = btn.dataset.id;
  });

  function handleInput(el) {
    const val = el.value;
    const count = val.length;
    const cc = document.getElementById('charCount');
    cc.textContent = count + ' chars';
    cc.classList.toggle('warn', count > 200);
    document.getElementById('inputBar').classList.toggle('active', count > 0);
    document.getElementById('analyzeBtn').disabled = !val.trim();
    if (val.trim()) document.getElementById('results').classList.remove('visible');
  }

  function scoreColor(s) {
    if (s >= 80) return '#00FF87';
    if (s >= 60) return '#FFE500';
    if (s >= 40) return '#FF8C00';
    return '#FF3B3B';
  }
  function scoreLabel(s) {
    if (s >= 80) return 'SCROLL STOPPER';
    if (s >= 60) return 'DECENT HOOK';
    if (s >= 40) return 'NEEDS WORK';
    return 'DEAD ON ARRIVAL';
  }

  function animateRing(id, targetScore, delay) {
    const CIRC = 2 * Math.PI * 36;
    const color = scoreColor(targetScore);
    const circle = document.getElementById('ring-' + id);
    const textEl = document.getElementById('ring-' + id + '-text');
    circle.setAttribute('stroke', color);
    textEl.setAttribute('fill', color);
    circle.style.filter = `drop-shadow(0 0 6px ${color})`;
    setTimeout(() => {
      let start = null;
      function step(ts) {
        if (!start) start = ts;
        const prog = Math.min((ts - start) / 700, 1);
        const val = Math.round(prog * targetScore);
        const dash = (val / 100) * CIRC;
        circle.setAttribute('stroke-dasharray', `${dash} ${CIRC}`);
        textEl.textContent = val;
        if (prog < 1) requestAnimationFrame(step);
      }
      requestAnimationFrame(step);
    }, delay);
  }

  function animateScore(targetScore) {
    const color = scoreColor(targetScore);
    const el = document.getElementById('scoreBig');
    const label = document.getElementById('scoreVerdictLabel');
    label.style.color = color;
    el.style.color = color;
    el.style.filter = `drop-shadow(0 0 20px ${color}88)`;
    let start = null;
    function step(ts) {
      if (!start) start = ts;
      const prog = Math.min((ts - start) / 800, 1);
      const val = Math.round(prog * targetScore);
      el.innerHTML = val + '<span>/100</span>';
      if (prog < 1) requestAnimationFrame(step);
      else label.textContent = scoreLabel(targetScore);
    }
    requestAnimationFrame(step);
  }

  function useRewrite(text) {
    const ta = document.getElementById('hookInput');
    ta.value = text;
    handleInput(ta);
    document.getElementById('results').classList.remove('visible');
    document.getElementById('empty-state').style.display = '';
    ta.focus();
  }

  async function analyze() {
    const hook = document.getElementById('hookInput').value.trim();
    if (!hook) return;

    document.getElementById('analyzeBtn').disabled = true;
    document.getElementById('pulseDots').style.display = 'flex';
    document.getElementById('errorMsg').textContent = '';
    document.getElementById('results').classList.remove('visible');
    document.getElementById('empty-state').style.display = 'none';

    const systemPrompt = `You are ScrollStop, an elite social media engagement analyst. Analyze hooks/headlines for scroll-stopping power.
Return ONLY valid JSON with this exact shape:
{
  "overall": <0-100 integer>,
  "scores": {
    "hookStrength": <0-100>,
    "curiosityGap": <0-100>,
    "emotionalPull": <0-100>,
    "ctr": <0-100>
  },
  "verdict": "<2-3 word brutal verdict>",
  "whatWorks": "<1 sentence max>",
  "whatFails": "<1 sentence max, or null if overall >= 75>",
  "rewrites": ["<improved version 1>", "<improved version 2>", "<improved version 3>"],
  "triggerWords": ["<word1>", "<word2>"],
  "missingElements": ["<element1>", "<element2>"]
}
Be brutally honest. Platform context: ${currentPlatform.toUpperCase()}.`;

    try {
      const res = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514',
          max_tokens: 1000,
          system: systemPrompt,
          messages: [{ role: 'user', content: `Analyze this hook for ${currentPlatform}: "${hook}"` }]
        })
      });

      const data = await res.json();
      const raw = data.content?.[0]?.text || '';
      const clean = raw.rep
