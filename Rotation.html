/* Daily Rotation Scheduler - plain HTML/JS
   - loads techs.json + rules.json
   - step flow: day -> roster -> shifts -> grid
   - generator: simple greedy fill of required coverage + optional slots
   - drag/drop: assign and swap
   - localStorage: saved per date
*/

const $ = (id) => document.getElementById(id);

const STORAGE_KEY_PREFIX = "rotation.day.v1:";

const SHIFT_PRESETS = [
  { label: "8-4", start: "08:00", end: "16:00" },
  { label: "8-2", start: "08:00", end: "14:00" },
  { label: "9-5", start: "09:00", end: "17:00" },
  { label: "10-6", start: "10:00", end: "18:00" },
  { label: "12-8", start: "12:00", end: "20:00" },
  { label: "2-8", start: "14:00", end: "20:00" },
  { label: "3-8", start: "15:00", end: "20:00" },
  { label: "Custom", start: null, end: null }
];

let TECHS = [];
let RULES = null;

let state = {
  dateISO: null,
  dayType: null, // weekday/saturday/sunday
  blocks: [], // [{startMin,endMin,label}]
  roster: [], // [{techId, status:'active'|'out', start:'HH:MM', end:'HH:MM', preset:'8-4'}]
  grid: {}, // key: cellKey => {techId, locked:boolean}
  lastSavedAt: null
};

// ---------------- time helpers ----------------
function toMinutes(hhmm){
  const [h,m] = hhmm.split(":").map(Number);
  return h*60+m;
}
function toHHMM(min){
  const h = Math.floor(min/60).toString().padStart(2,"0");
  const m = (min%60).toString().padStart(2,"0");
  return `${h}:${m}`;
}
function blockLabel(startMin,endMin){
  const s = formatAMPM(startMin);
  const e = formatAMPM(endMin);
  return `${s}–${e}`;
}
function formatAMPM(min){
  let h = Math.floor(min/60);
  const m = min%60;
  const ampm = h>=12 ? "p" : "a";
  h = h%12; if(h===0) h = 12;
  const mm = m===0 ? "" : `:${m.toString().padStart(2,"0")}`;
  return `${h}${mm}${ampm}`;
}
function dateToISO(d){
  // d is Date
  const y = d.getFullYear();
  const m = (d.getMonth()+1).toString().padStart(2,"0");
  const day = d.getDate().toString().padStart(2,"0");
  return `${y}-${m}-${day}`;
}
function inferDayType(dateISO){
  const d = new Date(dateISO+"T12:00:00");
  const dow = d.getDay(); // 0 Sun
  if(dow===0) return "sunday";
  if(dow===6) return "saturday";
  return "weekday";
}
function getHoursForDayType(dayType){
  return RULES.hours[dayType];
}
function buildBlocks(dayType){
  const hours = getHoursForDayType(dayType);
  const openMin = toMinutes(hours.open);
  const closeMin = toMinutes(hours.close);
  const step = RULES.timeBlockMinutes;
  const blocks = [];
  for(let t=openMin; t<closeMin; t+=step){
    const end = Math.min(t+step, closeMin);
    blocks.push({ startMin: t, endMin: end, label: blockLabel(t,end) });
  }
  return blocks;
}

// ---------------- storage ----------------
function storageKey(dateISO){ return STORAGE_KEY_PREFIX + dateISO; }

function saveState(){
  if(!state.dateISO) return;
  state.lastSavedAt = new Date().toISOString();
  localStorage.setItem(storageKey(state.dateISO), JSON.stringify(state));
  updateSubtitle();
}

function loadState(dateISO){
  const raw = localStorage.getItem(storageKey(dateISO));
  if(!raw) return null;
  try { return JSON.parse(raw); } catch { return null; }
}

function resetDay(){
  if(!state.dateISO) return;
  localStorage.removeItem(storageKey(state.dateISO));
  initNewDay(state.dateISO);
  gotoStep(1);
  renderAll();
  saveState();
}

// ---------------- UI step navigation ----------------
function gotoStep(n){
  ["step1","step2","step3","step4"].forEach((id, idx)=>{
    $(id).classList.toggle("hidden", (idx+1)!==n);
  });
  ["stepDot1","stepDot2","stepDot3","stepDot4"].forEach((id, idx)=>{
    $(id).classList.toggle("active", (idx+1)===n);
  });
}

function updateSubtitle(){
  const hours = getHoursForDayType(state.dayType);
  const saved = state.lastSavedAt ? `Saved ${new Date(state.lastSavedAt).toLocaleTimeString()}` : "Not saved yet";
  $("subtitle").textContent = `${state.dateISO} • ${state.dayType} • Hours ${hours.open}-${hours.close} • ${saved}`;
  $("dayTypePill").textContent = state.dayType;
  $("hoursPill").textContent = `${hours.open}–${hours.close}`;
}

// ---------------- tech lookup ----------------
function techById(id){ return TECHS.find(t=>t.id===id); }
function rosterEntry(id){ return state.roster.find(r=>r.techId===id); }

// ---------------- initialization ----------------
async function boot(){
  RULES = await fetch("./rules.json").then(r=>r.json());
  TECHS = await fetch("./techs.json").then(r=>r.json());

  // default date = today (local)
  const todayISO = dateToISO(new Date());
  $("datePicker").value = todayISO;

  // load or init
  const existing = loadState(todayISO);
  if(existing){
    state = existing;
  } else {
    initNewDay(todayISO);
    saveState();
  }

  wireUI();
  renderAll();
  gotoStep(1);
}

function initNewDay(dateISO){
  state.dateISO = dateISO;
  state.dayType = inferDayType(dateISO);
  state.blocks = buildBlocks(state.dayType);
  state.roster = [];
  state.grid = {};
  state.lastSavedAt = null;
}

function wireUI(){
  $("datePicker").addEventListener("change", (e)=>{
    const dateISO = e.target.value;
    if(!dateISO) return;

    const existing = loadState(dateISO);
    if(existing){
      state = existing;
    } else {
      initNewDay(dateISO);
      saveState();
    }
    renderAll();
    gotoStep(1);
  });

  $("toStep2").addEventListener("click", ()=>{
    gotoStep(2);
  });

  $("backTo1").addEventListener("click", ()=> gotoStep(1));
  $("toStep3").addEventListener("click", ()=>{
    gotoStep(3);
    renderShiftsTable();
    saveState();
  });

  $("backTo2").addEventListener("click", ()=> gotoStep(2));

  $("generateBtn").addEventListener("click", ()=>{
    // ensure shifts are valid
    normalizeRosterTimes();
    generateGrid({ preserveLocked: true, fromBlockIndex: 0 });
    gotoStep(4);
    renderGridAndPanels();
    saveState();
  });

  $("backTo3").addEventListener("click", ()=> gotoStep(3));

  $("btnResetDay").addEventListener("click", resetDay);
  $("btnSaveNow").addEventListener("click", saveState);

  $("techSearch").addEventListener("input", renderTechList);

  $("btnRerun").addEventListener("click", ()=>{
    generateGrid({ preserveLocked: true, fromBlockIndex: 0 });
    renderGridAndPanels();
    saveState();
  });

  $("btnExportCsv").addEventListener("click", exportCSV);

  // modal
  $("btnRosterChange").addEventListener("click", openRosterModal);
  $("closeModal").addEventListener("click", closeRosterModal);
  $("modal").addEventListener("click", (e)=>{
    if(e.target.id === "modal") closeRosterModal();
  });
  $("applyRosterChanges").addEventListener("click", applyRosterModal);
}

// ---------------- rendering: step 2 roster selection ----------------
function renderAll(){
  updateSubtitle();
  renderTechList();
  renderSelectedList();
  renderShiftsTable();
  renderGridAndPanels();
}

function renderTechList(){
  const q = ($("techSearch").value || "").toLowerCase().trim();
  const list = $("techList");
  list.innerHTML = "";

  TECHS
    .filter(t => !q || t.name.toLowerCase().includes(q))
    .forEach(t=>{
      const selected = state.roster.some(r=>r.techId===t.id);
      const row = document.createElement("div");
      row.className = "tech-row";

      const left = document.createElement("div");
      left.className = "tech-left";

      const cb = document.createElement("input");
      cb.type = "checkbox";
      cb.checked = selected;
      cb.addEventListener("change", ()=>{
        if(cb.checked){
          addToRoster(t.id);
        } else {
          removeFromRoster(t.id);
        }
        renderSelectedList();
        saveState();
      });

      const name = document.createElement("div");
      name.innerHTML = `<div style="font-weight:800;">${t.name}</div>`;

      const badges = document.createElement("div");
      badges.className = "badges";
      // hard cannot
      const cannot = t.hard?.cannot || [];
      if(cannot.length){
        badges.appendChild(makeBadge("hard", `Cannot: ${cannot.join(", ")}`));
      }
      // medical limits
      const maxRel = t.hard?.maxHoursPerDay?.Release;
      const maxDT = t.hard?.maxHoursPerDay?.DriveThru;
      if(maxRel || maxDT){
        const parts = [];
        if(maxRel) parts.push(`Rel≤${maxRel}h`);
        if(maxDT) parts.push(`DT≤${maxDT}h`);
        badges.appendChild(makeBadge("med", parts.join(" ")));
      }
      // soft dislikes
      const dislikes = t.soft?.dislikes || [];
      if(dislikes.length){
        badges.appendChild(makeBadge("soft", `Dislikes: ${dislikes.join(", ")}`));
      }

      name.appendChild(badges);

      left.appendChild(cb);
      left.appendChild(name);

      const right = document.createElement("div");
      right.className = "small";
      const strong = t.skills?.strong || [];
      right.textContent = strong.length ? `Strong: ${strong.slice(0,3).join(", ")}` : "";

      row.appendChild(left);
      row.appendChild(right);
      list.appendChild(row);
    });
}

function makeBadge(kind, text){
  const b = document.createElement("span");
  b.className = `badge ${kind}`;
  b.textContent = text;
  return b;
}

function addToRoster(techId){
  if(state.roster.some(r=>r.techId===techId)) return;
  // default preset = Custom with open/close (so it fits day)
  const hours = getHoursForDayType(state.dayType);
  state.roster.push({
    techId,
    status: "active",
    preset: "Custom",
    start: hours.open,
    end: hours.close
  });
}

function removeFromRoster(techId){
  state.roster = state.roster.filter(r=>r.techId!==techId);
  // remove from grid any placements
  Object.keys(state.grid).forEach(k=>{
    if(state.grid[k]?.techId===techId){
      delete state.grid[k];
    }
  });
}

function renderSelectedList(){
  const box = $("selectedList");
  box.innerHTML = "";

  if(state.roster.length===0){
    box.innerHTML = `<div class="small">No one selected yet.</div>`;
    return;
  }

  state.roster
    .slice()
    .sort((a,b)=> techById(a.techId).name.localeCompare(techById(b.techId).name))
    .forEach(r=>{
      const t = techById(r.techId);
      const item = document.createElement("div");
      item.className = "selected-item";
      item.innerHTML = `
        <div>
          <div style="font-weight:800;">${t.name}</div>
          <div class="small">${r.status==="out" ? "OUT" : `${r.start}–${r.end}`}</div>
        </div>
        <button class="btn" title="Remove">Remove</button>
      `;
      item.querySelector("button").addEventListener("click", ()=>{
        removeFromRoster(r.techId);
        renderTechList();
        renderSelectedList();
        saveState();
      });
      box.appendChild(item);
    });
}

// ---------------- step 3 shifts ----------------
function renderShiftsTable(){
  const body = $("shiftsTbody");
  if(!body) return;
  body.innerHTML = "";

  const hours = getHoursForDayType(state.dayType);

  state.roster
    .slice()
    .sort((a,b)=> techById(a.techId).name.localeCompare(techById(b.techId).name))
    .forEach(r=>{
      const t = techById(r.techId);

      const tr = document.createElement("tr");

      const tdName = document.createElement("td");
      tdName.textContent = t.name;

      const tdPreset = document.createElement("td");
      const sel = document.createElement("select");
      SHIFT_PRESETS.forEach(p=>{
        const opt = document.createElement("option");
        opt.value = p.label;
        opt.textContent = p.label;
        if(r.preset===p.label) opt.selected = true;
        sel.appendChild(opt);
      });
      sel.addEventListener("change", ()=>{
        r.preset = sel.value;
        const preset = SHIFT_PRESETS.find(p=>p.label===r.preset);
        if(preset && preset.start && preset.end){
          r.start = preset.start;
          r.end = preset.end;
        } else {
          // custom default to store open/close if empty
          r.start = r.start || hours.open;
          r.end = r.end || hours.close;
        }
        renderShiftsTable();
        saveState();
      });
      tdPreset.appendChild(sel);

      const tdStart = document.createElement("td");
      const start = document.createElement("input");
      start.type = "time";
      start.value = r.start || hours.open;
      start.disabled = (r.preset!=="Custom");
      start.addEventListener("change", ()=>{
        r.start = start.value;
        renderShiftsTable();
        saveState();
      });
      tdStart.appendChild(start);

      const tdEnd = document.createElement("td");
      const end = document.createElement("input");
      end.type = "time";
      end.value = r.end || hours.close;
      end.disabled = (r.preset!=="Custom");
      end.addEventListener("change", ()=>{
        r.end = end.value;
        renderShiftsTable();
        saveState();
      });
      tdEnd.appendChild(end);

      const tdHours = document.createElement("td");
      const mins = Math.max(0, toMinutes(r.end) - toMinutes(r.start));
      tdHours.textContent = (mins/60).toFixed(1);

      const tdBlocks = document.createElement("td");
      const blocks = Math.ceil(mins / RULES.timeBlockMinutes);
      tdBlocks.textContent = blocks;

      const tdStatus = document.createElement("td");
      const st = document.createElement("select");
      ["active","out"].forEach(v=>{
        const opt = document.createElement("option");
        opt.value = v;
        opt.textContent = v.toUpperCase();
        if(r.status===v) opt.selected = true;
        st.appendChild(opt);
      });
      st.addEventListener("change", ()=>{
        r.status = st.value;
        renderShiftsTable();
        saveState();
      });
      tdStatus.appendChild(st);

      tr.appendChild(tdName);
      tr.appendChild(tdPreset);
      tr.appendChild(tdStart);
      tr.appendChild(tdEnd);
      tr.appendChild(tdHours);
      tr.appendChild(tdBlocks);
      tr.appendChild(tdStatus);

      body.appendChild(tr);
    });
}

function normalizeRosterTimes(){
  // ensure start < end; if not, snap to store hours
  const hours = getHoursForDayType(state.dayType);
  state.roster.forEach(r=>{
    const s = toMinutes(r.start || hours.open);
    const e = toMinutes(r.end || hours.close);
    if(e<=s){
      r.start = hours.open;
      r.end = hours.close;
      r.preset = "Custom";
    }
  });
}

// ---------------- grid generation ----------------
function cellKey(rowKey, blockIndex){ return `${rowKey}__${blockIndex}`; }

function isTechWorkingThisBlock(techId, block){
  const r = rosterEntry(techId);
  if(!r || r.status!=="active") return false;
  const s = toMinutes(r.start);
  const e = toMinutes(r.end);
  return block.startMin >= s && block.endMin <= e;
}

function stationOfRow(rowKey){
  const row = RULES.stations.rows.find(r=>r.key===rowKey);
  return row?.station || rowKey;
}

function getAssignedTech(rowKey, blockIndex){
  const k = cellKey(rowKey, blockIndex);
  return state.grid[k]?.techId || null;
}
function setAssignment(rowKey, blockIndex, techId){
  const k = cellKey(rowKey, blockIndex);
  if(!techId){
    delete state.grid[k];
    return;
  }
  if(!state.grid[k]) state.grid[k] = { techId, locked: false };
  else state.grid[k].techId = techId;
}

function toggleLock(rowKey, blockIndex){
  const k = cellKey(rowKey, blockIndex);
  if(!state.grid[k]) return;
  state.grid[k].locked = !state.grid[k].locked;
}

function generateGrid({ preserveLocked, fromBlockIndex }){
  // Build the target rows in order
  let rows = RULES.stations.rows.slice();

  // apply slot priority tweak
  if(RULES.priorities?.prioritizeRelease3OverProduct3){
    // ensure Release3 is before Product3 in list
    const iRel3 = rows.findIndex(r=>r.key==="Release3");
    const iProd3 = rows.findIndex(r=>r.key==="Product3");
    if(iRel3>-1 && iProd3>-1 && iRel3>iProd3){
      const rel3 = rows.splice(iRel3,1)[0];
      rows.splice(iProd3,0,rel3);
    }
  }

  // wipe unlocked cells from fromBlockIndex onward
  for(let bi=fromBlockIndex; bi<state.blocks.length; bi++){
    rows.forEach(row=>{
      const k = cellKey(row.key, bi);
      if(state.grid[k]){
        if(preserveLocked && state.grid[k].locked) return;
        delete state.grid[k];
      }
    });
  }

  // Greedy fill per block
  for(let bi=fromBlockIndex; bi<state.blocks.length; bi++){
    const block = state.blocks[bi];

    // Determine minimum targets for this block
    const req = {
      Data: 1,
      Product: 1,
      DriveThru: 1,
      Release: Math.max(2, RULES.coverage.releaseMinAllDay || 2)
    };

    // peak release rule
    const peak = RULES.coverage.releasePeak;
    if(peak){
      const peakStart = toMinutes(peak.start);
      const peakEnd = toMinutes(peak.end);
      if(block.startMin >= peakStart && block.endMin <= peakEnd){
        req.Release = Math.max(req.Release, peak.min || 4);
      }
    }

    // misc weekday 12-2 required
    let miscRequired = false;
    if(state.dayType==="weekday" && RULES.coverage.miscWeekday?.required){
      const ms = toMinutes(RULES.coverage.miscWeekday.start);
      const me = toMinutes(RULES.coverage.miscWeekday.end);
      if(block.startMin >= ms && block.endMin <= me) miscRequired = true;
    }

    // Build list of available techIds for this block
    const available = state.roster
      .filter(r => r.status==="active")
      .map(r => r.techId)
      .filter(id => isTechWorkingThisBlock(id, block));

    // We'll assign by row order, but with required rows first.
    // Construct a desired fill plan:
    const desiredRows = [];

    // helper to push N rows of a given station in row order
    const rowsOfStation = (station) => rows.filter(r => r.station===station);

    // required: Release slots first
    desiredRows.push(...rowsOfStation("Release").slice(0, req.Release));
    // required: DriveThru1 then maybe more later
    desiredRows.push(...rowsOfStation("DriveThru").slice(0, req.DriveThru));
    desiredRows.push(...rowsOfStation("Data").slice(0, req.Data));
    desiredRows.push(...rowsOfStation("Product").slice(0, req.Product));

    // misc if required
    if(miscRequired){
      const miscRow = rows.find(r=>r.station==="Misc");
      if(miscRow) desiredRows.push(miscRow);
    }

    // optional fill remaining rows if people still unassigned
    const optionalRows = rows.filter(r=> !desiredRows.some(d=>d.key===r.key));
    desiredRows.push(...optionalRows);

    // Track usage so we don't put the same tech in multiple rows in same block
    const used = new Set();

    // Seed with locked cells if any
    desiredRows.forEach(row=>{
      const k = cellKey(row.key, bi);
      if(state.grid[k]?.techId){
        used.add(state.grid[k].techId);
      }
    });

    // For each row, assign best candidate
    for(const row of desiredRows){
      const k = cellKey(row.key, bi);
      if(state.grid[k]?.techId) continue; // already (locked or manually)
      const station = row.station;

      // enforce DT2 rule: only open after DT1 + Release1-3 staffed
      if(RULES.coverage.driveThru2Rule && row.key==="DriveThru2"){
        const dt1 = getAssignedTech("DriveThru1", bi);
        const r1 = getAssignedTech("Release1", bi);
        const r2 = getAssignedTech("Release2", bi);
        const r3 = getAssignedTech("Release3", bi);
        if(!(dt1 && r1 && r2 && r3)){
          continue; // leave empty
        }
      }

      const candidates = available
        .filter(id => !used.has(id))
        .filter(id => isAllowedAtStation(id, station, bi))
        .sort((a,b)=> scoreCandidate(bi, station, a) - scoreCandidate(bi, station, b)); // desc

      if(candidates.length){
        const pick = candidates[0];
        setAssignment(row.key, bi, pick);
        used.add(pick);
      }
    }
  }
}

function isAllowedAtStation(techId, station, blockIndex){
  const t = techById(techId);
  if(!t) return false;

  // hard cannot
  const cannot = t.hard?.cannot || [];
  if(cannot.includes(station)) return false;

  // must be working in this block
  const block = state.blocks[blockIndex];
  if(!isTechWorkingThisBlock(techId, block)) return false;

  // medical caps: Release/DriveThru caps by day
  const maxRel = t.hard?.maxHoursPerDay?.Release;
  const maxDT  = t.hard?.maxHoursPerDay?.DriveThru;
  const isRel = station==="Release";
  const isDT = station==="DriveThru";
  if(isRel && maxRel != null){
    const relHours = hoursAssignedToStation(techId, "Release");
    if(relHours >= maxRel) return false;
  }
  if(isDT && maxDT != null){
    const dtHours = hoursAssignedToStation(techId, "DriveThru");
    if(dtHours >= maxDT) return false;
  }

  // if later you add a combined "CustomerFacing" limit, apply it here
  const maxCF = t.hard?.maxHoursPerDay?.CustomerFacing;
  if(maxCF != null && (isRel || isDT)){
    const cfHours = hoursAssignedToStations(techId, ["Release","DriveThru"]);
    if(cfHours >= maxCF) return false;
  }

  return true;
}

function hoursAssignedToStation(techId, station){
  const stepHours = RULES.timeBlockMinutes / 60;
  let count = 0;
  for(let bi=0; bi<state.blocks.length; bi++){
    for(const row of RULES.stations.rows){
      if(row.station!==station) continue;
      const k = cellKey(row.key, bi);
      if(state.grid[k]?.techId===techId) count++;
    }
  }
  return count * stepHours;
}
function hoursAssignedToStations(techId, stations){
  return stations.reduce((sum, st)=> sum + hoursAssignedToStation(techId, st), 0);
}

function scoreCandidate(blockIndex, station, techId){
  const t = techById(techId);
  let score = 0;

  // prefer strong
  if((t.skills?.strong||[]).includes(station)) score += 30;

  // avoid weak
  if((t.skills?.weak||[]).includes(station)) score -= 12;

  // dislikes / prefers not
  if((t.soft?.dislikes||[]).includes(station)) score -= 18;
  if((t.soft?.prefersNot||[]).includes(station)) score -= 10;

  // rotate: penalize same station consecutive blocks (any row of that station)
  const prevBi = blockIndex - 1;
  if(prevBi >= 0){
    const prevStation = stationAssignedToTechInBlock(techId, prevBi);
    if(prevStation === station) score -= 20;
  }

  // if needs pairing in Data/Product, slightly penalize placing them there (until you add pairing logic)
  if((t.pairing?.needs||[]).includes(station)) score -= 8;

  // small randomness to avoid same patterns
  score += Math.random() * 2;

  return score;
}

function stationAssignedToTechInBlock(techId, blockIndex){
  for(const row of RULES.stations.rows){
    const k = cellKey(row.key, blockIndex);
    if(state.grid[k]?.techId===techId) return row.station;
  }
  return null;
}

// ---------------- render grid & panels ----------------
function renderGridAndPanels(){
  if(!$("gridContainer")) return;
  if(!state.blocks?.length || state.roster.length===0){
    $("gridContainer").innerHTML = `<div class="hint">No grid yet. Select roster, set shifts, then generate.</div>`;
    $("rosterSidebar").innerHTML = "";
    $("coveragePanel").innerHTML = "";
    $("issuesPanel").innerHTML = "";
    return;
  }
  renderRosterSidebar();
  renderGrid();
  renderCoverageAndIssues();
}

function renderRosterSidebar(){
  const box = $("rosterSidebar");
  box.innerHTML = "";

  const rosterSorted = state.roster
    .slice()
    .sort((a,b)=> techById(a.techId).name.localeCompare(techById(b.techId).name));

  rosterSorted.forEach(r=>{
    const t = techById(r.techId);
    const item = document.createElement("div");
    item.className = "roster-item";
    item.draggable = (r.status==="active");
    item.dataset.dragType = "roster";
    item.dataset.techId = r.techId;
    item.addEventListener("dragstart", onDragStart);

    item.innerHTML = `
      <div>
        <div class="roster-name">${t.name}</div>
        <div class="small">${r.status==="out" ? "OUT" : `${r.start}–${r.end}`}</div>
      </div>
      <div class="roster-actions">
        <button class="iconbtn" title="Mark out / edit shift" data-action="edit">✎</button>
      </div>
    `;
    item.querySelector('[data-action="edit"]').addEventListener("click", ()=>{
      openRosterModal();
    });

    box.appendChild(item);
  });
}

function renderGrid(){
  const rows = RULES.stations.rows;
  const blocks = state.blocks;

  const wrap = document.createElement("div");
  wrap.className = "grid";

  const table = document.createElement("table");

  // header
  const thead = document.createElement("thead");
  const hr = document.createElement("tr");
  const h0 = document.createElement("th");
  h0.textContent = "Station";
  hr.appendChild(h0);

  blocks.forEach((b, bi)=>{
    const th = document.createElement("th");
    th.textContent = b.label;
    th.title = `${toHHMM(b.startMin)}-${toHHMM(b.endMin)}`;
    hr.appendChild(th);
  });
  thead.appendChild(hr);
  table.appendChild(thead);

  const tbody = document.createElement("tbody");

  rows.forEach(row=>{
    const tr = document.createElement("tr");
    const tdLabel = document.createElement("td");
    tdLabel.textContent = row.label;
    tr.appendChild(tdLabel);

    blocks.forEach((b, bi)=>{
      const td = document.createElement("td");
      const cell = document.createElement("div");
      cell.className = "cell";
      cell.dataset.rowKey = row.key;
      cell.dataset.blockIndex = String(bi);

      cell.addEventListener("dragover", onDragOver);
      cell.addEventListener("dragleave", onDragLeave);
      cell.addEventListener("drop", onDrop);

      // render tile if exists
      const assignedId = getAssignedTech(row.key, bi);
      if(assignedId){
        const tech = techById(assignedId);
        const k = cellKey(row.key, bi);
        const locked = !!state.grid[k]?.locked;

        const tile = document.createElement("div");
        tile.className = "tile";
        tile.draggable = true;
        tile.dataset.dragType = "cell";
        tile.dataset.techId = assignedId;
        tile.dataset.fromRowKey = row.key;
        tile.dataset.fromBlockIndex = String(bi);
        tile.addEventListener("dragstart", onDragStart);

        tile.innerHTML = `
          <div class="name">${tech.name}</div>
          <div class="icons">
            <button class="iconbtn ${locked ? "locked" : ""}" title="Lock/unlock">🔒</button>
          </div>
        `;
        tile.querySelector("button").addEventListener("click", (e)=>{
          e.stopPropagation();
          toggleLock(row.key, bi);
          renderGridAndPanels();
          saveState();
        });

        // classify cell severity
        const sev = cellSeverity(row.key, bi, assignedId);
        cell.classList.add(sev);

        cell.appendChild(tile);
      }

      td.appendChild(cell);
      tr.appendChild(td);
    });

    tbody.appendChild(tr);
  });

  table.appendChild(tbody);
  wrap.appendChild(table);

  const container = $("gridContainer");
  container.innerHTML = "";
  container.appendChild(wrap);
}

function cellSeverity(rowKey, blockIndex, techId){
  const station = stationOfRow(rowKey);
  const block = state.blocks[blockIndex];

  // hard: not working this block
  if(!isTechWorkingThisBlock(techId, block)) return "bad";

  // hard: cannot station
  const t = techById(techId);
  if((t.hard?.cannot||[]).includes(station)) return "bad";

  // hard: medical caps exceeded (based on full-day assignments)
  const maxRel = t.hard?.maxHoursPerDay?.Release;
  if(station==="Release" && maxRel!=null && hoursAssignedToStation(techId, "Release") > maxRel) return "bad";
  const maxDT = t.hard?.maxHoursPerDay?.DriveThru;
  if(station==="DriveThru" && maxDT!=null && hoursAssignedToStation(techId, "DriveThru") > maxDT) return "bad";

  // soft: dislikes / weak
  if((t.soft?.dislikes||[]).includes(station)) return "warn";
  if((t.skills?.weak||[]).includes(station)) return "warn";

  return "ok";
}

// ---------------- coverage & issues ----------------
function renderCoverageAndIssues(){
  const coverageBox = $("coveragePanel");
  const issuesBox = $("issuesPanel");
  coverageBox.innerHTML = "";
  issuesBox.innerHTML = "";

  const allIssues = [];
  state.blocks.forEach((block, bi)=>{
    const counts = countStationsForBlock(bi);
    const req = requiredForBlock(block);

    // badge severity
    let badge = "ok";
    const lines = [];

    // required at least 1
    for(const station of RULES.stations.requiredAtLeastOne){
      const have = counts[station] || 0;
      if(have < 1){
        badge = "bad";
        lines.push(`${station}: 0 (needs 1)`);
      } else {
        lines.push(`${station}: ${have}`);
      }
    }

    // Release minimum (all day / peak)
    if((counts.Release||0) < req.Release){
      badge = "bad";
      lines.push(`Release: ${counts.Release||0} (needs ${req.Release})`);
    }

    // Misc if required
    if(req.MiscRequired && (counts.Misc||0) < 1){
      badge = badge==="bad" ? "bad" : "warn";
      lines.push(`Misc: ${counts.Misc||0} (needs 1)`);
    }

    // Build coverage card
    const card = document.createElement("div");
    card.className = "coverage-block";
    card.innerHTML = `
      <div class="coverage-top">
        <div class="coverage-title">${block.label}</div>
        <div class="coverage-badge ${badge}">${badge.toUpperCase()}</div>
      </div>
      <div class="coverage-lines"></div>
    `;
    const linesBox = card.querySelector(".coverage-lines");
    lines.forEach(l=>{
      const div = document.createElement("div");
      div.textContent = l;
      linesBox.appendChild(div);
    });
    coverageBox.appendChild(card);

    // Issues from cells this block
    for(const row of RULES.stations.rows){
      const techId = getAssignedTech(row.key, bi);
      if(!techId) continue;
      const sev = cellSeverity(row.key, bi, techId);
      if(sev==="bad"){
        allIssues.push({ type:"bad", text:`${block.label}: ${techById(techId).name} invalid at ${row.label}` });
      } else if(sev==="warn"){
        allIssues.push({ type:"warn", text:`${block.label}: ${techById(techId).name} warning at ${row.label}` });
      }
    }
  });

  if(allIssues.length===0){
    issuesBox.innerHTML = `<div class="small">No issues detected.</div>`;
    return;
  }
  allIssues.slice(0,50).forEach(i=>{
    const div = document.createElement("div");
    div.className = `issue ${i.type}`;
    div.textContent = i.text;
    issuesBox.appendChild(div);
  });
}

function countStationsForBlock(blockIndex){
  const counts = {};
  for(const row of RULES.stations.rows){
    const techId = getAssignedTech(row.key, blockIndex);
    if(!techId) continue;
    const station = row.station;
    counts[station] = (counts[station] || 0) + 1;
  }
  return counts;
}

function requiredForBlock(block){
  const req = {
    Data: 1,
    Product: 1,
    DriveThru: 1,
    Release: Math.max(2, RULES.coverage.releaseMinAllDay || 2),
    MiscRequired: false
  };

  const peak = RULES.coverage.releasePeak;
  if(peak){
    const ps = toMinutes(peak.start);
    const pe = toMinutes(peak.end);
    if(block.startMin >= ps && block.endMin <= pe){
      req.Release = Math.max(req.Release, peak.min || 4);
    }
  }

  if(state.dayType==="weekday" && RULES.coverage.miscWeekday?.required){
    const ms = toMinutes(RULES.coverage.miscWeekday.start);
    const me = toMinutes(RULES.coverage.miscWeekday.end);
    if(block.startMin >= ms && block.endMin <= me){
      req.MiscRequired = true;
    }
  }

  return req;
}

// ---------------- drag & drop ----------------
let dragPayload = null;

function onDragStart(e){
  const el = e.currentTarget;
  dragPayload = {
    type: el.dataset.dragType, // 'roster' or 'cell'
    techId: el.dataset.techId,
    fromRowKey: el.dataset.fromRowKey || null,
    fromBlockIndex: el.dataset.fromBlockIndex != null ? Number(el.dataset.fromBlockIndex) : null
  };
  e.dataTransfer.effectAllowed = "move";
  e.dataTransfer.setData("text/plain", JSON.stringify(dragPayload));
}

function onDragOver(e){
  e.preventDefault();
  const cell = e.currentTarget;
  const rowKey = cell.dataset.rowKey;
  const bi = Number(cell.dataset.blockIndex);

  const allowed = canDropInto(rowKey, bi, dragPayload);
  cell.classList.toggle("drop-ok", allowed);
  cell.classList.toggle("drop-bad", !allowed);
}

function onDragLeave(e){
  const cell = e.currentTarget;
  cell.classList.remove("drop-ok","drop-bad");
}

function onDrop(e){
  e.preventDefault();
  const cell = e.currentTarget;
  cell.classList.remove("drop-ok","drop-bad");

  if(!dragPayload) return;
  const rowKey = cell.dataset.rowKey;
  const bi = Number(cell.dataset.blockIndex);

  if(!canDropInto(rowKey, bi, dragPayload)) return;

  // locked target?
  const kTarget = cellKey(rowKey, bi);
  if(state.grid[kTarget]?.locked) return;

  const targetTech = getAssignedTech(rowKey, bi);
  const incomingTech = dragPayload.techId;

  // if dragging from a cell -> swap
  if(dragPayload.type==="cell"){
    const fromRow = dragPayload.fromRowKey;
    const fromBi = dragPayload.fromBlockIndex;
    const kFrom = cellKey(fromRow, fromBi);

    if(state.grid[kFrom]?.locked) return;

    // swap techs
    setAssignment(rowKey, bi, incomingTech);

    if(targetTech){
      setAssignment(fromRow, fromBi, targetTech);
    } else {
      // moved into empty: clear origin
      setAssignment(fromRow, fromBi, null);
    }
  } else {
    // from roster: assign into target; if occupied, swap "back to roster" = replace (we'll do swap with empty origin: effectively replace)
    setAssignment(rowKey, bi, incomingTech);
  }

  renderGridAndPanels();
  saveState();
}

function canDropInto(rowKey, blockIndex, payload){
  if(!payload || !payload.techId) return false;

  // must be working and allowed
  const station = stationOfRow(rowKey);
  if(!isAllowedAtStation(payload.techId, station, blockIndex)) return false;

  // prevent duplicates in same block (one tech in multiple rows at same time)
  const alreadyInBlock = stationAssignedToTechInBlock(payload.techId, blockIndex);
  if(alreadyInBlock){
    // allow if it's the same cell being dragged (no-op)
    if(payload.type==="cell" && payload.fromBlockIndex===blockIndex && payload.fromRowKey===rowKey) return true;
    return false;
  }

  // if dragging from cell, allow swap only if target tech is allowed in origin cell
  if(payload.type==="cell"){
    const targetTech = getAssignedTech(rowKey, blockIndex);
    if(targetTech){
      const originStation = stationOfRow(payload.fromRowKey);
      const okBack = isAllowedAtStation(targetTech, originStation, payload.fromBlockIndex);
      if(!okBack) return false;
    }
  }

  return true;
}

// ---------------- modal roster change ----------------
function openRosterModal(){
  const modal = $("modal");
  const body = $("modalBody");
  body.innerHTML = "";

  // rerunFrom options
  const sel = $("rerunFromSelect");
  sel.innerHTML = "";
  state.blocks.forEach((b, i)=>{
    const opt = document.createElement("option");
    opt.value = String(i);
    opt.textContent = b.label;
    sel.appendChild(opt);
  });
  sel.value = "0";

  state.roster
    .slice()
    .sort((a,b)=> techById(a.techId).name.localeCompare(techById(b.techId).name))
    .forEach(r=>{
      const t = techById(r.techId);

      const row = document.createElement("div");
      row.className = "modal-row";

      // name
      const name = document.createElement("div");
      name.innerHTML = `<div style="font-weight:800;">${t.name}</div><div class="small">${r.start}–${r.end}</div>`;

      // status
      const status = document.createElement("select");
      ["active","out"].forEach(v=>{
        const opt = document.createElement("option");
        opt.value = v;
        opt.textContent = v.toUpperCase();
        if(r.status===v) opt.selected = true;
        status.appendChild(opt);
      });

      // preset
      const preset = document.createElement("select");
      SHIFT_PRESETS.forEach(p=>{
        const opt = document.createElement("option");
        opt.value = p.label;
        opt.textContent = p.label;
        if(r.preset===p.label) opt.selected = true;
        preset.appendChild(opt);
      });

      // times
      const start = document.createElement("input");
      start.type = "time";
      start.value = r.start;
      start.disabled = (r.preset!=="Custom");

      const end = document.createElement("input");
      end.type = "time";
      end.value = r.end;
      end.disabled = (r.preset!=="Custom");

      preset.addEventListener("change", ()=>{
        r.preset = preset.value;
        const p = SHIFT_PRESETS.find(x=>x.label===r.preset);
        if(p && p.start && p.end){
          r.start = p.start;
          r.end = p.end;
          start.value = r.start;
          end.value = r.end;
        }
        start.disabled = (r.preset!=="Custom");
        end.disabled = (r.preset!=="Custom");
      });

      start.addEventListener("change", ()=> r.start = start.value);
      end.addEventListener("change", ()=> r.end = end.value);

      status.addEventListener("change", ()=>{
        r.status = status.value;
      });

      row.appendChild(name);
      row.appendChild(status);
      row.appendChild(preset);
      row.appendChild(start);
      row.appendChild(end);

      body.appendChild(row);
    });

  modal.classList.remove("hidden");
}

function closeRosterModal(){
  $("modal").classList.add("hidden");
}

function applyRosterModal(){
  normalizeRosterTimes();

  const keepLocks = $("keepLocks").checked;
  if(!keepLocks){
    // unlock everything
    Object.keys(state.grid).forEach(k=>{
      if(state.grid[k]) state.grid[k].locked = false;
    });
  }

  const fromIndex = Number($("rerunFromSelect").value || "0");
  generateGrid({ preserveLocked: true, fromBlockIndex: fromIndex });
  closeRosterModal();
  renderShiftsTable();
  renderGridAndPanels();
  saveState();
}

// ---------------- export CSV ----------------
function exportCSV(){
  const rows = RULES.stations.rows;
  const blocks = state.blocks;

  // header
  const header = ["Station", ...blocks.map(b=>b.label)];
  const lines = [csvLine(header)];

  for(const row of rows){
    const line = [row.label];
    for(let bi=0; bi<blocks.length; bi++){
      const techId = getAssignedTech(row.key, bi);
      line.push(techId ? techById(techId).name : "");
    }
    lines.push(csvLine(line));
  }

  const blob = new Blob([lines.join("\n")], { type: "text/csv;charset=utf-8" });
  const url = URL.createObjectURL(blob);
  const a = document.createElement("a");
  a.href = url;
  a.download = `rotation_${state.dateISO}.csv`;
  document.body.appendChild(a);
  a.click();
  a.remove();
  URL.revokeObjectURL(url);
}

function csvLine(arr){
  return arr.map(v=>{
    const s = String(v ?? "");
    if(s.includes(",") || s.includes('"') || s.includes("\n")){
      return `"${s.replace(/"/g,'""')}"`;
    }
    return s;
  }).join(",");
}

// ---------------- start ----------------
boot().catch(err=>{
  console.error(err);
  $("subtitle").textContent = "Error loading files. Check that techs.json and rules.json exist in /rotation/.";
});
