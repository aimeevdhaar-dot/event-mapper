<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Whistle Event Mapper</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --font-sans: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    --color-text-primary: #111;
    --color-text-secondary: #555;
    --color-text-tertiary: #888;
    --color-text-info: #185fa5;
    --color-text-success: #3b6d11;
    --color-text-warning: #854f0b;
    --color-text-danger: #a32d2d;
    --color-background-primary: #fff;
    --color-background-secondary: #f5f5f3;
    --color-background-info: #e6f1fb;
    --color-background-success: #eaf3de;
    --color-background-warning: #faeeda;
    --color-background-danger: #fcebeb;
    --color-border-tertiary: rgba(0,0,0,0.12);
    --color-border-secondary: rgba(0,0,0,0.22);
    --color-border-primary: rgba(0,0,0,0.35);
    --border-radius-md: 8px;
    --border-radius-lg: 12px;
  }
  @media (prefers-color-scheme: dark) {
    :root {
      --color-text-primary: #f0ede8;
      --color-text-secondary: #a09d98;
      --color-text-tertiary: #6b6865;
      --color-text-info: #85b7eb;
      --color-text-success: #97c459;
      --color-text-warning: #ef9f27;
      --color-text-danger: #f09595;
      --color-background-primary: #1c1b19;
      --color-background-secondary: #252420;
      --color-background-info: #0c447c;
      --color-background-success: #27500a;
      --color-background-warning: #633806;
      --color-background-danger: #791f1f;
      --color-border-tertiary: rgba(255,255,255,0.1);
      --color-border-secondary: rgba(255,255,255,0.18);
      --color-border-primary: rgba(255,255,255,0.28);
    }
  }
  body { font-family: var(--font-sans); background: var(--color-background-secondary); color: var(--color-text-primary); min-height: 100vh; padding: 2rem 1rem; }
  .page-wrap { max-width: 780px; margin: 0 auto; background: var(--color-background-primary); border-radius: var(--border-radius-lg); border: 0.5px solid var(--color-border-tertiary); padding: 2rem; }
  .gem-wrap { padding: 0; }
  .gem-header { display: flex; align-items: center; gap: 12px; margin-bottom: 2rem; }
  .gem-logo { width: 32px; height: 32px; background: #111; border-radius: 6px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
  .gem-logo svg { width: 18px; height: 18px; }
  .gem-title { font-size: 18px; font-weight: 500; color: var(--color-text-primary); }
  .gem-subtitle { font-size: 13px; color: var(--color-text-secondary); margin-top: 2px; }
  .section-label { font-size: 11px; font-weight: 500; color: var(--color-text-tertiary); letter-spacing: 0.08em; text-transform: uppercase; margin-bottom: 8px; margin-top: 1.5rem; }
  .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .form-grid.single { grid-template-columns: 1fr; }
  .field-wrap { display: flex; flex-direction: column; gap: 5px; }
  .field-wrap label { font-size: 13px; color: var(--color-text-secondary); }
  input[type=text], input[type=email], select, textarea {
    width: 100%; font-family: var(--font-sans); font-size: 14px;
    border: 0.5px solid var(--color-border-tertiary); border-radius: var(--border-radius-md);
    padding: 8px 10px; background: var(--color-background-primary); color: var(--color-text-primary);
    outline: none; transition: border-color 0.15s;
  }
  input[type=text]:focus, input[type=email]:focus, select:focus, textarea:focus { border-color: var(--color-border-primary); }
  textarea { resize: vertical; min-height: 72px; }
  .tag-input-wrap { border: 0.5px solid var(--color-border-tertiary); border-radius: var(--border-radius-md); padding: 6px 8px; min-height: 40px; display: flex; flex-wrap: wrap; gap: 6px; align-items: center; cursor: text; background: var(--color-background-primary); transition: border-color 0.15s; }
  .tag-input-wrap:focus-within { border-color: var(--color-border-primary); }
  .tag { display: flex; align-items: center; gap: 4px; background: var(--color-background-secondary); border: 0.5px solid var(--color-border-tertiary); border-radius: 20px; padding: 2px 10px; font-size: 12px; color: var(--color-text-secondary); }
  .tag-remove { cursor: pointer; font-size: 14px; color: var(--color-text-tertiary); line-height: 1; margin-left: 2px; }
  .tag-remove:hover { color: var(--color-text-primary); }
  .tag-text-input { border: none; outline: none; background: transparent; font-size: 13px; color: var(--color-text-primary); min-width: 80px; flex: 1; }
  .checkbox-group { display: flex; flex-direction: column; gap: 8px; }
  .checkbox-row { display: flex; align-items: center; gap: 8px; font-size: 13px; color: var(--color-text-secondary); cursor: pointer; }
  .checkbox-row input[type=checkbox] { width: 15px; height: 15px; cursor: pointer; }
  .run-btn { width: 100%; margin-top: 1.5rem; padding: 11px; background: #111; color: #fff; border: none; border-radius: var(--border-radius-md); font-size: 14px; font-weight: 500; cursor: pointer; letter-spacing: 0.01em; display: flex; align-items: center; justify-content: center; gap: 8px; transition: opacity 0.15s; }
  .run-btn:hover { opacity: 0.88; }
  .run-btn:disabled { opacity: 0.45; cursor: not-allowed; }
  .spinner { width: 14px; height: 14px; border: 2px solid rgba(255,255,255,0.3); border-top-color: #fff; border-radius: 50%; animation: spin 0.7s linear infinite; }
  @keyframes spin { to { transform: rotate(360deg); } }
  .divider { border: none; border-top: 0.5px solid var(--color-border-tertiary); margin: 1.5rem 0; }
  .results-toolbar { display: flex; align-items: center; justify-content: space-between; margin-bottom: 1rem; flex-wrap: wrap; gap: 8px; }
  .toolbar-btns { display: flex; gap: 8px; }
  .icon-btn { display: flex; align-items: center; gap: 6px; font-size: 12px; font-weight: 500; color: var(--color-text-secondary); background: var(--color-background-secondary); border: 0.5px solid var(--color-border-tertiary); border-radius: var(--border-radius-md); padding: 6px 12px; cursor: pointer; transition: all 0.15s; white-space: nowrap; }
  .icon-btn:hover { color: var(--color-text-primary); border-color: var(--color-border-primary); }
  .icon-btn.copied { color: var(--color-text-success); border-color: var(--color-background-success); background: var(--color-background-success); }
  .event-card { border: 0.5px solid var(--color-border-tertiary); border-radius: var(--border-radius-lg); padding: 1.25rem; margin-bottom: 12px; background: var(--color-background-primary); }
  .event-card-top { display: flex; align-items: flex-start; justify-content: space-between; gap: 12px; margin-bottom: 12px; }
  .event-name { font-size: 15px; font-weight: 500; color: var(--color-text-primary); text-decoration: none; }
  .event-name:hover { text-decoration: underline; }
  .event-badges { display: flex; gap: 6px; flex-wrap: wrap; flex-shrink: 0; align-items: center; }
  .badge { font-size: 11px; padding: 3px 9px; border-radius: 20px; font-weight: 500; }
  .badge-intl { background: var(--color-background-info); color: var(--color-text-info); }
  .badge-local { background: var(--color-background-success); color: var(--color-text-success); }
  .badge-app { background: var(--color-background-warning); color: var(--color-text-warning); }
  .badge-noapp { background: var(--color-background-secondary); color: var(--color-text-secondary); }
  .score-pill { display: flex; align-items: center; gap: 5px; font-size: 12px; font-weight: 500; padding: 3px 10px; border-radius: 20px; }
  .score-high { background: #eaf3de; color: #3b6d11; }
  .score-med { background: #faeeda; color: #854f0b; }
  .score-low { background: var(--color-background-secondary); color: var(--color-text-secondary); }
  .score-bar-wrap { display: flex; align-items: center; gap: 8px; margin-bottom: 10px; }
  .score-bar-track { flex: 1; height: 4px; background: var(--color-background-secondary); border-radius: 2px; overflow: hidden; }
  .score-bar-fill { height: 100%; border-radius: 2px; }
  .event-overview { font-size: 13px; color: var(--color-text-secondary); line-height: 1.65; margin-bottom: 12px; }
  .relevance-tag { font-size: 12px; color: var(--color-text-info); background: var(--color-background-info); border-radius: var(--border-radius-md); padding: 7px 11px; margin-bottom: 12px; }
  .event-meta { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
  .meta-block { background: var(--color-background-secondary); border-radius: var(--border-radius-md); padding: 10px 12px; }
  .meta-block-label { font-size: 11px; color: var(--color-text-tertiary); text-transform: uppercase; letter-spacing: 0.07em; font-weight: 500; margin-bottom: 5px; }
  .meta-block-val { font-size: 13px; color: var(--color-text-primary); line-height: 1.5; }
  .icp-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; }
  .error-msg { font-size: 13px; color: var(--color-text-danger); background: var(--color-background-danger); border-radius: var(--border-radius-md); padding: 10px 14px; margin-top: 1rem; }
  .tabs { display: flex; border-bottom: 0.5px solid var(--color-border-tertiary); margin-bottom: 1.5rem; }
  .tab { font-size: 13px; font-weight: 500; color: var(--color-text-secondary); padding: 8px 16px; cursor: pointer; border-bottom: 2px solid transparent; margin-bottom: -0.5px; transition: all 0.15s; }
  .tab.active { color: var(--color-text-primary); border-bottom-color: #111; }
  .tab-panel { display: none; }
  .tab-panel.active { display: block; }
  .saved-card { border: 0.5px solid var(--color-border-tertiary); border-radius: var(--border-radius-lg); padding: 1rem 1.25rem; margin-bottom: 10px; background: var(--color-background-primary); display: flex; align-items: center; justify-content: space-between; gap: 12px; cursor: pointer; transition: border-color 0.15s; }
  .saved-card:hover { border-color: var(--color-border-primary); }
  .saved-card-name { font-size: 14px; font-weight: 500; color: var(--color-text-primary); }
  .saved-card-meta { font-size: 12px; color: var(--color-text-secondary); margin-top: 3px; }
  .saved-card-actions { display: flex; gap: 8px; flex-shrink: 0; }
  .del-btn { font-size: 12px; color: var(--color-text-danger); background: none; border: 0.5px solid var(--color-border-tertiary); border-radius: var(--border-radius-md); padding: 4px 10px; cursor: pointer; }
  .del-btn:hover { background: var(--color-background-danger); }
  .empty-state { text-align: center; padding: 2rem 1rem; color: var(--color-text-tertiary); font-size: 13px; }
  @media (max-width: 600px) {
    .form-grid { grid-template-columns: 1fr; }
    .event-meta, .icp-grid { grid-template-columns: 1fr; }
    .event-card-top { flex-direction: column; }
    .page-wrap { padding: 1.25rem; }
  }
</style>
</head>
<body>
<div class="page-wrap">
<div class="gem-wrap">
  <div class="gem-header">
    <div class="gem-logo">
      <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M12 2L2 7L12 12L22 7L12 2Z" stroke="white" stroke-width="1.5" stroke-linejoin="round"/>
        <path d="M2 17L12 22L22 17" stroke="white" stroke-width="1.5" stroke-linejoin="round"/>
        <path d="M2 12L12 17L22 12" stroke="white" stroke-width="1.5" stroke-linejoin="round"/>
      </svg>
    </div>
    <div>
      <div class="gem-title">Event Mapper</div>
      <div class="gem-subtitle">Find the right events for your prospect — powered by Whistle</div>
    </div>
  </div>

  <div class="tabs">
    <div class="tab active" onclick="switchTab('search')">Search</div>
    <div class="tab" onclick="switchTab('saved')" id="saved-tab">Saved searches</div>
  </div>

  <div class="tab-panel active" id="panel-search">
    <div class="section-label" style="margin-top:0;">Lead details</div>
    <div class="form-grid">
      <div class="field-wrap"><label>Company name</label><input type="text" id="company" placeholder="e.g. Acme Corp" /></div>
      <div class="field-wrap"><label>Company website</label><input type="text" id="website" placeholder="e.g. acme.com" /></div>
      <div class="field-wrap"><label>Lead name</label><input type="text" id="lead_name" placeholder="e.g. Sarah Johnson" /></div>
      <div class="field-wrap"><label>Lead email</label><input type="text" id="lead_email" placeholder="e.g. sarah@acme.com" /></div>
    </div>

    <div class="section-label">Company profile</div>
    <div class="form-grid">
      <div class="field-wrap">
        <label>Industry</label>
        <select id="industry">
          <option value="">Select industry...</option>
          <option>SaaS & AI</option><option>Cybersecurity</option><option>Finance & Fintech</option>
          <option>Healthcare & MedTech</option><option>HR & Workforce Tech</option>
          <option>Marketing & AdTech</option><option>EdTech</option>
          <option>Real Estate & PropTech</option><option>E-commerce & Retail</option>
          <option>Manufacturing & Supply Chain</option><option>Legal Tech</option><option>Other</option>
        </select>
      </div>
      <div class="field-wrap">
        <label>Company size</label>
        <select id="company_size">
          <option value="">Select size...</option>
          <option>1–10 (Seed)</option><option>11–50 (Early growth)</option>
          <option>51–200 (Growth)</option><option>201–500 (Mid-market)</option>
          <option>501–1000 (Scale-up)</option><option>1000+ (Enterprise)</option>
        </select>
      </div>
      <div class="field-wrap"><label>Headquarters location</label><input type="text" id="location" placeholder="e.g. New York, USA" /></div>
      <div class="field-wrap"><label>Primary market / geography</label><input type="text" id="market" placeholder="e.g. North America, Global" /></div>
    </div>

    <div class="section-label">ICP — ideal customer profile</div>
    <div class="form-grid single">
      <div class="field-wrap">
        <label>Target industries (who they sell to)</label>
        <div class="tag-input-wrap" id="target_industries_wrap"><input class="tag-text-input" id="target_industries_input" placeholder="Type and press Enter..." /></div>
      </div>
    </div>
    <div class="form-grid" style="margin-top:12px;">
      <div class="field-wrap">
        <label>Target job titles</label>
        <div class="tag-input-wrap" id="job_titles_wrap"><input class="tag-text-input" id="job_titles_input" placeholder="e.g. CTO, VP Engineering..." /></div>
      </div>
      <div class="field-wrap">
        <label>Seniority level</label>
        <div class="checkbox-group" style="margin-top:4px;">
          <label class="checkbox-row"><input type="checkbox" value="C-Suite" class="seniority" /> C-Suite (CEO, CTO, CFO...)</label>
          <label class="checkbox-row"><input type="checkbox" value="VP / Director" class="seniority" /> VP / Director</label>
          <label class="checkbox-row"><input type="checkbox" value="Manager" class="seniority" /> Manager</label>
          <label class="checkbox-row"><input type="checkbox" value="Individual Contributor" class="seniority" /> Individual Contributor</label>
        </div>
      </div>
    </div>

    <div class="section-label" style="margin-top:1.5rem;">Additional context (optional)</div>
    <div class="form-grid single">
      <div class="field-wrap">
        <label>Goals / what they're trying to achieve</label>
        <textarea id="goals" placeholder="e.g. Scale their sales team, expand into EMEA, improve SDR conversion rates..."></textarea>
      </div>
    </div>

    <button class="run-btn" id="runBtn" onclick="runSearch()">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="none"><circle cx="11" cy="11" r="8" stroke="currentColor" stroke-width="2"/><path d="M21 21L16.65 16.65" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
      Find matching events
    </button>

    <div id="results_area"></div>
  </div>

  <div class="tab-panel" id="panel-saved">
    <div id="saved_list"></div>
  </div>
</div>
</div>

<script>
  let currentEvents = [];
  let currentCompany = '';
  const targetIndustriesTags = makeTagInput('target_industries_wrap', 'target_industries_input');
  const jobTitlesTags = makeTagInput('job_titles_wrap', 'job_titles_input');

  function makeTagInput(wrapId, inputId) {
    const wrap = document.getElementById(wrapId);
    const inp = document.getElementById(inputId);
    const tags = [];
    wrap.addEventListener('click', () => inp.focus());
    inp.addEventListener('keydown', (e) => {
      if ((e.key === 'Enter' || e.key === ',') && inp.value.trim()) {
        e.preventDefault();
        const val = inp.value.trim().replace(/,$/, '');
        if (val && !tags.includes(val)) {
          tags.push(val);
          const tag = document.createElement('span');
          tag.className = 'tag';
          tag.innerHTML = escHtml(val) + '<span class="tag-remove" data-val="' + escHtml(val) + '">&times;</span>';
          tag.querySelector('.tag-remove').onclick = (ev) => {
            const i = tags.indexOf(ev.target.dataset.val);
            if (i > -1) tags.splice(i, 1);
            tag.remove();
          };
          wrap.insertBefore(tag, inp);
        }
        inp.value = '';
      }
    });
    return tags;
  }

  function getSeniority() {
    return Array.from(document.querySelectorAll('.seniority:checked')).map(c => c.value);
  }

  function switchTab(tab) {
    document.querySelectorAll('.tab').forEach((t, i) => t.classList.toggle('active', ['search','saved'][i] === tab));
    document.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
    document.getElementById('panel-' + tab).classList.add('active');
    if (tab === 'saved') renderSavedList();
  }

  function getSaved() {
    try { return JSON.parse(localStorage.getItem('whistle_event_searches') || '[]'); } catch { return []; }
  }
  function setSaved(data) {
    try { localStorage.setItem('whistle_event_searches', JSON.stringify(data)); } catch {}
  }

  function saveSearch(company, industry, location, events) {
    const saved = getSaved();
    const entry = { id: Date.now(), company, industry, location, date: new Date().toLocaleDateString('en-GB', {day:'numeric',month:'short',year:'numeric'}), events };
    saved.unshift(entry);
    setSaved(saved.slice(0, 20));
    updateSavedCount();
  }

  function updateSavedCount() {
    const n = getSaved().length;
    document.getElementById('saved-tab').textContent = 'Saved searches' + (n > 0 ? ' (' + n + ')' : '');
  }

  function renderSavedList() {
    const saved = getSaved();
    const el = document.getElementById('saved_list');
    if (!saved.length) {
      el.innerHTML = '<div class="empty-state">No saved searches yet.<br>Run a search and it will be saved automatically.</div>';
      return;
    }
    el.innerHTML = saved.map(s => `
      <div class="saved-card" onclick="loadSaved(${s.id})">
        <div class="saved-card-info">
          <div class="saved-card-name">${escHtml(s.company)}</div>
          <div class="saved-card-meta">${escHtml(s.industry)} &middot; ${escHtml(s.location)} &middot; ${escHtml(s.date)} &middot; ${s.events.length} events</div>
        </div>
        <div class="saved-card-actions">
          <button class="icon-btn" onclick="event.stopPropagation(); loadSaved(${s.id})">View &#8599;</button>
          <button class="del-btn" onclick="event.stopPropagation(); deleteSaved(${s.id})">Delete</button>
        </div>
      </div>`).join('');
  }

  function loadSaved(id) {
    const entry = getSaved().find(s => s.id === id);
    if (!entry) return;
    currentEvents = entry.events;
    currentCompany = entry.company;
    switchTab('search');
    document.getElementById('company').value = entry.company;
    document.getElementById('industry').value = entry.industry;
    document.getElementById('location').value = entry.location;
    renderResults(entry.events, entry.company, false);
    setTimeout(() => document.getElementById('results_area').scrollIntoView({ behavior: 'smooth', block: 'start' }), 100);
  }

  function deleteSaved(id) {
    setSaved(getSaved().filter(s => s.id !== id));
    renderSavedList();
    updateSavedCount();
  }

  async function runSearch() {
    const btn = document.getElementById('runBtn');
    const company = document.getElementById('company').value.trim();
    const website = document.getElementById('website').value.trim();
    const leadName = document.getElementById('lead_name').value.trim();
    const industry = document.getElementById('industry').value;
    const companySize = document.getElementById('company_size').value;
    const location = document.getElementById('location').value.trim();
    const market = document.getElementById('market').value.trim();
    const goals = document.getElementById('goals').value.trim();
    const seniority = getSeniority();

    if (!company || !industry || !location) {
      document.getElementById('results_area').innerHTML = '<div class="error-msg">Please fill in at least company name, industry and location before searching.</div>';
      return;
    }

    btn.disabled = true;
    btn.innerHTML = '<div class="spinner"></div> Searching for events...';
    document.getElementById('results_area').innerHTML = '';

    const isUSA = /\busa\b|\bunited states\b|\bus\b/i.test(location);

    const prompt = `You are an expert B2B event researcher working for Whistle, a B2B sales development agency. Find the most relevant real industry events for a prospective client.

LEAD PROFILE:
- Company: ${company}${website ? ' (' + website + ')' : ''}
- Lead: ${leadName || 'Not specified'}
- Industry: ${industry}
- Company size: ${companySize || 'Not specified'}
- Headquarters: ${location}
- Primary market: ${market || 'Not specified'}

ICP (who they sell to):
- Target industries: ${targetIndustriesTags.length ? targetIndustriesTags.join(', ') : 'Not specified'}
- Target job titles: ${jobTitlesTags.length ? jobTitlesTags.join(', ') : 'Not specified'}
- Seniority levels: ${seniority.length ? seniority.join(', ') : 'Not specified'}

Additional context: ${goals || 'None'}

TASK: Search the web for REAL, upcoming (2025-2026) B2B industry events and conferences that this company should attend or sponsor.

LOCATION RULES:
${isUSA ? '- USA-based company: recommend 4-5 USA events + 1 major international event only if large and highly relevant.' : '- Non-USA company: recommend 4-6 relevant events prioritising their region and global events.'}

For each event return a JSON array. Each object must have:
- name: string
- website: string (full URL)
- location: string (city, country)
- date: string
- overview: string (2-3 sentences, why relevant for this company)
- pricing: string (or "Contact organiser")
- icp_industries: string[]
- icp_job_titles: string[]
- icp_seniority: string
- has_app: boolean
- app_name: string | null
- is_international: boolean
- relevance_reason: string (1 sentence, specific to this company's ICP)
- fit_score: number (0-100)
- fit_score_rationale: string (1 sentence explaining the score)

Return ONLY a valid JSON array. No preamble, no markdown. Start with [ and end with ].`;

    try {
      const response = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          model: 'claude-sonnet-4-20250514',
          max_tokens: 4000,
          tools: [{ type: 'web_search_20250305', name: 'web_search' }],
          messages: [{ role: 'user', content: prompt }]
        })
      });
      const data = await response.json();
      let jsonText = '';
      for (const block of data.content) { if (block.type === 'text') jsonText += block.text; }
      const clean = jsonText.replace(/```json|```/g, '').trim();
      const s = clean.indexOf('['), e = clean.lastIndexOf(']');
      if (s === -1 || e === -1) throw new Error('No JSON array found in response');
      const events = JSON.parse(clean.substring(s, e + 1));
      currentEvents = events;
      currentCompany = company;
      saveSearch(company, industry, location, events);
      updateSavedCount();
      renderResults(events, company, true);
    } catch (err) {
      document.getElementById('results_area').innerHTML = '<div class="error-msg">Something went wrong. Please try again. (' + err.message + ')</div>';
    }

    btn.disabled = false;
    btn.innerHTML = '<svg width="14" height="14" viewBox="0 0 24 24" fill="none"><circle cx="11" cy="11" r="8" stroke="currentColor" stroke-width="2"/><path d="M21 21L16.65 16.65" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg> Find matching events';
  }

  function getScoreColor(score) {
    if (score >= 75) return '#639922';
    if (score >= 50) return '#ba7517';
    return '#888780';
  }
  function getScoreClass(score) {
    if (score >= 75) return 'score-high';
    if (score >= 50) return 'score-med';
    return 'score-low';
  }
  function getScoreLabel(score) {
    if (score >= 75) return 'Strong fit';
    if (score >= 50) return 'Good fit';
    return 'Possible fit';
  }

  function buildCopyText(events, company) {
    let txt = 'Event Recommendations for ' + company + '\nGenerated by Whistle Event Mapper\n\n';
    events.forEach((ev, i) => {
      txt += (i + 1) + '. ' + ev.name + '\n';
      txt += 'Website: ' + ev.website + '\n';
      txt += 'Location: ' + ev.location + (ev.date ? ' | ' + ev.date : '') + '\n';
      txt += 'Fit score: ' + ev.fit_score + '/100 (' + getScoreLabel(ev.fit_score) + ')\n';
      txt += 'Overview: ' + ev.overview + '\n';
      txt += 'Pricing: ' + ev.pricing + '\n';
      txt += 'ICP Industries: ' + (Array.isArray(ev.icp_industries) ? ev.icp_industries.join(', ') : ev.icp_industries) + '\n';
      txt += 'ICP Job Titles: ' + (Array.isArray(ev.icp_job_titles) ? ev.icp_job_titles.join(', ') : ev.icp_job_titles) + '\n';
      txt += 'ICP Seniority: ' + ev.icp_seniority + '\n';
      txt += 'Event App: ' + (ev.has_app ? (ev.app_name || 'Yes') : 'No') + '\n';
      txt += 'Why it fits: ' + ev.relevance_reason + '\n\n';
    });
    return txt;
  }

  function copyResults() {
    const btn = document.getElementById('copyBtn');
    if (!currentEvents.length) return;
    navigator.clipboard.writeText(buildCopyText(currentEvents, currentCompany)).then(() => {
      btn.classList.add('copied');
      btn.innerHTML = '&#10003; Copied!';
      setTimeout(() => {
        btn.classList.remove('copied');
        btn.innerHTML = 'Copy results';
      }, 2000);
    });
  }

  function renderResults(events, company, isNew) {
    if (!events || events.length === 0) {
      document.getElementById('results_area').innerHTML = '<div class="error-msg">No events found. Try adjusting the lead details.</div>';
      return;
    }
    const sorted = [...events].sort((a, b) => (b.fit_score || 0) - (a.fit_score || 0));
    let html = '<hr class="divider"><div class="results-toolbar"><div style="font-size:15px;font-weight:500;color:var(--color-text-primary);">Recommended events for ' + escHtml(company) + '<span style="font-size:13px;font-weight:400;margin-left:10px;color:var(--color-text-secondary);">' + events.length + ' events &middot; sorted by fit</span></div><div class="toolbar-btns"><button class="icon-btn" id="copyBtn" onclick="copyResults()">Copy results</button></div></div>';

    for (const ev of sorted) {
      const score = ev.fit_score || 0;
      html += '<div class="event-card">';
      html += '<div class="event-card-top"><div style="min-width:0;"><a class="event-name" href="' + escHtml(ev.website || '#') + '" target="_blank">' + escHtml(ev.name) + '</a><div style="font-size:12px;color:var(--color-text-secondary);margin-top:4px;">' + escHtml(ev.location || '') + (ev.date ? ' &nbsp;&middot;&nbsp; ' + escHtml(ev.date) : '') + '</div></div>';
      html += '<div class="event-badges"><span class="score-pill ' + getScoreClass(score) + '">' + score + '/100 &nbsp;' + getScoreLabel(score) + '</span>';
      if (ev.is_international) html += '<span class="badge badge-intl">International</span>';
      else html += '<span class="badge badge-local">Local</span>';
      if (ev.has_app) html += '<span class="badge badge-app">App available</span>';
      else html += '<span class="badge badge-noapp">No app</span>';
      html += '</div></div>';
      html += '<div class="score-bar-wrap"><span style="font-size:11px;color:var(--color-text-tertiary);min-width:50px;">ICP fit</span><div class="score-bar-track"><div class="score-bar-fill" style="width:' + score + '%;background:' + getScoreColor(score) + ';"></div></div><span style="font-size:11px;color:var(--color-text-tertiary);">' + score + '%</span></div>';
      if (ev.fit_score_rationale) html += '<div style="font-size:12px;color:var(--color-text-tertiary);margin-bottom:10px;">' + escHtml(ev.fit_score_rationale) + '</div>';
      html += '<div class="event-overview">' + escHtml(ev.overview || '') + '</div>';
      if (ev.relevance_reason) html += '<div class="relevance-tag">' + escHtml(ev.relevance_reason) + '</div>';
      html += '<div class="event-meta"><div class="meta-block"><div class="meta-block-label">Pricing</div><div class="meta-block-val">' + escHtml(ev.pricing || 'Contact organiser') + '</div></div>';
      html += '<div class="meta-block"><div class="meta-block-label">Event app</div><div class="meta-block-val">' + (ev.has_app ? (ev.app_name ? escHtml(ev.app_name) : 'Yes (app available)') : 'No official app') + '</div></div></div>';
      html += '<div style="margin-top:10px;"><div style="font-size:11px;font-weight:500;color:var(--color-text-tertiary);text-transform:uppercase;letter-spacing:0.07em;margin-bottom:8px;">ICP of attendees</div><div class="icp-grid">';
      html += '<div class="meta-block"><div class="meta-block-label">Target industries</div><div class="meta-block-val">' + escHtml(Array.isArray(ev.icp_industries) ? ev.icp_industries.join(', ') : (ev.icp_industries || '')) + '</div></div>';
      html += '<div class="meta-block"><div class="meta-block-label">Job titles</div><div class="meta-block-val">' + escHtml(Array.isArray(ev.icp_job_titles) ? ev.icp_job_titles.join(', ') : (ev.icp_job_titles || '')) + '</div></div>';
      html += '<div class="meta-block"><div class="meta-block-label">Seniority</div><div class="meta-block-val">' + escHtml(ev.icp_seniority || '') + '</div></div>';
      html += '</div></div></div>';
    }

    document.getElementById('results_area').innerHTML = html;
  }

  function escHtml(str) {
    if (!str) return '';
    return String(str).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
  }

  updateSavedCount();
</script>
</body>
</html>
