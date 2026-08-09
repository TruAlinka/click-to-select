// Simple prototype: click-to-select matching game with editor + iframe config generation

const langSelect = document.getElementById('lang');
const pairsInput = document.getElementById('pairsInput');
const previewBtn = document.getElementById('previewBtn');
const generateBtn = document.getElementById('generateBtn');
const startBtn = document.getElementById('startBtn');
const bgType = document.getElementById('bgType');
const color1 = document.getElementById('color1');
const color2 = document.getElementById('color2');
const stage = document.getElementById('stage');
const gameArea = document.getElementById('gameArea');
const embedCode = document.getElementById('embedCode');
const iframeW = document.getElementById('iframeW');
const iframeH = document.getElementById('iframeH');

// translations (minimal)
const I18N = {
  en: {
    title: 'Matching Game Editor',
    editor_heading: 'Editor',
    pairs_label: 'Pairs (one per line, A - B)',
    pairs_hint: "Each line: A and B separated by a tab, ' - ', '|' or '↔'. A or B can be text or image URL.",
    background: 'Background',
    primary_color: 'Primary',
    secondary_color: 'Secondary',
    iframe_size: 'Iframe size',
    preview: 'Preview',
    generate_iframe: 'Generate iframe',
    start: 'Start',
    preview_heading: 'Preview / Game',
    embed_code: 'Embed code'
  },
  ru: {
    title: '\u0420\u0435\u0434\u0430\u043a\u0442\u043e\u0440 \u0438\u0433\u0440\u044b: \u043f\u043e\u0434\u043e\u0431\u0440\u0430\u0442\u044c \u043f\u0430\u0440\u044b',
    editor_heading: '\u0420\u0435\u0434\u0430\u043a\u0442\u043e\u0440',
    pairs_label: '\u0417\u0430\u0434\u0430\u043d\u0438\u044f: \u043f\u0430\u0440\u044b (\u043f\u043e \u043e\u0434\u043d\u043e\u0439 \u043d\u0430 \u0441\u0442\u0440\u043e\u043a\u0435)',
    pairs_hint: '\u041a\u0430\u0436\u0434\u0430\u044f \u0441\u0442\u0440\u043e\u043a\u0430: A \u0438 B, \u0440\u0430\u0437\u0434\u0435\u043b\u0451\u043d\u043d\u044b\u0435 \u0447\u0435\u0440\u0435\u0437 \u0442\u0430\u0431\u0443, ' - ', '|' \u0438\u043b\u0438 '↔'. A \u0438\u043b\u0438 B \u043c\u043e\u0433\u0443\u0442 \u0431\u044b\u0442\u044c \u0442\u0435\u043a\u0441\u0442\u043e\u043c \u0438\u043b\u0438 \u0441\u0441\u044b\u043b\u043a\u043e\u0439'.
  }
};

function t(key){
  const l = langSelect.value || 'en';
  return (I18N[l] && I18N[l][key]) || I18N['en'][key] || key;
}

function applyTranslations(){
  document.querySelectorAll('[data-i18n]').forEach(el=>{
    const k = el.getAttribute('data-i18n');
    el.textContent = t(k);
  });
  document.title = t('title');
}
langSelect.addEventListener('change', applyTranslations);
applyTranslations();

// parsing pairs input into pairs array
function isImageUrl(u){
  try{ const url = new URL(u); return /\.(png|jpg|jpeg|gif|webp|svg)(\?|$)/i.test(url.pathname); }catch(e){return false}
}

function parsePairs(text){
  const lines = text.split('\n').map(s=>s.trim()).filter(Boolean);
  const sep = /\t|\s*-\s*|\s*\|\s*|↔|→|—/;
  const pairs = [];
  let id = 0;
  for(const line of lines){
    const parts = line.split(sep);
    if(parts.length < 2) continue; // skip invalid lines
    const leftRaw = parts.shift().trim();
    const rightRaw = parts.join(' - ').trim();
    pairs.push({
      id: id++,
      left: { raw: leftRaw, isImage: isImageUrl(leftRaw), id: id-1 },
      right: { raw: rightRaw, isImage: isImageUrl(rightRaw), id: id-1 }
    });
  }
  return pairs;
}

// build preview/game view
let state = {pairs:[], left:[], right:[], selected:null, matches:[]};

function renderPreview(){
  // background
  document.documentElement.style.setProperty('--bg1', color1.value);
  document.documentElement.style.setProperty('--bg2', color2.value);
  stage.className = 'stage';
  // clear possible overlays
  Array.from(stage.querySelectorAll('.glitch, .mist')).forEach(n=>n.remove());
  stage.style.filter = '';

  if(bgType.value === 'transparent'){
    stage.style.background = 'transparent';
  }else if(bgType.value === 'blur'){
    stage.style.filter = 'blur(4px)';
  }else if(bgType.value === 'glitch'){
    const g = document.createElement('div'); g.className='glitch'; stage.appendChild(g);
  }else if(bgType.value === 'mist'){
    const m = document.createElement('div'); m.className='mist'; stage.appendChild(m);
  }else{
    stage.style.background = `linear-gradient(135deg, ${color1.value}, ${color2.value})`;
  }

  gameArea.innerHTML = '';
  const leftCol = document.createElement('div'); leftCol.className='col-list';
  const rightCol = document.createElement('div'); rightCol.className='col-list';

  state.left.forEach((it, i)=>{
    const c = makeCard(it, 'L', i);
    leftCol.appendChild(c);
  });
  state.right.forEach((it, i)=>{
    const c = makeCard(it, 'R', i);
    rightCol.appendChild(c);
  });
  gameArea.appendChild(leftCol);
  gameArea.appendChild(rightCol);
}

function makeCard(item, side, idx){
  const card = document.createElement('div'); card.className='card';
  card.dataset.side = side; card.dataset.index = idx; card.dataset.pairId = item.id;
  if(item.isImage){
    const img = document.createElement('img'); img.src = item.raw; card.appendChild(img);
    const span = document.createElement('span'); span.textContent = item.raw.split('/').pop(); card.appendChild(span);
  }else{
    const span = document.createElement('span'); span.textContent = item.raw; card.appendChild(span);
  }
  card.addEventListener('click', ()=>onCardClick(card));
  return card;
}

function onCardClick(card){
  if(card.classList.contains('matched')) return;
  if(state.selected == null){
    state.selected = card;
    card.style.outline = '2px solid #f59e0b';
    return;
  }
  const a = state.selected; const b = card;
  if(a === b){ a.style.outline=''; state.selected=null; return; }
  const la = a.dataset.side; const lb = b.dataset.side;
  if(la === lb){ // same side selected -> switch selection
    a.style.outline=''; state.selected=b; b.style.outline='2px solid #f59e0b'; return;
  }
  const leftCard = la === 'L' ? a : b; const rightCard = la==='L'? b : a;
  const leftId = leftCard.dataset.pairId; const rightId = rightCard.dataset.pairId;
  const match = (leftId === rightId);
  if(match){
    [leftCard,rightCard].forEach(c=>{c.classList.add('matched'); c.style.background='#d1fae5'; c.style.outline=''; c.classList.add('correct');});
    state.matches.push([Number(leftCard.dataset.index), Number(rightCard.dataset.index)]);
  }else{
    [leftCard,rightCard].forEach(c=>{c.style.outline='2px solid #ef4444'; setTimeout(()=>c.style.outline='',400)});
  }
  state.selected = null;
}

previewBtn.addEventListener('click', ()=>{
  const pairs = parsePairs(pairsInput.value);
  state.pairs = pairs;
  state.left = pairs.map(p=>({ raw: p.left.raw, isImage: p.left.isImage, id: p.left.id }));
  // shuffle right while keeping pair ids
  state.right = shuffleArray(pairs.map(p=>({ raw: p.right.raw, isImage: p.right.isImage, id: p.right.id })));
  state.matches = [];
  state.selected = null;
  renderPreview();
});

startBtn.addEventListener('click', ()=>{
  const cfg = buildConfig();
  const s = encodeURIComponent(btoa(JSON.stringify(cfg)));
  const url = `${location.origin}${location.pathname}?game=${s}`;
  window.open(url, '_blank');
});

generateBtn.addEventListener('click', ()=>{
  const cfg = buildConfig();
  const s = encodeURIComponent(btoa(JSON.stringify(cfg)));
  const url = `${location.origin}${location.pathname}?game=${s}`;
  const w = iframeW.value || 800; const h = iframeH.value || 600;
  const code = `<iframe src=\"${url}\" width=\"${w}\" height=\"${h}\" frameborder=\"0\" allowfullscreen></iframe>`;
  embedCode.value = code;
});

function buildConfig(){
  // send pairs array to config
  const pairs = parsePairs(pairsInput.value);
  return { pairs: pairs, bgType: bgType.value, color1: color1.value, color2: color2.value, lang: langSelect.value };
}

// on load: if ?game= present -> run game mode
function loadFromUrl(){
  const params = new URLSearchParams(location.search);
  if(params.has('game')){
    try{
      const s = params.get('game');
      const json = JSON.parse(atob(decodeURIComponent(s)));
      // support both old {left,right} and new {pairs}
      let pairs = [];
      if(json.pairs && Array.isArray(json.pairs)){
        pairs = json.pairs.map(p=>({ id: p.id, left: { raw: p.left.raw, isImage: p.left.isImage, id: p.left.id }, right: { raw: p.right.raw, isImage: p.right.isImage, id: p.right.id } }));
      }else if(json.left && json.right){
        pairs = json.left.map((l,i)=>({ id:i, left: { raw: l.raw || l, isImage: l.isImage || isImageUrl(l.raw||l), id:i }, right: { raw: (json.right[i] && (json.right[i].raw||json.right[i])) || '', isImage: (json.right[i] && (json.right[i].isImage)) || isImageUrl((json.right[i] && (json.right[i].raw||json.right[i]))||''), id:i } }));
      }
      state.pairs = pairs;
      state.left = pairs.map(p=>({ raw: p.left.raw, isImage: p.left.isImage, id: p.left.id }));
      state.right = shuffleArray(pairs.map(p=>({ raw: p.right.raw, isImage: p.right.isImage, id: p.right.id })));
      state.selected = null; state.matches=[];
      // remove editor UI
      document.querySelector('.editor').style.display='none';
      document.getElementById('title').textContent = 'Matching Game';
      // apply colors
      color1.value = json.color1 || color1.value; color2.value = json.color2 || color2.value; bgType.value = json.bgType || bgType.value;
      renderPreview();
    }catch(e){console.error('bad game config', e)}
  }
}
loadFromUrl();

// utils
function shuffleArray(a){
  const arr = a.slice();
  for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; }
  return arr;
}
