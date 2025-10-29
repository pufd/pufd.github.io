# pufd.github.io

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Quick Calc ×1.1</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#10b981;--muted:#94a3b8;--glass: rgba(255,255,255,0.03)}
    html,body{height:100%;margin:0;font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,'Helvetica Neue',Arial}
    body{display:flex;align-items:center;justify-content:center;background:linear-gradient(180deg,#071029 0%, #092036 100%);color:#e6eef8;padding:20px}
    .card{width:100%;max-width:520px;background:linear-gradient(180deg,var(--glass), rgba(255,255,255,0.02));border-radius:14px;padding:18px;box-shadow:0 8px 30px rgba(2,6,23,0.6);backdrop-filter: blur(6px)}
    h1{font-size:1.1rem;margin:0 0 12px 0;color:#fff}
    label{font-size:0.85rem;color:var(--muted);display:block;margin-bottom:8px}
    .row{display:flex;gap:8px}
    input[type=number]{flex:1;padding:14px 12px;font-size:1.15rem;border-radius:10px;border:1px solid rgba(255,255,255,0.06);background:transparent;color:inherit;outline:none}
    input[type=number]::placeholder{color:#5b6b78}
    .result{margin-top:16px;padding:14px;border-radius:10px;background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.03);font-size:1.2rem;display:flex;align-items:center;justify-content:space-between}
    .muted{font-size:0.85rem;color:var(--muted)}
    button.copy{margin-left:8px;padding:10px 12px;border-radius:10px;border:none;background:var(--accent);color:#042018;font-weight:600}
    .footer{margin-top:12px;font-size:0.8rem;color:var(--muted)}
    @media (max-width:420px){.card{padding:14px}input[type=number]{padding:12px;font-size:1rem}.result{font-size:1rem}}
  </style>
</head>
<body>
  <main class="card" role="main">
    <h1>Quick mobile calculator — shows ((input)*1.1)*1 in real time</h1>

    <label for="num">Enter a number</label>
    <div class="row">
      <input id="num" type="number" inputmode="decimal" step="any" placeholder="e.g. 100" autocomplete="off" autocorrect="off" autofocus>
      <button id="clear" title="Clear" aria-label="Clear">✕</button>
    </div>

    <div class="result" aria-live="polite">
      <div>
        <div class="muted">Expression</div>
        <div id="expr">((input) × 1.1) × 1</div>
      </div>

      <div style="text-align:right">
        <div class="muted">Result</div>
        <div id="out" style="font-weight:700;font-size:1.25rem">—</div>
      </div>
    </div>

    <div style="display:flex;gap:8px;margin-top:12px;align-items:center">
      <button id="copy" class="copy">Copy result</button>
      <div class="footer">Updates instantly as you type. Results shown with 2 decimal places.</div>
    </div>
  </main>

  <script>
    const num = document.getElementById('num');
    const out = document.getElementById('out');
    const expr = document.getElementById('expr');
    const copyBtn = document.getElementById('copy');
    const clearBtn = document.getElementById('clear');

    function formatNumber(n){
      // show up to 2 decimal places, but remove trailing zeros
      if (!isFinite(n)) return '—';
      return Number.isInteger(n) ? n.toString() : n.toFixed(2).replace(/\.0+$/,'').replace(/(\.[0-9]*?)0+$/,'$1');
    }

    function compute(value){
      const input = parseFloat(value);
      if (Number.isNaN(input)) return null;
      // compute ((input)*1.1)*1 which simplifies to input * 1.1
      const result = (input * 1.1) * 1;
      return result;
    }

    function update(){
      const v = num.value;
      const r = compute(v);
      if (r === null){
        out.textContent = '—';
      } else {
        out.textContent = formatNumber(r);
      }
    }

    // real-time
    num.addEventListener('input', update);

    // copy
    copyBtn.addEventListener('click', async ()=>{
      const text = out.textContent;
      if (!text || text==='—') return;
      try { await navigator.clipboard.writeText(text); copyBtn.textContent='Copied!'; setTimeout(()=>copyBtn.textContent='Copy result',1200);} catch(e){copyBtn.textContent='Copy';}
    });

    // clear
    clearBtn.addEventListener('click', ()=>{ num.value=''; update(); num.focus(); });

    // initialize
    update();
  </script>
</body>
</html>
