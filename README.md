[dashboard.html](https://github.com/user-attachments/files/22975721/dashboard.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Smart Agriculture Dashboard — Regional Design</title>

  <!-- Tailwind CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Chart.js CDN -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <style>
    /* Colorful regional theme: warm yellows, earthy browns, greens */
    :root{
      --brand-1: #f59e0b; /* warm amber */
      --brand-2: #b45309; /* rustic brown */
      --brand-3: #16a34a; /* fresh green */
      --muted: #6b7280;
      --card-shadow: 0 10px 30px rgba(11,7,0,0.06);
    }

    /* Global base */
    body { background: linear-gradient(180deg, #fffaf0 0%, #fbf7f1 100%); font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }

    /* Header gradient */
    header {
      background: linear-gradient(90deg, rgba(245,158,11,0.95), rgba(180,83,9,0.95));
      box-shadow: 0 6px 18px rgba(180,83,9,0.12);
    }

    /* Card visuals */
    .card {
      background: linear-gradient(180deg, rgba(255,255,255,0.95), rgba(254,250,236,0.9));
      border-radius: 14px;
      box-shadow: var(--card-shadow);
      transition: transform .28s cubic-bezier(.2,.9,.2,1), box-shadow .28s;
      will-change: transform;
      overflow: hidden;
    }
    .card:hover { transform: translateY(-6px); box-shadow: 0 18px 48px rgba(11,7,0,0.12); }

    /* Accent ribbon on cards */
    .ribbon {
      height: 6px;
      border-top-left-radius: 14px;
      border-top-right-radius: 14px;
      background: linear-gradient(90deg, var(--brand-3), var(--brand-1));
      margin-bottom: 10px;
    }

    /* Gentle rounded buttons */
    .btn-primary {
      background: linear-gradient(90deg, var(--brand-1), var(--brand-3));
      color: white;
      border-radius: 10px;
      padding: .5rem .9rem;
      font-weight: 600;
      box-shadow: 0 6px 18px rgba(21,128,61,0.12);
      transition: transform .18s;
    }
    .btn-primary:hover { transform: translateY(-3px); }

    /* Small hover effect for list items */
    .hover-soft:hover { background: rgba(20,83,45,0.03); transform: translateX(4px); transition: transform .18s, background .18s; }

    /* Weather icon style */
    .weather-icon {
      width: 56px; height:56px; border-radius: 12px;
      display:inline-flex; align-items:center; justify-content:center;
      background: linear-gradient(180deg, rgba(255,255,255,0.8), rgba(250,245,235,0.7));
      box-shadow: 0 6px 18px rgba(0,0,0,0.06);
      font-size: 20px;
    }

    /* Chart container sizing */
    #weatherChartContainer { min-height: 170px; }

    /* Small responsive tweaks */
    @media (max-width: 900px) {
      .ribbon { border-radius: 12px; }
      .weather-icon { width:48px; height:48px; font-size:18px; }
    }

    /* Smooth fade-in for main content */
    .fade-in { opacity:0; transform: translateY(8px); animation: fadeInUp .6s forwards ease; }
    @keyframes fadeInUp { to { opacity:1; transform: translateY(0); } }

    /* Tiny utility */
    .muted { color: var(--muted); }
  </style>
</head>
<body class="antialiased text-gray-800">

  <!-- Header -->
  <header class="p-4 text-white flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3">
    <div class="flex items-center gap-3">
      <div class="w-12 h-12 flex items-center justify-center rounded-lg bg-white/10">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.6" d="M3 15a4 4 0 004 4h10a4 4 0 004-4V7a4 4 0 00-4-4H7a4 4 0 00-4 4v8z" />
        </svg>
      </div>
      <div>
        <div class="text-lg font-bold">🌾 Smart Agriculture — Regional</div>
        <div class="text-sm muted">Warm, regional palette · adaptive layout · polished interactions</div>
      </div>
    </div>

    <div class="flex items-center gap-3">
      <div id="networkStatus" class="px-3 py-1 rounded-full bg-white/20 text-sm">Offline</div>
      <div id="userCountryBadge" class="text-sm px-3 py-1 rounded bg-white/10">—</div>
      <button id="logoutBtn" class="btn-primary text-sm">Logout</button>
    </div>
  </header>

  <!-- Main layout -->
  <main class="max-w-7xl mx-auto p-4 grid grid-cols-1 lg:grid-cols-4 gap-4 fade-in">

    <!-- Left column: profile & controls -->
    <aside class="lg:col-span-1 space-y-4">
      <div class="card p-4">
        <div class="ribbon"></div>
        <h3 class="font-semibold text-lg text-[var(--brand-2)]">Profile & Region</h3>
        <p class="text-sm muted mt-1">Set your location to receive region-specific crop tips.</p>

        <label class="block mt-3 text-sm">
          <select id="countrySelect" class="mt-2 w-full p-2 rounded border">
            <option value="">-- Choose country --</option>
            <option>South Africa</option><option>Zimbabwe</option><option>Namibia</option><option>Botswana</option><option>Lesotho</option><option>Eswatini</option><option>Zambia</option>
          </select>
        </label>

        <label class="block mt-3 text-sm">
          <input id="cityInput" placeholder="Nearest town (optional)" class="mt-2 w-full p-2 rounded border" />
        </label>

        <div class="flex gap-2 mt-3">
          <button id="saveReg" class="btn-primary flex-1">Save</button>
          <button id="useLocation" class="px-3 py-2 rounded border text-sm">Use my location</button>
        </div>

        <div id="offlineQueueBlock" class="text-xs muted mt-3">No queued actions</div>
      </div>

      <div class="card p-4">
        <div class="ribbon"></div>
        <h4 class="font-semibold text-[var(--brand-2)]">Quick Actions</h4>
        <div class="mt-3 grid grid-cols-2 gap-2">
          <button id="showOverviewBtn" class="p-2 rounded hover-soft text-sm text-left">Overview</button>
          <button id="showSoilBtn" class="p-2 rounded hover-soft text-sm text-left">Soil & Crops</button>
          <button id="showMapBtn" class="p-2 rounded hover-soft text-sm text-left">GPS Map</button>
          <button id="generateAI" class="p-2 rounded text-sm bg-amber-100">AI Report</button>
        </div>
      </div>

      <div class="card p-4">
        <div class="ribbon"></div>
        <h4 class="font-semibold text-[var(--brand-2)]">Theme & Hints</h4>
        <p class="text-xs muted mt-2">Regional colors: warm amber, rustic brown & fresh green. Smooth interactions enabled.</p>
        <div id="apiKeyHint" class="text-xs text-amber-700 mt-2"></div>
      </div>
    </aside>

    <!-- Right: main content (span 3 columns) -->
    <section class="lg:col-span-3 space-y-4">

      <!-- Top overview row -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <div class="card p-4">
          <div class="ribbon"></div>
          <div class="flex justify-between items-center">
            <div>
              <div class="text-sm muted">Registered Crops</div>
              <div id="registeredCropsCount" class="text-2xl font-bold">0</div>
            </div>
            <div class="text-right">
              <div class="text-sm muted">Soil</div>
              <div id="selectedSoil" class="font-semibold text-lg">—</div>
            </div>
          </div>
        </div>

        <div class="card p-4">
          <div class="ribbon"></div>
          <div class="flex items-center gap-3">
            <div class="weather-icon" id="weatherIcon">☁️</div>
            <div>
              <div id="weatherSummary" class="font-semibold">Weather (demo)</div>
              <div class="text-xs muted mt-1" id="lastFetch">Last fetch —</div>
            </div>
          </div>
        </div>

        <div class="card p-4">
          <div class="ribbon"></div>
          <div>
            <div class="text-sm muted">Smart Tools</div>
            <div class="mt-2 flex gap-2">
              <button id="aiReportBtn" class="btn-primary text-sm">Generate AI</button>
              <button id="enableGPS" class="px-3 py-2 rounded border text-sm">Enable GPS</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Main grid: soil/crops & fertilizer -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
        <div class="card p-4">
          <div class="ribbon"></div>
          <h3 class="font-semibold text-[var(--brand-2)]">Soil & Crop Recommendations</h3>

          <div class="mt-3 grid grid-cols-1 md:grid-cols-3 gap-3">
            <label>
              <select id="soilType" class="p-2 rounded border">
                <option value="">-- select soil --</option>
                <option value="sandy">Sandy</option><option value="clay">Clay</option><option value="loam">Loam</option>
              </select>
            </label>

            <label>
              <select id="seasonSelect" class="p-2 rounded border">
                <option value="spring">Spring</option><option value="summer">Summer</option><option value="autumn">Autumn</option><option value="winter">Winter</option>
              </select>
            </label>

            <div class="flex items-end">
              <button id="showRecs" class="btn-primary w-full">Show crops</button>
            </div>
          </div>

          <div id="cropRecommendations" class="mt-4"></div>
        </div>

        <div class="card p-4">
          <div class="ribbon"></div>
          <h3 class="font-semibold text-[var(--brand-2)]">Fertilizer & Cultivation</h3>
          <div id="fertilizerGuides" class="mt-3 text-sm muted"></div>

          <div id="appReviewAlert" class="mt-4 p-3 rounded border-l-4 border-amber-300 hidden">
            <div class="flex justify-between items-start">
              <div>
                <strong>App Review Alert</strong>
                <div id="alertSummary" class="text-xs muted">Seasonal tips</div>
              </div>
              <button id="dismissAlert" class="text-xs muted">Dismiss</button>
            </div>
            <div id="alertDetails" class="text-sm mt-2 muted"></div>
          </div>
        </div>
      </div>

      <!-- Chart + Map row -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <div class="card md:col-span-2 p-4">
          <div class="ribbon"></div>
          <h3 class="font-semibold text-[var(--brand-2)]">7-Day Temperature Trend</h3>
          <div id="weatherChartContainer" class="mt-3">
            <canvas id="weatherChart"></canvas>
          </div>
        </div>

        <div class="card p-4">
          <div class="ribbon"></div>
          <h3 class="font-semibold text-[var(--brand-2)]">Map / Progress</h3>
          <div id="mapArea" class="mt-3 h-36 bg-gradient-to-b from-amber-50 to-white rounded flex items-center justify-center muted">🗺️ Map placeholder</div>
        </div>
      </div>

      <footer class="text-xs muted text-center mt-2">© 2025 SmartAgri — Regional Design</footer>
    </section>
  </main>
  

  <!-- Script: logic & demo data -->
  <script>
    /* ========== CONFIG ========== */
    const OPENWEATHER_API_KEY = 'YOUR_OPENWEATHER_API_KEY_HERE';

    /* small dataset */
    const countryCropRecommendations = {
      "South Africa": { defaultSeason: 'spring', cropsBySoil: { sandy: ["Watermelon","Sweet potato","Groundnuts"], clay:["Cabbage","Sugarcane","Rice (irrigated)"], loam:["Maize","Wheat","Tomato"] } },
      "Zimbabwe": { defaultSeason: 'summer', cropsBySoil: { sandy:["Groundnuts","Millet","Sorghum"], clay:["Rice (irrigated)","Sugarcane"], loam:["Maize","Beans"] } },
      "Namibia": { defaultSeason: 'autumn', cropsBySoil: { sandy:["Melon","Millet"], clay:["Cotton"], loam:["Maize"] } },
      "Botswana": { defaultSeason: 'summer', cropsBySoil: { sandy:["Millet","Sorghum"], clay:["Cotton"], loam:["Maize"] } },
      "Lesotho": { defaultSeason: 'spring', cropsBySoil: { sandy:["Carrot"], clay:["Cabbage"], loam:["Potato","Maize"] } },
      "Eswatini": { defaultSeason: 'summer', cropsBySoil: { sandy:["Sugarcane"], clay:["Banana"], loam:["Maize","Beans"] } },
      "Zambia": { defaultSeason: 'summer', cropsBySoil: { sandy:["Groundnuts"], clay:["Rice"], loam:["Maize","Soybean"] } }
    };

    /* ========== Storage helpers ========== */
    function loadProfile(){ return JSON.parse(localStorage.getItem('agProfile') || 'null'); }
    function saveProfile(p){ localStorage.setItem('agProfile', JSON.stringify(p)); }
    function getRegisteredCrops(){ return JSON.parse(localStorage.getItem('registeredCrops') || '[]'); }
    function saveRegisteredCrops(a){ localStorage.setItem('registeredCrops', JSON.stringify(a)); }

    /* ========== Network & offline placeholders ========== */
    function renderNetworkStatus(){
      const online = navigator.onLine;
      const badge = document.getElementById('networkStatus');
      badge.textContent = online ? 'Online' : 'Offline';
      document.getElementById('offlineQueueBlock').textContent = online ? 'No queued actions' : 'Offline: actions queued';
    }
    window.addEventListener('online', renderNetworkStatus);
    window.addEventListener('offline', renderNetworkStatus);

    /* ========== Init UI ========== */
    (function init(){
      renderNetworkStatus();
      bindUi();
      const p = loadProfile();
      if (p){ document.getElementById('userCountryBadge').textContent = p.country || '—'; }
      updateRegisteredCountUI();
      initWeatherChart();
      showApiKeyHintIfMissing();
      // demo: fetch demo weather if profile exists
      if (p) fetchAndShowWeatherForProfile(p);
    })();

    function bindUi(){
      document.getElementById('saveReg').addEventListener('click', async ()=>{
        const country = document.getElementById('countrySelect').value;
        const city = document.getElementById('cityInput').value.trim();
        if(!country){ alert('Choose country'); return; }
        const profile = { country, city, createdAt: Date.now() };
        saveProfile(profile);
        document.getElementById('userCountryBadge').textContent = country;
        await fetchAndShowWeatherForProfile(profile);
      });

      document.getElementById('useLocation').addEventListener('click', async ()=>{
        try {
          const pos = await new Promise((res, rej) => navigator.geolocation.getCurrentPosition(res, rej, {timeout:7000}));
          const coords = pos.coords;
          const profile = { country: 'Unknown', coords:{lat:coords.latitude, lon:coords.longitude}, createdAt: Date.now() };
          saveProfile(profile);
          document.getElementById('userCountryBadge').textContent = profile.country;
          await fetchAndShowWeatherForProfile(profile);
        } catch(e){ alert('Location not available'); }
      });

      document.getElementById('showRecs').addEventListener('click', applySoilCrop);
      document.getElementById('aiReportBtn').addEventListener('click', generateAiReport);
      document.getElementById('generateAI').addEventListener('click', generateAiReport);
      document.getElementById('enableGPS').addEventListener('click', enableGpsMapping);
      document.getElementById('dismissAlert').addEventListener('click', ()=>document.getElementById('appReviewAlert').classList.add('hidden'));
      document.getElementById('logoutBtn').addEventListener('click', ()=>{ localStorage.removeItem('agProfile'); location.reload(); });
      document.getElementById('countrySelect').addEventListener('change', (e)=>{
        const cfg = countryCropRecommendations[e.target.value];
        if(cfg) document.getElementById('seasonSelect').value = cfg.defaultSeason || 'summer';
      });
    }

    /* ========== Weather (demo fallback) ========== */
    async function fetchCurrentWeatherByCoords(lat, lon){
      if (!OPENWEATHER_API_KEY || OPENWEATHER_API_KEY.includes('YOUR_OPENWEATHER')) {
        // demo synthetic 7-day temps
        return { demo:true, temps:[24,26,28,27,25,23,24], desc:'Partly cloudy', humidity:65 };
      }
      try {
        const url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&units=metric&exclude=minutely,alerts&appid=${OPENWEATHER_API_KEY}`;
        const res = await fetch(url);
        if(!res.ok) throw new Error('Weather fetch failed');
        const json = await res.json();
        return { demo:false, temps:(json.daily||[]).slice(0,7).map(d => Math.round(d.temp.day)), desc: json.current.weather?.[0]?.description, humidity: json.current.humidity };
      } catch(e){ console.error(e); return null; }
    }

    async function getBestCoordsForProfile(profile){
      if(profile?.coords) return profile.coords;
      if(profile?.city && profile.country && OPENWEATHER_API_KEY && !OPENWEATHER_API_KEY.includes('YOUR_OPENWEATHER')){
        // geocode by name (best-effort)
        try {
          const q = encodeURIComponent(`${profile.city},${profile.country}`);
          const url = `https://api.openweathermap.org/geo/1.0/direct?q=${q}&limit=1&appid=${OPENWEATHER_API_KEY}`;
          const r = await fetch(url); if(!r.ok) throw '';
          const d = await r.json(); if(d && d.length) return { lat:d[0].lat, lon:d[0].lon };
        } catch(_) {}
      }
      // fallback centroid mapping
      const centroids = {
        "South Africa": {lat:-30.5595, lon:22.9375}, "Zimbabwe":{lat:-19.0154,lon:29.1549},
        "Namibia":{lat:-22.9576,lon:18.4904},"Botswana":{lat:-22.3285,lon:24.6849},
        "Lesotho":{lat:-29.609987,lon:28.233608},"Eswatini":{lat:-26.5225,lon:31.4659},"Zambia":{lat:-13.1339,lon:27.8493}
      };
      return centroids[profile.country] || null;
    }

    async function fetchAndShowWeatherForProfile(profile){
      document.getElementById('weatherSummary').textContent = 'Fetching...';
      const loc = await getBestCoordsForProfile(profile);
      if(!loc){ document.getElementById('weatherSummary').textContent = 'Weather unavailable'; return; }
      const data = await fetchCurrentWeatherByCoords(loc.lat, loc.lon);
      if(!data){ document.getElementById('weatherSummary').textContent = 'Weather unavailable'; return; }
      const avg = Math.round(data.temps.reduce((a,b)=>a+b,0)/data.temps.length);
      document.getElementById('weatherSummary').textContent = `${data.desc} · Avg ${avg}°C`;
      document.getElementById('lastFetch').textContent = new Date().toLocaleString();
      document.getElementById('weatherIcon').textContent = chooseWeatherEmoji(data.desc || 'cloud');
      updateWeatherChart(data.temps);
      applyClimateGuidance({ tempC: data.temps[0], humidity: data.humidity, demo: data.demo });
    }

    function chooseWeatherEmoji(desc){
      desc = (desc||'').toLowerCase();
      if(desc.includes('rain')) return '🌧️';
      if(desc.includes('cloud')) return '☁️';
      if(desc.includes('clear')) return '☀️';
      if(desc.includes('snow')) return '❄️';
      return '⛅';
    }

    /* ========== Climate guidance ========== */
    function applyClimateGuidance(weather){
      const el = document.getElementById('fertilizerGuides');
      if(!weather){ el.innerHTML=''; return; }
      let html = '<div class="p-3 rounded bg-amber-50">';
      if(weather.demo) html += `<div class="font-semibold">Demo guidance</div><div class="text-sm muted mt-1">Moderate temps — follow standard NPK and irrigation schedules.</div>`;
      else {
        if(weather.tempC <=5) html += `<div class="text-red-600 font-semibold">Frost risk — protect seedlings.</div>`;
        else if(weather.tempC <=15) html += `<div class="text-sm muted">Cool conditions — reduce watering; use slow-release N.</div>`;
        else if(weather.tempC <=30) html += `<div class="text-sm muted">Good growing conditions — standard NPK application.</div>`;
        else html += `<div class="text-orange-700 font-semibold">High heat — increase irrigation; avoid midday fertiliser.</div>`;
        if(weather.humidity && weather.humidity > 85) html += `<div class="text-red-600 text-sm mt-1">High humidity — watch for fungal disease.</div>`;
      }
      html += '</div>';
      el.innerHTML = html;
    }

    /* ========== Soil -> crop logic ========== */
    function applySoilCrop(){
      const soil = document.getElementById('soilType').value;
      const season = document.getElementById('seasonSelect').value;
      const profile = loadProfile();
      const country = profile?.country;
      const recDiv = document.getElementById('cropRecommendations');
      if(!soil){ recDiv.innerHTML = '<div class="text-red-500">Please choose a soil type.</div>'; return; }
      if(!country){ recDiv.innerHTML = '<div class="text-red-500">Register your country first.</div>'; return; }
      const cfg = countryCropRecommendations[country] || null;
      let crops = cfg && cfg.cropsBySoil && cfg.cropsBySoil[soil] ? cfg.cropsBySoil[soil] : (soil==='sandy'?["Carrot","Watermelon","Groundnut"]:soil==='clay'?["Rice","Cabbage"]:["Maize","Tomato"]);
      const seasonFiltered = crops.filter(c => !(season==='winter' && ['Tomato','Maize','Watermelon'].includes(c)));
      recDiv.innerHTML = `<div class="mt-3 text-sm"><strong>Recommended:</strong></div><ul class="mt-2 ml-5 list-disc">${seasonFiltered.map(c=>`<li class="py-1 hover-soft">${c} <button onclick="viewCrop('${c}')" class="ml-3 text-xs px-2 py-0.5 rounded border">View</button> <button onclick="registerCrop('${c}')" class="ml-2 text-xs px-2 py-0.5 rounded text-white" style="background:linear-gradient(90deg,var(--brand-1),var(--brand-3));">Register</button></li>`).join('')}</ul>`;
      showAppReviewAlert(season, seasonFiltered);
      document.getElementById('selectedSoil').textContent = soil;
    }

    function viewCrop(name){
      const guides = {
        "Maize": {fert:"N-P-K 10-20-10 (split)", water:"2x/week", pests:"Stemborer", harvest:"90-120d"},
        "Tomato": {fert:"Balanced NPK", water:"Drip", pests:"Blight", harvest:"60-90d"},
        "Potato": {fert:"High K late", water:"Consistent", pests:"Beetle", harvest:"80-110d"}
      };
      const g = guides[name] || {fert:"General NPK", water:"Adapt to rainfall", pests:"Monitor", harvest:"Varies"};
      document.getElementById('fertilizerGuides').innerHTML = `<div class="p-3 rounded bg-amber-50"><div class="font-semibold">${name}</div><ul class="mt-2 ml-4 text-sm muted"><li><strong>Fertilizer:</strong> ${g.fert}</li><li><strong>Watering:</strong> ${g.water}</li><li><strong>Pests:</strong> ${g.pests}</li><li><strong>Harvest:</strong> ${g.harvest}</li></ul></div>`;
    }

    function registerCrop(name){
      const arr = getRegisteredCrops(); arr.push({crop:name, ts:Date.now()}); saveRegisteredCrops(arr); updateRegisteredCountUI(); alert(`${name} registered`);
    }

    function updateRegisteredCountUI(){ document.getElementById('registeredCropsCount').textContent = getRegisteredCrops().length; renderProgressList(); }
    function renderProgressList(){ const arr = getRegisteredCrops(); const el = document.getElementById('mapArea'); if(!arr.length) el.innerHTML = '<div class="muted">No registered crops yet</div>'; else el.innerHTML = arr.slice(-3).map(a=>`<div class="text-sm"><strong>${a.crop}</strong><div class="text-xs muted">${new Date(a.ts).toLocaleDateString()}</div></div>`).join(''); }

    /* ========== App Review Alert ========== */
    function showAppReviewAlert(season, crops){
      const c = document.getElementById('appReviewAlert'); c.classList.remove('hidden');
      document.getElementById('alertSummary').textContent = `Tips for ${season}`;
      const advice = { spring:'Prepare fields; test soil pH; protect seedlings.', summer:'Increase irrigation; stagger fertilizer.', autumn:'Post-harvest nutrient care; prepare for cooler', winter:'Protect against frost; reduce watering' };
      document.getElementById('alertDetails').innerHTML = `<div class="text-sm muted">${advice[season] || 'Follow best practices.'}</div><div class="mt-2 text-sm"><strong>Suggested:</strong> ${(crops||[]).slice(0,5).join(', ')}</div>`;
    }

    /* ========== GPS mapping stub ========== */
    async function enableGpsMapping(){
      try{
        const pos = await new Promise((res, rej)=>navigator.geolocation.getCurrentPosition(res, rej, {timeout:7000}));
        const coords = pos.coords;
        document.getElementById('mapArea').innerHTML = `<div class="text-sm">Coords: ${coords.latitude.toFixed(4)}, ${coords.longitude.toFixed(4)}</div>`;
        const profile = loadProfile() || {}; profile.coords = {lat:coords.latitude, lon:coords.longitude}; saveProfile(profile);
        alert('Location saved (demo).');
      }catch(e){ alert('Unable to get GPS'); }
    }

    /* ========== AI Report (demo) ========== */
    function generateAiReport(){
      const profile = loadProfile() || {country:'N/A'};
      const report = {generatedAt:new Date().toISOString(), profile, crops:getRegisteredCrops()};
      const blob = new Blob([JSON.stringify(report, null, 2)], {type:'application/json'});
      const url = URL.createObjectURL(blob); const a = document.createElement('a'); a.href=url; a.download=`ai_report_${Date.now()}.json`; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    }

    /* ========== Weather Chart ========== */
    let weatherChart = null;
    function initWeatherChart(){
      const ctx = document.getElementById('weatherChart').getContext('2d');
      weatherChart = new Chart(ctx, {
        type:'line',
        data:{
          labels:['D-6','D-5','D-4','D-3','D-2','D-1','Today'],
          datasets:[
            { label:'Temp °C', data:[24,26,28,27,25,23,24], tension:0.35, pointRadius:4, borderWidth:3, borderColor:'rgba(180,83,9,0.92)', backgroundColor:'rgba(245,158,11,0.12)', fill:true }
          ]
        },
        options:{
          responsive:true, maintainAspectRatio:false,
          scales:{ y:{ beginAtZero:false } },
          plugins:{ legend:{ display:false } }
        }
      });
    }
    function updateWeatherChart(temps){
      if(!weatherChart) return;
      weatherChart.data.datasets[0].data = temps;
      weatherChart.update();
    }

    /* ========== Helpers ========== */
    function showApiKeyHintIfMissing(){ if(!OPENWEATHER_API_KEY || OPENWEATHER_API_KEY.includes('YOUR_OPENWEATHER')) document.getElementById('apiKeyHint').textContent = 'Tip: add OpenWeather API key in the code to enable live weather.'; }

    /* Expose small functions for inline buttons */
    window.applySoilCrop = applySoilCrop; window.viewCrop = viewCrop; window.registerCrop = registerCrop; window.generateAiReport = generateAiReport; window.enableGpsMapping = enableGpsMapping;
  </script>
</body>
</html>
