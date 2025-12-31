# 📋 Vault Dashboard

**Cairn-PKM** · Installed: `$= dv.io.load("_cairn-pkm/VERSION.txt").then(v => v ? v.trim() : "0.5.0").catch(() => "0.5.0")`

[![Status](https://cairn.gregbilke.me/_cairn-pkm/docs/version/cairn-status.svg)](https://github.com/SFZC-WebOps/cairn-pkm/releases/latest)

## 📊 Stats

```dataviewjs
const tracks = dv.pages('"Tracks"');
const projects = tracks.where(p => p.type === "project" && p.status === "active").length;
const areas = tracks.where(p => p.type === "area" && p.status === "active").length;

const allTasks = tracks.file.tasks;
const open = allTasks.where(t => !t.completed).length;
const completed = allTasks.where(t => t.completed).length;
const overdueCount = allTasks.where(t => !t.completed && t.due && t.due < dv.date("today")).length;

dv.paragraph(`**Active:** ${projects} projects, ${areas} areas`);
dv.paragraph(`**Tasks:** ${open} open, ${completed} done`);
if (overdueCount > 0) {
  dv.paragraph(`**⚠️ Overdue:** ${overdueCount}`);
}
```

## 🚨 Alerts

```dataviewjs
const allPages = dv.pages('"Tracks"');

const overdueTasks = allPages
  .where(p => p.file.folder.includes('/tasks'))
  .where(p => p.due_date && p.due_date < dv.date("today"))
  .where(p => p.status !== "complete")
  .sort(p => p.due_date, 'asc');

const blockedWaitingTasks = allPages
  .where(p => p.file.folder.includes('/tasks'))
  .where(p => p.status === "blocked" || p.status === "waiting" || p.viz === "blocked" || p.viz === "waiting")
  .where(p => p.status !== "complete");

let hasAlerts = false;

if (overdueTasks.length > 0) {
  hasAlerts = true;
  dv.header(4, "🔴 Overdue Tasks");
  dv.table(
    ["Task", "Due", "Project", "Priority"],
    overdueTasks.map(t => [t.file.link, t.due_date, t.project, t.priority || "-"])
  );
}

if (blockedWaitingTasks.length > 0) {
  hasAlerts = true;
  dv.header(4, "⏸️ Blocked & Waiting Tasks");
  dv.table(
    ["Task", "Status", "Project", "Section"],
    blockedWaitingTasks.map(t => [t.file.link, t.status || t.viz, t.project, t.section || "-"])
  );
}

if (!hasAlerts) {
  dv.paragraph("✅ **All clear!** No urgent items.");
}
```

---

## 📅 Due This Week

<details open> <summary style="cursor: pointer; font-size: 1.1em; font-weight: 600; padding: 8px 0;"> This Week's Tasks </summary>

```dataviewjs
const weekStart = dv.date("today");
const weekEnd = weekStart.plus({days: 7});

const dueTasks = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes('/tasks'))
  .where(p => p.due_date && p.due_date >= weekStart && p.due_date <= weekEnd)
  .where(p => p.status !== "complete")
  .sort(p => p.due_date, 'asc');

if (dueTasks.length === 0) {
  dv.paragraph("✅ No tasks due this week");
} else {
  dv.table(
    ["Task", "Project", "Due", "Priority", "Status"],
    dueTasks.map(t => [t.file.link, t.project, t.due_date, t.priority || "-", t.status || "active"])
  );
}
```

</details>

---

## 📥 Inbox Items

<details open> <summary style="cursor: pointer; font-size: 1.1em; font-weight: 600; padding: 8px 0;"> Capture Inbox (<span id="inbox-count">loading...</span> items) </summary>

```dataviewjs
const items = dv.pages('"Capture"').sort(p => p.file.ctime, 'asc');

if (items.length === 0) {
  dv.paragraph("✅ **Inbox Zero!**");
} else {
  dv.table(
    ["Item", "Captured", "Age"],
    items.map(p => {
      const age = Math.round((new Date() - new Date(p.file.ctime)) / (1000 * 60 * 60 * 24));
      return [p.file.link, p.file.ctime.toFormat("yyyy-MM-dd"), (age > 7 ? "⚠️ " : "") + age + " days"];
    })
  );
}

const inboxCount = document.getElementById('inbox-count');
if (inboxCount) inboxCount.textContent = items.length;
```

</details>

---

## 📋 All Tasks by Project/Viz

<details id="main-tasks-view"> <summary style="cursor: pointer; font-size: 1.2em; font-weight: 600; padding: 12px 0; margin-bottom: 16px;"> Tasks by Project/Viz (<span id="task-count">loading...</span> tasks) </summary>

```dataviewjs
// ========== STYLES ==========
const styles = `
<style>
.dash-card {
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}
.dash-title { 
  font-size: 1.15em; 
  font-weight: 600; 
  color: #1a1a1a; 
  text-decoration: none; 
  display: block;
  margin-bottom: 12px;
  line-height: 1.4;
}
.dash-badges { display: flex; flex-wrap: wrap; gap: 8px; margin: 10px 0; }
.dash-badge {
  padding: 5px 12px;
  border-radius: 6px;
  font-size: 0.85em;
  font-weight: 500;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}
.badge-viz { background: rgba(0,0,0,0.08); color: #333; }
.badge-status { background: rgba(34,197,94,0.15); color: #166534; }
.badge-priority { background: rgba(239,68,68,0.15); color: #991b1b; }
.badge-phase { background: rgba(59,130,246,0.15); color: #1e40af; }
.badge-effort { background: rgba(168,85,247,0.15); color: #6b21a8; }
.badge-subtasks { background: rgba(99,102,241,0.15); color: #4338ca; }
.badge-orphan { background: rgba(234,179,8,0.2); color: #854d0e; }
.dash-meta { font-size: 0.9em; color: #555; margin-top: 8px; }
.dash-due { 
  display: inline-block;
  padding: 6px 12px; 
  border-radius: 6px; 
  font-size: 0.9em; 
  font-weight: 500;
  margin-top: 8px;
}
.dash-due.overdue { background: rgba(239,68,68,0.2); color: #991b1b; border: 1px solid rgba(239,68,68,0.3); }
.dash-due.upcoming { background: rgba(59,130,246,0.15); color: #1e40af; border: 1px solid rgba(59,130,246,0.3); }
.dash-due.complete { background: rgba(34,197,94,0.15); color: #166534; border: 1px solid rgba(34,197,94,0.3); }
.dash-history { margin-top: 12px; font-size: 0.95em; }
.dash-history strong { color: #1a1a1a; }
.dash-subtasks { margin: 15px 0 0 10px; }
.dash-subtasks summary { cursor: pointer; font-size: 0.9em; color: #6b7280; font-weight: 500; }
.dash-subtask-item { padding: 4px 0; font-size: 0.9em; }
.dash-subtask-done { text-decoration: line-through; color: #9ca3af; }
.dash-type-tags {
  background: rgba(255,255,255,0.5);
  padding: 12px 14px;
  border-radius: 6px;
  border: 1px solid rgba(0,0,0,0.05);
  margin-top: 15px;
}
.proj-box {
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  padding: 15px;
  border-radius: 8px;
  border-left: 4px solid #0ea5e9;
  margin-bottom: 16px;
}
.proj-status { padding: 6px 12px; border-radius: 6px; font-size: 0.95em; font-weight: 600; }
.proj-progress-bar {
  width: 200px;
  height: 20px;
  background: rgba(0,0,0,0.1);
  border-radius: 10px;
  overflow: hidden;
  display: inline-block;
}
.proj-progress-fill { height: 100%; transition: width 0.3s; }
details[open] .collapse-arrow { transform: rotate(90deg); }
details summary::-webkit-details-marker { display: none; }
details summary::marker { display: none; }
</style>
`;
dv.el("div", "").innerHTML = styles;

// ========== HELPERS ==========
const clean = v => (v == null || v === "null" || v === "NULL") ? "" : String(v);
const formatDate = d => d ? String(d).split('T')[0] : "—";
const vaultName = dv.app.vault.getName();
const today = new Date(); today.setHours(0,0,0,0);

const vizOrder = {now:1, next:2, soon:3, later:4, blocked:5, waiting:6};
const getVizOrder = v => vizOrder[v] || 10;
const sectionOrder = {"In Progress":1, "Review":2, "Blocked":3, "Backlog":4, "Complete":5};
const getSectionOrder = s => sectionOrder[s] || 10;

// Viz-based card styling
function getVizBackground(viz) {
  const backgrounds = {
    now: "linear-gradient(135deg, #ffe5e5 0%, #ffd5d5 100%)",      // Red - active work
    next: "linear-gradient(135deg, #fff9e5 0%, #ffedd5 100%)",     // Orange - eyes on
    soon: "linear-gradient(135deg, #f0ecf9 0%, #e5dff5 100%)",     // Purple - near-term
    later: "linear-gradient(135deg, #e5f9f5 0%, #d5f0eb 100%)",    // Green - backlog
    blocked: "linear-gradient(135deg, #f0f0f2 0%, #e5e5e8 100%)",  // Gray - blocked
    waiting: "linear-gradient(135deg, #fff0eb 0%, #ffe5d9 100%)"   // Light orange - waiting
  };
  return backgrounds[viz] || "linear-gradient(135deg, #f5f5f5 0%, #ececec 100%)";
}

function getVizBorderColor(viz) {
  const colors = {
    now: "#ff4444",      // Red
    next: "#ffaa00",     // Orange
    soon: "#8b5cf6",     // Purple
    later: "#00b8a9",    // Green
    blocked: "#6b7280",  // Gray
    waiting: "#ff6b35"   // Light orange
  };
  return colors[viz] || "#999999";
}

function getVizIndent(viz) {
  const indents = {now: 0, next: 10, soon: 20, blocked: 30, waiting: 40, later: 50};
  return indents[viz] || 0;
}

const statusStyles = {
  active: "background:#22c55e20;color:#166534;border:1px solid #22c55e40",
  blocked: "background:#ef444420;color:#991b1b;border:1px solid #ef444440",
  complete: "background:#a855f720;color:#6b21a8;border:1px solid #a855f740",
  planning: "background:#3b82f620;color:#1e40af;border:1px solid #3b82f640",
  onhold: "background:#6b728020;color:#374151;border:1px solid #6b728040"
};
const statusEmojis = {planning:"📋", active:"🚀", blocked:"🚫", complete:"✅", onhold:"⏸️"};

// ========== LOAD PROJECT METADATA ==========
const projectMeta = {};
for (let p of dv.pages('"Tracks"').where(p => p.file.name.endsWith('-home') && p.track_id)) {
  // If no title field, generate one from track_id
  // p012-engw-migr → "Engw Migr" or area-admin → "Admin"
  let fallbackTitle = "";
  const tid = p.track_id || "";
  if (tid.startsWith("p") && tid.match(/^p\d{3}-/)) {
    // Project: p012-engw-migr → engw-migr → Engw Migr
    fallbackTitle = tid.replace(/^p\d{3}-/, "").split("-").map(w => w.charAt(0).toUpperCase() + w.slice(1)).join(" ");
  } else if (tid.startsWith("area-")) {
    // Area: area-admin → Admin
    fallbackTitle = tid.replace(/^area-/, "").split("-").map(w => w.charAt(0).toUpperCase() + w.slice(1)).join(" ");
  }
  
  projectMeta[p.track_id] = {
    title: clean(p.title) || fallbackTitle,
    status: clean(p.status),
    summary: clean(p.summary),
    progress: p.progress
  };
}

// ========== VIZ TOGGLE ==========
// Viz filter state from localStorage (toggle buttons control this)
const showAllViz = localStorage.getItem('dash-viz-mode') === 'all';

// ========== LOAD TASKS ==========
// Get ALL tasks (unfiltered) for accurate button counts
const allTasksUnfiltered = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes('/tasks') && p.project && p.title)
  .where(p => clean(p.status).toLowerCase() !== "complete");

const nowNextCount = allTasksUnfiltered.filter(t => t.viz === "now" || t.viz === "next").length;
const allVizCount = allTasksUnfiltered.length;

// Now filter based on current mode
const allTasks = allTasksUnfiltered
  .where(p => {
    if (showAllViz) return true;
    return p.viz === "now" || p.viz === "next";
  });

// ========== LOAD FILE CONTENTS FOR HISTORY ==========
const fileContents = new Map();
for (let task of allTasks) {
  const content = await dv.io.load(task.file.path);
  fileContents.set(task.file.path, content || "");
}

// ========== PARSE TASK HISTORY ==========
function parseHistory(content) {
  if (!content) return "No history";
  const start = content.indexOf('### Task History');
  if (start === -1) return "No history";
  let section = content.substring(start);
  const end = section.indexOf('\n---');
  if (end > 0) section = section.substring(0, end);
  const lines = section.split('\n').filter(l => l.trim().startsWith('-')).slice(0, 3);
  return lines.length ? lines.map(l => "• " + l.substring(l.indexOf('-') + 1).trim()).join('<br>') : "No history";
}

// ========== BUILD PARENT-CHILD MAP ==========
const tasksByFile = new Map();
const parentMap = new Map();
for (let t of allTasks) tasksByFile.set(t.file.name, t);
for (let t of allTasks) {
  const parent = clean(t.parent_task);
  if (parent) {
    if (!parentMap.has(parent)) parentMap.set(parent, []);
    parentMap.get(parent).push(t);
  }
}
const orphans = new Set();
for (let t of allTasks) {
  const parent = clean(t.parent_task);
  if (parent && !tasksByFile.has(parent)) orphans.add(t.file.name);
}

// ========== RENDER TASK CARD ==========
function renderTask(t, level = 0, parentIndent = 0) {
  const title = clean(t.title) || "Untitled";
  const status = clean(t.status) || "—";
  const priority = clean(t.priority);
  const viz = clean(t.viz);
  const phase = clean(t.phase);
  const effort = clean(t.effort);
  const assignee = clean(t.assignee);
  const dueDate = formatDate(t.due_date);
  const created = formatDate(t.created_date);
  const history = parseHistory(fileContents.get(t.file.path));
  const fileTasks = t.file.tasks?.values ?? [];
  const subtaskCount = fileTasks.length;
  const subtaskDone = fileTasks.filter(st => st.completed).length;
  const isOrphan = orphans.has(t.file.name);
  
  // Viz-based styling
  const vizBg = getVizBackground(viz);
  const vizBorderColor = getVizBorderColor(viz);
  const totalIndent = level === 0 ? getVizIndent(viz) : parentIndent + 20;
  
  let html = `<div class="dash-card" style="background:${vizBg};margin-left:${totalIndent}px;margin-bottom:20px;border-radius:8px;border-left:5px solid ${vizBorderColor};box-shadow:0 2px 8px rgba(0,0,0,0.08);padding:18px;">`;
  html += `<a class="dash-title" href="obsidian://open?vault=${encodeURIComponent(vaultName)}&file=${encodeURIComponent(t.file.path)}">${title}</a>`;
  
  // Badges
  html += `<div class="dash-badges">`;
  if (isOrphan) html += `<span class="dash-badge badge-orphan">⚠️ Parent not found</span>`;
  if (viz) html += `<span class="dash-badge badge-viz">Viz: ${viz}</span>`;
  if (status !== "—") html += `<span class="dash-badge badge-status">Status: ${status}</span>`;
  if (priority) html += `<span class="dash-badge badge-priority">Priority: ${priority}</span>`;
  if (phase) html += `<span class="dash-badge badge-phase">Phase: ${phase}</span>`;
  if (effort) html += `<span class="dash-badge badge-effort">Effort: ${effort}</span>`;
  if (subtaskCount) html += `<span class="dash-badge badge-subtasks">📋 ${subtaskDone}/${subtaskCount}</span>`;
  html += `</div>`;
  
  // Meta line
  if (assignee || dueDate !== "—" || created !== "—") {
    html += `<div class="dash-meta">`;
    if (assignee) html += `👤 ${assignee} `;
    if (dueDate !== "—") html += `• 📅 Due: ${dueDate} `;
    if (created !== "—") html += `• 🆕 ${created}`;
    html += `</div>`;
  }
  
  // History
  html += `<div class="dash-history"><strong>📋 History:</strong><br>${history}</div>`;
  
  // Subtasks
  if (subtaskCount > 0) {
    html += `<details class="dash-subtasks" open><summary>📋 Subtasks (${subtaskDone}/${subtaskCount})</summary><div>`;
    for (let st of fileTasks) {
      const check = st.completed ? "☑" : "☐";
      const cls = st.completed ? "dash-subtask-done" : "";
      html += `<div class="dash-subtask-item ${cls}">${check} ${st.text}</div>`;
    }
    html += `</div></details>`;
  }
  
  html += `</div>`;
  
  // Render children
  const children = parentMap.get(t.file.name) || [];
  for (let child of children) {
    html += renderTask(child, level + 1, totalIndent);
  }
  
  return html;
}

// ========== RENDER PROJECT INFO BOX ==========
function renderProjectInfo(meta) {
  if (!meta || (!meta.status && meta.progress == null && !meta.summary)) return "";
  let html = `<div class="proj-box">`;
  if (meta.status || meta.progress != null) {
    html += `<div style="display:flex;gap:15px;margin-bottom:10px;flex-wrap:wrap;align-items:center;">`;
    if (meta.status) {
      const emoji = statusEmojis[meta.status] || "📌";
      const style = statusStyles[meta.status] || statusStyles.onhold;
      html += `<span class="proj-status" style="${style}">${emoji} ${meta.status}</span>`;
    }
    if (meta.progress != null) {
      const pct = meta.progress || 0;
      const color = pct >= 75 ? '#059669' : pct >= 50 ? '#0ea5e9' : pct >= 25 ? '#f59e0b' : '#6b7280';
      html += `<div style="display:flex;align-items:center;gap:8px;">`;
      html += `<strong>Progress:</strong>`;
      html += `<div class="proj-progress-bar"><div class="proj-progress-fill" style="width:${pct}%;background:${color}"></div></div>`;
      html += `<span style="font-weight:600;color:${color}">${pct}%</span></div>`;
    }
    html += `</div>`;
  }
  if (meta.summary) html += `<div><strong>Summary:</strong> ${meta.summary}</div>`;
  html += `</div>`;
  return html;
}

// ========== CONTROLS ==========
const focusActive = !showAllViz;
const allActive = showAllViz;

dv.el("div", "").innerHTML = `
<div style="display:flex;gap:24px;align-items:center;margin-bottom:16px;flex-wrap:wrap;">
  <div style="display:inline-flex;gap:8px;">
    <button onclick="localStorage.setItem('dash-viz-mode','focus');location.reload();" style="
      padding:8px 16px;
      border:2px solid ${focusActive ? '#4f46e5' : '#e5e7eb'};
      border-radius:8px;
      cursor:pointer;
      font-size:0.9em;
      font-weight:600;
      ${focusActive ? 'background:#4f46e5;color:white;' : 'background:white;color:#666;'}
    ">Now + Next only (${nowNextCount})</button>
    <button onclick="localStorage.setItem('dash-viz-mode','all');location.reload();" style="
      padding:8px 16px;
      border:2px solid ${allActive ? '#4f46e5' : '#e5e7eb'};
      border-radius:8px;
      cursor:pointer;
      font-size:0.9em;
      font-weight:600;
      ${allActive ? 'background:#4f46e5;color:white;' : 'background:white;color:#666;'}
    ">All Viz values (${allVizCount})</button>
  </div>
  <span style="color:#ccc;">|</span>
  <div style="display:flex;gap:8px;">
    <button onclick="document.querySelectorAll('[id^=tasks-]').forEach(t=>{t.style.display='block';const id=t.id.replace('tasks-','');document.querySelector('#proj-'+id+' .collapse-arrow').style.transform='rotate(90deg)';localStorage.setItem('hub-proj-'+id,'true');})" style="padding:6px 12px;border-radius:6px;border:1px solid #ddd;background:#f9f9f9;cursor:pointer;font-size:0.8em;color:#666;">📂 Expand All</button>
    <button onclick="document.querySelectorAll('[id^=tasks-]').forEach(t=>{t.style.display='none';const id=t.id.replace('tasks-','');document.querySelector('#proj-'+id+' .collapse-arrow').style.transform='rotate(0deg)';localStorage.setItem('hub-proj-'+id,'false');})" style="padding:6px 12px;border-radius:6px;border:1px solid #ddd;background:#f9f9f9;cursor:pointer;font-size:0.8em;color:#666;">📁 Collapse All</button>
  </div>
</div>`;

// ========== MAIN RENDER ==========
const projectGroups = allTasks.groupBy(t => t.project ?? "Unassigned");

for (let projGroup of projectGroups) {
  const projTasks = projGroup.rows;
  const hasNow = projTasks.some(t => t.viz === "now");
  const projId = projGroup.key.replace(/[^a-zA-Z0-9]/g, '-');
  const meta = projectMeta[projGroup.key];
  
  // Build info (always visible) and tasks (collapsible) separately
  const infoHtml = renderProjectInfo(meta);
  let tasksHtml = "";
  
  // Group by section, then viz
  const sectionGroups = [...projGroup.rows.groupBy(t => t.section ?? "(No Section)")].sort((a,b) => getSectionOrder(a.key) - getSectionOrder(b.key));
  
  for (let secGroup of sectionGroups) {
    // Filter to top-level tasks only (not children with valid parents)
    const topLevel = secGroup.rows.filter(t => {
      const parent = clean(t.parent_task);
      return !parent || !tasksByFile.has(parent);
    });
    if (topLevel.length === 0) continue;
    
    tasksHtml += `<h3 style="margin:16px 0 12px;font-size:1.1em;color:#444;">▶ ${secGroup.key} (${topLevel.length} tasks)</h3>`;
    
    const vizGroups = [...topLevel.groupBy(t => t.viz ?? "no-viz")].sort((a,b) => getVizOrder(a.key) - getVizOrder(b.key));
    for (let vizGroup of vizGroups) {
      const sorted = [...vizGroup.rows].sort((a,b) => {
        const ad = a.due_date ? new Date(a.due_date) : null;
        const bd = b.due_date ? new Date(b.due_date) : null;
        if (!ad && !bd) return 0;
        if (!ad) return 1;
        if (!bd) return -1;
        return ad - bd;
      });
      for (let t of sorted) {
        tasksHtml += renderTask(t, 0);
      }
    }
  }
  
  // Render project with always-visible info, collapsible tasks
  const nowDot = hasNow ? '<span style="color:#ff4444;margin-left:8px;font-size:0.9em;">●</span>' : '';
  const projTitle = meta?.title ? ` — ${meta.title}` : '';
  const saved = localStorage.getItem('hub-proj-' + projId);
  const isOpen = saved === 'true';
  const arrowRotation = isOpen ? 'rotate(90deg)' : 'rotate(0deg)';
  const tasksDisplay = isOpen ? 'block' : 'none';
  
  const container = dv.el("div", "");
  container.innerHTML = `
    <div id="proj-${projId}" style="margin-bottom:24px;border:1px solid rgba(0,0,0,0.1);border-radius:8px;padding:8px 16px;background:rgba(255,255,255,0.5);">
      <div onclick="
        const tasks = document.getElementById('tasks-${projId}');
        const arrow = document.querySelector('#proj-${projId} .collapse-arrow');
        const isOpen = tasks.style.display !== 'none';
        tasks.style.display = isOpen ? 'none' : 'block';
        arrow.style.transform = isOpen ? 'rotate(0deg)' : 'rotate(90deg)';
        localStorage.setItem('hub-proj-${projId}', !isOpen);
      " style="cursor:pointer;font-size:1.3em;font-weight:600;padding:12px 0;color:#1a1a1a;display:flex;align-items:center;gap:8px;">
        <span class="collapse-arrow" style="display:inline-block;transition:transform 0.2s;font-size:0.8em;transform:${arrowRotation};">▶</span>
        📁 ${projGroup.key}${projTitle} (${projTasks.length} tasks)${nowDot}
      </div>
      ${infoHtml}
      <div id="tasks-${projId}" style="display:${tasksDisplay};padding-left:8px;margin-top:8px;">
        ${tasksHtml}
      </div>
    </div>
  `;
}

// Update task count
const taskCount = allTasks.length;
const summaryCount = document.getElementById('task-count');
if (summaryCount) summaryCount.textContent = taskCount;
```

</details>

---

_Dashboard updated: `= date(today)`_
