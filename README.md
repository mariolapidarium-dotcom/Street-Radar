# Street-Radar
Street radar
<!DOCTYPE html>
<html lang="hr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>RUPA RADAR — Prototip</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Oswald:wght@500;600;700&family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<style>
  :root{
    --asphalt-900:#161618;
    --asphalt-800:#222226;
    --asphalt-700:#2f2f35;
    --line-hairline:#3d3d44;
    --safety-yellow:#F5C518;
    --hazard-orange:#E8622C;
    --danger-red:#D9402A;
    --signal-green:#4CAF6D;
    --off-white:#ECEAE4;
    --muted:#9B9B9F;
  }
  *{box-sizing:border-box; -webkit-tap-highlight-color:transparent;}
  html,body{margin:0; padding:0; background:var(--asphalt-900); color:var(--off-white); font-family:'Inter',sans-serif; overflow-x:hidden;}
  .app{max-width:520px; margin:0 auto; min-height:100vh; display:flex; flex-direction:column;}

  /* header */
  header{padding:16px 18px 12px; position:relative; z-index:5;}
  .eyebrow{font-family:'JetBrains Mono',monospace; font-size:11px; letter-spacing:.12em; color:var(--muted); text-transform:uppercase; display:flex; justify-content:space-between; align-items:center;}
  .dot{display:inline-block; width:7px; height:7px; border-radius:50%; margin-right:5px; background:var(--muted);}
  .dot.on{background:var(--signal-green); box-shadow:0 0 6px var(--signal-green);}
  .dot.off{background:var(--danger-red); box-shadow:0 0 6px var(--danger-red);}
  h1{font-family:'Oswald',sans-serif; font-weight:700; font-size:30px; letter-spacing:.01em; margin:6px 0 2px; text-transform:uppercase;}
  h1 span{color:var(--safety-yellow);}
  .subtitle{font-size:13px; color:var(--muted); margin:0 0 10px; line-height:1.4;}

  /* hazard tape divider — signature element */
  .tape{height:10px; width:100%;
    background: repeating-linear-gradient(135deg, var(--safety-yellow) 0 14px, var(--asphalt-900) 14px 28px);
    box-shadow: inset 0 -1px 0 rgba(0,0,0,.3);
  }

  /* map */
  #map{width:100%; height:38vh; min-height:230px;}
  .leaflet-container{background:#1b1b1e;}

  /* warning banner */
  #warning{
    display:none; margin:0; padding:12px 18px;
    background:var(--danger-red); color:#fff; font-family:'Oswald',sans-serif;
    font-weight:600; letter-spacing:.02em; text-transform:uppercase; font-size:14px;
    align-items:center; gap:10px;
    animation: pulse 1.1s infinite;
  }
  @keyframes pulse{ 0%,100%{opacity:1;} 50%{opacity:.72;} }

  main{padding:16px 18px 90px; flex:1;}

  .panel{background:var(--asphalt-800); border:1px solid var(--line-hairline); border-radius:10px; padding:14px 16px; margin-bottom:14px;}
  .panel h2{font-family:'Oswald',sans-serif; font-size:13px; text-transform:uppercase; letter-spacing:.1em; color:var(--muted); margin:0 0 10px; font-weight:600;}

  .slider-row{display:flex; align-items:center; gap:12px;}
  input[type=range]{flex:1; accent-color:var(--safety-yellow); height:4px;}
  .slider-val{font-family:'JetBrains Mono',monospace; font-size:13px; width:34px; text-align:right; color:var(--safety-yellow);}

  .btn-main{
    width:100%; padding:16px; border:none; border-radius:10px;
    font-family:'Oswald',sans-serif; font-size:16px; font-weight:700; letter-spacing:.05em; text-transform:uppercase;
    background:var(--safety-yellow); color:var(--asphalt-900); cursor:pointer;
    transition:transform .1s ease, background .15s ease;
  }
  .btn-main:active{transform:scale(0.98);}
  .btn-main.active{background:var(--danger-red); color:#fff;}
  .btn-main:disabled{background:var(--asphalt-700); color:var(--muted);}

  .stat-grid{display:grid; grid-template-columns:1fr 1fr; gap:10px; margin-top:12px;}
  .stat{background:var(--asphalt-900); border:1px solid var(--line-hairline); border-radius:8px; padding:10px 12px;}
  .stat .num{font-family:'JetBrains Mono',monospace; font-size:22px; font-weight:600; color:var(--safety-yellow);}
  .stat .lbl{font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:.06em;}

  .list-item{display:flex; justify-content:space-between; align-items:center; padding:9px 0; border-bottom:1px solid var(--line-hairline); font-size:13px;}
  .list-item:last-child{border-bottom:none;}
  .list-item .meta{color:var(--muted); font-family:'JetBrains Mono',monospace; font-size:11px;}
  .badge{font-family:'JetBrains Mono',monospace; font-size:11px; padding:2px 7px; border-radius:20px; font-weight:600;}
  .badge.b1{background:rgba(245,197,24,.15); color:var(--safety-yellow);}
  .badge.b2{background:rgba(232,98,44,.18); color:var(--hazard-orange);}
  .badge.b3{background:rgba(217,64,42,.2); color:var(--danger-red);}

  .empty{color:var(--muted); font-size:13px; text-align:center; padding:10px 0;}

  .notice{font-size:11.5px; color:var(--muted); line-height:1.5; padding:10px 12px; background:var(--asphalt-900); border:1px dashed var(--line-hairline); border-radius:8px;}
  .notice b{color:var(--off-white);}

  a, a:visited{color:var(--safety-yellow);}
</style>
</head>
<body>
<div class="app">

  <header>
    <div class="eyebrow">
      <span><span class="dot" id="dotGps"></span>GPS</span>
      <span><span class="dot" id="dotMotion"></span>SENZOR</span>
      <span id="sessionId">—</span>
    </div>
    <h1>RUPA<span>RADAR</span></h1>
    <p class="subtitle">Prototip za otkrivanje rupa i neravnina na cesti pomoću akcelerometra, uz dijeljenu kartu svih korisnika.</p>
  </header>

  <div class="tape"></div>

  <div id="warning">⚠ <span id="warningText">Opasnost na cesti ispred</span></div>

  <div id="map"></div>

  <main>

    <div class="panel">
      <h2>Osjetljivost detekcije</h2>
      <div class="slider-row">
        <span style="font-size:12px;color:var(--muted)">Nisko</span>
        <input type="range" id="sensSlider" min="1" max="10" value="5">
        <span style="font-size:12px;color:var(--muted)">Visoko</span>
        <span class="slider-val" id="sensVal">5</span>
      </div>
    </div>

    <button class="btn-main" id="trackBtn">Započni praćenje</button>

    <div class="panel" style="margin-top:14px;">
      <h2>Status</h2>
      <div class="stat-grid">
        <div class="stat"><div class="num" id="statMine">0</div><div class="lbl">Otkrio/la si</div></div>
        <div class="stat"><div class="num" id="statNearby">0</div><div class="lbl">Ukupno u okolici</div></div>
      </div>
    </div>

    <div class="panel">
      <h2>Nedavne anomalije</h2>
      <div id="recentList"><div class="empty">Još nema zabilježenih anomalija.</div></div>
    </div>

    <div class="notice">
      <b>Napomena:</b> Ovo je funkcionalni prototip. Lokacije anomalija spremaju se u <b>dijeljenu</b> bazu vidljivu svim korisnicima ove aplikacije. Aplikacija mora ostati otvorena i ekran uključen — mobilni preglednici ne dopuštaju rad u pozadini niti dok je druga app (npr. Google Maps) aktivna na vrhu ekrana.
    </div>

  </main>
</div>

<script>
(function(){
  const STORAGE_KEY = 'road-anomalies';
  const CLUSTER_RADIUS_M = 20;
  const WARN_RADIUS_M = 60;
  const WARN_COOLDOWN_MS = 15000;

  let tracking = false;
  let watchId = null;
  let motionHandler = null;
  let currentPos = null; // {lat,lng,accuracy}
  let sessionId = 'g-' + Math.random().toString(36).slice(2,8);
  document.getElementById('sessionId').textContent = sessionId;

  let sensitivity = 5; // 1..10
  let lastDetectionTime = 0;
  let lastDetectionPos = null;
  let lastWarnTime = 0;
  let myDetections = 0;

  // rolling baseline for gravity-inclusive magnitude
  let baseline = null;
  const ALPHA = 0.08;

  // ---------- Map setup ----------
  const map = L.map('map', {zoomControl:false, attributionControl:true}).setView([45.815, 15.982], 14); // default: Zagreb
  L.control.zoom({position:'bottomright'}).addTo(map);
  L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
    maxZoom:20, attribution:'&copy; OpenStreetMap &copy; CARTO'
  }).addTo(map);

  let meMarker = null;
  let anomalyMarkers = {}; // id -> marker

  function colorForConfirmations(c){
    if(c >= 4) return '#D9402A';
    if(c >= 2) return '#E8622C';
    return '#F5C518';
  }

  function renderMeMarker(lat,lng){
    if(!meMarker){
      meMarker = L.circleMarker([lat,lng], {radius:7, color:'#5B8CFF', fillColor:'#5B8CFF', fillOpacity:0.9, weight:2}).addTo(map);
    } else {
      meMarker.setLatLng([lat,lng]);
    }
  }

  function renderAnomalies(list){
    const seen = new Set();
    list.forEach(a=>{
      seen.add(a.id);
      const color = colorForConfirmations(a.confirmations);
      const radius = 6 + Math.min(a.confirmations, 6) * 1.4;
      if(anomalyMarkers[a.id]){
        anomalyMarkers[a.id].setLatLng([a.lat,a.lng]);
        anomalyMarkers[a.id].setStyle({radius, color, fillColor:color});
      } else {
        const m = L.circleMarker([a.lat,a.lng], {radius, color, fillColor:color, fillOpacity:0.55, weight:1.5}).addTo(map);
        m.bindPopup(`<b>Anomalija</b><br>Potvrde: ${a.confirmations}<br>Jačina: ${a.magnitude.toFixed(1)}`);
        anomalyMarkers[a.id] = m;
      }
    });
    Object.keys(anomalyMarkers).forEach(id=>{
      if(!seen.has(id)){ map.removeLayer(anomalyMarkers[id]); delete anomalyMarkers[id]; }
    });
  }

  // ---------- Geo helpers ----------
  function haversine(lat1,lon1,lat2,lon2){
    const R = 6371000;
    const toRad = d => d*Math.PI/180;
    const dLat = toRad(lat2-lat1), dLon = toRad(lon2-lon1);
    const a = Math.sin(dLat/2)**2 + Math.cos(toRad(lat1))*Math.cos(toRad(lat2))*Math.sin(dLon/2)**2;
    return 2*R*Math.asin(Math.sqrt(a));
  }

  // ---------- Shared storage ----------
  async function loadAnomalies(){
    try{
      const res = await window.storage.get(STORAGE_KEY, true);
      return res && res.value ? JSON.parse(res.value) : [];
    }catch(e){
      return [];
    }
  }

  async function saveAnomalies(list){
    try{
      await window.storage.set(STORAGE_KEY, JSON.stringify(list), true);
    }catch(e){ console.error('Storage save error', e); }
  }

  async function addAnomaly(lat,lng,magnitude){
    const list = await loadAnomalies();
    let matched = null;
    for(const a of list){
      if(haversine(lat,lng,a.lat,a.lng) <= CLUSTER_RADIUS_M){ matched = a; break; }
    }
    if(matched){
      matched.confirmations += 1;
      matched.lastSeen = Date.now();
      matched.magnitude = Math.max(matched.magnitude, magnitude);
    } else {
      list.push({
        id: 'a-' + Date.now() + '-' + Math.random().toString(36).slice(2,6),
        lat, lng, magnitude, confirmations:1,
        firstSeen: Date.now(), lastSeen: Date.now(), reporter: sessionId
      });
    }
    await saveAnomalies(list);
    return list;
  }

  function refreshUI(list){
    renderAnomalies(list);
    document.getElementById('statNearby').textContent = list.length;
    document.getElementById('statMine').textContent = myDetections;

    const recentBox = document.getElementById('recentList');
    if(list.length === 0){
      recentBox.innerHTML = '<div class="empty">Još nema zabilježenih anomalija.</div>';
      return;
    }
    const sorted = [...list].sort((a,b)=>b.lastSeen - a.lastSeen).slice(0,8);
    recentBox.innerHTML = sorted.map(a=>{
      const badgeClass = a.confirmations>=4 ? 'b3' : (a.confirmations>=2 ? 'b2' : 'b1');
      const mins = Math.max(1, Math.round((Date.now()-a.lastSeen)/60000));
      return `<div class="list-item">
        <span>${a.lat.toFixed(4)}, ${a.lng.toFixed(4)}</span>
        <span class="meta">prije ${mins} min</span>
        <span class="badge ${badgeClass}">${a.confirmations}× potvrđeno</span>
      </div>`;
    }).join('');
  }

  async function pollAndRefresh(){
    const list = await loadAnomalies();
    refreshUI(list);
    if(currentPos){ checkWarnings(list); }
  }

  function checkWarnings(list){
    const now = Date.now();
    if(now - lastWarnTime < WARN_COOLDOWN_MS) return;
    for(const a of list){
      const d = haversine(currentPos.lat, currentPos.lng, a.lat, a.lng);
      if(d <= WARN_RADIUS_M){
        showWarning(`Opasnost na ${Math.round(d)} m — ${a.confirmations}× potvrđeno`);
        lastWarnTime = now;
        if(navigator.vibrate) navigator.vibrate([120,60,120]);
        break;
      }
    }
  }

  function showWarning(text){
    const el = document.getElementById('warning');
    document.getElementById('warningText').textContent = text;
    el.style.display = 'flex';
    clearTimeout(showWarning._t);
    showWarning._t = setTimeout(()=>{ el.style.display='none'; }, 6000);
  }

  // ---------- Sensors ----------
  function thresholdFromSensitivity(s){
    // higher sensitivity -> lower threshold (easier to trigger)
    return 22 - s * 1.8; // s=1 -> ~20.2, s=10 -> ~4
  }

  function onMotion(e){
    const acc = e.acceleration && (e.acceleration.x !== null) ? e.acceleration : e.accelerationIncludingGravity;
    if(!acc) return;
    const mag = Math.sqrt((acc.x||0)**2 + (acc.y||0)**2 + (acc.z||0)**2);
    if(baseline === null){ baseline = mag; return; }
    const deviation = Math.abs(mag - baseline);
    baseline = baseline + ALPHA * (mag - baseline);

    const threshold = thresholdFromSensitivity(sensitivity);
    if(deviation > threshold){
      const now = Date.now();
      const tooSoon = (now - lastDetectionTime) < 2500;
      let tooClose = false;
      if(lastDetectionPos && currentPos){
        tooClose = haversine(currentPos.lat, currentPos.lng, lastDetectionPos.lat, lastDetectionPos.lng) < 15;
      }
      if(!tooSoon && !tooClose && currentPos){
        lastDetectionTime = now;
        lastDetectionPos = {lat:currentPos.lat, lng:currentPos.lng};
        myDetections += 1;
        addAnomaly(currentPos.lat, currentPos.lng, deviation).then(list=>refreshUI(list));
      }
    }
  }

  async function requestMotionPermission(){
    if(typeof DeviceMotionEvent !== 'undefined' && typeof DeviceMotionEvent.requestPermission === 'function'){
      try{
        const res = await DeviceMotionEvent.requestPermission();
        return res === 'granted';
      }catch(e){ return false; }
    }
    return true; // Android / desktop don't need explicit permission
  }

  function startGeolocation(){
    if(!navigator.geolocation){ return false; }
    watchId = navigator.geolocation.watchPosition(pos=>{
      currentPos = {lat:pos.coords.latitude, lng:pos.coords.longitude, accuracy:pos.coords.accuracy};
      document.getElementById('dotGps').className = 'dot on';
      renderMeMarker(currentPos.lat, currentPos.lng);
      map.setView([currentPos.lat, currentPos.lng], Math.max(map.getZoom(), 16));
    }, err=>{
      document.getElementById('dotGps').className = 'dot off';
    }, {enableHighAccuracy:true, maximumAge:2000, timeout:10000});
    return true;
  }

  function stopGeolocation(){
    if(watchId !== null){ navigator.geolocation.clearWatch(watchId); watchId = null; }
    document.getElementById('dotGps').className = 'dot';
  }

  // ---------- UI wiring ----------
  const sensSlider = document.getElementById('sensSlider');
  const sensVal = document.getElementById('sensVal');
  sensSlider.addEventListener('input', ()=>{
    sensitivity = parseInt(sensSlider.value, 10);
    sensVal.textContent = sensitivity;
  });

  const trackBtn = document.getElementById('trackBtn');
  trackBtn.addEventListener('click', async ()=>{
    if(!tracking){
      trackBtn.disabled = true;
      trackBtn.textContent = 'Traženje dozvola…';

      const motionOk = await requestMotionPermission();
      const geoOk = startGeolocation();

      if(!motionOk){
        alert('Nije odobren pristup senzoru pokreta — detekcija neće raditi bez njega.');
      } else {
        document.getElementById('dotMotion').className = 'dot on';
        motionHandler = onMotion;
        window.addEventListener('devicemotion', motionHandler);
      }

      if(!geoOk){
        alert('GPS nije dostupan u ovom pregledniku.');
      }

      tracking = true;
      trackBtn.disabled = false;
      trackBtn.textContent = 'Zaustavi praćenje';
      trackBtn.classList.add('active');
    } else {
      if(motionHandler){ window.removeEventListener('devicemotion', motionHandler); motionHandler = null; }
      stopGeolocation();
      document.getElementById('dotMotion').className = 'dot';
      tracking = false;
      trackBtn.textContent = 'Započni praćenje';
      trackBtn.classList.remove('active');
    }
  });

  // Try to center map on user's current location once, even before tracking starts
  if(navigator.geolocation){
    navigator.geolocation.getCurrentPosition(pos=>{
      map.setView([pos.coords.latitude, pos.coords.longitude], 15);
    }, ()=>{}, {enableHighAccuracy:false, timeout:5000});
  }

  pollAndRefresh();
  setInterval(pollAndRefresh, 8000);

})();
</script>
</body>
</html>
