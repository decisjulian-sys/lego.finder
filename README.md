# lego.finder<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Où est ma pièce ?">
<title>Où est ma pièce ?</title>
<style>
  :root {
    --bg: #0F1115;
    --surface: #1A1D24;
    --surface2: #17181C;
    --border: #2E323C;
    --text: #F4F1E8;
    --muted: #9AA1B4;
    --dim: #565D72;
    --yellow: #FFD500;
    --red: #D0111B;
    --blue: #0057A6;
  }
  * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body { margin: 0; padding: 0; background: var(--bg); overscroll-behavior-y: none; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    color: var(--text);
    padding-bottom: 100px;
    background-image: radial-gradient(circle, rgba(255,255,255,0.04) 1.5px, transparent 1.5px);
    background-size: 22px 22px;
  }
  .mono { font-family: ui-monospace, "SF Mono", Menlo, monospace; }
  header { padding: 32px 20px 20px; }
  .dots { display: flex; gap: 4px; margin-bottom: 6px; }
  .dot { width: 12px; height: 12px; border-radius: 50%; }
  .eyebrow { font-size: 12px; letter-spacing: 0.15em; text-transform: uppercase; color: var(--muted); }
  h1 { font-size: 28px; font-weight: 700; margin: 4px 0 2px; }
  .sub { font-size: 14px; color: var(--muted); margin: 0; }

  .search-wrap { padding: 0 20px 22px; }
  .search-box { display: flex; align-items: center; gap: 10px; background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 14px 16px; }
  .search-box input { flex: 1; background: transparent; border: none; outline: none; color: var(--text); font-size: 16px; }
  .search-box input::placeholder { color: var(--dim); }
  .search-status { font-size: 12px; margin: 8px 4px 0; }

  .empty { margin: 0 20px; background: var(--surface); border: 1px dashed var(--border); border-radius: 16px; padding: 40px 20px; text-align: center; color: var(--muted); }

  .cabinets { padding: 0 20px; display: flex; flex-direction: column; gap: 28px; }
  .cab-header { display: flex; justify-content: space-between; align-items: center; padding: 0 4px 8px; }
  .cab-name { font-weight: 600; font-size: 14px; }
  .cab-edit { background: none; border: none; color: var(--muted); font-size: 12px; display: flex; align-items: center; gap: 4px; cursor: pointer; }

  .cab-body { border-radius: 10px; padding: 10px; background: linear-gradient(160deg, #24262B 0%, #17181C 100%); border: 1px solid #34363C; box-shadow: 0 10px 26px rgba(0,0,0,0.45); }
  .cab-notches { display: flex; justify-content: space-between; padding: 0 12px; margin-bottom: 8px; }
  .notch { width: 32px; height: 8px; border-radius: 0 0 6px 6px; background: #0F1012; }
  .grid { display: grid; gap: 3px; }
  .drawer { position: relative; border-radius: 3px; text-align: left; padding: 7px 5px 6px; min-height: 44px; border: 1px solid rgba(255,255,255,0.08); border-top: 1px solid rgba(255,255,255,0.14); background: linear-gradient(160deg, rgba(200,208,216,0.07) 0%, rgba(160,168,178,0.03) 100%); cursor: pointer; transition: opacity .2s; }
  .drawer.filled { background: linear-gradient(160deg, rgba(200,208,216,0.16) 0%, rgba(160,168,178,0.08) 100%); }
  .drawer .strip { position: absolute; top: 0; left: 0; right: 0; height: 3px; border-radius: 3px 3px 0 0; display: flex; overflow: hidden; }
  .drawer .handle { margin: 0 auto 6px; width: 55%; height: 4px; border-radius: 999px; background: rgba(20,21,24,0.7); }
  .drawer .count { display: flex; justify-content: center; font-size: 10px; color: var(--dim); }
  .drawer.filled .count { color: #E8E4D8; }
  .drawer.dim { opacity: 0.28; }
  @keyframes popOut { 0%, 100% { transform: translateY(-5px) scale(1.04); } 50% { transform: translateY(-9px) scale(1.06); } }
  .drawer.open { animation: popOut 1.3s ease-in-out infinite; box-shadow: 0 12px 22px rgba(0,0,0,0.5), 0 0 0 2px var(--yellow), 0 0 18px 3px rgba(255,213,0,0.5); z-index: 5; }
  .nameplate { margin-top: 10px; width: 66%; margin-left: auto; margin-right: auto; border-radius: 3px; padding: 4px 0; text-align: center; background: #0F1012; }
  .nameplate span { font-size: 9px; letter-spacing: 0.2em; text-transform: uppercase; color: var(--dim); }

  .add-cab-btn { margin: 24px 20px 0; display: flex; align-items: center; justify-content: center; gap: 8px; padding: 16px; border-radius: 16px; background: var(--surface); border: 1px dashed var(--border); color: var(--muted); font-weight: 500; cursor: pointer; width: calc(100% - 40px); font-size: 15px; }

  .fab { position: fixed; bottom: 24px; right: 20px; display: flex; align-items: center; gap: 8px; padding: 14px 20px; border-radius: 999px; background: var(--yellow); color: #1B1F2A; font-weight: 700; border: none; box-shadow: 0 8px 20px rgba(0,0,0,0.4); cursor: pointer; font-size: 15px; }

  .overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.6); display: flex; align-items: flex-end; justify-content: center; z-index: 50; }
  .sheet { width: 100%; max-width: 480px; max-height: 88vh; overflow-y: auto; background: var(--surface); border: 1px solid var(--border); border-radius: 24px 24px 0 0; padding: 20px 20px 32px; }
  .sheet-title { display: flex; align-items: center; justify-content: space-between; margin-bottom: 18px; }
  .sheet-title h2 { font-size: 18px; font-weight: 700; margin: 0; }
  .sheet-title button, .back-btn { background: none; border: none; color: var(--muted); cursor: pointer; padding: 4px; }
  .row-title { display: flex; align-items: center; gap: 8px; margin-bottom: 16px; }

  label.field { display: block; margin-bottom: 14px; }
  label.field span { display: block; font-size: 11px; text-transform: uppercase; letter-spacing: 0.05em; color: var(--muted); margin-bottom: 6px; }
  input, select, textarea { width: 100%; background: var(--bg); border: 1px solid var(--border); border-radius: 10px; padding: 10px 12px; color: var(--text); font-size: 15px; outline: none; font-family: inherit; }
  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .hint { font-size: 11px; color: var(--dim); margin: -8px 0 14px 2px; }

  .btn-row { display: flex; gap: 12px; margin-top: 8px; }
  .btn-primary { flex: 1; border-radius: 12px; padding: 13px; background: var(--yellow); color: #1B1F2A; font-weight: 700; border: none; font-size: 15px; cursor: pointer; }
  .btn-danger { display: flex; align-items: center; justify-content: center; gap: 8px; border-radius: 12px; padding: 13px 16px; background: transparent; border: 1px solid var(--red); color: var(--red); font-weight: 500; font-size: 14px; cursor: pointer; }

  .piece-item { background: var(--surface2); border: 1px solid var(--border); border-radius: 12px; padding: 12px 14px; display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 10px; }
  .piece-name { display: flex; align-items: center; gap: 8px; font-weight: 600; font-size: 14px; }
  .cat-dot { width: 8px; height: 8px; border-radius: 50%; flex-shrink: 0; }
  .piece-meta { font-size: 12px; color: var(--muted); margin-top: 4px; padding-left: 16px; }
  .piece-actions { display: flex; gap: 10px; }
  .piece-actions button { background: none; border: none; color: var(--muted); cursor: pointer; }
  .icon { width: 18px; height: 18px; display: inline-block; vertical-align: middle; }
</style>
</head>
<body>

<header>
  <div class="dots">
    <div class="dot" style="background:#FFD500"></div>
    <div class="dot" style="background:#D0111B"></div>
    <div class="dot" style="background:#0057A6"></div>
  </div>
  <div class="eyebrow">Inventaire</div>
  <h1>Où est ma pièce ?</h1>
  <p class="sub mono" id="subCount">0 armoire · 0 pièce</p>
</header>

<div class="search-wrap">
  <div class="search-box">
    <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="#9AA1B4" stroke-width="2"><circle cx="11" cy="11" r="8"/><path d="M21 21l-4.35-4.35"/></svg>
    <input id="searchInput" placeholder="Ex : roue, plaque bleue, antenne..." autocomplete="off">
    <button id="clearSearch" style="display:none;background:none;border:none;cursor:pointer;">
      <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="#9AA1B4" stroke-width="2"><path d="M18 6L6 18M6 6l12 12"/></svg>
    </button>
  </div>
  <p class="search-status mono" id="searchStatus"></p>
</div>

<div id="emptyState" class="empty" style="display:none;">Aucune armoire pour l'instant.<br>Crée ta première armoire pour commencer.</div>

<div class="cabinets" id="cabinets"></div>

<button class="add-cab-btn" id="addArmoireBtn">+ Ajouter une armoire</button>

<button class="fab" id="fabAddPiece" style="display:none;">
  <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="#1B1F2A" stroke-width="2.5"><path d="M12 5v14M5 12h14"/></svg>
  Pièce
</button>

<div id="modalRoot"></div>

<script>
const CATEGORY_COLORS = {
  'Briques': '#D0111B', 'Plaques': '#FFD500', 'Tuiles': '#0057A6', 'Technic': '#6B7280',
  'Roues': '#1A1A1A', 'Minifigs': '#00852B', 'Pentes': '#F97316', 'Accessoires': '#9333EA', 'Autres': '#64748B'
};
const CATS = Object.keys(CATEGORY_COLORS);
const colorFor = c => CATEGORY_COLORS[c] || CATEGORY_COLORS['Autres'];

let armoires = JSON.parse(localStorage.getItem('lego_armoires') || '[]');
let pieces = JSON.parse(localStorage.getItem('lego_pieces') || '[]');
let query = '';
let openDrawerState = null; // {armoireId, tiroir}

function saveArmoires() { localStorage.setItem('lego_armoires', JSON.stringify(armoires)); }
function savePieces() { localStorage.setItem('lego_pieces', JSON.stringify(pieces)); }
function uid() { return Date.now().toString() + Math.random().toString(36).slice(2,7); }

function labelFor(a, tiroir) {
  const cols = a.cols || 1;
  const idx = Number(tiroir) - 1;
  const row = Math.floor(idx / cols) + 1;
  const col = (idx % cols) + 1;
  return `Rangée ${row}, col. ${col}`;
}
function piecesInDrawer(armoireId, tiroir) {
  return pieces.filter(p => p.armoireId === armoireId && String(p.tiroir) === String(tiroir));
}
function categoriesInDrawer(armoireId, tiroir) {
  return [...new Set(piecesInDrawer(armoireId, tiroir).map(p => p.category))];
}
function matchingPieces() {
  if (!query.trim()) return [];
  const q = query.trim().toLowerCase();
  return pieces.filter(p =>
    p.name.toLowerCase().includes(q) ||
    p.category.toLowerCase().includes(q) ||
    (p.color||'').toLowerCase().includes(q) ||
    (p.notes||'').toLowerCase().includes(q)
  );
}
function matchingKeys() {
  return new Set(matchingPieces().map(p => `${p.armoireId}::${p.tiroir}`));
}

function render() {
  document.getElementById('subCount').textContent =
    `${armoires.length} armoire${armoires.length!==1?'s':''} · ${pieces.length} pièce${pieces.length!==1?'s':''}`;

  document.getElementById('emptyState').style.display = armoires.length === 0 ? 'block' : 'none';
  document.getElementById('fabAddPiece').style.display = armoires.length > 0 ? 'flex' : 'none';

  const mk = matchingKeys();
  const mp = matchingPieces();
  const statusEl = document.getElementById('searchStatus');
  if (query.trim()) {
    statusEl.style.color = mp.length ? '#FFD500' : '#9AA1B4';
    statusEl.textContent = mp.length === 0
      ? `Aucune pièce ne correspond à « ${query} »`
      : `${mp.length} résultat${mp.length>1?'s':''} — le tiroir s'ouvre ci-dessous`;
  } else statusEl.textContent = '';

  document.getElementById('clearSearch').style.display = query ? 'block' : 'none';

  const cabWrap = document.getElementById('cabinets');
  cabWrap.innerHTML = '';
  armoires.forEach(a => cabWrap.appendChild(renderCabinet(a, mk)));

  if (query.trim() && mp.length) {
    const key = `${mp[0].armoireId}::${mp[0].tiroir}`;
    const el = document.querySelector(`[data-key="${key}"]`);
    if (el) setTimeout(() => el.scrollIntoView({behavior:'smooth', block:'center'}), 50);
  }
}

function renderCabinet(a, matchSet) {
  const wrap = document.createElement('div');
  const cols = a.cols || 1;
  const total = cols * a.rows;

  const header = document.createElement('div');
  header.className = 'cab-header';
  header.innerHTML = `<span class="cab-name">${escapeHtml(a.name)}</span>
    <button class="cab-edit">✎ Modifier</button>`;
  header.querySelector('.cab-edit').onclick = () => openArmoireForm(a);
  wrap.appendChild(header);

  const body = document.createElement('div');
  body.className = 'cab-body';
  body.innerHTML = `<div class="cab-notches"><div class="notch"></div><div class="notch"></div></div>`;

  const grid = document.createElement('div');
  grid.className = 'grid';
  grid.style.gridTemplateColumns = `repeat(${cols}, 1fr)`;

  for (let i = 1; i <= total; i++) {
    const count = piecesInDrawer(a.id, i).length;
    const cats = categoriesInDrawer(a.id, i);
    const key = `${a.id}::${i}`;
    const isMatch = matchSet.has(key);
    const dim = query.trim() && !isMatch;

    const d = document.createElement('button');
    d.className = 'drawer' + (count>0?' filled':'') + (isMatch?' open':'') + (dim?' dim':'');
    d.setAttribute('data-key', key);
    d.title = `Tiroir ${i}`;
    d.onclick = () => openDrawer(a.id, i);

    let stripHtml = '';
    if (cats.length) {
      stripHtml = `<div class="strip">${cats.slice(0,4).map(c=>`<div style="background:${colorFor(c)};flex:1"></div>`).join('')}</div>`;
    }
    d.innerHTML = `${stripHtml}<div class="handle"></div><div class="count">${count>0?count:''}</div>`;
    grid.appendChild(d);
  }
  body.appendChild(grid);

  const plate = document.createElement('div');
  plate.className = 'nameplate';
  plate.innerHTML = `<span>${escapeHtml(a.name)}</span>`;
  body.appendChild(plate);

  wrap.appendChild(body);
  return wrap;
}

function escapeHtml(s) {
  const d = document.createElement('div'); d.textContent = s ?? ''; return d.innerHTML;
}

function showModal(html, onMount) {
  const root = document.getElementById('modalRoot');
  root.innerHTML = `<div class="overlay" id="ov"><div class="sheet" id="sheetInner">${html}</div></div>`;
  document.getElementById('ov').onclick = (e) => { if (e.target.id === 'ov') closeModal(); };
  if (onMount) onMount(document.getElementById('sheetInner'));
}
function closeModal() { document.getElementById('modalRoot').innerHTML = ''; openDrawerState = null; }

function openDrawer(armoireId, tiroir) {
  openDrawerState = {armoireId, tiroir};
  renderDrawerModal();
}
function renderDrawerModal() {
  const {armoireId, tiroir} = openDrawerState;
  const a = armoires.find(x=>x.id===armoireId);
  const list = piecesInDrawer(armoireId, tiroir);
  const itemsHtml = list.length ? list.map(p => `
    <div class="piece-item">
      <div style="min-width:0">
        <div class="piece-name"><span class="cat-dot" style="background:${colorFor(p.category)}"></span>${escapeHtml(p.name)}</div>
        ${(p.color||p.quantity||p.notes) ? `<div class="piece-meta">${[p.color, p.quantity?('x'+p.quantity):null, p.notes].filter(Boolean).map(escapeHtml).join(' · ')}</div>` : ''}
      </div>
      <div class="piece-actions">
        <button data-edit="${p.id}">✎</button>
        <button data-del="${p.id}">🗑</button>
      </div>
    </div>`).join('') : `<p class="mono" style="text-align:center;color:#9AA1B4;padding:16px 0;">Ce tiroir est vide.</p>`;

  showModal(`
    <div class="row-title">
      <button class="back-btn" id="backBtn">←</button>
      <div>
        <h2 style="margin:0;font-size:17px;font-weight:700;">${escapeHtml(a.name)} · ${labelFor(a, tiroir)}</h2>
        <p class="mono" style="margin:2px 0 0;font-size:12px;color:#9AA1B4;">${list.length} pièce(s)</p>
      </div>
    </div>
    <div id="drawerItems">${itemsHtml}</div>
    <button class="btn-primary" id="addPieceHereBtn" style="width:100%;display:flex;align-items:center;justify-content:center;gap:8px;">+ Ajouter une pièce ici</button>
  `, (root) => {
    root.querySelector('#backBtn').onclick = closeModal;
    root.querySelector('#addPieceHereBtn').onclick = () => openPieceForm(null, armoireId, tiroir);
    root.querySelectorAll('[data-edit]').forEach(btn => {
      btn.onclick = () => { const p = pieces.find(x=>x.id===btn.dataset.edit); openPieceForm(p); };
    });
    root.querySelectorAll('[data-del]').forEach(btn => {
      btn.onclick = () => { pieces = pieces.filter(x=>x.id!==btn.dataset.del); savePieces(); renderDrawerModal(); render(); };
    });
  });
}

function openPieceForm(existing, defaultArmoireId, defaultTiroir) {
  const editing = !!existing;
  const armoireId = existing?.armoireId || defaultArmoireId || armoires[0]?.id || '';
  const tiroir = existing?.tiroir || defaultTiroir || '1';

  const armoireOptions = armoires.map(a => `<option value="${a.id}" ${a.id===armoireId?'selected':''}>${escapeHtml(a.name)}</option>`).join('');
  const curArmoire = armoires.find(a=>a.id===armoireId);
  const total = curArmoire ? curArmoire.cols*curArmoire.rows : 1;
  const tiroirOptions = Array.from({length: total}).map((_,i) => {
    const t = i+1;
    return `<option value="${t}" ${String(t)===String(tiroir)?'selected':''}>${curArmoire?labelFor(curArmoire,t):'Tiroir '+t}</option>`;
  }).join('');

  showModal(`
    <div class="sheet-title"><h2>${editing?'Modifier la pièce':'Nouvelle pièce'}</h2><button id="closeBtn">✕</button></div>
    <form id="pieceForm">
      <label class="field"><span>Nom de la pièce</span><input id="f_name" required value="${escapeHtml(existing?.name||'')}" placeholder="Ex : Roue 18mm, Antenne 1x1..."></label>
      <label class="field"><span>Catégorie</span>
        <select id="f_category">${CATS.map(c=>`<option value="${c}" ${existing?.category===c?'selected':''}>${c}</option>`).join('')}</select>
      </label>
      <div class="grid2">
        <label class="field"><span>Armoire</span><select id="f_armoire">${armoireOptions}</select></label>
        <label class="field"><span>Tiroir</span><select id="f_tiroir">${tiroirOptions}</select></label>
      </div>
      <div class="grid2">
        <label class="field"><span>Couleur</span><input id="f_color" value="${escapeHtml(existing?.color||'')}" placeholder="Optionnel"></label>
        <label class="field"><span>Quantité</span><input id="f_qty" value="${escapeHtml(existing?.quantity||'')}" placeholder="Optionnel"></label>
      </div>
      <label class="field"><span>Notes</span><textarea id="f_notes" rows="2" placeholder="Optionnel">${escapeHtml(existing?.notes||'')}</textarea></label>
      <div class="btn-row">
        ${editing?'<button type="button" class="btn-danger" id="delPieceBtn">🗑 Supprimer</button>':''}
        <button type="submit" class="btn-primary">${editing?'Enregistrer':'Ajouter'}</button>
      </div>
    </form>
  `, (root) => {
    root.querySelector('#closeBtn').onclick = () => { openDrawerState ? renderDrawerModal() : closeModal(); };
    const armoireSel = root.querySelector('#f_armoire');
    armoireSel.onchange = () => {
      const a = armoires.find(x=>x.id===armoireSel.value);
      const tot = a ? a.cols*a.rows : 1;
      const tSel = root.querySelector('#f_tiroir');
      tSel.innerHTML = Array.from({length:tot}).map((_,i)=>{const t=i+1;return `<option value="${t}">${a?labelFor(a,t):'Tiroir '+t}</option>`;}).join('');
    };
    if (editing) {
      root.querySelector('#delPieceBtn').onclick = () => {
        pieces = pieces.filter(x=>x.id!==existing.id); savePieces();
        openDrawerState ? renderDrawerModal() : closeModal(); render();
      };
    }
    root.querySelector('#pieceForm').onsubmit = (e) => {
      e.preventDefault();
      const data = {
        name: root.querySelector('#f_name').value.trim(),
        category: root.querySelector('#f_category').value,
        armoireId: root.querySelector('#f_armoire').value,
        tiroir: root.querySelector('#f_tiroir').value,
        color: root.querySelector('#f_color').value.trim(),
        quantity: root.querySelector('#f_qty').value.trim(),
        notes: root.querySelector('#f_notes').value.trim(),
      };
      if (!data.name || !data.armoireId) return;
      if (editing) {
        pieces = pieces.map(p => p.id===existing.id ? {...data, id: existing.id} : p);
      } else {
        pieces.push({...data, id: uid()});
      }
      savePieces();
      if (openDrawerState) { openDrawerState = {armoireId: data.armoireId, tiroir: data.tiroir}; renderDrawerModal(); }
      else closeModal();
      render();
    };
  });
}

function openArmoireForm(existing) {
  const editing = !!existing;
  showModal(`
    <div class="sheet-title"><h2>${editing?"Modifier l'armoire":'Nouvelle armoire'}</h2><button id="closeBtn">✕</button></div>
    <form id="armForm">
      <label class="field"><span>Nom de l'armoire</span><input id="a_name" required value="${escapeHtml(existing?.name||'')}" placeholder="Ex : Armoire garage"></label>
      <div class="grid2">
        <label class="field"><span>Colonnes</span><input id="a_cols" type="number" min="1" max="10" required value="${existing?.cols||4}"></label>
        <label class="field"><span>Rangées</span><input id="a_rows" type="number" min="1" max="20" required value="${existing?.rows||8}"></label>
      </div>
      <p class="hint" id="a_total"></p>
      <div class="btn-row">
        ${editing?'<button type="button" class="btn-danger" id="delArmBtn">🗑 Supprimer</button>':''}
        <button type="submit" class="btn-primary">${editing?'Enregistrer':'Créer'}</button>
      </div>
    </form>
  `, (root) => {
    root.querySelector('#closeBtn').onclick = closeModal;
    const colsEl = root.querySelector('#a_cols'), rowsEl = root.querySelector('#a_rows'), totalEl = root.querySelector('#a_total');
    const updateTotal = () => totalEl.textContent = `= ${(Math.max(1,+colsEl.value||1))*(Math.max(1,+rowsEl.value||1))} tiroirs au total`;
    colsEl.oninput = updateTotal; rowsEl.oninput = updateTotal; updateTotal();
    if (editing) {
      root.querySelector('#delArmBtn').onclick = () => {
        armoires = armoires.filter(x=>x.id!==existing.id);
        pieces = pieces.filter(p=>p.armoireId!==existing.id);
        saveArmoires(); savePieces(); closeModal(); render();
      };
    }
    root.querySelector('#armForm').onsubmit = (e) => {
      e.preventDefault();
      const name = root.querySelector('#a_name').value.trim();
      if (!name) return;
      const cols = Math.max(1, Math.min(10, +colsEl.value||1));
      const rows = Math.max(1, Math.min(20, +rowsEl.value||1));
      if (editing) {
        armoires = armoires.map(a => a.id===existing.id ? {...a, name, cols, rows} : a);
      } else {
        armoires.push({id: uid(), name, cols, rows});
      }
      saveArmoires(); closeModal(); render();
    };
  });
}

document.getElementById('searchInput').addEventListener('input', (e) => { query = e.target.value; render(); });
document.getElementById('clearSearch').addEventListener('click', () => { query=''; document.getElementById('searchInput').value=''; render(); });
document.getElementById('addArmoireBtn').addEventListener('click', () => openArmoireForm(null));
document.getElementById('fabAddPiece').addEventListener('click', () => openPieceForm(null));

render();
</script>
</body>
</html>
