<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Mindful Me — Wellness Tracker</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <meta name="color-scheme" content="light dark" />
  <style>
    /* Gentle breathing animation */
    @keyframes breathe {
      0% { transform: scale(1); opacity: .9; }
      50% { transform: scale(1.08); opacity: 1; }
      100% { transform: scale(1); opacity: .9; }
    }
    .breathe { animation: breathe 3.5s ease-in-out infinite; }

    /* Smooth transitions for interactive elements */
    .tween { transition: all .25s ease; }

    /* Tooltip styling */
    .tooltip::after {
      content: attr(data-tip);
      position: absolute;
      bottom: 110%;
      left: 50%;
      transform: translateX(-50%);
      background: rgba(17,24,39,.95);
      color: white;
      padding: .35rem .5rem;
      border-radius: .5rem;
      font-size: .75rem;
      white-space: nowrap;
      pointer-events: none;
      opacity: 0;
      transition: opacity .2s ease;
    }
    .tooltip:hover::after { opacity: 1; }

    /* Hide number spinners on some browsers for cleaner UI if used */
    input[type=number]::-webkit-inner-spin-button,
    input[type=number]::-webkit-outer-spin-button { -webkit-appearance: none; margin: 0; }
  </style>
</head>
<body class="min-h-screen bg-gradient-to-br from-indigo-900 via-purple-900 to-fuchsia-900 text-slate-100 antialiased">
  <div class="max-w-6xl mx-auto p-4 sm:p-6 lg:p-8">
    <!-- Header -->
    <header class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 mb-6">
      <div>
        <h1 class="text-3xl sm:text-4xl font-bold tracking-tight">Mindful Me</h1>
        <p class="text-slate-300">Daily mood check-ins, insight, and gentle guidance</p>
      </div>
      <div class="flex items-center gap-2 bg-white/10 backdrop-blur rounded-xl px-4 py-2">
        <span class="text-slate-300">Today</span>
        <span id="todayLabel" class="font-semibold"></span>
        <span id="todayStatus" class="ml-2 text-xs px-2 py-1 rounded-full bg-emerald-500/20 text-emerald-200">Not checked in</span>
      </div>
    </header>

    <!-- Quick stats -->
    <section class="grid md:grid-cols-3 gap-4 mb-6">
      <div class="rounded-2xl p-4 bg-white/10 backdrop-blur border border-white/10 flex items-center justify-between">
        <div>
          <p class="text-slate-300 text-sm">Current Streak</p>
          <p id="streakDays" class="text-2xl font-bold">0 days</p>
        </div>
        <div class="w-12 h-12 rounded-xl grid place-content-center bg-amber-400/20 text-amber-200">🔥</div>
      </div>
      <div class="rounded-2xl p-4 bg-white/10 backdrop-blur border border-white/10 flex items-center justify-between">
        <div>
          <p class="text-slate-300 text-sm">7-day Avg Mood</p>
          <p id="avgMood7" class="text-2xl font-bold">–</p>
        </div>
        <div class="w-12 h-12 rounded-xl grid place-content-center bg-sky-400/20 text-sky-200">📈</div>
      </div>
      <div class="rounded-2xl p-4 bg-white/10 backdrop-blur border border-white/10 flex items-center justify-between">
        <div>
          <p class="text-slate-300 text-sm">Positive Days (30d)</p>
          <p id="posDays30" class="text-2xl font-bold">–</p>
        </div>
        <div class="w-12 h-12 rounded-xl grid place-content-center bg-emerald-400/20 text-emerald-200">🌿</div>
      </div>
    </section>

    <!-- Main -->
    <main class="grid lg:grid-cols-2 gap-6">
      <!-- Left: Check-in & Recommendations -->
      <section class="space-y-6">
        <!-- Daily Check-in -->
        <div class="rounded-2xl p-5 bg-white/10 backdrop-blur border border-white/10">
          <div class="flex items-center justify-between mb-4">
            <h2 class="text-xl font-semibold">Daily Check-in</h2>
            <button id="breatheBtn" class="tween text-sm px-3 py-1.5 rounded-lg bg-sky-500/20 hover:bg-sky-500/30 text-sky-100">1‑min Breathe</button>
          </div>

          <div class="mb-4">
            <p class="text-slate-300 text-sm mb-2">How are you feeling?</p>
            <div class="grid grid-cols-5 gap-2">
              <!-- Mood buttons -->
              <button data-mood="1" class="mood-btn tween w-full rounded-xl py-3 bg-rose-500/20 hover:bg-rose-500/30 border border-rose-300/20">😔<div class="text-xs mt-1 text-rose-200/90">Low</div></button>
              <button data-mood="2" class="mood-btn tween w-full rounded-xl py-3 bg-orange-500/20 hover:bg-orange-500/30 border border-orange-300/20">😕<div class="text-xs mt-1 text-orange-200/90">Meh</div></button>
              <button data-mood="3" class="mood-btn tween w-full rounded-xl py-3 bg-amber-500/20 hover:bg-amber-500/30 border border-amber-300/20">😐<div class="text-xs mt-1 text-amber-200/90">Okay</div></button>
              <button data-mood="4" class="mood-btn tween w-full rounded-xl py-3 bg-lime-500/20 hover:bg-lime-500/30 border border-lime-300/20">🙂<div class="text-xs mt-1 text-lime-200/90">Good</div></button>
              <button data-mood="5" class="mood-btn tween w-full rounded-xl py-3 bg-emerald-500/20 hover:bg-emerald-500/30 border border-emerald-300/20">😄<div class="text-xs mt-1 text-emerald-200/90">Great</div></button>
            </div>
          </div>

          <div class="mb-4">
            <label for="journal" class="text-slate-300 text-sm">Write a few words (optional)</label>
            <textarea id="journal" rows="4" class="mt-2 w-full rounded-xl bg-white/5 border border-white/10 focus:border-white/30 focus:outline-none p-3 placeholder:text-slate-400" placeholder="What's on your mind?"></textarea>
            <p class="text-xs mt-2 text-slate-400">Tip: Mention themes like sleep, stress, work, family, or exercise for better insights.</p>
          </div>

          <div class="flex flex-wrap items-center gap-3">
            <button id="analyzeBtn" class="tween px-4 py-2 rounded-xl bg-fuchsia-500 hover:bg-fuchsia-600 text-white font-semibold">Analyze & Save</button>
            <span id="sentimentBadge" class="hidden text-xs px-2 py-1 rounded-full bg-white/10 border border-white/10"></span>
            <span id="keywordsBadge" class="hidden text-xs px-2 py-1 rounded-full bg-white/10 border border-white/10"></span>
          </div>

          <div id="toast" class="hidden mt-4 p-3 rounded-xl bg-emerald-500/15 text-emerald-100 border border-emerald-400/20">Saved! Your day is logged.</div>
        </div>

        <!-- Personalized Recommendations -->
        <div class="rounded-2xl p-5 bg-white/10 backdrop-blur border border-white/10">
          <h2 class="text-xl font-semibold mb-3">Personalized Tips</h2>
          <ul id="tipsList" class="space-y-2"></ul>
          <div class="mt-4 flex flex-wrap gap-2">
            <button id="gratitudeBtn" class="tween text-sm px-3 py-1.5 rounded-lg bg-amber-500/20 hover:bg-amber-500/30 text-amber-100">Gratitude prompt</button>
            <button id="waterBtn" class="tween text-sm px-3 py-1.5 rounded-lg bg-cyan-500/20 hover:bg-cyan-500/30 text-cyan-100">Drink water</button>
            <button id="resetTipsBtn" class="tween text-sm px-3 py-1.5 rounded-lg bg-white/10 hover:bg-white/20">Refresh tips</button>
          </div>
          <p id="microAction" class="mt-3 text-sm text-slate-300"></p>
        </div>
      </section>

      <!-- Right: Trends & History -->
      <section class="space-y-6">
        <!-- Trends -->
        <div class="rounded-2xl p-5 bg-white/10 backdrop-blur border border-white/10">
          <div class="flex items-center justify-between mb-4">
            <h2 class="text-xl font-semibold">Your Trends</h2>
            <div class="flex gap-2">
              <button data-tab="seven" class="trend-tab tween text-sm px-3 py-1.5 rounded-lg bg-white/20">Last 7 days</button>
              <button data-tab="month" class="trend-tab tween text-sm px-3 py-1.5 rounded-lg bg-white/10 hover:bg-white/20">30-day heatmap</button>
              <button data-tab="insights" class="trend-tab tween text-sm px-3 py-1.5 rounded-lg bg-white/10 hover:bg-white/20">Insights</button>
            </div>
          </div>

          <!-- 7-day line -->
          <div id="tab-seven" class="space-y-3">
            <div class="relative w-full h-48 rounded-xl bg-gradient-to-b from-white/10 to-white/5 border border-white/10 overflow-hidden">
              <svg id="lineChart" viewBox="0 0 700 220" class="w-full h-full"></svg>
            </div>
            <p class="text-xs text-slate-300">Mood scale: 1 (low) to 5 (great). Tap points for details.</p>
          </div>

          <!-- 30-day heatmap -->
          <div id="tab-month" class="hidden space-y-3">
            <div id="heatmap" class="grid grid-cols-10 gap-2"></div>
            <div class="flex items-center gap-2 text-xs text-slate-300">
              <span>Low</span>
              <div class="h-3 w-3 rounded bg-rose-500/60"></div>
              <div class="h-3 w-3 rounded bg-orange-500/60"></div>
              <div class="h-3 w-3 rounded bg-amber-500/60"></div>
              <div class="h-3 w-3 rounded bg-lime-500/60"></div>
              <div class="h-3 w-3 rounded bg-emerald-500/60"></div>
              <span>High</span>
            </div>
          </div>

          <!-- Insights -->
          <div id="tab-insights" class="hidden">
            <div class="grid sm:grid-cols-3 gap-3">
              <div class="rounded-xl p-4 bg-white/5 border border-white/10">
                <p class="text-slate-300 text-xs">Most used theme</p>
                <p id="topTheme" class="text-lg font-semibold">–</p>
              </div>
              <div class="rounded-xl p-4 bg-white/5 border border-white/10">
                <p class="text-slate-300 text-xs">Best day</p>
                <p id="bestDay" class="text-lg font-semibold">–</p>
              </div>
              <div class="rounded-xl p-4 bg-white/5 border border-white/10">
                <p class="text-slate-300 text-xs">Entry count (30d)</p>
                <p id="entryCount30" class="text-lg font-semibold">0</p>
              </div>
            </div>
          </div>
        </div>

        <!-- History -->
        <div class="rounded-2xl p-5 bg-white/10 backdrop-blur border border-white/10">
          <div class="flex items-center justify-between mb-3">
            <h2 class="text-xl font-semibold">History</h2>
            <div class="flex gap-2">
              <select id="moodFilter" class="rounded-lg bg-white/10 border border-white/10 p-2 text-sm">
                <option value="all">All moods</option>
                <option value="5">Great</option>
                <option value="4">Good</option>
                <option value="3">Okay</option>
                <option value="2">Meh</option>
                <option value="1">Low</option>
              </select>
              <input id="searchText" class="rounded-lg bg-white/10 border border-white/10 p-2 text-sm" placeholder="Search notes"/>
            </div>
          </div>
          <ul id="historyList" class="space-y-3"></ul>

          <div class="mt-4 flex flex-wrap gap-2">
            <button id="exportBtn" class="tween text-sm px-3 py-1.5 rounded-lg bg-emerald-500/20 hover:bg-emerald-500/30 text-emerald-100">Export data</button>
            <label class="tween cursor-pointer text-sm px-3 py-1.5 rounded-lg bg-sky-500/20 hover:bg-sky-500/30 text-sky-100">
              Import data
              <input id="importInput" type="file" accept="application/json" class="hidden"/>
            </label>
            <button id="clearBtn" class="tween text-sm px-3 py-1.5 rounded-lg bg-rose-500/20 hover:bg-rose-500/30 text-rose-100">Clear all</button>
          </div>
          <p class="text-xs text-slate-400 mt-2">Your entries are stored privately in your browser. Export before clearing or switching devices.</p>
        </div>
      </section>
    </main>
  </div>

  <!-- Breathing modal -->
  <div id="breatheModal" class="fixed inset-0 hidden items-center justify-center p-4 z-50">
    <div class="absolute inset-0 bg-black/60"></div>
    <div class="relative w-full max-w-md rounded-2xl p-6 bg-gradient-to-br from-indigo-800 to-fuchsia-800 border border-white/10">
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold">One‑minute Breathing</h3>
        <button id="closeBreathe" class="tween px-2 py-1 rounded bg-white/10 hover:bg-white/20">✕</button>
      </div>
      <div class="flex flex-col items-center text-center">
        <div class="breathe w-40 h-40 rounded-full bg-white/10 border border-white/20 grid place-content-center">
          <div class="text-2xl">🫁</div>
        </div>
        <p id="breatheCue" class="mt-4 text-slate-200">Inhale…</p>
        <p class="text-xs text-slate-300 mt-1">Follow the circle as it gently expands and contracts.</p>
        <div class="mt-4 text-sm">
          <span id="timerLabel" class="px-2 py-1 rounded bg-white/10 border border-white/10">60s</span>
        </div>
        <button id="restartBreathe" class="mt-4 tween px-3 py-1.5 rounded-lg bg-sky-500/20 hover:bg-sky-500/30 text-sky-100">Restart</button>
      </div>
    </div>
  </div>

  <script>
    // --------- Data & Utilities ---------
    const $ = (sel) => document.querySelector(sel);
    const $$ = (sel) => document.querySelectorAll(sel);

    const STORAGE_KEY = 'mindfulme_entries_v1';
    const todayISO = () => new Date().toISOString().slice(0,10);

    function loadEntries() {
      try { return JSON.parse(localStorage.getItem(STORAGE_KEY)) || []; }
      catch { return []; }
    }
    function saveEntries(entries) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(entries));
    }

    function formatDate(iso) {
      const d = new Date(iso + 'T00:00:00');
      return d.toLocaleDateString(undefined, { weekday: 'short', month: 'short', day: 'numeric' });
    }

    function clamp(n, min, max) { return Math.max(min, Math.min(max, n)); }

    // Simple on-device sentiment analysis (keyword-based)
    const POSITIVE = ['grateful','calm','happy','joy','proud','rested','excited','love','great','good','better','peace','relaxed','energized','productive'];
    const NEGATIVE = ['tired','sad','down','angry','upset','stressed','anxious','worried','overwhelmed','bad','worst','exhausted','lonely','pain','sick'];
    const THEMES = {
      sleep: ['sleep','rest','insomnia','nap','dream','tired','rested'],
      stress: ['stress','stressed','pressure','anxious','anxiety','overwhelmed','worry','worried'],
      work: ['work','meeting','deadline','boss','project','task','study','exam','school'],
      family: ['family','mom','dad','kid','partner','friend','friends','relationship'],
      exercise: ['exercise','run','walk','yoga','gym','workout','stretch','move'],
      food: ['eat','meal','diet','snack','hungry','full','coffee','tea','water']
    };

    function analyzeSentiment(text) {
      if (!text || !text.trim()) return { score: 0, keywords: [], themes: [] };
      const words = text.toLowerCase().match(/[a-zA-Z']+/g) || [];
      let pos = 0, neg = 0;
      for (const w of words) {
        if (POSITIVE.includes(w)) pos++;
        if (NEGATIVE.includes(w)) neg++;
      }
      const raw = pos - neg; // could be negative
      const score = clamp(raw, -5, 5);
      // themes detection
      const themes = [];
      for (const [key, group] of Object.entries(THEMES)) {
        if (group.some(g => words.includes(g))) themes.push(key);
      }
      // top keywords (just pick unique intersection)
      const keywords = [...new Set(words.filter(w => POSITIVE.includes(w) || NEGATIVE.includes(w)))].slice(0,5);
      return { score, keywords, themes };
    }

    function moodEmoji(m) {
      return {1:'😔',2:'😕',3:'😐',4:'🙂',5:'😄'}[m] || '❓';
    }
    function moodName(m) {
      return {1:'Low',2:'Meh',3:'Okay',4:'Good',5:'Great'}[m] || 'Unknown';
    }
    function moodColor(m, alpha=0.6) {
      // Return a nice RGBA for chart points
      const map = {
        1:`rgba(244,63,94,${alpha})`,   // rose-500
        2:`rgba(249,115,22,${alpha})`, // orange-500
        3:`rgba(245,158,11,${alpha})`, // amber-500
        4:`rgba(132,204,22,${alpha})`, // lime-500
        5:`rgba(16,185,129,${alpha})`, // emerald-500
      };
      return map[m] || `rgba(148,163,184,${alpha})`;
    }

    // Personalized tips based on last mood + sentiment + themes
    function generateTips(entry, trend = {}) {
      const tips = [];
      const mood = entry?.mood ?? 3;
      const s = entry?.sentimentScore ?? 0;
      const themes = entry?.themes ?? [];

      const add = (t) => tips.push(t);

      if (mood <= 2 || s < 0) {
        add('Take 5 deep breaths: 4s inhale, 4s hold, 6s exhale (repeat 5 times).');
        add('Step outside for a 10‑minute walk to reset your nervous system.');
      }
      if (themes.includes('sleep')) {
        add('Try winding down: dim lights, no screens 30 minutes before bed.');
      }
      if (themes.includes('stress')) {
        add('Brain-dump: write everything on your mind for 2 minutes without editing.');
      }
      if (themes.includes('work')) {
        add('Pick one tiny task and spend 10 minutes on it. Small wins matter.');
      }
      if (themes.includes('exercise')) {
        add('Gentle movement: 8 bodyweight squats + 20s stretch + 5 deep breaths.');
      }
      if (themes.includes('family')) {
        add('Send a caring message to someone you trust; connection boosts mood.');
      }
      if (themes.includes('food')) {
        add('Have a glass of water and a protein-rich snack for steady energy.');
      }
      // Trend-based
      if (trend.avg7 && trend.avg7 < 3) add('Your weekly average is a bit low—plan one joyful activity today.');
      if ((trend.streak || 0) >= 3) add(`Nice streak of ${trend.streak} days—keep going with something easy!`);

      if (tips.length === 0) {
        add('Keep doing what works—capture one thing you’re grateful for.');
        add('Micro-move: stand up, roll shoulders, slow breath for 30s.');
      }
      return [...new Set(tips)].slice(0,5);
    }

    // --------- State ---------
    let entries = loadEntries();
    let selectedMood = null;
    let activeTab = 'seven';

    // --------- DOM Init ---------
    function initTodayLabel() {
      const now = new Date();
      $('#todayLabel').textContent = now.toLocaleDateString(undefined, { weekday:'long', month:'short', day:'numeric' });
    }

    function setTodayStatus() {
      const has = entries.some(e => e.date === todayISO());
      const el = $('#todayStatus');
      el.textContent = has ? 'Checked in' : 'Not checked in';
      el.className = 'ml-2 text-xs px-2 py-1 rounded-full ' + (has ? 'bg-emerald-500/20 text-emerald-200' : 'bg-white/10 text-slate-200');
    }

    function renderMoodButtons() {
      $$('.mood-btn').forEach(btn => {
        const val = Number(btn.getAttribute('data-mood'));
        btn.classList.remove('ring-2','ring-offset-2','ring-white/60','ring-offset-transparent');
        if (selectedMood === val) {
          btn.classList.add('ring-2','ring-offset-2','ring-white/60','ring-offset-transparent');
        }
        btn.onclick = () => {
          selectedMood = val;
          renderMoodButtons();
        };
      });
    }

    function showToast(msg, type='success') {
      const t = $('#toast');
      t.textContent = msg;
      t.classList.remove('hidden');
      t.className = 'mt-4 p-3 rounded-xl border ' + (type==='success'
        ? 'bg-emerald-500/15 text-emerald-100 border-emerald-400/20'
        : 'bg-rose-500/15 text-rose-100 border-rose-400/20');
      setTimeout(() => { t.classList.add('hidden'); }, 2200);
    }

    // --------- Save / Analyze ---------
    function saveToday() {
      if (!selectedMood) {
        showToast('Choose a mood first.', 'error');
        return;
      }
      const text = $('#journal').value.trim();
      const { score, keywords, themes } = analyzeSentiment(text);

      // Update badges
      const sb = $('#sentimentBadge');
      const kb = $('#keywordsBadge');
      sb.classList.remove('hidden');
      sb.textContent = `Sentiment: ${score > 0 ? '+' : ''}${score}`;
      kb.classList.toggle('hidden', keywords.length === 0);
      kb.textContent = keywords.length ? `Noticed: ${keywords.join(', ')}` : '';

      // Upsert today's entry
      const date = todayISO();
      const existingIdx = entries.findIndex(e => e.date === date);
      const newEntry = {
        id: existingIdx >= 0 ? entries[existingIdx].id : crypto.randomUUID(),
        date, mood: selectedMood, journal: text,
        sentimentScore: score, keywords, themes,
        ts: Date.now()
      };
      if (existingIdx >= 0) entries[existingIdx] = newEntry;
      else entries.push(newEntry);
      // Keep sorted by date
      entries.sort((a,b) => a.date.localeCompare(b.date));
      saveEntries(entries);

      setTodayStatus();
      renderEverything();
      showToast('Saved! Your day is logged.');
    }

    // --------- Trends & Insights ---------
    function getLastNDays(n) {
      const out = [];
      const today = new Date(todayISO() + 'T00:00:00');
      for (let i=n-1;i>=0;i--) {
        const d = new Date(today);
        d.setDate(d.getDate() - i);
        out.push(d.toISOString().slice(0,10));
      }
      return out;
    }

    function calcAvgMood(dates) {
      const vals = dates
        .map(d => entries.find(e => e.date === d)?.mood)
        .filter(v => typeof v === 'number');
      if (!vals.length) return null;
      return (vals.reduce((a,b)=>a+b,0) / vals.length);
    }

    function calcPosDays30() {
      const days = getLastNDays(30);
      return days.reduce((acc, d) => {
        const m = entries.find(e => e.date === d)?.mood || null;
        return acc + (m >= 4 ? 1 : 0);
      }, 0);
    }

    function calcStreak() {
      let streak = 0;
      let i = 0;
      while (true) {
        const d = new Date(todayISO() + 'T00:00:00');
        d.setDate(d.getDate() - i);
        const iso = d.toISOString().slice(0,10);
        const has = entries.some(e => e.date === iso);
        if (has) { streak++; i++; } else break;
      }
      return streak;
    }

    function topTheme30() {
      const days = getLastNDays(30);
      const counts = {};
      days.forEach(d => {
        const e = entries.find(x => x.date === d);
        if (!e) return;
        (e.themes || []).forEach(t => counts[t] = (counts[t]||0) + 1);
      });
      const top = Object.entries(counts).sort((a,b)=>b[1]-a[1])[0];
      return top ? top[0] : '—';
    }

    function bestDay30() {
      const days = getLastNDays(30);
      let best = null;
      days.forEach(d => {
        const e = entries.find(x => x.date === d);
        if (e && (!best || e.mood > best.mood)) best = e;
      });
      return best ? `${formatDate(best.date)} (${moodName(best.mood)})` : '—';
    }

    function buildLineChart() {
      const svg = $('#lineChart');
      svg.innerHTML = '';

      const W = 700, H = 220, P = 24;
      const days = getLastNDays(7);
      const points = days.map((d, i) => {
        const e = entries.find(x => x.date === d);
        return { x: i, mood: e?.mood ?? null, date: d };
      });

      // Scales
      const xStep = (W - 2*P) / (days.length - 1);
      const yFor = (m) => H - P - (m - 1) * ((H - 2*P) / 4); // mood 1..5

      // Grid + labels
      const grid = document.createElementNS('http://www.w3.org/2000/svg', 'g');
      for (let m=1; m<=5; m++) {
        const y = yFor(m);
        const line = document.createElementNS(svg.namespaceURI, 'line');
        line.setAttribute('x1', String(P));
        line.setAttribute('x2', String(W-P));
        line.setAttribute('y1', String(y));
        line.setAttribute('y2', String(y));
        line.setAttribute('stroke', 'rgba(255,255,255,0.15)');
        line.setAttribute('stroke-dasharray', '4 6');
        grid.appendChild(line);

        const lbl = document.createElementNS(svg.namespaceURI, 'text');
        lbl.setAttribute('x', String(6));
        lbl.setAttribute('y', String(y+4));
        lbl.setAttribute('fill', 'rgba(255,255,255,0.7)');
        lbl.setAttribute('font-size', '10');
        lbl.textContent = String(m);
        grid.appendChild(lbl);
      }
      svg.appendChild(grid);

      // Path
      const path = document.createElementNS(svg.namespaceURI, 'path');
      let dAttr = '';
      points.forEach((p, i) => {
        if (p.mood === null) return;
        const x = P + xStep * i;
        const y = yFor(p.mood);
        dAttr += (dAttr ? ' L ' : 'M ') + x + ' ' + y;
      });
      path.setAttribute('d', dAttr || '');
      path.setAttribute('fill', 'none');
      path.setAttribute('stroke', 'rgba(255,255,255,0.7)');
      path.setAttribute('stroke-width', '2');
      svg.appendChild(path);

      // Points
      points.forEach((p, i) => {
        const x = P + xStep * i;
        const lbl = document.createElementNS(svg.namespaceURI, 'text');
        lbl.setAttribute('x', String(x));
        lbl.setAttribute('y', String(H - 4));
        lbl.setAttribute('text-anchor', 'middle');
        lbl.setAttribute('fill', 'rgba(255,255,255,0.8)');
        lbl.setAttribute('font-size', '10');
        lbl.textContent = new Date(p.date + 'T00:00:00').toLocaleDateString(undefined,{ weekday:'short' }).slice(0,2);
        svg.appendChild(lbl);

        if (p.mood !== null) {
          const dot = document.createElementNS(svg.namespaceURI, 'circle');
          dot.setAttribute('cx', String(x));
          dot.setAttribute('cy', String(yFor(p.mood)));
          dot.setAttribute('r', '6');
          dot.setAttribute('fill', moodColor(p.mood, 0.85));
          dot.setAttribute('stroke', 'rgba(255,255,255,0.9)');
          dot.setAttribute('stroke-width', '1');
          dot.style.cursor = 'pointer';
          dot.addEventListener('mouseenter', () => {
            dot.setAttribute('r','8');
          });
          dot.addEventListener('mouseleave', () => {
            dot.setAttribute('r','6');
          });
          dot.addEventListener('click', () => {
            const e = entries.find(x => x.date === p.date);
            if (e) alert(`${formatDate(e.date)}\nMood: ${moodName(e.mood)} ${moodEmoji(e.mood)}\nSentiment: ${e.sentimentScore > 0 ? '+' : ''}${e.sentimentScore}\n${e.journal ? 'Note: ' + e.journal : ''}`);
          });
          svg.appendChild(dot);
        } else {
          const hollow = document.createElementNS(svg.namespaceURI, 'circle');
          hollow.setAttribute('cx', String(x));
          hollow.setAttribute('cy', String(yFor(3)));
          hollow.setAttribute('r', '4');
          hollow.setAttribute('fill', 'none');
          hollow.setAttribute('stroke', 'rgba(255,255,255,0.4)');
          hollow.setAttribute('stroke-dasharray','2 2');
          svg.appendChild(hollow);
        }
      });
    }

    function buildHeatmap() {
      const wrap = $('#heatmap');
      wrap.innerHTML = '';
      const days = getLastNDays(30); // oldest to newest
      days.forEach((d, idx) => {
        const e = entries.find(x => x.date === d);
        const m = e?.mood ?? 0;
        const cell = document.createElement('div');
        let bg = 'bg-white/10 border-white/10';
        if (m === 1) bg = 'bg-rose-500/60 border-rose-300/40';
        if (m === 2) bg = 'bg-orange-500/60 border-orange-300/40';
        if (m === 3) bg = 'bg-amber-500/60 border-amber-300/40';
        if (m === 4) bg = 'bg-lime-500/60 border-lime-300/40';
        if (m === 5) bg = 'bg-emerald-500/60 border-emerald-300/40';
        cell.className = `tooltip relative h-6 rounded-lg border ${bg} hover:scale-[1.04] tween`;
        cell.setAttribute('data-tip', e ? `${formatDate(d)} • ${moodName(m)} ${moodEmoji(m)}` : `${formatDate(d)} • No entry`);
        wrap.appendChild(cell);
      });
    }

    function renderStats() {
      const avg7 = calcAvgMood(getLastNDays(7));
      $('#avgMood7').textContent = avg7 ? avg7.toFixed(1) : '–';
      $('#posDays30').textContent = String(calcPosDays30());
      $('#streakDays').textContent = `${calcStreak()} ${calcStreak() === 1 ? 'day' : 'days'}`;

      $('#topTheme').textContent = topTheme30();
      $('#bestDay').textContent = bestDay30();
      $('#entryCount30').textContent = String(getLastNDays(30).filter(d => entries.some(e => e.date === d)).length);
    }

    function currentTrendSnapshot() {
      return {
        avg7: calcAvgMood(getLastNDays(7)) || null,
        streak: calcStreak()
      };
    }

    function renderTips() {
      const latest = [...entries].sort((a,b)=>b.date.localeCompare(a.date))[0];
      const tips = generateTips(latest, currentTrendSnapshot());
      const list = $('#tipsList');
      list.innerHTML = '';
      tips.forEach(t => {
        const li = document.createElement('li');
        li.className = 'rounded-xl p-3 bg-white/5 border border-white/10';
        li.textContent = '• ' + t;
        list.appendChild(li);
      });
    }

    // --------- History ---------
    function renderHistory() {
      const list = $('#historyList');
      list.innerHTML = '';
      const moodF = $('#moodFilter').value;
      const q = $('#searchText').value.toLowerCase().trim();

      const filtered = [...entries].filter(e => {
        const moodOk = moodF === 'all' ? true : String(e.mood) === moodF;
        const textOk = !q || (e.journal || '').toLowerCase().includes(q) || (e.keywords || []).some(k => k.includes(q));
        return moodOk && textOk;
      }).sort((a,b)=> b.date.localeCompare(a.date));

      filtered.slice(0,10).forEach(e => {
        const li = document.createElement('li');
        li.className = 'rounded-xl p-3 bg-white/5 border border-white/10';
        const row = document.createElement('div');
        row.className = 'flex items-start justify-between gap-3';
        const left = document.createElement('div');
        left.innerHTML = `
          <div class="flex items-center gap-2">
            <span class="text-xl">${moodEmoji(e.mood)}</span>
            <span class="font-semibold">${formatDate(e.date)}</span>
            <span class="text-xs px-2 py-1 rounded-full bg-white/10 border border-white/10">Sent: ${e.sentimentScore > 0 ? '+' : ''}${e.sentimentScore}</span>
          </div>
          ${e.journal ? `<p class="mt-2 text-sm text-slate-200">${e.journal}</p>` : `<p class="mt-2 text-sm text-slate-400 italic">No notes</p>`}
        `;
        const actions = document.createElement('div');
        actions.className = 'flex gap-2';
        const loadBtn = document.createElement('button');
        loadBtn.className = 'tween text-sm px-3 py-1.5 rounded-lg bg-sky-500/20 hover:bg-sky-500/30 text-sky-100';
        loadBtn.textContent = 'Load';
        loadBtn.onclick = () => {
          selectedMood = e.mood;
          $('#journal').value = e.journal || '';
          renderMoodButtons();
          const sb = $('#sentimentBadge'); const kb = $('#keywordsBadge');
          sb.classList.add('hidden'); kb.classList.add('hidden');
          document.documentElement.scrollTo({ top: 0, behavior: 'smooth' });
          // Refresh tips to reflect this entry context
          const tips = generateTips(e, currentTrendSnapshot());
          const list = $('#tipsList'); list.innerHTML = '';
          tips.forEach(t => {
            const li = document.createElement('li');
            li.className = 'rounded-xl p-3 bg-white/5 border border-white/10';
            li.textContent = '• ' + t;
            list.appendChild(li);
          });
        };
        const delBtn = document.createElement('button');
        delBtn.className = 'tween text-sm px-3 py-1.5 rounded-lg bg-rose-500/20 hover:bg-rose-500/30 text-rose-100';
        delBtn.textContent = 'Delete';
        delBtn.onclick = () => {
          if (confirm('Delete this entry?')) {
            entries = entries.filter(x => x.id !== e.id);
            saveEntries(entries);
            setTodayStatus();
            renderEverything();
            showToast('Entry deleted.');
          }
        };
        actions.append(loadBtn, delBtn);
        row.append(left, actions);
        li.appendChild(row);
        list.appendChild(li);
      });

      if (filtered.length === 0) {
        const empty = document.createElement('div');
        empty.className = 'text-sm text-slate-300';
        empty.textContent = 'No entries match your filters.';
        list.appendChild(empty);
      }
    }

    // --------- Tabs ---------
    function switchTab(tab) {
      activeTab = tab;
      ['seven','month','insights'].forEach(t => {
        $('#tab-'+t).classList.toggle('hidden', t !== tab);
      });
      $$('.trend-tab').forEach(btn => {
        btn.classList.toggle('bg-white/20', btn.getAttribute('data-tab') === tab);
        btn.classList.toggle('bg-white/10', btn.getAttribute('data-tab') !== tab);
      });
    }

    // --------- Breathing Modal ---------
    let breatheInterval = null;
    let breatheTime = 60;
    function openBreathe() {
      $('#breatheModal').classList.remove('hidden');
      startBreathing();
    }
    function closeBreathe() {
      $('#breatheModal').classList.add('hidden');
      stopBreathing();
    }
    function startBreathing() {
      breatheTime = 60;
      $('#timerLabel').textContent = `${breatheTime}s`;
      $('#breatheCue').textContent = 'Inhale…';
      if (breatheInterval) clearInterval(breatheInterval);
      let phase = 0; // 0 inhale, 1 hold, 2 exhale
      let phaseTime = 0;
      breatheInterval = setInterval(() => {
        breatheTime--;
        $('#timerLabel').textContent = `${breatheTime}s`;
        phaseTime++;
        if (phase === 0 && phaseTime >= 4) { phase = 1; phaseTime = 0; $('#breatheCue').textContent = 'Hold…'; }
        else if (phase === 1 && phaseTime >= 4) { phase = 2; phaseTime = 0; $('#breatheCue').textContent = 'Exhale…'; }
        else if (phase === 2 && phaseTime >= 6) { phase = 0; phaseTime = 0; $('#breatheCue').textContent = 'Inhale…'; }

        if (breatheTime <= 0) {
          clearInterval(breatheInterval);
          breatheInterval = null;
          $('#breatheCue').textContent = 'Done! Notice how you feel.';
        }
      }, 1000);
    }
    function stopBreathing() {
      if (breatheInterval) clearInterval(breatheInterval);
      breatheInterval = null;
    }

    // --------- Export / Import ---------
    function exportData() {
      const blob = new Blob([JSON.stringify(entries, null, 2)], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `mindful-me-${todayISO()}.json`;
      document.body.appendChild(a);
      a.click();
      a.remove();
      URL.revokeObjectURL(url);
      showToast('Export started.');
    }

    function importData(file) {
      const reader = new FileReader();
      reader.onload = () => {
        try {
          const data = JSON.parse(reader.result);
          if (!Array.isArray(data)) throw new Error('Invalid file');
          // naive merge by id
          const map = new Map(entries.map(e => [e.id, e]));
          data.forEach(d => { map.set(d.id || crypto.randomUUID(), { ...d, id: d.id || crypto.randomUUID() }); });
          entries = Array.from(map.values()).sort((a,b)=>a.date.localeCompare(b.date));
          saveEntries(entries);
          setTodayStatus();
          renderEverything();
          showToast('Import complete.');
        } catch {
          showToast('Import failed. Check file.', 'error');
        }
      };
      reader.readAsText(file);
    }

    // --------- Micro actions ---------
    const gratitudePrompts = [
      'Write one thing that made you smile today.',
      'Name a person you appreciate and why.',
      'Recall a tiny win from today.',
      'What’s something you’re looking forward to?'
    ];
    function randomPrompt() {
      return gratitudePrompts[Math.floor(Math.random()*gratitudePrompts.length)];
    }

    // --------- Render All ---------
    function renderEverything() {
      buildLineChart();
      buildHeatmap();
      renderStats();
      renderTips();
      renderHistory();
    }

    // --------- Event listeners ---------
    $('#analyzeBtn').addEventListener('click', saveToday);
    $('#breatheBtn').addEventListener('click', openBreathe);
    $('#closeBreathe').addEventListener('click', closeBreathe);
    $('#restartBreathe').addEventListener('click', startBreathing);

    $$('.trend-tab').forEach(btn => btn.addEventListener('click', () => switchTab(btn.getAttribute('data-tab'))));

    $('#moodFilter').addEventListener('change', renderHistory);
    $('#searchText').addEventListener('input', () => {
      // Debounce-lite
      clearTimeout(window._searchDeb);
      window._searchDeb = setTimeout(renderHistory, 200);
    });

    $('#exportBtn').addEventListener('click', exportData);
    $('#importInput').addEventListener('change', (e) => {
      const file = e.target.files[0];
      if (file) importData(file);
      e.target.value = '';
    });
    $('#clearBtn').addEventListener('click', () => {
      if (confirm('This will delete all entries from this browser. Continue?')) {
        entries = [];
        saveEntries(entries);
        setTodayStatus();
        renderEverything();
        showToast('All data cleared.');
      }
    });

    $('#gratitudeBtn').addEventListener('click', () => {
      $('#microAction').textContent = randomPrompt();
    });
    $('#waterBtn').addEventListener('click', () => {
      $('#microAction').textContent = 'Sip a glass of water now. Set it within reach for later.';
    });
    $('#resetTipsBtn').addEventListener('click', () => renderTips());

    // --------- Init ---------
    (function init() {
      initTodayLabel();
      setTodayStatus();
      renderMoodButtons();
      switchTab('seven');
      renderEverything();
    })();
  </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'97e53dca3608bcff',t:'MTc1Nzc0MTMwOS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
