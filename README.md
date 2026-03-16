<!DOCTYPE html>
<html lang="cs">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover" />
  <meta name="apple-mobile-web-app-capable" content="yes" />
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
  <meta name="apple-mobile-web-app-title" content="Zpravodaj" />
  <meta name="theme-color" content="#0a0a0f" />
  <title>Ranní Zpravodaj</title>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700;900&family=Source+Serif+4:ital,wght@0,300;0,400;1,300&display=swap" rel="stylesheet" />
  <style>
    :root {
      --bg: #0a0a0f; --bg2: #111118; --border: #1e1e2e;
      --gold: #c4956a; --gold2: #a07040; --text: #e8e0d0;
      --muted: #7a7060; --muted2: #4a4438; --red: #e63946; --teal: #2a9d8f;
      --safe-top: env(safe-area-inset-top, 0px); --safe-bottom: env(safe-area-inset-bottom, 0px);
    }
    * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
    body { background: var(--bg); color: var(--text); font-family: 'Source Serif 4', Georgia, serif; min-height: 100vh; min-height: -webkit-fill-available; overflow-x: hidden; }
    #setup-screen { display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 100vh; padding: 2rem 1.5rem calc(2rem + var(--safe-bottom)); text-align: center; }
    #setup-screen .logo { font-family: 'Playfair Display', serif; font-size: 2.2rem; font-weight: 900; background: linear-gradient(135deg, #f5e6d0, #c4956a); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 0.4rem; line-height: 1.1; }
    #setup-screen .subtitle { font-size: 0.65rem; letter-spacing: 0.3em; color: var(--muted2); text-transform: uppercase; margin-bottom: 2.5rem; }
    .setup-card { background: var(--bg2); border: 1px solid var(--border); border-radius: 12px; padding: 1.75rem 1.5rem; width: 100%; max-width: 380px; text-align: left; }
    .setup-card label { display: block; font-size: 0.65rem; letter-spacing: 0.2em; text-transform: uppercase; color: var(--muted); margin-bottom: 0.6rem; }
    .setup-card input { width: 100%; background: #0d0d15; border: 1px solid var(--border); border-radius: 6px; color: var(--text); font-family: monospace; font-size: 0.8rem; padding: 0.75rem 1rem; outline: none; transition: border-color 0.2s; letter-spacing: 0.05em; }
    .setup-card input:focus { border-color: var(--gold); }
    .setup-hint { font-size: 0.72rem; color: var(--muted2); margin-top: 0.6rem; line-height: 1.5; }
    .setup-hint a { color: var(--gold); text-decoration: none; }
    .btn-primary { width: 100%; margin-top: 1.25rem; background: linear-gradient(135deg, var(--gold), var(--gold2)); border: none; border-radius: 6px; color: #fff; font-family: 'Playfair Display', serif; font-size: 0.85rem; font-weight: 700; letter-spacing: 0.1em; padding: 0.85rem; cursor: pointer; text-transform: uppercase; }
    #app-screen { display: none; }
    .header { background: rgba(10,10,15,0.95); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); border-bottom: 1px solid var(--border); padding: calc(var(--safe-top) + 1.25rem) 1.25rem 1.25rem; position: sticky; top: 0; z-index: 100; }
    .header-top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 1rem; }
    .app-title { font-family: 'Playfair Display', serif; font-size: 1.6rem; font-weight: 900; background: linear-gradient(135deg, #f5e6d0, #c4956a); -webkit-background-clip: text; -webkit-text-fill-color: transparent; line-height: 1.1; }
    .app-date { font-size: 0.6rem; letter-spacing: 0.15em; color: var(--muted2); text-transform: uppercase; margin-top: 0.15rem; }
    #clock { font-family: monospace; font-size: 1.5rem; color: var(--gold); font-weight: 300; letter-spacing: 0.05em; }
    .controls { display: flex; gap: 0.6rem; flex-wrap: wrap; }
    .btn { border: none; border-radius: 4px; font-size: 0.7rem; font-weight: 700; letter-spacing: 0.1em; padding: 0.55rem 1rem; cursor: pointer; text-transform: uppercase; font-family: inherit; transition: opacity 0.15s, transform 0.1s; white-space: nowrap; }
    .btn:active { transform: scale(0.96); opacity: 0.85; }
    .btn:disabled { opacity: 0.4; cursor: not-allowed; }
    .btn-load { background: linear-gradient(135deg, var(--gold), var(--gold2)); color: #fff; }
    .btn-play { background: linear-gradient(135deg, var(--teal), #1a7a6e); color: #fff; }
    .btn-stop { background: linear-gradient(135deg, var(--red), #b02a33); color: #fff; }
    .btn-reset { background: transparent; border: 1px solid var(--border); color: var(--muted); }
    .content { padding: 1.25rem 1.25rem calc(2rem + var(--safe-bottom)); max-width: 640px; margin: 0 auto; }
    .loading-wrap { display: flex; flex-direction: column; align-items: center; padding: 4rem 1rem; gap: 1.5rem; }
    .spinner { width: 44px; height: 44px; border: 2px solid var(--border); border-top-color: var(--gold); border-radius: 50%; animation: spin 0.9s linear infinite; }
    @keyframes spin { to { transform: rotate(360deg); } }
    .loading-text { font-size: 0.75rem; letter-spacing: 0.15em; color: var(--muted); text-transform: uppercase; text-align: center; }
    .error-box { background: #120808; border: 1px solid #4a1010; border-radius: 8px; padding: 1.25rem; color: var(--red); font-size: 0.82rem; line-height: 1.5; margin-top: 1rem; }
    .section-label { font-size: 0.6rem; letter-spacing: 0.25em; color: var(--muted2); text-transform: uppercase; padding-bottom: 0.75rem; border-bottom: 1px solid var(--border); margin-bottom: 0.5rem; }
    .news-item { display: flex; gap: 1rem; padding: 1rem 0; border-bottom: 1px solid #111118; cursor: pointer; border-left: 3px solid transparent; padding-left: 0.6rem; transition: background 0.15s, border-color 0.15s; border-radius: 0 4px 4px 0; -webkit-user-select: none; user-select: none; }
    .news-item.active { border-left-color: var(--gold); background: rgba(196,149,106,0.05); }
    .news-num { font-family: monospace; font-size: 1.4rem; font-weight: 700; color: #222230; line-height: 1; min-width: 2rem; padding-top: 0.15rem; }
    .news-body { flex: 1; }
    .news-meta { display: flex; align-items: center; gap: 0.5rem; margin-bottom: 0.35rem; flex-wrap: wrap; }
    .cat-badge { font-size: 0.55rem; font-weight: 700; letter-spacing: 0.12em; text-transform: uppercase; padding: 0.15rem 0.45rem; border-radius: 2px; color: #fff; }
    .reading-badge { font-size: 0.55rem; color: var(--gold); letter-spacing: 0.1em; text-transform: uppercase; animation: pulse 1s ease-in-out infinite; }
    @keyframes pulse { 0%,100% { opacity: 1; } 50% { opacity: 0.4; } }
    .news-title { font-family: 'Playfair Display', serif; font-size: 0.95rem; font-weight: 700; line-height: 1.3; margin-bottom: 0.35rem; color: var(--text); transition: color 0.15s; }
    .news-item.active .news-title { color: var(--gold); }
    .news-summary { font-size: 0.78rem; color: var(--muted); line-height: 1.6; font-style: italic; }
    .news-importance { font-family: monospace; font-size: 1.2rem; font-weight: 700; color: #222230; align-self: center; min-width: 1.5rem; text-align: right; }
    .empty-state { text-align: center; padding: 4rem 1rem; color: var(--muted2); }
    .empty-icon { font-size: 2.5rem; margin-bottom: 1rem; opacity: 0.4; }
    .empty-text { font-size: 0.82rem; letter-spacing: 0.05em; line-height: 1.6; }
    .progress-bar { height: 2px; background: var(--border); margin-top: 1rem; border-radius: 2px; overflow: hidden; }
    .progress-fill { height: 100%; background: linear-gradient(90deg, var(--gold), var(--teal)); border-radius: 2px; transition: width 0.4s ease; }
    .cat-svet { background: #e63946; } .cat-cesko { background: #2a9d8f; } .cat-ekonomika { background: #c79d30; }
    .cat-technologie { background: #4361ee; } .cat-veda { background: #7b2d8b; } .cat-sport { background: #f4a261; }
    .cat-kultura { background: #e76f51; } .cat-default { background: #556; }
    .footer { font-size: 0.58rem; letter-spacing: 0.2em; color: var(--muted2); text-align: center; text-transform: uppercase; margin-top: 2rem; padding-top: 1rem; border-top: 1px solid var(--border); }
  </style>
</head>
<body>
<div id="setup-screen">
  <div class="logo">Ranní Zpravodaj</div>
  <div class="subtitle">AI · Zprávy · Hlas</div>
  <div class="setup-card">
    <label>Anthropic API klíč</label>
    <input type="password" id="api-key-input" placeholder="sk-ant-..." autocomplete="off" />
    <p class="setup-hint">Klíč získáš zdarma na <a href="https://console.anthropic.com" target="_blank">console.anthropic.com</a> → API Keys → Create Key.<br/>Klíč se ukládá pouze v tvém prohlížeči.</p>
    <button class="btn-primary" onclick="saveKey()">Začít →</button>
  </div>
</div>
<div id="app-screen">
  <div class="header">
    <div class="header-top">
      <div>
        <div class="app-title">Ranní Zpravodaj</div>
        <div class="app-date" id="date-label"></div>
      </div>
      <div id="clock"></div>
    </div>
    <div class="controls">
      <button class="btn btn-load" id="btn-load" onclick="fetchNews()">🔄 Načíst</button>
      <button class="btn btn-play" id="btn-play" onclick="startReading()" style="display:none">▶ Číst vše</button>
      <button class="btn btn-stop" id="btn-stop" onclick="stopReading()" style="display:none">⏹ Stop</button>
      <button class="btn btn-reset" onclick="resetKey()">Klíč</button>
    </div>
    <div class="progress-bar" id="progress-bar" style="display:none">
      <div class="progress-fill" id="progress-fill" style="width:0%"></div>
    </div>
  </div>
  <div class="content" id="content">
    <div class="empty-state">
      <div class="empty-icon">📰</div>
      <div class="empty-text">Klepni na „Načíst" pro dnešní přehled zpráv</div>
    </div>
  </div>
</div>
<script>
  const CAT_CLASS = {'Svět':'cat-svet','Česko':'cat-cesko','Ekonomika':'cat-ekonomika','Technologie':'cat-technologie','Věda':'cat-veda','Sport':'cat-sport','Kultura':'cat-kultura'};
  let news=[], currentIndex=-1, isReading=false, synth=window.speechSynthesis;
  function updateClock(){const n=new Date();document.getElementById('clock').textContent=n.toLocaleTimeString('cs-CZ',{hour:'2-digit',minute:'2-digit',second:'2-digit'});document.getElementById('date-label').textContent=n.toLocaleDateString('cs-CZ',{weekday:'long',day:'numeric',month:'long',year:'numeric'});}
  setInterval(updateClock,1000);updateClock();
  function saveKey(){const k=document.getElementById('api-key-input').value.trim();if(!k.startsWith('sk-')){alert('Klíč musí začínat sk-ant-…');return;}localStorage.setItem('anthropic_key',k);document.getElementById('setup-screen').style.display='none';document.getElementById('app-screen').style.display='block';}
  function resetKey(){if(confirm('Zadat nový API klíč?')){localStorage.removeItem('anthropic_key');stopReading();document.getElementById('app-screen').style.display='none';document.getElementById('setup-screen').style.display='flex';document.getElementById('api-key-input').value='';}}
  window.addEventListener('load',()=>{const k=localStorage.getItem('anthropic_key');if(k){document.getElementById('setup-screen').style.display='none';document.getElementById('app-screen').style.display='block';}});
  async function fetchNews(){
    stopReading();
    const key=localStorage.getItem('anthropic_key');if(!key){resetKey();return;}
    const btn=document.getElementById('btn-load');btn.disabled=true;document.getElementById('btn-play').style.display='none';
    const msgs=['Prohledávám světová média…','Analyzuji nejdůležitější události…','Sestavuji ranní přehled…','Připravuji zprávy pro tebe…'];
    let mi=0;
    const content=document.getElementById('content');
    content.innerHTML=`<div class="loading-wrap"><div class="spinner"></div><div class="loading-text" id="loading-msg">${msgs[0]}</div></div>`;
    const timer=setInterval(()=>{mi=(mi+1)%msgs.length;const el=document.getElementById('loading-msg');if(el)el.textContent=msgs[mi];},1800);
    try{
      const today=new Date().toLocaleDateString('cs-CZ',{weekday:'long',year:'numeric',month:'long',day:'numeric'});
      const headers={'Content-Type':'application/json','x-api-key':key,'anthropic-version':'2023-06-01','anthropic-dangerous-direct-browser-access':'true'};
      const r1=await fetch('https://api.anthropic.com/v1/messages',{method:'POST',headers,body:JSON.stringify({model:'claude-sonnet-4-20250514',max_tokens:2000,tools:[{type:'web_search_20250305',name:'web_search'}],messages:[{role:'user',content:`Datum: ${today}. Vyhledej 10 nejdůležitějších zpráv dneška ze světa i z České republiky.`}]})});
      if(!r1.ok){const e=await r1.json().catch(()=>({}));if(r1.status===401)throw new Error('Neplatný API klíč.');if(r1.status===429)throw new Error('Překročen limit. Zkus za chvíli.');throw new Error(e.error?.message||`Chyba ${r1.status}`);}
      const d1=await r1.json();
      const assistantMsg={role:'assistant',content:d1.content};
      const toolResults=d1.content.filter(b=>b.type==='tool_use').map(b=>({type:'tool_result',tool_use_id:b.id,content:''}));
      const messages2=[{role:'user',content:`Datum: ${today}. Vyhledej 10 nejdůležitějších zpráv dneška.`},assistantMsg];
      if(toolResults.length>0)messages2.push({role:'user',content:toolResults});
      messages2.push({role:'user',content:'Napiš výsledky POUZE jako JSON pole. Začni [ a skonči ]. Každý objekt: {"title":"max 8 slov","summary":"2-3 věty","category":"Svět nebo Česko nebo Ekonomika nebo Technologie nebo Věda nebo Sport nebo Kultura","importance":číslo}'});
      const r2=await fetch('https://api.anthropic.com/v1/messages',{method:'POST',headers,body:JSON.stringify({model:'claude-sonnet-4-20250514',max_tokens:2000,messages:messages2})});
      clearInterval(timer);
      if(!r2.ok){const e=await r2.json().catch(()=>({}));throw new Error(e.error?.message||`Chyba ${r2.status}`);}
      const d2=await r2.json();
      const raw=d2.content.filter(b=>b.type==='text').map(b=>b.text).join('');
      const clean=raw.replace(/```json|```/g,'').trim();
      let parsed=null;
      try{parsed=JSON.parse(clean);}catch{}
      if(!parsed){const m=clean.match(/\[[\s\S]*\]/);if(m)try{parsed=JSON.parse(m[0]);}catch{}}
      if(!parsed||!Array.isArray(parsed))throw new Error('Chyba formátu. Zkus to znovu.');
      news=parsed.slice(0,10);renderNews();
    }catch(e){clearInterval(timer);content.innerHTML=`<div class="error-box">⚠ ${e.message}</div>`;btn.disabled=false;}
  }
  function renderNews(){
    const content=document.getElementById('content');
    const today=new Date().toLocaleDateString('cs-CZ',{weekday:'long',day:'numeric',month:'long'});
    let html=`<div class="section-label">10 nejdůležitějších zpráv · ${today}</div>`;
    news.forEach((item,i)=>{const cc=CAT_CLASS[item.category]||'cat-default';html+=`<div class="news-item" id="news-${i}" onclick="readSingle(${i})"><div class="news-num">${String(i+1).padStart(2,'0')}</div><div class="news-body"><div class="news-meta"><span class="cat-badge ${cc}">${item.category}</span><span class="reading-badge" id="badge-${i}" style="display:none">🔊 Čtení…</span></div><div class="news-title">${item.title}</div><div class="news-summary">${item.summary}</div></div><div class="news-importance">${item.importance}</div></div>`;});
    html+=`<div class="footer">Ranní Zpravodaj · AI · Klepni na zprávu pro přečtení</div>`;
    content.innerHTML=html;document.getElementById('btn-load').disabled=false;document.getElementById('btn-play').style.display='inline-flex';
  }
  function setActive(i){document.querySelectorAll('.news-item').forEach((el,j)=>{el.classList.remove('active');const b=document.getElementById(`badge-${j}`);if(b)b.style.display='none';});if(i>=0){const el=document.getElementById(`news-${i}`);if(el){el.classList.add('active');el.scrollIntoView({behavior:'smooth',block:'nearest'});}const b=document.getElementById(`badge-${i}`);if(b)b.style.display='inline';document.getElementById('progress-bar').style.display='block';document.getElementById('progress-fill').style.width=((i+1)/news.length*100)+'%';}else{document.getElementById('progress-bar').style.display='none';}}
  function speak(text,idx,onEnd){synth.cancel();const u=new SpeechSynthesisUtterance(text);u.lang='cs-CZ';u.rate=0.92;u.pitch=1.05;const v=synth.getVoices();const cv=v.find(x=>x.lang.startsWith('cs'))||v.find(x=>x.lang.startsWith('sk'));if(cv)u.voice=cv;u.onend=onEnd;u.onerror=onEnd;synth.speak(u);}
  function startReading(){if(!news.length)return;isReading=true;document.getElementById('btn-play').style.display='none';document.getElementById('btn-stop').style.display='inline-flex';readFrom(0);}
  function readFrom(i){if(!isReading||i>=news.length){finishReading();return;}setActive(i);currentIndex=i;const item=news[i];const text=(i===0?'Dobré ráno! Ranní přehled zpráv. ':'')+`Zpráva číslo ${i+1}. ${item.title}. ${item.summary}`;speak(text,i,()=>{if(isReading)setTimeout(()=>readFrom(i+1),500);});}
  function readSingle(i){if(isReading){stopReading();return;}isReading=true;document.getElementById('btn-stop').style.display='inline-flex';document.getElementById('btn-play').style.display='none';setActive(i);const item=news[i];speak(`${item.title}. ${item.summary}`,i,()=>{finishReading();});}
  function stopReading(){isReading=false;synth.cancel();setActive(-1);finishReading();}
  function finishReading(){isReading=false;currentIndex=-1;document.getElementById('btn-stop').style.display='none';if(news.length>0)document.getElementById('btn-play').style.display='inline-flex';}
  if(synth.onvoiceschanged!==undefined)synth.onvoiceschanged=()=>synth.getVoices();
</script>
</body>
</html>
