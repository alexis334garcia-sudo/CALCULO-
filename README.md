<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Calculadora de Dosis de Agroquímicos</title>
<link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;500;600;700&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a1628;
    --surface: #0f2040;
    --card: #132847;
    --border: #1e3a5f;
    --accent: #00e5a0;
    --accent2: #00b8ff;
    --warn: #ffb300;
    --text: #cde6ff;
    --muted: #5a7fa8;
    --result-bg: #071520;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Rajdhani', sans-serif;
    font-size: 15px;
    min-height: 100vh;
    padding: 20px 12px 40px;
    background-image: radial-gradient(ellipse at 20% 20%, #0d2a4a 0%, transparent 60%),
                      radial-gradient(ellipse at 80% 80%, #071a30 0%, transparent 60%);
  }
  h1 {
    text-align: center;
    font-size: 1.9rem;
    font-weight: 700;
    letter-spacing: 2px;
    color: var(--accent);
    text-shadow: 0 0 20px rgba(0,229,160,0.3);
    margin-bottom: 6px;
    text-transform: uppercase;
  }
  .subtitle {
    text-align: center;
    color: var(--muted);
    font-size: 0.85rem;
    letter-spacing: 3px;
    text-transform: uppercase;
    margin-bottom: 28px;
  }
  .tabs {
    display: flex;
    gap: 6px;
    max-width: 780px;
    margin: 0 auto 22px;
    border-bottom: 1px solid var(--border);
    padding-bottom: 0;
  }
  .tab {
    padding: 8px 20px;
    cursor: pointer;
    border: 1px solid transparent;
    border-bottom: none;
    border-radius: 6px 6px 0 0;
    color: var(--muted);
    font-family: 'Rajdhani', sans-serif;
    font-weight: 600;
    font-size: 0.9rem;
    letter-spacing: 1px;
    text-transform: uppercase;
    background: transparent;
    transition: all 0.2s;
  }
  .tab:hover { color: var(--text); border-color: var(--border); }
  .tab.active {
    background: var(--card);
    color: var(--accent);
    border-color: var(--border);
    border-bottom-color: var(--card);
    margin-bottom: -1px;
  }
  .page { display: none; max-width: 780px; margin: 0 auto; }
  .page.active { display: block; }

  /* ── CARDS ── */
  .card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 18px 20px;
    margin-bottom: 16px;
  }
  .card-title {
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: var(--accent2);
    margin-bottom: 14px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .card-title::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  /* ── FIELDS ── */
  .field {
    display: grid;
    grid-template-columns: 1fr auto auto;
    align-items: center;
    gap: 8px;
    margin-bottom: 10px;
  }
  .field label {
    font-weight: 500;
    color: var(--text);
    font-size: 0.95rem;
  }
  .field input[type=number] {
    width: 90px;
    background: var(--result-bg);
    border: 1px solid var(--border);
    border-radius: 6px;
    color: var(--accent);
    font-family: 'Share Tech Mono', monospace;
    font-size: 1rem;
    padding: 6px 10px;
    text-align: right;
    outline: none;
    transition: border-color 0.2s;
  }
  .field input[type=number]:focus { border-color: var(--accent); }
  .field select {
    background: var(--result-bg);
    border: 1px solid var(--border);
    border-radius: 6px;
    color: var(--text);
    font-family: 'Rajdhani', sans-serif;
    font-size: 0.9rem;
    padding: 6px 8px;
    outline: none;
    cursor: pointer;
    transition: border-color 0.2s;
  }
  .field select:focus { border-color: var(--accent); }
  .hint {
    grid-column: 1 / -1;
    font-size: 0.75rem;
    color: var(--muted);
    margin-top: -6px;
    margin-bottom: 4px;
    font-style: italic;
  }

  /* ── RESULTS ── */
  .results-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
  }
  .result-item {
    background: var(--result-bg);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 12px 14px;
  }
  .result-item.highlight {
    border-color: var(--accent);
    background: rgba(0,229,160,0.05);
  }
  .result-item.highlight2 {
    border-color: var(--accent2);
    background: rgba(0,184,255,0.05);
  }
  .result-label {
    font-size: 0.72rem;
    color: var(--muted);
    letter-spacing: 1px;
    text-transform: uppercase;
    margin-bottom: 4px;
  }
  .result-value {
    font-family: 'Share Tech Mono', monospace;
    font-size: 1.35rem;
    color: var(--accent);
    font-weight: 400;
  }
  .result-item.highlight2 .result-value { color: var(--accent2); }
  .result-item.warn .result-value { color: var(--warn); }
  .result-item.warn { border-color: var(--warn); background: rgba(255,179,0,0.05); }

  /* ── BOMBA RESULT ── */
  .bomba-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    gap: 10px;
    margin-top: 4px;
  }

  /* ── UNIDADES TABLE ── */
  table {
    width: 100%;
    border-collapse: collapse;
    font-family: 'Share Tech Mono', monospace;
    font-size: 0.88rem;
  }
  th {
    background: var(--result-bg);
    color: var(--accent2);
    padding: 8px 12px;
    text-align: left;
    font-family: 'Rajdhani', sans-serif;
    font-weight: 700;
    letter-spacing: 1px;
    text-transform: uppercase;
    font-size: 0.75rem;
    border-bottom: 1px solid var(--border);
  }
  td {
    padding: 7px 12px;
    border-bottom: 1px solid rgba(30,58,95,0.5);
    color: var(--text);
  }
  tr:hover td { background: rgba(0,229,160,0.03); }

  /* ── CONVERSIONES ── */
  .conv-section { margin-bottom: 20px; }
  .conv-row {
    display: grid;
    grid-template-columns: 1fr auto 1fr;
    align-items: center;
    gap: 12px;
    margin-bottom: 12px;
  }
  .conv-arrow {
    font-size: 1.5rem;
    color: var(--accent);
    text-align: center;
  }
  .conv-result-box {
    background: var(--result-bg);
    border: 1px solid var(--accent);
    border-radius: 8px;
    padding: 14px;
    text-align: center;
    margin-top: 8px;
  }
  .conv-result-value {
    font-family: 'Share Tech Mono', monospace;
    font-size: 2rem;
    color: var(--accent);
  }
  .all-equiv {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
    gap: 8px;
    margin-top: 12px;
  }
  .equiv-item {
    background: var(--result-bg);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 8px 10px;
    text-align: center;
  }
  .equiv-unit { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; }
  .equiv-val { font-family: 'Share Tech Mono', monospace; color: var(--text); font-size: 0.95rem; margin-top: 2px; }

  .divider { height: 1px; background: var(--border); margin: 14px 0; }
  input::-webkit-inner-spin-button { opacity: 0.4; }

  @media(max-width: 520px) {
    .results-grid { grid-template-columns: 1fr; }
    .conv-row { grid-template-columns: 1fr; }
    .conv-arrow { transform: rotate(90deg); }
    h1 { font-size: 1.4rem; }
  }
</style>
</head>
<body>

<h1>🌿 Dosis de Agroquímicos</h1>
<p class="subtitle">Calculadora Agrícola Profesional</p>

<div class="tabs">
  <button class="tab active" onclick="showTab('calc')">Calculadora</button>
  <button class="tab" onclick="showTab('conv')">Conversiones</button>
  <button class="tab" onclick="showTab('units')">Unidades</button>
</div>

<!-- ══════════════ CALCULADORA ══════════════ -->
<div id="tab-calc" class="page active">

  <div class="card">
    <div class="card-title">Parámetros de Área</div>
    <div class="field">
      <label>Área a tratar</label>
      <input type="number" id="area" value="1" min="0" step="0.01" oninput="calc()">
      <select id="areaUnit" onchange="calc()">
        <option value="1">Hectárea (ha)</option>
        <option value="0.6987">Manzana</option>
        <option value="0.404686">Acre</option>
        <option value="0.0001">m²</option>
        <option value="0.3929">Cuerda</option>
      </select>
    </div>
    <div class="field">
      <label>Área total del terreno</label>
      <input type="number" id="areaTotal" value="1" min="0" step="0.01" oninput="calc()">
      <select id="areaTotalUnit" onchange="calc()">
        <option value="1">Hectárea (ha)</option>
        <option value="0.6987">Manzana</option>
        <option value="0.404686">Acre</option>
        <option value="0.0001">m²</option>
        <option value="0.3929">Cuerda</option>
      </select>
    </div>
  </div>

  <div class="card">
    <div class="card-title">Dosis del Producto</div>
    <div class="field">
      <label>Dosis recomendada</label>
      <input type="number" id="dosis" value="2.5" min="0" step="0.01" oninput="calc()">
      <select id="dosisUnit" onchange="calc()">
        <option value="1">mL</option>
        <option value="1000" selected>L</option>
        <option value="1000">cc (cm³)</option>
        <option value="29.5735">fl oz</option>
        <option value="3785.41">galón (gal)</option>
        <option value="946.353">cuarto (qt)</option>
        <option value="473.176">pinta (pt)</option>
        <option value="236.588">taza (cup)</option>
      </select>
    </div>
    <p class="hint">← Ingrese la dosis recomendada por etiqueta</p>
    <div class="field">
      <label>Por unidad de área</label>
      <span></span>
      <select id="dosisAreaUnit" onchange="calc()">
        <option value="1">Hectárea (ha)</option>
        <option value="0.6987">Manzana</option>
        <option value="0.404686">Acre</option>
        <option value="0.0001">m²</option>
        <option value="0.3929">Cuerda</option>
      </select>
    </div>
    <p class="hint">← Unidad base de la dosis</p>
  </div>

  <div class="card">
    <div class="card-title">Agua y Mezcla</div>
    <div class="field">
      <label>Volumen de agua por área</label>
      <input type="number" id="agua" value="286" min="0" step="1" oninput="calc()">
      <select id="aguaUnit" onchange="calc()">
        <option value="1000" selected>L</option>
        <option value="1">mL</option>
        <option value="3785410">galón (gal)</option>
      </select>
    </div>
    <div class="field">
      <label>Capacidad del equipo/bomba</label>
      <input type="number" id="bomba" value="25" min="0.1" step="0.5" oninput="calc()">
      <select id="bombaUnit" onchange="calc()">
        <option value="1000" selected>L</option>
        <option value="1">mL</option>
        <option value="3785410">galón (gal)</option>
      </select>
    </div>
  </div>

  <!-- RESULTADOS -->
  <div class="card">
    <div class="card-title">Resultados — Dosis y Producto Total</div>
    <div class="results-grid">
      <div class="result-item">
        <div class="result-label">Dosis en mL / ha</div>
        <div class="result-value" id="r-dosis-ml">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Dosis en L / ha</div>
        <div class="result-value" id="r-dosis-l">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Área a tratar (ha)</div>
        <div class="result-value" id="r-area-ha">—</div>
      </div>
      <div class="result-item highlight">
        <div class="result-label">Producto TOTAL (mL)</div>
        <div class="result-value" id="r-prod-ml">—</div>
      </div>
      <div class="result-item highlight">
        <div class="result-label">Producto TOTAL (L)</div>
        <div class="result-value" id="r-prod-l">—</div>
      </div>
    </div>
  </div>

  <div class="card">
    <div class="card-title">💧 Dosis por Bomba / Equipo</div>
    <div class="bomba-grid">
      <div class="result-item highlight2">
        <div class="result-label">Agua total (L)</div>
        <div class="result-value" id="r-agua-total">—</div>
      </div>
      <div class="result-item warn">
        <div class="result-label">Núm. de bombas</div>
        <div class="result-value" id="r-num-bombas">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Área / bomba (ha)</div>
        <div class="result-value" id="r-area-bomba">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Agua / bomba (L)</div>
        <div class="result-value" id="r-agua-bomba">—</div>
      </div>
    </div>
    <div class="divider"></div>
    <div class="bomba-grid">
      <div class="result-item highlight">
        <div class="result-label">Producto / bomba (mL)</div>
        <div class="result-value" id="r-prod-bomba-ml">—</div>
      </div>
      <div class="result-item highlight">
        <div class="result-label">Producto / bomba (L)</div>
        <div class="result-value" id="r-prod-bomba-l">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Producto / bomba (cc/cm³)</div>
        <div class="result-value" id="r-prod-bomba-cc">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Producto / bomba (fl oz)</div>
        <div class="result-value" id="r-prod-bomba-floz">—</div>
      </div>
      <div class="result-item">
        <div class="result-label">Producto / bomba (galón)</div>
        <div class="result-value" id="r-prod-bomba-gal">—</div>
      </div>
    </div>
  </div>
</div>

<!-- ══════════════ CONVERSIONES ══════════════ -->
<div id="tab-conv" class="page">
  <div class="card">
    <div class="card-title">Conversión Rápida de Volumen</div>
    <p style="color:var(--muted);font-size:0.82rem;margin-bottom:14px;">Ingrese un valor y seleccione las unidades de origen</p>
    <div class="conv-row">
      <div>
        <div style="font-size:0.75rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:6px;">Valor</div>
        <input type="number" id="conv-val" value="1" min="0" step="any"
          style="width:100%;background:var(--result-bg);border:1px solid var(--border);border-radius:6px;color:var(--accent);font-family:'Share Tech Mono',monospace;font-size:1.2rem;padding:10px 12px;outline:none;"
          oninput="convert()">
      </div>
      <div class="conv-arrow">→</div>
      <div>
        <div style="font-size:0.75rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:6px;">Unidad Origen</div>
        <select id="conv-from" style="width:100%;background:var(--result-bg);border:1px solid var(--border);border-radius:6px;color:var(--text);font-family:'Rajdhani',sans-serif;font-size:1rem;padding:10px 8px;outline:none;" onchange="convert()">
          <option value="1">mL</option>
          <option value="1000">L</option>
          <option value="1">cc (cm³)</option>
          <option value="29.5735">fl oz</option>
          <option value="3785.41">galón (gal)</option>
          <option value="946.353">cuarto (qt)</option>
          <option value="473.176">pinta (pt)</option>
          <option value="236.588">taza (cup)</option>
        </select>
      </div>
    </div>
    <div style="margin-bottom:12px;">
      <div style="font-size:0.75rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:6px;">Unidad Destino</div>
      <select id="conv-to" style="width:100%;background:var(--result-bg);border:1px solid var(--border);border-radius:6px;color:var(--text);font-family:'Rajdhani',sans-serif;font-size:1rem;padding:10px 8px;outline:none;" onchange="convert()">
        <option value="1">mL</option>
        <option value="1000" selected>L</option>
        <option value="1">cc (cm³)</option>
        <option value="29.5735">fl oz</option>
        <option value="3785.41">galón (gal)</option>
        <option value="946.353">cuarto (qt)</option>
        <option value="473.176">pinta (pt)</option>
        <option value="236.588">taza (cup)</option>
      </select>
    </div>
    <div class="conv-result-box">
      <div style="font-size:0.75rem;color:var(--muted);text-transform:uppercase;letter-spacing:2px;margin-bottom:6px;">▼ Resultado</div>
      <div class="conv-result-value" id="conv-result">0.001</div>
      <div style="font-size:0.8rem;color:var(--muted);margin-top:4px;" id="conv-unit-label">L</div>
    </div>
  </div>

  <div class="card">
    <div class="card-title">Equivalencias del valor en todas las unidades</div>
    <div class="all-equiv" id="all-equiv"></div>
  </div>
</div>

<!-- ══════════════ UNIDADES ══════════════ -->
<div id="tab-units" class="page">
  <div class="card">
    <div class="card-title">Unidades de Medida — Volumen</div>
    <table>
      <thead>
        <tr><th>Unidad</th><th>Símbolo</th><th>→ mL</th><th>→ L</th><th>→ cc (cm³)</th></tr>
      </thead>
      <tbody>
        <tr><td>Mililitro</td><td>mL</td><td>1</td><td>0.001</td><td>1</td></tr>
        <tr><td>Litro</td><td>L</td><td>1000</td><td>1</td><td>1000</td></tr>
        <tr><td>Centímetro³</td><td>cc/cm³</td><td>1</td><td>0.001</td><td>1</td></tr>
        <tr><td>Galón (US)</td><td>gal</td><td>3785.41</td><td>3.78541</td><td>3785.41</td></tr>
      </tbody>
    </table>
  </div>
  <div class="card">
    <div class="card-title">Factores de Conversión de Área</div>
    <table>
      <thead>
        <tr><th>Unidad</th><th>Factor → ha</th></tr>
      </thead>
      <tbody>
        <tr><td>Hectárea (ha)</td><td>1.000000</td></tr>
        <tr><td>Manzana</td><td>0.698700</td></tr>
        <tr><td>Acre</td><td>0.404686</td></tr>
        <tr><td>m²</td><td>0.000100</td></tr>
        <tr><td>Cuerda</td><td>0.392900</td></tr>
      </tbody>
    </table>
  </div>
  <div class="card">
    <div class="card-title">Factores de Conversión — Volumen completo</div>
    <table>
      <thead>
        <tr><th>Unidad</th><th>Factor → mL</th></tr>
      </thead>
      <tbody>
        <tr><td>mL</td><td>1.0000</td></tr>
        <tr><td>L</td><td>1000.0000</td></tr>
        <tr><td>cc (cm³)</td><td>1.0000</td></tr>
        <tr><td>fl oz</td><td>29.5735</td></tr>
        <tr><td>galón (gal)</td><td>3785.4100</td></tr>
        <tr><td>cuarto (qt)</td><td>946.3530</td></tr>
        <tr><td>pinta (pt)</td><td>473.1760</td></tr>
        <tr><td>taza (cup)</td><td>236.5880</td></tr>
      </tbody>
    </table>
  </div>
</div>

<script>
function fmt(n) {
  if (!isFinite(n)) return '—';
  if (n === 0) return '0';
  if (Math.abs(n) >= 1000) return n.toLocaleString('es', {maximumFractionDigits: 2});
  if (Math.abs(n) >= 10)   return n.toFixed(2);
  if (Math.abs(n) >= 1)    return n.toFixed(4);
  return n.toFixed(6).replace(/0+$/, '').replace(/\.$/, '');
}

function ceiling(n) { return Math.ceil(n); }

function calc() {
  // ── Inputs ──────────────────────────────────────────────────────────────
  const C4  = parseFloat(document.getElementById('area').value) || 0;       // Área a tratar (número)
  const E4f = parseFloat(document.getElementById('areaUnit').value);         // factor E4 → ha
  const C5  = parseFloat(document.getElementById('areaTotal').value) || 0;  // Área total
  const C8  = parseFloat(document.getElementById('dosis').value) || 0;      // Dosis recomendada
  const C9f = parseFloat(document.getElementById('dosisUnit').value);        // VLOOKUP(C9) → mL factor
  const C10f= parseFloat(document.getElementById('dosisAreaUnit').value);    // VLOOKUP(C10) → ha factor
  const C13 = parseFloat(document.getElementById('agua').value) || 0;       // Agua por área
  const E13f= parseFloat(document.getElementById('aguaUnit').value);         // VLOOKUP(E13) mL factor
  const C14 = parseFloat(document.getElementById('bomba').value) || 1;      // Cap. bomba
  const E14f= parseFloat(document.getElementById('bombaUnit').value);        // VLOOKUP(E14) mL factor

  // ── Traducción exacta de fórmulas Excel ─────────────────────────────────
  // C18: =C8 * VLOOKUP(C9) / VLOOKUP(C10)
  const C18 = C8 * C9f / C10f;                          // Dosis en mL/ha
  // C19: =C8 * VLOOKUP(C9) / 1000 / VLOOKUP(C10)
  const C19 = C8 * C9f / 1000 / C10f;                   // Dosis en L/ha
  // C20: =C4 * VLOOKUP(E4)
  const C20 = C4 * E4f;                                  // Área a tratar en ha
  // C21: =C8*VLOOKUP(C9)/VLOOKUP(C10) * C4*VLOOKUP(E4)
  const C21 = C8 * C9f / C10f * C4 * E4f;               // Producto TOTAL mL
  // C22: =C21 / 1000
  const C22 = C8 * C9f / 1000 / C10f * C4 * E4f;        // Producto TOTAL L

  // C25: =C13 * VLOOKUP(E13,mL) / 1000 * C4 * VLOOKUP(E4)
  const C25 = C13 * E13f / 1000 * C4 * E4f;             // Agua total (L)

  // numBombas = CEILING( (C13*VLOOKUP(E13)*C4*VLOOKUP(E4)) / (C14*VLOOKUP(E14)) , 1 )
  const _bombaDenom = C14 * E14f;
  const _bombaNum   = C13 * E13f * C4 * E4f;
  const C26 = ceiling(_bombaNum / _bombaDenom);           // Número de bombas

  // C27: =C4*VLOOKUP(E4) / C26
  const C27 = C26 > 0 ? C20 / C26 : 0;                  // Área por bomba (ha)

  // C28: =C14 * VLOOKUP(E14) / 1000
  const C28 = C14 * E14f / 1000;                         // Agua por bomba (L)

  // C29: =C21 / C26   (producto total mL / num bombas)
  const C29 = C26 > 0 ? C21 / C26 : 0;                  // Producto/bomba mL
  const C30 = C26 > 0 ? C22 / C26 : 0;                  // Producto/bomba L
  const C31 = C29;                                        // cc = mL
  const C32 = C26 > 0 ? C21 / 29.5735 / C26 : 0;       // fl oz
  const C33 = C26 > 0 ? C21 / 3785.41 / C26 : 0;       // galón

  document.getElementById('r-dosis-ml').textContent    = fmt(C18);
  document.getElementById('r-dosis-l').textContent     = fmt(C19);
  document.getElementById('r-area-ha').textContent     = fmt(C20);
  document.getElementById('r-prod-ml').textContent     = fmt(C21);
  document.getElementById('r-prod-l').textContent      = fmt(C22);
  document.getElementById('r-agua-total').textContent  = fmt(C25);
  document.getElementById('r-num-bombas').textContent  = fmt(C26);
  document.getElementById('r-area-bomba').textContent  = fmt(C27);
  document.getElementById('r-agua-bomba').textContent  = fmt(C28);
  document.getElementById('r-prod-bomba-ml').textContent   = fmt(C29);
  document.getElementById('r-prod-bomba-l').textContent    = fmt(C30);
  document.getElementById('r-prod-bomba-cc').textContent   = fmt(C31);
  document.getElementById('r-prod-bomba-floz').textContent = fmt(C32);
  document.getElementById('r-prod-bomba-gal').textContent  = fmt(C33);
}

// Volume unit factors → mL
const volFactors = {
  'mL': 1, 'L': 1000, 'cc (cm³)': 1, 'fl oz': 29.5735,
  'galón (gal)': 3785.41, 'cuarto (qt)': 946.353,
  'pinta (pt)': 473.176, 'taza (cup)': 236.588
};
const volNames = Object.keys(volFactors);

function convert() {
  const val    = parseFloat(document.getElementById('conv-val').value) || 0;
  const fromF  = parseFloat(document.getElementById('conv-from').value);
  const toF    = parseFloat(document.getElementById('conv-to').value);
  const toSel  = document.getElementById('conv-to');
  const toLabel = toSel.options[toSel.selectedIndex].text;

  const valML = val * fromF;
  const result = valML / toF;
  document.getElementById('conv-result').textContent = fmt(result);
  document.getElementById('conv-unit-label').textContent = toLabel;

  // All equivalences
  const eq = document.getElementById('all-equiv');
  eq.innerHTML = volNames.map(name => {
    const v = valML / volFactors[name];
    return `<div class="equiv-item">
      <div class="equiv-unit">${name}</div>
      <div class="equiv-val">${fmt(v)}</div>
    </div>`;
  }).join('');
}

function showTab(id) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.getElementById('tab-' + id).classList.add('active');
  event.target.classList.add('active');
}

// Init
calc();
convert();
</script>
</body>
</html>
