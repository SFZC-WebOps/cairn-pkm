# 📋 Vault Dashboard

**Cairn-PKM** · Installed: `$= dv.io.load("_cairn-pkm/VERSION").then(v => v.trim())`

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

// Get overdue tasks
const overdueTasks = allPages
  .where(p => p.file.folder.includes('/tasks'))
  .where(p => p.due_date && p.due_date < dv.date("today"))
  .where(p => p.status !== "complete")
  .sort(p => p.due_date, 'asc');

// Get blocked/waiting tasks
const blockedWaitingTasks = allPages
  .where(p => p.file.folder.includes('/tasks'))
  .where(p => p.status === "blocked" || p.status === "waiting" || p.viz === "blocked" || p.viz === "waiting")
  .where(p => p.status !== "complete");

// Display alerts
let hasAlerts = false;

if (overdueTasks.length > 0) {
  hasAlerts = true;
  dv.header(4, "🔴 Overdue Tasks");
  dv.table(
    ["Task", "Due", "Project", "Priority"],
    overdueTasks.map(t => [
      t.file.link,
      t.due_date,
      t.project,
      t.priority || "-"
    ])
  );
}

if (blockedWaitingTasks.length > 0) {
  hasAlerts = true;
  dv.header(4, "⏸️ Blocked & Waiting Tasks");
  dv.table(
    ["Task", "Status", "Project", "Section"],
    blockedWaitingTasks.map(t => [
      t.file.link,
      t.status || t.viz,
      t.project,
      t.section || "-"
    ])
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
    dueTasks.map(t => [
      t.file.link,
      t.project,
      t.due_date,
      t.priority || "-",
      t.status || "active"
    ])
  );
}
```

</details>

---

## 📥 Inbox Items

<details open> <summary style="cursor: pointer; font-size: 1.1em; font-weight: 600; padding: 8px 0;"> Capture Inbox (<span id="inbox-count">loading...</span> items) </summary>

```dataviewjs
const items = dv.pages('"Capture"')
  .sort(p => p.file.ctime, 'asc');

if (items.length === 0) {
  dv.paragraph("✅ **Inbox Zero!**");
} else {
  dv.table(
    ["Item", "Captured", "Age"],
    items.map(p => {
      const age = Math.round((new Date() - new Date(p.file.ctime)) / (1000 * 60 * 60 * 24));
      const ageWarning = age > 7 ? "⚠️ " : "";
      return [
        p.file.link,
        p.file.ctime.toFormat("yyyy-MM-dd"),
        ageWarning + age + " days"
      ];
    })
  );
}

// Update count
const inboxCount = document.getElementById('inbox-count');
if (inboxCount) inboxCount.textContent = items.length;
```

</details>

---

## 📋 All Tasks by Project/Viz

- [x] Show all viz tags ✅ 2025-12-20

<details id="main-tasks-view"> <summary style="cursor: pointer; font-size: 1.2em; font-weight: 600; padding: 12px 0; margin-bottom: 16px;"> Tasks by Project/Viz (<span id="task-count">loading...</span> tasks) </summary>

```dataviewjs
// Helper functions
function normStatus(s) {
  return (s ?? "").toLowerCase();
}
function clean(v) {
  if (v === null || v === undefined) return "";
  if (v === "null" || v === "NULL") return "";
  return v;
}
function formatTags(t) {
  if (!t) return "";
  if (Array.isArray(t)) return t.join(", ");
  return t;
}
function formatDate(d) {
  if (!d) return "—";
  const dateStr = String(d);
  if (dateStr.includes('T')) {
    return dateStr.split('T')[0];
  }
  return dateStr;
}


// ===== TASK HISTORY PARSER =====
function parseTaskHistory(fileContent) {
  if (!fileContent) return "No history available";
  
  // Find the Task History section using string methods instead of regex
  const historyStart = fileContent.indexOf('### Task History');
  if (historyStart === -1) return "No history recorded";
  
  // Get content after the heading
  let afterHeading = fileContent.substring(historyStart);
  
  // Find the end (next section or end of file)
  let endPos = afterHeading.indexOf('\n---');
  if (endPos === -1) endPos = afterHeading.indexOf('\n###');
  if (endPos === -1) endPos = afterHeading.length;
  
  const historySection = afterHeading.substring(0, endPos);
  
  // Extract bullet points (- YYYY-MM-DD: text)
  const lines = historySection.split('\n')
    .map(line => line.trim())
    .filter(line => line.startsWith('-'))
    .slice(0, 3);  // Take top 3
  
  if (lines.length === 0) return "No history recorded";
  
  return lines.map(line => `• ${line.substring(1).trim()}`).join('<br>');
}

function formatTaskHistory(history) {
  if (!history) return "—";
  
  if (Array.isArray(history)) {
    const recentEntries = history.slice(0, 3);
    return recentEntries.map(entry => `• ${entry}`).join('<br>');
  }
  
  return `• ${history}`;
}

// ===== SUBTASK HELPERS (using file.tasks from body checkboxes) =====
function getSubtaskSummary(tasks) {
  if (!tasks || tasks.length === 0) return null;
  const complete = tasks.filter(t => t.completed).length;
  const total = tasks.length;
  return { complete, total, text: `${complete}/${total} complete` };
}

function renderSubtasksHTML(tasks, filePath, vaultName) {
  if (!tasks || tasks.length === 0) return "";
  
  const complete = tasks.filter(t => t.completed).length;
  const total = tasks.length;
  
  let html = `
  <details open style="margin-top: 15px; margin-left: 10px;">
    <summary style="cursor: pointer; font-size: 0.9em; color: #6b7280; padding: 4px 0; font-weight: 500;">
      📋 Subtasks (${complete}/${total})
    </summary>
    <div style="padding-left: 10px; margin-top: 8px;">
  `;
  
  for (let task of tasks) {
    const checkbox = task.completed ? "☑" : "☐";
    const textStyle = task.completed 
      ? "text-decoration: line-through; color: #9ca3af;" 
      : "color: #333;";
    const linkUrl = `obsidian://open?vault=${encodeURIComponent(vaultName)}&file=${encodeURIComponent(filePath)}`;
    
    html += `
      <div style="padding: 4px 0; font-size: 0.9em;">
        <span style="${textStyle}">${checkbox} ${task.text}</span>
        <a href="${linkUrl}" style="margin-left: 8px; font-size: 0.8em; color: #6b7280; text-decoration: none;">↗</a>
      </div>
    `;
  }
  
  html += `
    </div>
  </details>
  `;
  
  return html;
}

// ===== LOAD PROJECT SUMMARIES =====
// UPDATED: Now looks for -home.md files with 'summary' field (was project_summary)
function loadProjectSummaries() {
  const projectPages = dv.pages('"Tracks"')
    .where(p => p.file.name.endsWith('-home') && p.project_id);
  
  const summaryMap = {};
  for (let page of projectPages) {
    const summary = clean(page.summary);  // Changed from project_summary
    if (summary) {
      summaryMap[page.project_id] = summary;
    }
  }
  return summaryMap;
}

// ===== LOAD PROJECT STATUSES =====
// UPDATED: Now looks for -home.md files with 'status' field (was project_status)
function loadProjectStatuses() {
  const projectPages = dv.pages('"Tracks"')
    .where(p => p.file.name.endsWith('-home') && p.project_id);
  
  const statusMap = {};
  for (let page of projectPages) {
    const status = clean(page.status);  // Changed from project_status
    if (status) {
      statusMap[page.project_id] = status;
    }
  }
  return statusMap;
}

// ===== LOAD PROJECT PROGRESS =====
function loadProjectProgress() {
  const projectPages = dv.pages('"Tracks"')
    .where(p => p.file.name.endsWith('-home') && p.project_id);
  
  const progressMap = {};
  for (let page of projectPages) {
    const progress = page.progress;
    if (progress !== null && progress !== undefined) {
      progressMap[page.project_id] = progress;
    }
  }
  return progressMap;
}

// ===== STATUS EMOJI MAPPING =====
function getStatusEmoji(status) {
  const s = clean(status).toLowerCase();
  if (s === "planning") return "📋";
  if (s === "active") return "🚀";
  if (s === "blocked") return "🚫";
  if (s === "review") return "👀";
  if (s === "complete") return "✅";
  if (s === "archived") return "📦";
  return "📌";
}

// ===== STATUS BADGE STYLE =====
function getStatusBadgeStyle(status) {
  const s = clean(status).toLowerCase();
  const styles = {
    "active": "background: #22c55e20; color: #166534; border: 1px solid #22c55e40;",
    "blocked": "background: #ef444420; color: #991b1b; border: 1px solid #ef444440;",
    "complete": "background: #a855f720; color: #6b21a8; border: 1px solid #a855f740;",
    "planning": "background: #3b82f620; color: #1e40af; border: 1px solid #3b82f640;",
    "review": "background: #f59e0b20; color: #92400e; border: 1px solid #f59e0b40;",
    "archived": "background: #6b728020; color: #374151; border: 1px solid #6b728040;"
  };
  return styles[s] || "background: #6b728020; color: #374151; border: 1px solid #6b728040;";
}

const today = (() => {
  const d = new Date();
  d.setHours(0, 0, 0, 0);
  return d;
})();

// ===== CHECKBOX TOGGLE DETECTION =====
const currentFile = dv.current();
const showAllViz = currentFile.file.tasks?.some(t => 
  t.text.includes("Show all viz tags") && t.completed
) ?? false;

const projectSummaries = loadProjectSummaries();
const projectStatuses = loadProjectStatuses();
const projectProgress = loadProjectProgress();

// UPDATED: Tasks now live in Tracks/*/tasks/ folders
const allTasks = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes('/tasks') && p.project && p.title)
  .where(p => {
    const viz = p.viz;
    if (showAllViz) {
      return viz !== null && viz !== undefined && viz !== "";
    } else {
      // Focus on active work and what's next
      return viz === "now" || viz === "next";
    }
  });

function cmpDate(a, b) {
  const ad = a ? new Date(a) : null;
  const bd = b ? new Date(b) : null;
  if (!ad && !bd) return 0;
  if (!ad) return 1;
  if (!bd) return -1;
  return ad - bd;
}

function getVizLabel(viz) {
  if (viz === "now") return "Viz: now";
  if (viz === "next") return "Viz: next";
  if (viz === "soon") return "Viz: soon";
  if (viz === "later") return "Viz: later";
  if (viz === "blocked") return "Viz: blocked";
  if (viz === "waiting") return "Viz: waiting";
  return viz ? `Viz: ${viz}` : "";
}

function getVizOrder(viz) {
  if (viz === "now") return 1;
  if (viz === "next") return 2;
  if (viz === "soon") return 3;
  if (viz === "later") return 4;
  if (viz === "blocked") return 5;
  if (viz === "waiting") return 6;
  return 10;
}

function getSectionOrder(section) {
  if (section === "In Progress") return 1;
  if (section === "Review") return 2;
  if (section === "Blocked") return 3;
  if (section === "Backlog") return 4;
  if (section === "Complete") return 5;
  return 10;
}

// ===== PARENT-CHILD RELATIONSHIP HELPERS =====
function buildParentChildMap(tasks) {
  const tasksByFile = new Map();
  const parentMap = new Map();
  
  for (let task of tasks) {
    tasksByFile.set(task.file.name, task);
  }
  
  for (let task of tasks) {
    const parentFile = clean(task.parent_task);
    if (parentFile) {
      if (!parentMap.has(parentFile)) {
        parentMap.set(parentFile, []);
      }
      parentMap.get(parentFile).push(task);
    }
  }
  
  return { parentMap, tasksByFile };
}

function identifyOrphans(tasks, tasksByFile) {
  const orphans = new Set();
  for (let task of tasks) {
    const parentFile = clean(task.parent_task);
    if (parentFile && !tasksByFile.has(parentFile)) {
      orphans.add(task.file.name);
    }
  }
  return orphans;
}

function isNonOrphanedChild(task, tasksByFile, orphans) {
  const parentFile = clean(task.parent_task);
  if (!parentFile) return false;
  if (orphans.has(task.file.name)) return false;
  return tasksByFile.has(parentFile);
}

const vaultName = dv.app.vault.getName();

// ===== TASK CARD HTML BUILDER (with parent/child indenting) =====
function buildTaskCardHTML(t, parentMap, orphans, baseIndent, today, vaultName, level) {
  const title = clean(t.title) || "Untitled";
  const status = clean(t.status) || "—";
  const priority = clean(t.priority);
  const viz = clean(t.viz);
  const phase = clean(t.phase);
  const effort = clean(t.effort);
  const assignee = clean(t.assignee);
  const dueDate = formatDate(t.due_date);
  const createdDate = formatDate(t.created_date);
  const tags = formatTags(t.tags);
  
  const vizLabel = getVizLabel(viz);
  
  const statusClass = normStatus(status);
  const vizClass = viz ? viz.toLowerCase().replace(/\s+/g, '-') : "no-viz";
  
  // Get task history
  const taskHistory = formatTaskHistory(t.last_update);
  
  // Get subtasks from file.tasks (body checkboxes)
  const fileTasks = t.file.tasks?.values ?? [];
  const subtaskSummary = getSubtaskSummary(fileTasks);
  const subtaskBadge = subtaskSummary 
    ? `<span style="
        padding: 5px 12px; 
        border-radius: 6px; 
        font-size: 0.85em; 
        font-weight: 500;
        background: rgba(99, 102, 241, 0.15);
        color: #4338ca;
        box-shadow: 0 1px 3px rgba(0,0,0,0.1);
      ">📋 ${subtaskSummary.text}</span>`
    : "";
  
  // Render subtasks HTML
  const subtasksHtml = renderSubtasksHTML(fileTasks, t.file.path, vaultName);
  
  // Filter tags for display
  const typeTagsToShow = [];
  const otherTagsToShow = [];
  
  if (Array.isArray(t.tags)) {
    for (let tag of t.tags) {
      if (!tag) continue;
      const tagStr = String(tag).toLowerCase();
      
      // Skip known metadata tags
      if (["now", "next", "soon", "later", "blocked", "waiting", "eyeson", "backlog"].includes(tagStr)) {
        continue;
      }
      
      // Type tags
      if (tagStr.startsWith("type/")) {
        typeTagsToShow.push(tag);
      } else {
        otherTagsToShow.push(tag);
      }
    }
  }
  
  const otherTags = otherTagsToShow.length > 0 
    ? otherTagsToShow.map(t => `#${t}`).join(" ")
    : "";
  
  const orphanWarning = orphans.has(t.file.name)
    ? `<span style="padding: 5px 12px; border-radius: 6px; font-size: 0.85em; background: rgba(239, 68, 68, 0.2); color: #991b1b; box-shadow: 0 1px 3px rgba(0,0,0,0.1);">⚠️ Parent not found</span>`
    : "";
  
  const totalIndent = baseIndent + (level * 30);
  
  let detailsHtml = "";
  if (assignee || dueDate !== "—" || createdDate !== "—") {
    detailsHtml = `
    <div style="font-size: 0.9em; color: #555; margin-top: 8px; line-height: 1.6;">
      ${assignee ? `👤 ${assignee}` : ""}
      ${dueDate !== "—" ? ` • 📅 Due: ${dueDate}` : ""}
      ${createdDate !== "—" ? ` • 🆕 ${createdDate}` : ""}
    </div>
    `;
  }
  
  let html = `
<div class="dashboard-card viz-${vizClass} status-${statusClass}" style="
  background: rgba(255, 255, 255, 0.8);
  margin-left: ${totalIndent}px;
  margin-bottom: 16px;
  border-radius: 8px;
  border-left: 4px solid #6366f1;
  padding: 16px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.06);
">
  <div class="dashboard-title" style="margin-bottom: 10px;">
    <a href="obsidian://open?vault=${encodeURIComponent(vaultName)}&file=${encodeURIComponent(t.file.path)}" style="font-size: 1.05em; font-weight: 600; color: #1a1a1a; text-decoration: none;">
      ${title}
    </a>
  </div>
  <div class="dashboard-badges" style="
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 8px;
  ">
    ${orphanWarning}
    ${vizLabel ? `<span class="dashboard-badge viz" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      background: rgba(0,0,0,0.08);
      color: #333;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">${vizLabel}</span>` : ""}
    ${status !== "—" ? `<span class="dashboard-badge status-${statusClass}" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      background: rgba(34, 197, 94, 0.15);
      color: #166534;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">Status: ${status}</span>` : ""}
    ${priority ? `<span class="dashboard-badge priority" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      background: rgba(239, 68, 68, 0.15);
      color: #991b1b;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">Priority: ${priority}</span>` : ""}
    ${phase ? `<span class="dashboard-badge phase" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      background: rgba(59, 130, 246, 0.15);
      color: #1e40af;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">Phase: ${phase}</span>` : ""}
    ${effort ? `<span class="dashboard-badge effort" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      background: rgba(168, 85, 247, 0.15);
      color: #6b21a8;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">Effort: ${effort}</span>` : ""}
    ${otherTags ? `<span class="dashboard-badge tags" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      background: rgba(0,0,0,0.05);
      color: #555;
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">${otherTags}</span>` : ""}
    ${subtaskBadge}
  </div>
  ${detailsHtml}
  <div class="dashboard-update" style="margin-top: 12px;">
    <p style="margin: 0; line-height: 1.4; color: #333;">
      <strong style="color: #1a1a1a; font-size: 0.95em;">📋 Task History:</strong><br>
      <span style="margin-top: 4px; display: block; font-size: 0.95em;">${taskHistory}</span>
    </p>
  </div>
  ${subtasksHtml}
  ${t.type && t.type.length > 0 ? `
  <hr style="border: none; border-top: 1px solid rgba(0,0,0,0.08); margin: 15px 0;">
  <div class="dashboard-type-container" style="
    background: rgba(255, 255, 255, 0.5); 
    padding: 12px 14px; 
    border-radius: 6px; 
    border: 1px solid rgba(0,0,0,0.05);
  ">
    <div class="dashboard-type-tags" style="display: flex; flex-wrap: wrap; gap: 20px;">
      ${(Array.isArray(t.type) ? t.type : [t.type]).map(tag => 
        `<span style="font-size: 0.85em; color: #666;">${tag}</span>`
      ).join('')}
    </div>
  </div>
  ` : ''}
</div>
  `;
  
  // Recursively build children HTML
  const children = parentMap.get(t.file.name) || [];
  for (let child of children) {
    html += buildTaskCardHTML(child, parentMap, orphans, totalIndent, today, vaultName, level + 1);
  }
  
  return html;
}

// ===== MAIN RENDERING LOGIC =====

// Display filter mode indicator
// Display filter mode indicator + collapse controls
const modeText = showAllViz 
  ? "🔓 Showing ALL viz tags" 
  : "⚡ Focused: now + next only";

const controlsHtml = `
<div style="display: flex; gap: 12px; align-items: center; margin-bottom: 16px; flex-wrap: wrap;">
  <span style="font-size: 0.9em; color: #666; padding: 8px 12px; background: rgba(0,0,0,0.05); border-radius: 6px;">
    ${modeText}
  </span>
  <button onclick="document.querySelectorAll('details').forEach(d => d.open = true)" style="padding: 6px 12px; border-radius: 6px; border: 1px solid #ccc; background: white; cursor: pointer; font-size: 0.85em;">📂 Expand All</button>
  <button onclick="document.querySelectorAll('details').forEach(d => d.open = false)" style="padding: 6px 12px; border-radius: 6px; border: 1px solid #ccc; background: white; cursor: pointer; font-size: 0.85em;">📁 Collapse All</button>
</div>
`;
const controlsContainer = dv.el("div", "");
controlsContainer.innerHTML = controlsHtml;

// Group by project first
const projectGroups = allTasks.groupBy(t => t.project ?? "Unassigned");

// For each project - build complete HTML and render as collapsible
for (let projectGroup of projectGroups) {
  const projectTasks = projectGroup.rows;
  
  // Check if any task in this project has viz = now
  const hasNow = projectTasks.some(t => t.viz === "now");
  
  // Build parent-child relationships for this project
  const { parentMap, tasksByFile } = buildParentChildMap(projectTasks);
  const orphans = identifyOrphans(projectTasks, tasksByFile);
  
  // Get project metadata
  const projectStatus = projectStatuses[projectGroup.key];
  const statusEmoji = projectStatus ? getStatusEmoji(projectStatus) : "";
  const summary = projectSummaries[projectGroup.key];
  const progress = projectProgress[projectGroup.key];
  
  // Build project info box
  let projectInfoHtml = "";
  if (projectStatus || progress !== null && progress !== undefined || summary) {
    projectInfoHtml = '<div style="background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%); padding: 15px; border-radius: 8px; border-left: 4px solid #0ea5e9; margin-bottom: 16px;">';
    
    // Status and Progress on same line
    if (projectStatus || progress !== null && progress !== undefined) {
      projectInfoHtml += '<div style="display: flex; gap: 15px; margin-bottom: 10px; flex-wrap: wrap; align-items: center;">';
      
      if (projectStatus) {
        const statusStyle = getStatusBadgeStyle(projectStatus);
        projectInfoHtml += `<span style="padding: 6px 12px; border-radius: 6px; font-size: 0.95em; font-weight: 600; ${statusStyle}">${statusEmoji} ${projectStatus}</span>`;
      }
      
      if (progress !== null && progress !== undefined) {
        const progressPercent = progress || 0;
        const progressColor = progressPercent >= 75 ? '#059669' : progressPercent >= 50 ? '#0ea5e9' : progressPercent >= 25 ? '#f59e0b' : '#6b7280';
        projectInfoHtml += `
          <div style="display: flex; align-items: center; gap: 8px;">
            <span style="font-weight: 600; color: #334155;">Progress:</span>
            <div style="width: 200px; height: 20px; background: rgba(0,0,0,0.1); border-radius: 10px; overflow: hidden;">
              <div style="width: ${progressPercent}%; height: 100%; background: ${progressColor}; transition: width 0.3s;"></div>
            </div>
            <span style="font-weight: 600; color: ${progressColor};">${progressPercent}%</span>
          </div>
        `;
      }
      
      projectInfoHtml += '</div>';
    }
    
    // Summary
    if (summary) {
      projectInfoHtml += `<div style="color: #334155; line-height: 1.6; font-size: 0.95em;"><strong>Summary:</strong> ${summary}</div>`;
    }
    
    projectInfoHtml += '</div>';
  }
  
  // Build all content for this project (sections and tasks)
  let projectContentHtml = "";
  
  // Add project info box after header
  projectContentHtml += projectInfoHtml;
  
  // Group this project's tasks by section
  const sectionGroups = projectGroup.rows.groupBy(t => t.section ?? "(No Section)");
  
  // Sort section groups by priority
  const sortedSectionGroups = [...sectionGroups].sort((a, b) => 
    getSectionOrder(a.key) - getSectionOrder(b.key)
  );
  
  // For each section within this project
  for (let sectionGroup of sortedSectionGroups) {
    const topLevelTasks = sectionGroup.rows.filter(t => 
      !isNonOrphanedChild(t, tasksByFile, orphans)
    );
    
    if (topLevelTasks.length === 0) continue;
    
    projectContentHtml += `<h3 style="margin: 16px 0 12px 0; font-size: 1.1em; color: #444;">▶ ${sectionGroup.key} (${topLevelTasks.length} tasks)</h3>`;
    
    const vizGroups = topLevelTasks.groupBy(t => t.viz ?? "no-viz");
    const sortedVizGroups = [...vizGroups].sort((a, b) => 
      getVizOrder(a.key) - getVizOrder(b.key)
    );
    
    for (let vizGroup of sortedVizGroups) {
      if (vizGroup.rows.length === 0) continue;
      
      const sortedTasks = [...vizGroup.rows].sort((a, b) => cmpDate(a.due_date, b.due_date));
      
      for (let t of sortedTasks) {
        projectContentHtml += buildTaskCardHTML(t, parentMap, orphans, 0, today, vaultName, 0);
      }
    }
  }
  
  // Build the complete details element HTML with red dot indicator for viz-now
  const nowIndicator = hasNow ? '<span style="color: #ff4444; margin-left: 8px; font-size: 0.9em;">●</span>' : '';
  const projectHeaderHtml = `📁 ${projectGroup.key} (${projectTasks.length} tasks)${nowIndicator}`;
  const projectId = projectGroup.key.replace(/[^a-zA-Z0-9]/g, '-');
  
  const fullDetailsHtml = `
    <details id="proj-${projectId}" style="
      margin-bottom: 24px;
      border: 1px solid rgba(0,0,0,0.1);
      border-radius: 8px;
      padding: 8px 16px;
      background: rgba(255,255,255,0.5);
    " ontoggle="localStorage.setItem('hub-proj-${projectId}', this.open)">
      <summary style="
        cursor: pointer;
        font-size: 1.3em;
        font-weight: 600;
        padding: 12px 0;
        color: #1a1a1a;
        list-style: none;
        display: flex;
        align-items: center;
        gap: 8px;
      ">
        <span class="collapse-arrow" style="
          display: inline-block;
          transition: transform 0.2s ease;
          font-size: 0.8em;
        ">▶</span>
        ${projectHeaderHtml}
      </summary>
      <div style="padding-left: 8px; margin-top: 8px;">
        ${projectContentHtml}
      </div>
    </details>
  `;
  
  // Create a container div and set innerHTML directly to avoid escaping
  const container = dv.el("div", "");
  container.innerHTML = fullDetailsHtml;
  
  // Restore saved state from localStorage
  const savedState = localStorage.getItem('hub-proj-' + projectId);
  if (savedState === 'true') {
    const el = container.querySelector('details');
    if (el) el.open = true;
  }
}

// Add CSS for arrow rotation when open
const styleEl = dv.el("style", "");
styleEl.innerHTML = `
  details[open] .collapse-arrow {
    transform: rotate(90deg);
  }
  details summary::-webkit-details-marker {
    display: none;
  }
  details summary::marker {
    display: none;
  }
`;

// Update task count in summary
const taskCount = allTasks.length;
const summaryCount = document.getElementById('task-count');
if (summaryCount) summaryCount.textContent = taskCount;
```

</details>

---

_Dashboard updated: `= date(today)`_
