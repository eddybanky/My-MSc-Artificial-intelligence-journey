<div class="wrap hero">
  <div class="hero-name">Edward Bankole <span>· MSc Artificial Intelligence, University of Essex</span></div>
  <h1>Building systems that reason, learn, and adapt.</h1>
  <p class="hero-sub">
    This portfolio brings together the coursework, projects, and dissertation work from my
    MSc Artificial Intelligence at the University of Essex — a running record of what I've
    built, what I've learned, and where each module has taken my thinking.
  </p>
  <div class="hero-meta">
    <div><b id="stat-modules">7</b><small>Modules</small></div>
    <div><b id="stat-entries">0</b><small>Assignments &amp; projects logged</small></div>
    <div><b>Colchester</b><small>Campus</small></div>
  </div>
</div>

<div class="wrap about">
  <h2>About this portfolio</h2>
  <p>
    I'm an MSc Artificial Intelligence student at the University of Essex, working across
    machine learning, intelligent systems, and neural networks, with a dissertation project
    still to come. I'm drawn to the parts of this field where theory has to survive contact
    with messy data — where a clean idea from a lecture meets a dataset that doesn't cooperate.
  </p>
  <p>
    Each module below is broken down into its own units, and each unit has its own space for
    the work that came out of it: assignments, projects, notes on what I tried and what I'd
    do differently next time. It's less a highlight reel than a working log — a place to
    track how the pieces of the degree are starting to connect.
  </p>
  <p class="edit-hint">
    Replace this text with your own introduction — your background, what drew you to AI,
    and what you're hoping to specialise in.
  </p>
</div>

<div class="wrap modules">
  <h2>Modules</h2>
  <p class="lead">Open a module, then add a unit for each topic it covers — each unit gets its own space for assignments and projects.</p>
  <div class="module-list" id="module-list"></div>
</div>

<div class="wrap"><footer>
  Portfolio last updated automatically as you add work · entries are saved in this browser
</footer></div>

<script>
const MODULES = [
  { id: "ml", title: "Machine Learning (CE802)", blurb: "Learning algorithms that pick up patterns from examples rather than explicit rules — classification, recognition, and related methods.", defaultUnits: ["Supervised learning", "Unsupervised learning", "Model evaluation"] },
  { id: "isr", title: "Intelligent Systems and Robotics (CE801)", blurb: "Sensing and manipulating the real world — localisation, mapping, and fuzzy logic control for real robots.", defaultUnits: ["Sensing & hardware", "Localisation & mapping", "Fuzzy logic control"] },
  { id: "nndl", title: "Neural Networks and Deep Learning (CE889)", blurb: "How artificial neural networks are built and trained, and what gives them the ability to learn and adapt.", defaultUnits: ["Network architectures", "Training & optimisation", "Applied project"] },
  { id: "ppr", title: "Professional Practice and Research Methodology (CE902)", blurb: "Research methods, basic statistics, and proposal-writing to prepare for the dissertation project.", defaultUnits: ["Research methods", "Statistics for research", "Proposal writing"] },
  { id: "group", title: "Group Project (CE903)", blurb: "A team-based software project, from an outline problem through to a fully implemented solution.", defaultUnits: ["Requirements & design", "Implementation", "Final delivery"] },
  { id: "optional", title: "Optional Module(s)", blurb: "45 credits of options chosen to specialise the degree — add the ones you're taking.", defaultUnits: ["Optional module 1"] },
  { id: "project", title: "MSc Project and Dissertation (CE901)", blurb: "An independent research project of your choosing, written up as a dissertation with an oral presentation.", defaultUnits: ["Proposal", "Implementation", "Dissertation write-up"] }
];

const listEl = document.getElementById("module-list");
const unitsCache = {};   // moduleId -> [{id, title}]
const entriesCache = {}; // "moduleId::unitId" -> [entry]

function escapeHtml(str){
  return (str || "").replace(/[&<>"']/g, c => ({
    "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
  }[c]));
}
function makeId(){
  return Date.now().toString(36) + Math.random().toString(36).slice(2, 6);
}

function storageGet(key){
  try{
    const raw = localStorage.getItem(key);
    return raw ? JSON.parse(raw) : null;
  }catch(e){
    return null;
  }
}
function storageSet(key, value){
  try{
    localStorage.setItem(key, JSON.stringify(value));
  }catch(e){
    console.error("Could not save", key, e);
  }
}
function storageDelete(key){
  try{
    localStorage.removeItem(key);
  }catch(e){}
}

async function loadUnits(mod){
  if(unitsCache[mod.id]) return unitsCache[mod.id];
  unitsCache[mod.id] = storageGet(`units:${mod.id}`);
  if(!unitsCache[mod.id]){
    unitsCache[mod.id] = mod.defaultUnits.map(t => ({ id: makeId(), title: t }));
    await saveUnits(mod.id, unitsCache[mod.id]);
  }
  return unitsCache[mod.id];
}

async function saveUnits(moduleId, units){
  unitsCache[moduleId] = units;
  storageSet(`units:${moduleId}`, units);
}

async function loadEntries(moduleId, unitId){
  const key = `${moduleId}::${unitId}`;
  if(entriesCache[key]) return entriesCache[key];
  entriesCache[key] = storageGet(`unit-work:${moduleId}:${unitId}`) || [];
  return entriesCache[key];
}

async function saveEntries(moduleId, unitId, entries){
  const key = `${moduleId}::${unitId}`;
  entriesCache[key] = entries;
  storageSet(`unit-work:${moduleId}:${unitId}`, entries);
}

function updateTotals(){
  const total = Object.values(entriesCache).reduce((sum, arr) => sum + (arr ? arr.length : 0), 0);
  document.getElementById("stat-entries").textContent = total;
  document.getElementById("stat-modules").textContent = MODULES.length;
}

function renderEntries(moduleId, unitId){
  const box = document.querySelector(`.entries[data-for="${moduleId}::${unitId}"]`);
  if(!box) return;
  const entries = entriesCache[`${moduleId}::${unitId}`] || [];
  if(entries.length === 0){
    box.innerHTML = `<div class="empty">Nothing added yet for this unit.</div>`;
    return;
  }
  box.innerHTML = entries.map(e => `
    <div class="entry">
      <div class="entry-body">
        <b>${escapeHtml(e.title)}</b><span class="entry-tag">${escapeHtml(e.type)}</span>
        ${e.description ? `<p>${escapeHtml(e.description)}</p>` : ""}
        ${e.link ? `<p><a class="file-link" href="${escapeHtml(e.link)}" target="_blank" rel="noopener">${escapeHtml(e.link)}</a></p>` : ""}
        ${e.fileName ? `<p>File reference: ${escapeHtml(e.fileName)}</p>` : ""}
      </div>
      <button class="entry-del" data-module="${moduleId}" data-unit="${unitId}" data-id="${e.id}">Remove</button>
    </div>
  `).join("");
}

function unitTemplate(moduleId, unit){
  return `
    <div class="unit" data-unit="${unit.id}">
      <div class="unit-head">
        <input class="unit-title-input" data-module="${moduleId}" data-unit="${unit.id}" type="text" value="${escapeHtml(unit.title)}">
        <div class="unit-actions">
          <button class="unit-toggle" type="button" data-module="${moduleId}" data-unit="${unit.id}">Add / view work</button>
          <button class="unit-del" type="button" data-module="${moduleId}" data-unit="${unit.id}">Remove</button>
        </div>
      </div>
      <div class="unit-body">
        <form class="entry-form" data-module="${moduleId}" data-unit="${unit.id}">
          <input type="text" name="title" placeholder="Title (e.g. Assignment 1 — Decision Trees)" required>
          <select name="type">
            <option>Assignment</option>
            <option>Project</option>
            <option>Coursework</option>
            <option>Lab exercise</option>
          </select>
          <textarea name="description" placeholder="Short description — what it covers, key results, what you learned"></textarea>
          <input class="full" type="text" name="link" placeholder="Link to the file (Google Drive, GitHub, etc.) — optional">
          <div class="file-row">
            <input type="file" name="file">
            <span>Selecting a file only records its name here — it isn't uploaded or stored.</span>
          </div>
          <button class="add-btn" type="submit">Add to this unit</button>
        </form>
        <div class="entries" data-for="${moduleId}::${unit.id}"></div>
      </div>
    </div>
  `;
}

async function renderUnits(mod){
  const box = document.querySelector(`.unit-list[data-module="${mod.id}"]`);
  const units = await loadUnits(mod);
  box.innerHTML = units.map(u => unitTemplate(mod.id, u)).join("");
  document.querySelector(`.unit-count[data-module="${mod.id}"]`).textContent =
    `${units.length} unit${units.length === 1 ? "" : "s"}`;
}

async function toggleModule(moduleId, el){
  const isOpen = el.classList.contains("open");
  document.querySelectorAll(".module.open").forEach(m => { if(m !== el) m.classList.remove("open"); });
  if(isOpen){
    el.classList.remove("open");
    return;
  }
  el.classList.add("open");
  const mod = MODULES.find(m => m.id === moduleId);
  await renderUnits(mod);
}

function buildModule(mod, index){
  const el = document.createElement("div");
  el.className = "module";
  el.innerHTML = `
    <div class="module-dot">${index + 1}</div>
    <div class="module-card">
      <div class="module-head" data-module="${mod.id}">
        <div>
          <div class="module-title">${escapeHtml(mod.title)}</div>
          <div class="module-blurb">${escapeHtml(mod.blurb)}</div>
        </div>
        <button class="module-toggle" type="button">Open module</button>
      </div>
      <div class="module-panel">
        <div class="unit-count" data-module="${mod.id}"></div>
        <div class="add-unit-row">
          <input type="text" class="new-unit-input" data-module="${mod.id}" placeholder="Add a unit (e.g. Week 3 — Convolutional Networks)">
          <button type="button" class="new-unit-btn" data-module="${mod.id}">Add unit</button>
        </div>
        <div class="unit-list" data-module="${mod.id}"></div>
      </div>
    </div>
  `;
  return el;
}

MODULES.forEach((mod, i) => listEl.appendChild(buildModule(mod, i)));

listEl.addEventListener("click", async (e) => {
  // open/close a module
  const head = e.target.closest(".module-head");
  if(head){
    const moduleEl = head.closest(".module");
    await toggleModule(head.dataset.module, moduleEl);
    return;
  }
  const modToggle = e.target.closest(".module-toggle");
  if(modToggle){
    const moduleEl = modToggle.closest(".module");
    await toggleModule(moduleEl.querySelector(".module-head").dataset.module, moduleEl);
    return;
  }
  // open/close a unit
  const unitToggle = e.target.closest(".unit-toggle");
  if(unitToggle){
    const unitEl = unitToggle.closest(".unit");
    const wasOpen = unitEl.classList.contains("open");
    unitEl.parentElement.querySelectorAll(".unit.open").forEach(u => { if(u !== unitEl) u.classList.remove("open"); });
    if(wasOpen){
      unitEl.classList.remove("open");
    }else{
      unitEl.classList.add("open");
      await loadEntries(unitToggle.dataset.module, unitToggle.dataset.unit);
      renderEntries(unitToggle.dataset.module, unitToggle.dataset.unit);
    }
    return;
  }
  // add a unit
  const addUnitBtn = e.target.closest(".new-unit-btn");
  if(addUnitBtn){
    const moduleId = addUnitBtn.dataset.module;
    const input = document.querySelector(`.new-unit-input[data-module="${moduleId}"]`);
    const title = input.value.trim();
    if(!title) return;
    const mod = MODULES.find(m => m.id === moduleId);
    const units = await loadUnits(mod);
    units.push({ id: makeId(), title });
    await saveUnits(moduleId, units);
    await renderUnits(mod);
    input.value = "";
    return;
  }
  // remove a unit
  const delUnitBtn = e.target.closest(".unit-del");
  if(delUnitBtn){
    const moduleId = delUnitBtn.dataset.module;
    const unitId = delUnitBtn.dataset.unit;
    const mod = MODULES.find(m => m.id === moduleId);
    const units = (await loadUnits(mod)).filter(u => u.id !== unitId);
    await saveUnits(moduleId, units);
    delete entriesCache[`${moduleId}::${unitId}`];
    storageDelete(`unit-work:${moduleId}:${unitId}`);
    await renderUnits(mod);
    updateTotals();
    return;
  }
  // delete an entry
  const delEntryBtn = e.target.closest(".entry-del");
  if(delEntryBtn){
    const moduleId = delEntryBtn.dataset.module;
    const unitId = delEntryBtn.dataset.unit;
    const entries = await loadEntries(moduleId, unitId);
    const filtered = entries.filter(en => en.id !== delEntryBtn.dataset.id);
    await saveEntries(moduleId, unitId, filtered);
    renderEntries(moduleId, unitId);
    updateTotals();
    return;
  }
});

listEl.addEventListener("change", async (e) => {
  const titleInput = e.target.closest(".unit-title-input");
  if(!titleInput) return;
  const moduleId = titleInput.dataset.module;
  const unitId = titleInput.dataset.unit;
  const mod = MODULES.find(m => m.id === moduleId);
  const units = await loadUnits(mod);
  const unit = units.find(u => u.id === unitId);
  if(unit){
    unit.title = titleInput.value.trim() || unit.title;
    await saveUnits(moduleId, units);
  }
});

listEl.addEventListener("submit", async (e) => {
  e.preventDefault();
  const form = e.target;
  if(!form.classList.contains("entry-form")) return;
  const moduleId = form.dataset.module;
  const unitId = form.dataset.unit;
  const fileInput = form.querySelector('input[name="file"]');
  const newEntry = {
    id: makeId(),
    title: form.title.value.trim(),
    type: form.type.value,
    description: form.description.value.trim(),
    link: form.link.value.trim(),
    fileName: fileInput.files[0] ? fileInput.files[0].name : ""
  };
  if(!newEntry.title) return;
  const entries = await loadEntries(moduleId, unitId);
  entries.unshift(newEntry);
  await saveEntries(moduleId, unitId, entries);
  renderEntries(moduleId, unitId);
  updateTotals();
  form.reset();
});

(async function init(){
  for(const mod of MODULES){
    const units = await loadUnits(mod);
    for(const unit of units){
      await loadEntries(mod.id, unit.id);
    }
  }
  updateTotals();
})();
</script>

</body>
</html>
