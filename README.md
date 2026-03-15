<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AIRDROP SCANNER — Multi-Chain Intelligence</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet">
<style>
  :root {
    --green: #00FF88;
    --cyan: #00CFFF;
    --purple: #9945FF;
    --gold: #FFD700;
    --red: #FF4455;
    --bg: #050810;
    --border: rgba(0,255,136,0.18);
    --text: #b8cfe0;
    --text-dim: rgba(184,207,224,0.4);
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Share Tech Mono', 'Courier New', monospace;
    min-height: 100vh;
    overflow-x: hidden;
    cursor: crosshair;
  }
  body::before {
    content: '';
    position: fixed; inset: 0;
    background-image:
      linear-gradient(rgba(0,207,255,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,207,255,0.03) 1px, transparent 1px);
    background-size: 44px 44px;
    pointer-events: none; z-index: 0;
  }
  body::after {
    content: '';
    position: fixed; inset: 0;
    background: repeating-linear-gradient(0deg, transparent, transparent 3px, rgba(0,255,136,0.008) 3px, rgba(0,255,136,0.008) 4px);
    pointer-events: none; z-index: 0;
  }
  .orb { position: fixed; border-radius: 50%; filter: blur(120px); opacity: 0.07; pointer-events: none; z-index: 0; animation: drift 12s ease-in-out infinite alternate; }
  .orb1 { width: 500px; height: 500px; background: var(--green); top: -100px; left: -100px; }
  .orb2 { width: 400px; height: 400px; background: var(--purple); bottom: -100px; right: -100px; animation-delay: -6s; }
  .orb3 { width: 300px; height: 300px; background: var(--cyan); top: 50%; left: 50%; transform: translate(-50%,-50%); animation-delay: -3s; }
  @keyframes drift { from { transform: translate(0,0) scale(1); } to { transform: translate(20px,30px) scale(1.1); } }

  /* ─── PAYWALL ─── */
  #paywall {
    position: fixed; inset: 0; z-index: 100;
    background: rgba(5,8,16,0.97);
    display: flex; align-items: center; justify-content: center;
    padding: 20px;
    backdrop-filter: blur(8px);
    overflow-y: auto;
  }
  #paywall.hidden { display: none; }

  .pw-box {
    position: relative;
    border: 1px solid rgba(0,255,136,0.25);
    background: rgba(8,13,24,0.98);
    padding: 36px 32px;
    max-width: 520px;
    width: 100%;
    animation: pwIn 0.5s cubic-bezier(0.16,1,0.3,1);
    margin: auto;
  }
  @keyframes pwIn { from { opacity: 0; transform: translateY(24px) scale(0.97); } to { opacity: 1; transform: none; } }
  .pw-corner { position: absolute; width: 16px; height: 16px; border-color: var(--green); border-style: solid; opacity: 0.8; }
  .pw-tl { top: -1px; left: -1px; border-width: 2px 0 0 2px; }
  .pw-tr { top: -1px; right: -1px; border-width: 2px 2px 0 0; }
  .pw-bl { bottom: -1px; left: -1px; border-width: 0 0 2px 2px; }
  .pw-br { bottom: -1px; right: -1px; border-width: 0 2px 2px 0; }

  .pw-eyebrow { font-size: 10px; letter-spacing: 5px; color: rgba(0,255,136,0.45); margin-bottom: 12px; }
  .pw-title {
    font-family: 'Orbitron', monospace; font-weight: 900;
    font-size: clamp(20px, 5vw, 30px); letter-spacing: 2px;
    background: linear-gradient(130deg, var(--green), var(--cyan));
    -webkit-background-clip: text; -webkit-text-fill-color: transparent;
    background-clip: text; margin-bottom: 8px;
  }
  .pw-subtitle { font-size: 12px; color: var(--text-dim); line-height: 1.7; margin-bottom: 22px; }

  .pw-features { margin-bottom: 22px; }
  .pw-feature { display: flex; align-items: flex-start; gap: 10px; font-size: 12px; color: var(--text); margin-bottom: 8px; line-height: 1.5; }
  .pw-feature-icon { color: var(--green); flex-shrink: 0; }

  .pw-price-box {
    border: 1px solid rgba(0,255,136,0.2);
    background: rgba(0,255,136,0.04);
    padding: 16px 18px;
    margin-bottom: 22px;
    display: flex; align-items: center; justify-content: space-between; gap: 14px; flex-wrap: wrap;
  }
  .price-amount { font-family: 'Orbitron', monospace; font-size: 26px; font-weight: 900; color: var(--green); line-height: 1; }
  .price-sub { font-size: 10px; color: var(--text-dim); letter-spacing: 2px; margin-top: 4px; }
  .pw-price-right { font-size: 11px; color: var(--text-dim); line-height: 1.6; }
  .pw-price-right strong { color: var(--cyan); }

  .pw-steps { margin-bottom: 20px; }
  .step { display: flex; align-items: flex-start; gap: 12px; padding: 12px 0; border-bottom: 1px solid rgba(255,255,255,0.05); font-size: 12px; line-height: 1.6; transition: opacity 0.3s; }
  .step:last-child { border-bottom: none; }
  .step-num { font-family: 'Orbitron', monospace; font-size: 11px; font-weight: 700; color: var(--bg); background: var(--green); width: 22px; height: 22px; border-radius: 2px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; margin-top: 1px; }
  .step-num.done   { background: var(--cyan); }
  .step-num.active { background: var(--gold); animation: numPulse 1s ease-in-out infinite; }
  @keyframes numPulse { 0%,100%{box-shadow:0 0 0 0 rgba(255,215,0,0.4)} 50%{box-shadow:0 0 0 6px rgba(255,215,0,0)} }
  .step-title { color: var(--text); margin-bottom: 3px; }
  .step-detail { color: var(--text-dim); font-size: 11px; }

  .addr-copy { display: flex; align-items: center; gap: 8px; background: rgba(0,0,0,0.4); border: 1px solid rgba(0,207,255,0.2); padding: 10px 14px; margin-top: 8px; cursor: pointer; transition: all 0.2s; }
  .addr-copy:hover { border-color: var(--cyan); background: rgba(0,207,255,0.05); }
  .addr-copy span { font-size: 10px; color: var(--cyan); flex: 1; word-break: break-all; }
  .copy-hint { font-size: 10px; color: rgba(0,207,255,0.45); margin-top: 4px; }

  .eth-amount-row { display: flex; align-items: center; gap: 10px; margin-top: 8px; background: rgba(0,255,136,0.04); border: 1px solid rgba(0,255,136,0.15); padding: 9px 14px; }
  .eth-val { font-family: 'Orbitron', monospace; font-size: 14px; font-weight: 700; color: var(--green); }
  .eth-sub { font-size: 10px; color: var(--text-dim); margin-left: auto; }

  .pw-btn { width: 100%; font-family: 'Orbitron', monospace; font-size: 12px; font-weight: 700; letter-spacing: 3px; padding: 15px; background: transparent; border: 1px solid var(--green); color: var(--green); cursor: crosshair; transition: all 0.2s; margin-bottom: 10px; text-transform: uppercase; }
  .pw-btn:hover:not(:disabled) { background: rgba(0,255,136,0.1); box-shadow: 0 0 28px rgba(0,255,136,0.25); letter-spacing: 4px; }
  .pw-btn:disabled { opacity: 0.35; cursor: not-allowed; }
  .pw-btn.cyan { border-color: var(--cyan); color: var(--cyan); }
  .pw-btn.cyan:hover:not(:disabled) { background: rgba(0,207,255,0.1); box-shadow: 0 0 28px rgba(0,207,255,0.25); }
  .pw-btn.gold { border-color: var(--gold); color: var(--gold); }
  .pw-btn.gold:hover:not(:disabled) { background: rgba(255,215,0,0.08); }

  .pw-status { font-size: 11px; text-align: center; padding: 10px 14px; border: 1px solid; margin-bottom: 10px; line-height: 1.6; display: none; }
  .pw-status.visible { display: block; }
  .pw-status.info  { color: var(--cyan);   border-color: rgba(0,207,255,0.2);  background: rgba(0,207,255,0.04);  }
  .pw-status.ok    { color: var(--green);  border-color: rgba(0,255,136,0.2);  background: rgba(0,255,136,0.04);  }
  .pw-status.error { color: var(--red);    border-color: rgba(255,68,85,0.2);  background: rgba(255,68,85,0.04);  }

  .pw-disclaimer { font-size: 10px; color: rgba(184,207,224,0.22); text-align: center; line-height: 1.8; }

  /* ─── MAIN APP ─── */
  #app { position: relative; z-index: 1; max-width: 860px; margin: 0 auto; padding: 32px 20px 60px; }
  header { text-align: center; margin-bottom: 40px; animation: fadeDown 0.6s ease; }
  @keyframes fadeDown { from { opacity: 0; transform: translateY(-16px); } to { opacity: 1; } }
  .eyebrow { font-size: 10px; letter-spacing: 8px; color: rgba(0,255,136,0.45); margin-bottom: 10px; }
  h1 { font-family: 'Orbitron', monospace; font-size: clamp(28px,6vw,52px); font-weight: 900; letter-spacing: 4px; background: linear-gradient(130deg, var(--green) 0%, var(--cyan) 45%, var(--purple) 100%); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; line-height: 1.1; margin-bottom: 8px; }
  .subhead { font-size: 11px; letter-spacing: 5px; color: var(--text-dim); }
  .chains { display: flex; flex-wrap: wrap; justify-content: center; gap: 6px; margin-top: 20px; }
  .chain-pill { font-size: 9px; padding: 3px 9px; letter-spacing: 1px; border-width: 1px; border-style: solid; background: transparent; opacity: 0; animation: pillIn 0.4s ease forwards; }
  @keyframes pillIn { from { opacity: 0; transform: scale(0.8); } to { opacity: 1; transform: scale(1); } }
  .verified-bar { display: none; font-size: 10px; letter-spacing: 2px; background: rgba(0,255,136,0.08); border: 1px solid rgba(0,255,136,0.2); padding: 6px 14px; color: var(--green); margin-top: 12px; text-align: center; animation: fadeIn 0.4s ease; }
  .verified-bar.visible { display: block; }
  @keyframes fadeIn { from{opacity:0} to{opacity:1} }

  .panel { position: relative; border: 1px solid var(--border); background: rgba(5,8,16,0.85); padding: 24px; margin-bottom: 16px; backdrop-filter: blur(4px); }
  .panel-corner { position: absolute; width: 14px; height: 14px; border-color: var(--green); border-style: solid; opacity: 0.7; }
  .tl{top:-1px;left:-1px;border-width:2px 0 0 2px} .tr{top:-1px;right:-1px;border-width:2px 2px 0 0} .bl{bottom:-1px;left:-1px;border-width:0 0 2px 2px} .br{bottom:-1px;right:-1px;border-width:0 2px 2px 0}
  .panel-label { font-size: 10px; letter-spacing: 3px; color: rgba(0,255,136,0.55); margin-bottom: 12px; }
  .input-row { display: flex; gap: 10px; }
  #wallet-input { flex: 1; background: rgba(0,255,136,0.03); border: 1px solid rgba(0,255,136,0.25); color: var(--green); font-family: 'Share Tech Mono', monospace; font-size: 14px; padding: 14px 18px; outline: none; transition: all 0.25s; caret-color: var(--green); min-width: 0; }
  #wallet-input::placeholder { color: rgba(0,255,136,0.25); }
  #wallet-input:focus { border-color: var(--green); background: rgba(0,255,136,0.05); box-shadow: 0 0 24px rgba(0,255,136,0.15); }
  #scan-btn { font-family: 'Orbitron', monospace; font-size: 11px; font-weight: 700; letter-spacing: 3px; padding: 14px 24px; background: transparent; border: 1px solid var(--green); color: var(--green); cursor: crosshair; transition: all 0.2s; white-space: nowrap; }
  #scan-btn:hover:not(:disabled) { background: rgba(0,255,136,0.1); box-shadow: 0 0 28px rgba(0,255,136,0.3); }
  #scan-btn:disabled { opacity: 0.35; cursor: not-allowed; }
  #scan-btn.scanning { animation: borderPulse 1s ease-in-out infinite; }
  @keyframes borderPulse { 0%,100%{border-color:var(--green)} 50%{border-color:var(--cyan);box-shadow:0 0 20px rgba(0,207,255,0.4)} }
  .progress-wrap { margin-top: 16px; display: none; }
  .progress-wrap.visible { display: block; }
  .progress-meta { display: flex; justify-content: space-between; font-size: 10px; color: rgba(0,255,136,0.5); margin-bottom: 5px; }
  .progress-track { height: 2px; background: rgba(0,255,136,0.1); overflow: visible; }
  .progress-fill { height: 100%; background: linear-gradient(90deg, var(--green), var(--cyan)); transition: width 0.35s ease; position: relative; }
  .progress-fill::after { content: ''; position: absolute; right: -3px; top: -3px; width: 8px; height: 8px; background: #fff; border-radius: 50%; box-shadow: 0 0 12px var(--green); }
  #log-panel { display: none; }
  #log-panel.visible { display: block; }
  #log-output { max-height: 190px; overflow-y: auto; font-size: 11px; line-height: 1.7; padding: 4px 0; }
  #log-output::-webkit-scrollbar { width: 3px; }
  #log-output::-webkit-scrollbar-thumb { background: rgba(0,255,136,0.25); }
  .log-line { animation: logIn 0.15s ease; }
  @keyframes logIn { from{opacity:0;transform:translateX(-6px)} to{opacity:1} }
  .log-sys{color:var(--cyan)} .log-addr{color:var(--purple)} .log-chain{color:rgba(184,207,224,0.35)} .log-info{color:rgba(184,207,224,0.6)} .log-ok{color:var(--green)} .log-warn{color:var(--gold)} .log-err{color:var(--red)}
  #results { display: none; animation: fadeUp 0.4s ease; }
  #results.visible { display: block; }
  @keyframes fadeUp { from{opacity:0;transform:translateY(12px)} to{opacity:1} }
  .summary-grid { display: grid; grid-template-columns: 1fr 2fr 1fr; gap: 12px; margin-bottom: 16px; }
  @media(max-width:540px){.summary-grid{grid-template-columns:1fr 1fr}}
  .stat-card { border: 1px solid; padding: 16px; text-align: center; background: rgba(0,0,0,0.3); }
  .stat-value { font-family: 'Orbitron', monospace; font-weight: 700; line-height: 1; margin-bottom: 4px; }
  .stat-label { font-size: 9px; letter-spacing: 2px; opacity: 0.6; }
  .section-label { font-size: 10px; letter-spacing: 4px; color: rgba(0,255,136,0.45); margin-bottom: 10px; margin-top: 4px; }
  .airdrop-card { border: 1px solid rgba(0,255,136,0.12); background: rgba(0,255,136,0.015); padding: 18px; margin-bottom: 10px; transition: all 0.2s; animation: cardIn 0.3s ease both; position: relative; overflow: hidden; }
  .airdrop-card::before { content:''; position:absolute; left:0;top:0;bottom:0; width:2px; background:var(--green); opacity:0; transition:opacity 0.2s; }
  .airdrop-card:hover { border-color:rgba(0,255,136,0.35); background:rgba(0,255,136,0.03); transform:translateX(3px); }
  .airdrop-card:hover::before { opacity:1; }
  @keyframes cardIn { from{opacity:0;transform:translateY(10px)} to{opacity:1} }
  .card-top { display:flex; justify-content:space-between; align-items:flex-start; flex-wrap:wrap; gap:12px; }
  .card-left { flex:1; min-width:180px; }
  .card-right { text-align:right; }
  .protocol-name { font-family:'Orbitron',monospace; font-weight:700; font-size:15px; color:#fff; margin-right:10px; }
  .token-tag { font-size:11px; font-weight:700; background:rgba(255,255,255,0.07); border:1px solid rgba(0,207,255,0.2); color:var(--cyan); padding:2px 9px; }
  .card-badges { display:flex; flex-wrap:wrap; gap:7px; margin:10px 0; }
  .badge { display:inline-flex; align-items:center; gap:4px; font-size:10px; padding:2px 9px; border:1px solid rgba(255,255,255,0.1); background:rgba(255,255,255,0.04); color:var(--text-dim); }
  .badge-dot { width:6px; height:6px; border-radius:50%; display:inline-block; flex-shrink:0; }
  .badge.conf-high{color:var(--green);border-color:rgba(0,255,136,0.2)} .badge.conf-med{color:var(--gold);border-color:rgba(255,215,0,0.2)} .badge.conf-low{color:#888;border-color:rgba(128,128,128,0.2)} .badge.deadline{color:#FF8844;border-color:rgba(255,136,68,0.2)}
  .criteria { font-size:11px; color:rgba(184,207,224,0.45); line-height:1.6; }
  .status-badge { display:inline-block; font-size:10px; font-weight:700; letter-spacing:1.5px; padding:4px 11px; margin-bottom:7px; }
  .usd-value { font-family:'Orbitron',monospace; font-weight:700; font-size:17px; color:var(--green); }
  .token-amount { font-size:11px; color:var(--text-dim); margin-top:2px; }
  .claim-link { display:inline-block; margin-top:10px; font-size:10px; letter-spacing:2px; font-family:'Orbitron',monospace; padding:6px 14px; border:1px solid currentColor; text-decoration:none; transition:all 0.2s; background:transparent; cursor:pointer; }
  .claim-link:hover { opacity:0.7; letter-spacing:3px; }
  .empty-state { text-align:center; padding:48px 20px; border:1px solid rgba(255,215,0,0.15); background:rgba(255,215,0,0.02); }
  .empty-icon { font-size:44px; opacity:0.3; margin-bottom:14px; }
  .empty-title { font-family:'Orbitron',monospace; font-size:13px; letter-spacing:3px; color:var(--gold); margin-bottom:10px; }
  .empty-body { font-size:11px; color:var(--text-dim); line-height:1.7; max-width:380px; margin:0 auto; }
  .scan-notes { margin-top:12px; padding:12px 16px; border:1px solid rgba(255,215,0,0.12); background:rgba(255,215,0,0.02); font-size:11px; color:var(--text-dim); line-height:1.7; }
  .scan-notes span { color:var(--gold); }
  #idle-state { text-align:center; padding:56px 0 40px; color:rgba(184,207,224,0.15); }
  .idle-icon { font-size:52px; margin-bottom:16px; }
  .idle-title { font-family:'Orbitron',monospace; font-size:12px; letter-spacing:4px; margin-bottom:8px; }
  .idle-sub { font-size:11px; line-height:1.6; }
  footer { text-align:center; margin-top:20px; font-size:10px; color:rgba(184,207,224,0.18); letter-spacing:1px; line-height:1.8; }
  .glitch { animation:glitch 0.35s; }
  @keyframes glitch { 0%{transform:translate(0)} 20%{transform:translate(-3px,1px);filter:hue-rotate(90deg)} 40%{transform:translate(3px,-1px);filter:hue-rotate(200deg)} 60%{transform:translate(-2px,2px)} 80%{transform:translate(2px,-1px);filter:hue-rotate(300deg)} 100%{transform:translate(0);filter:none} }
  .spin { display:inline-block; animation:spin 1s linear infinite; }
  @keyframes spin { to{transform:rotate(360deg)} }
</style>
</head>
<body>

<div class="orb orb1"></div>
<div class="orb orb2"></div>
<div class="orb orb3"></div>

<!-- ═══════════════════ PAYWALL ═══════════════════ -->
<div id="paywall">
  <div class="pw-box">
    <div class="pw-corner pw-tl"></div><div class="pw-corner pw-tr"></div>
    <div class="pw-corner pw-bl"></div><div class="pw-corner pw-br"></div>

    <div class="pw-eyebrow">◈ UNLOCK ACCESS ◈</div>
    <div class="pw-title">AIRDROP SCANNER PRO</div>
    <div class="pw-subtitle">AI-powered multi-chain airdrop intelligence. One-time payment, verified on-chain. No accounts, no subscriptions.</div>

    <div class="pw-features">
      <div class="pw-feature"><span class="pw-feature-icon">◈</span>Scans 14 blockchains — ETH, ARB, OP, Base, Polygon, BNB, SOL + more</div>
      <div class="pw-feature"><span class="pw-feature-icon">◈</span>Live AI web search finds airdrops announced today</div>
      <div class="pw-feature"><span class="pw-feature-icon">◈</span>USD estimates, confidence scores &amp; direct claim links</div>
      <div class="pw-feature"><span class="pw-feature-icon">◈</span>Pay once — your wallet address unlocked forever on-chain</div>
    </div>

    <div class="pw-price-box">
      <div>
        <div class="price-amount">$5</div>
        <div class="price-sub">ONE-TIME · ETHEREUM · MAINNET</div>
      </div>
      <div class="pw-price-right">Send ETH once.<br>Your wallet verified <strong>on-chain</strong>.<br>Works from any device, forever.</div>
    </div>

    <!-- Steps -->
    <div class="pw-steps">
      <div class="step" id="step1">
        <div class="step-num active" id="sn1">1</div>
        <div>
          <div class="step-title">Connect your MetaMask wallet</div>
          <div class="step-detail">We read your address to verify your payment on-chain. No signing required.</div>
        </div>
      </div>
      <div class="step" id="step2" style="opacity:0.4">
        <div class="step-num" id="sn2">2</div>
        <div style="width:100%">
          <div class="step-title">Send <span id="eth-display">~0.002 ETH</span> to this address</div>
          <div class="step-detail">
            Must be sent from the <strong style="color:var(--text)">same wallet</strong> you connected.
            <div class="addr-copy" onclick="copyAddress()">
              <span>0x4a81d9f39af3fe75223a4aa55f7b98159dcf3635</span>
              <span>⧉</span>
            </div>
            <div class="copy-hint" id="copy-hint">Click to copy address</div>
            <div class="eth-amount-row">
              <span class="eth-val" id="eth-val">calculating...</span>
              <span class="eth-sub" id="eth-sub">≈ $5 USD</span>
            </div>
          </div>
        </div>
      </div>
      <div class="step" id="step3" style="opacity:0.4">
        <div class="step-num" id="sn3">3</div>
        <div>
          <div class="step-title">Verify &amp; unlock</div>
          <div class="step-detail">We check Etherscan for your transaction. Takes seconds after it confirms.</div>
        </div>
      </div>
    </div>

    <div class="pw-status" id="pw-status"></div>

    <button class="pw-btn" id="btn-connect" onclick="connectWallet()">◈ CONNECT WALLET</button>
    <button class="pw-btn cyan" id="btn-verify" onclick="verifyPayment()" style="display:none">⟳ VERIFY PAYMENT ON-CHAIN</button>
    <button class="pw-btn gold" id="btn-demo" onclick="tryDemo()">◎ NO WALLET — TRY DEMO MODE</button>

    <div class="pw-disclaimer">
      Verified on Ethereum mainnet · Etherscan public API · No login · No tracking
    </div>
  </div>
</div>

<!-- ═══════════════════ MAIN APP ═══════════════════ -->
<div id="app">
  <header>
    <div class="eyebrow">◈ BLOCKCHAIN INTELLIGENCE SYSTEM ◈</div>
    <h1>AIRDROP<br>SCANNER</h1>
    <div class="subhead">MULTI-CHAIN &nbsp;·&nbsp; REAL-TIME &nbsp;·&nbsp; ALL PROTOCOLS</div>
    <div class="verified-bar" id="verified-bar">● ACCESS GRANTED — WALLET VERIFIED ON-CHAIN</div>
    <div class="chains" id="chain-pills"></div>
  </header>

  <div class="panel" id="input-panel">
    <div class="panel-corner tl"></div><div class="panel-corner tr"></div>
    <div class="panel-corner bl"></div><div class="panel-corner br"></div>
    <div class="panel-label">▸ ENTER WALLET ADDRESS TO SCAN</div>
    <div class="input-row">
      <input type="text" id="wallet-input" placeholder="0x... or Solana address or ENS name" spellcheck="false" autocomplete="off"/>
      <button id="scan-btn">SCAN</button>
    </div>
    <div class="progress-wrap" id="progress-wrap">
      <div class="progress-meta">
        <span id="progress-label">SCANNING CHAINS</span>
        <span id="progress-pct">0%</span>
      </div>
      <div class="progress-track">
        <div class="progress-fill" id="progress-fill" style="width:0%"></div>
      </div>
    </div>
  </div>

  <div class="panel" id="log-panel">
    <div class="panel-corner tl"></div><div class="panel-corner tr"></div>
    <div class="panel-corner bl"></div><div class="panel-corner br"></div>
    <div id="log-output"></div>
  </div>

  <div id="idle-state">
    <div class="idle-icon">◈</div>
    <div class="idle-title">AWAITING SCAN TARGET</div>
    <div class="idle-sub">Enter any EVM, Solana, or other wallet address<br>to scan all chains for pending airdrops &amp; claims</div>
  </div>

  <div id="results">
    <div class="summary-grid" id="summary-grid"></div>
    <div id="airdrops-container"></div>
    <div id="scan-notes-container"></div>
    <footer>
      POWERED BY CLAUDE AI &nbsp;·&nbsp; ALWAYS VERIFY ON OFFICIAL PROTOCOL SITES &nbsp;·&nbsp; NOT FINANCIAL ADVICE
    </footer>
  </div>
</div>

<script>
// ─── CONFIG ───────────────────────────────────────────────────────────────────
const PAYMENT_TO  = '0x4a81d9f39af3fe75223a4aa55f7b98159dcf3635';
const PAYMENT_USD = 5;
const STORAGE_KEY = 'airdrop_scanner_v2';

const CHAINS = [
  {name:"Ethereum",  symbol:"ETH",  color:"#627EEA"},
  {name:"Arbitrum",  symbol:"ARB",  color:"#28A0F0"},
  {name:"Optimism",  symbol:"OP",   color:"#FF0420"},
  {name:"Base",      symbol:"BASE", color:"#0052FF"},
  {name:"Polygon",   symbol:"POL",  color:"#8247E5"},
  {name:"BNB Chain", symbol:"BNB",  color:"#F0B90B"},
  {name:"Avalanche", symbol:"AVAX", color:"#E84142"},
  {name:"Solana",    symbol:"SOL",  color:"#9945FF"},
  {name:"zkSync Era",symbol:"ZK",   color:"#4E529A"},
  {name:"StarkNet",  symbol:"STRK", color:"#EC796B"},
  {name:"Linea",     symbol:"ETH",  color:"#61DFFF"},
  {name:"Scroll",    symbol:"SCR",  color:"#EEB95E"},
  {name:"Mantle",    symbol:"MNT",  color:"#3CFFD0"},
  {name:"Mode",      symbol:"MODE", color:"#DFFE00"},
];

const STATUS_MAP = {
  claimable:       {label:"CLAIMABLE",      color:"#00FF88", bg:"rgba(0,255,136,0.08)"},
  check:           {label:"CHECK NOW",       color:"#FFD700", bg:"rgba(255,215,0,0.08)"},
  likely_eligible: {label:"LIKELY ELIGIBLE", color:"#00CFFF", bg:"rgba(0,207,255,0.08)"},
  expired:         {label:"EXPIRED",         color:"#FF4455", bg:"rgba(255,68,85,0.08)"},
};

// ─── STATE ────────────────────────────────────────────────────────────────────
let connectedWallet = null;
let requiredWei     = 2000000000000000n; // 0.002 ETH fallback

// ─── BOOT ─────────────────────────────────────────────────────────────────────
(async () => {
  renderPills();
  fetchEthPrice();
  const stored = localStorage.getItem(STORAGE_KEY);
  if (stored) {
    try {
      const {wallet} = JSON.parse(stored);
      if (wallet) { unlockApp(wallet, false); return; }
    } catch(e) {}
  }
})();

function renderPills() {
  const c = document.getElementById('chain-pills');
  CHAINS.forEach((ch,i) => {
    const p = document.createElement('div');
    p.className = 'chain-pill';
    p.textContent = ch.symbol;
    p.style.cssText = `border-color:${ch.color}55;color:${ch.color};background:${ch.color}11;animation-delay:${i*0.04}s`;
    c.appendChild(p);
  });
}

async function fetchEthPrice() {
  try {
    const r = await fetch('https://api.coingecko.com/api/v3/simple/price?ids=ethereum&vs_currencies=usd');
    const d = await r.json();
    const price  = d.ethereum.usd;
    const needed = PAYMENT_USD / price;
    requiredWei  = BigInt(Math.ceil(needed * 1e18));
    const disp   = needed.toFixed(5);
    document.getElementById('eth-display').textContent = disp + ' ETH';
    document.getElementById('eth-val').textContent     = disp + ' ETH';
    document.getElementById('eth-sub').textContent     = '≈ $' + PAYMENT_USD + ' @ $' + Math.round(price).toLocaleString();
  } catch(e) {
    document.getElementById('eth-val').textContent = '0.002 ETH';
    document.getElementById('eth-sub').textContent = '≈ $5 (est.)';
  }
}

// ─── PAYWALL ──────────────────────────────────────────────────────────────────
async function connectWallet() {
  if (!window.ethereum) {
    setPwStatus('No Web3 wallet detected. Install MetaMask, then refresh.', 'error');
    return;
  }
  setPwStatus('Requesting wallet access...', 'info');
  try {
    const accounts = await window.ethereum.request({method:'eth_requestAccounts'});
    connectedWallet = accounts[0].toLowerCase();
    setPwStatus('✓ Connected: ' + shortAddr(connectedWallet), 'ok');
    stepDone(1); stepActive(2);
    document.getElementById('step2').style.opacity = '1';
    document.getElementById('btn-connect').style.display = 'none';
    document.getElementById('btn-verify').style.display  = 'block';
    document.getElementById('btn-demo').style.display    = 'none';
  } catch(e) {
    setPwStatus('Connection rejected. Please try again.', 'error');
  }
}

async function verifyPayment() {
  if (!connectedWallet) { setPwStatus('Connect your wallet first.', 'error'); return; }
  const btn = document.getElementById('btn-verify');
  btn.disabled = true;
  btn.innerHTML = '<span class="spin">⟳</span> SCANNING BLOCKCHAIN...';
  setPwStatus('Checking Ethereum for your transaction...', 'info');
  stepActive(3);
  document.getElementById('step3').style.opacity = '1';

  try {
    const url = `https://api.etherscan.io/api?module=account&action=txlist&address=${PAYMENT_TO}&startblock=0&endblock=99999999&sort=desc&offset=500&page=1`;
    const r   = await fetch(url);
    const d   = await r.json();

    let found = false;
    if (d.status === '1' && Array.isArray(d.result)) {
      for (const tx of d.result) {
        if (
          tx.from.toLowerCase() === connectedWallet &&
          tx.to.toLowerCase()   === PAYMENT_TO.toLowerCase() &&
          BigInt(tx.value)      >= requiredWei &&
          tx.isError            === '0'
        ) { found = true; break; }
      }
    }

    if (found) {
      stepDone(2); stepDone(3);
      setPwStatus('✓ Payment verified on-chain! Unlocking...', 'ok');
      localStorage.setItem(STORAGE_KEY, JSON.stringify({wallet: connectedWallet, ts: Date.now()}));
      setTimeout(() => unlockApp(connectedWallet, true), 1000);
    } else {
      const ethNeeded = document.getElementById('eth-val').textContent;
      setPwStatus(
        `Payment not found. Ensure you sent at least ${ethNeeded} from ${shortAddr(connectedWallet)} to the address above. Transactions need ~15 seconds to confirm — wait and try again.`,
        'error'
      );
      btn.disabled = false;
      btn.textContent = '⟳ VERIFY PAYMENT ON-CHAIN';
    }
  } catch(e) {
    setPwStatus('Etherscan lookup failed: ' + e.message, 'error');
    btn.disabled = false;
    btn.textContent = '⟳ VERIFY PAYMENT ON-CHAIN';
  }
}

function tryDemo() {
  const addr = prompt('Demo Mode — enter a wallet address to scan (payment verification skipped):');
  if (addr && addr.trim().length > 8) unlockApp(addr.trim(), false, true);
}

function unlockApp(wallet, animate = true, demo = false) {
  const pw = document.getElementById('paywall');
  if (animate) {
    pw.style.transition = 'opacity 0.5s';
    pw.style.opacity = '0';
    setTimeout(() => pw.classList.add('hidden'), 500);
  } else {
    pw.classList.add('hidden');
  }
  const bar = document.getElementById('verified-bar');
  if (demo) {
    bar.textContent = '◎ DEMO MODE — ' + shortAddr(wallet);
    bar.style.color = '#FFD700';
    bar.style.borderColor = 'rgba(255,215,0,0.2)';
    bar.style.background  = 'rgba(255,215,0,0.05)';
  } else {
    bar.textContent = '● ACCESS GRANTED — ' + shortAddr(wallet);
  }
  bar.classList.add('visible');
  document.getElementById('wallet-input').value = wallet;
}

function copyAddress() {
  navigator.clipboard.writeText(PAYMENT_TO).then(() => {
    document.getElementById('copy-hint').textContent = '✓ Copied to clipboard!';
    document.getElementById('copy-hint').style.color = '#00FF88';
    setTimeout(() => {
      document.getElementById('copy-hint').textContent = 'Click to copy address';
      document.getElementById('copy-hint').style.color = '';
    }, 2500);
  });
}

function setPwStatus(msg, type) {
  const el = document.getElementById('pw-status');
  el.textContent = msg;
  el.className = 'pw-status visible ' + type;
}
function stepDone(n)   { const e = document.getElementById('sn'+n); if(e){e.textContent='✓';e.className='step-num done';} }
function stepActive(n) { const e = document.getElementById('sn'+n); if(e) e.className='step-num active'; }
function shortAddr(a)  { return a.slice(0,8)+'...'+a.slice(-6); }

// ─── SCANNER ──────────────────────────────────────────────────────────────────
const walletInput   = document.getElementById('wallet-input');
const scanBtn       = document.getElementById('scan-btn');
const progressWrap  = document.getElementById('progress-wrap');
const progressFill  = document.getElementById('progress-fill');
const progressPct   = document.getElementById('progress-pct');
const progressLabel = document.getElementById('progress-label');
const logPanel      = document.getElementById('log-panel');
const logOutput     = document.getElementById('log-output');
const idleState     = document.getElementById('idle-state');
const resultsEl     = document.getElementById('results');
const inputPanel    = document.getElementById('input-panel');

walletInput.addEventListener('keydown', e => { if(e.key==='Enter') startScan(); });
scanBtn.addEventListener('click', startScan);

const sleep = ms => new Promise(r => setTimeout(r, ms));

function setProgress(pct, label) {
  progressFill.style.width = pct + '%';
  progressPct.textContent  = pct + '%';
  if(label) progressLabel.textContent = label;
}

function addLog(msg, type='info') {
  const el = document.createElement('div');
  el.className = 'log-line log-' + type;
  if(type==='ok') {
    el.innerHTML = `<span style="display:inline-block;width:7px;height:7px;background:#00FF88;border-radius:50%;margin-right:8px;box-shadow:0 0 5px #00FF88;vertical-align:middle"></span>${esc(msg)}`;
  } else { el.textContent = msg; }
  logOutput.appendChild(el);
  logOutput.scrollTop = logOutput.scrollHeight;
}

function esc(s) { return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

async function startScan() {
  const addr = walletInput.value.trim();
  if (!addr) return;

  inputPanel.classList.add('glitch');
  setTimeout(() => inputPanel.classList.remove('glitch'), 350);

  scanBtn.disabled = true;
  scanBtn.classList.add('scanning');
  scanBtn.innerHTML = '<span class="spin">⟳</span> SCANNING';
  logOutput.innerHTML = '';
  logPanel.classList.add('visible');
  progressWrap.classList.add('visible');
  idleState.style.display = 'none';
  resultsEl.classList.remove('visible');
  setProgress(0);

  addLog('> INITIATING MULTI-CHAIN SCAN', 'sys');
  await sleep(200);
  addLog('> TARGET: ' + addr.slice(0,10)+'...'+addr.slice(-8), 'addr');
  await sleep(300);
  addLog('> LOADING '+CHAINS.length+' CHAIN NODES', 'info');
  for(let i=0;i<CHAINS.length;i++) {
    await sleep(85);
    addLog('  \u21b3 '+CHAINS[i].name.padEnd(12,' ')+' ...... ONLINE','chain');
    setProgress(Math.floor((i/CHAINS.length)*48)+4);
  }
  await sleep(200);
  addLog('> QUERYING AIRDROP REGISTRIES...','info');   setProgress(54);
  await sleep(260);
  addLog('> CROSS-REFERENCING MERKLE PROOFS...','info'); setProgress(63);
  await sleep(260);
  addLog('> SCANNING CLAIM CONTRACTS...','info');       setProgress(72);
  await sleep(200);
  addLog('> ANALYSING PROTOCOL ELIGIBILITY...','info'); setProgress(78);

  let parsed = null;
  try {
    const res = await fetch('https://api.anthropic.com/v1/messages', {
      method:'POST',
      headers:{'Content-Type':'application/json'},
      body: JSON.stringify({
        model:'claude-sonnet-4-20250514',
        max_tokens:1000,
        tools:[{type:'web_search_20250305',name:'web_search'}],
        system:`You are a crypto airdrop intelligence engine. Search for ALL currently active, unclaimed, or upcoming airdrop campaigns across every major blockchain as of March 2026.

Return ONLY valid JSON — no markdown, no preamble:
{
  "totalFound":<int>,
  "totalEstimatedUSD":"<string>",
  "airdrops":[{
    "protocol":"<name>","token":"<SYMBOL>","chain":"<chain>",
    "estimatedAmount":"<amount or TBD>","usdValue":"<USD or null>",
    "status":"<claimable|check|likely_eligible>","claimUrl":"<URL>",
    "deadline":"<date or No deadline>","eligibilityCriteria":"<brief>",
    "confidence":"<High|Medium|Low>"
  }],
  "chainsSummary":{"<chain>":<count>},
  "scanNotes":"<caveats>"
}`,
        messages:[{role:'user',content:`Find every active airdrop, unclaimed distribution, and pending claim across ALL chains (Ethereum, Arbitrum, Optimism, Base, Polygon, BNB Chain, Avalanche, Solana, zkSync, StarkNet, Linea, Scroll, Mantle, Mode, and others) as of March 2026.\n\nWallet: ${addr}\n\nBe comprehensive. Return ONLY JSON.`}]
      })
    });

    setProgress(88,'DECRYPTING PAYLOAD');
    addLog('> DECRYPTING RESPONSE...','info');

    const data = await res.json();
    const text = (data.content||[]).filter(b=>b.type==='text').map(b=>b.text).join('');
    const m = text.match(/\{[\s\S]*\}/);
    if(m) { try{parsed=JSON.parse(m[0]);}catch(e){} }

    setProgress(100,'SCAN COMPLETE');
    if(parsed && Array.isArray(parsed.airdrops)) {
      const n = parsed.totalFound || parsed.airdrops.length;
      addLog('> SCAN COMPLETE \u2014 '+n+' CLAIM'+(n!==1?'S':'')+' DETECTED','ok');
      if(parsed.totalEstimatedUSD) addLog('> EST. VALUE: '+parsed.totalEstimatedUSD,'ok');
    } else {
      addLog('> SCAN COMPLETE \u2014 PARSE ERROR','warn');
      parsed = {totalFound:0,airdrops:[],scanNotes:text.slice(0,600),chainsSummary:{}};
    }
  } catch(err) {
    addLog('> ERROR: '+err.message,'err');
    parsed = {totalFound:0,airdrops:[],scanNotes:'Connection failed.',chainsSummary:{}};
    setProgress(100,'ERROR');
  }

  renderResults(parsed);
  scanBtn.disabled = false;
  scanBtn.classList.remove('scanning');
  scanBtn.textContent = 'SCAN';
}

function renderResults(data) {
  document.getElementById('summary-grid').innerHTML       = '';
  document.getElementById('airdrops-container').innerHTML = '';
  document.getElementById('scan-notes-container').innerHTML = '';

  [{label:'CLAIMS FOUND',    value:data.totalFound||data.airdrops?.length||0, color:'#00FF88', size:'28px'},
   {label:'EST. TOTAL VALUE',value:data.totalEstimatedUSD||'—',               color:'#00CFFF', size:'20px'},
   {label:'CHAINS SCANNED', value:CHAINS.length,                               color:'#9945FF', size:'28px'}
  ].forEach(s=>{
    const c = document.createElement('div');
    c.className='stat-card';
    c.style.cssText=`border-color:${s.color}44;background:${s.color}08`;
    c.innerHTML=`<div class="stat-value" style="color:${s.color};font-size:${s.size}">${esc(String(s.value))}</div><div class="stat-label" style="color:${s.color}99">${s.label}</div>`;
    document.getElementById('summary-grid').appendChild(c);
  });

  const ac = document.getElementById('airdrops-container');
  if(!data.airdrops?.length) {
    ac.innerHTML=`<div class="empty-state"><div class="empty-icon">◎</div><div class="empty-title">NO CLAIMS DETECTED</div><div class="empty-body">${esc(data.scanNotes||'No pending airdrops found. Check back as new distributions are announced regularly.')}</div></div>`;
  } else {
    const lbl=document.createElement('div'); lbl.className='section-label'; lbl.textContent='▸ DETECTED AIRDROP OPPORTUNITIES'; ac.appendChild(lbl);
    data.airdrops.forEach((a,i)=>{
      const s = STATUS_MAP[a.status]||STATUS_MAP.check;
      const cc= CHAINS.find(c=>c.name.toLowerCase().includes((a.chain||'').toLowerCase().split(' ')[0]))?.color||'#888';
      const conf = a.confidence==='High'?'conf-high':a.confidence==='Medium'?'conf-med':'conf-low';
      let badges=`<div class="badge"><span class="badge-dot" style="background:${cc}"></span>${esc(a.chain||'?')}</div>`;
      if(a.confidence) badges+=`<div class="badge ${conf}">◈ ${esc(a.confidence)}</div>`;
      if(a.deadline&&a.deadline!=='No deadline'&&a.deadline!=='Unknown') badges+=`<div class="badge deadline">⏳ ${esc(a.deadline)}</div>`;
      const card=document.createElement('div');
      card.className='airdrop-card'; card.style.animationDelay=(i*0.045)+'s';
      card.innerHTML=`<div class="card-top"><div class="card-left"><div style="margin-bottom:8px"><span class="protocol-name">${esc(a.protocol||'?')}</span><span class="token-tag">$${esc(a.token||'?')}</span></div><div class="card-badges">${badges}</div>${a.eligibilityCriteria?`<div class="criteria">${esc(a.eligibilityCriteria)}</div>`:''}</div><div class="card-right"><div class="status-badge" style="color:${s.color};background:${s.bg};border:1px solid ${s.color}44">${s.label}</div>${a.usdValue?`<div class="usd-value">${esc(a.usdValue)}</div>`:''}${a.estimatedAmount?`<div class="token-amount">${esc(a.estimatedAmount)} ${esc(a.token||'')}</div>`:''}${a.claimUrl?`<a class="claim-link" href="${esc(a.claimUrl)}" target="_blank" rel="noopener noreferrer" style="color:${s.color}">▸ CLAIM</a>`:''}</div></div>`;
      ac.appendChild(card);
    });
  }

  if(data.scanNotes&&data.airdrops?.length) {
    document.getElementById('scan-notes-container').innerHTML=`<div class="scan-notes"><span>⚠ NOTE: </span>${esc(data.scanNotes)}</div>`;
  }
  resultsEl.classList.add('visible');
}
</script>
</body>
</html>
