## 📋 Tasks by Project/Viz
- [x] Show all viz tags ✅ 2025-12-20
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
  
  return lines.map(line => `• ${line.substring(1).trim()}`).join('<br><br>');
}

function formatTaskHistory(history) {
  if (!history) return "—";
  
  if (Array.isArray(history)) {
    const recentEntries = history.slice(0, 3);
    return recentEntries.map(entry => `• ${entry}`).join('<br><br>');
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
  return "Viz: no-viz";
}

function getVizOrder(viz) {
  if (viz === "now") return 1;      // Active work first
  if (viz === "next") return 2;     // Keep eyes on second
  if (viz === "soon") return 3;     // Near-term third
  if (viz === "blocked") return 4;  // Blocked fourth
  if (viz === "waiting") return 5;  // Waiting fifth
  if (viz === "later") return 6;    // Backlog last
  return 99;
}

function getVizBackground(viz) {
  if (viz === "now") return "linear-gradient(135deg, #ffe5e5 0%, #ffd5d5 100%)";      // Red - active work
  if (viz === "next") return "linear-gradient(135deg, #fff9e5 0%, #ffedd5 100%)";     // Orange - eyes on
  if (viz === "soon") return "linear-gradient(135deg, #f0ecf9 0%, #e5dff5 100%)";     // Purple - near-term
  if (viz === "later") return "linear-gradient(135deg, #e5f9f5 0%, #d5f0eb 100%)";    // Green - backlog
  if (viz === "blocked") return "linear-gradient(135deg, #f0f0f2 0%, #e5e5e8 100%)";  // Gray - blocked
  if (viz === "waiting") return "linear-gradient(135deg, #fff0eb 0%, #ffe5d9 100%)";  // Light orange - waiting
  return "linear-gradient(135deg, #f5f5f5 0%, #ececec 100%)";
}

function getVizBorderColor(viz) {
  if (viz === "now") return "#ff4444";      // Red
  if (viz === "next") return "#ffaa00";     // Orange
  if (viz === "soon") return "#8b5cf6";     // Purple
  if (viz === "later") return "#00b8a9";    // Green
  if (viz === "blocked") return "#6b7280";  // Gray
  if (viz === "waiting") return "#ff6b35";  // Light orange
  return "#999999";
}

function getVizIndent(viz) {
  if (viz === "now") return 0;
  if (viz === "next") return 10;
  if (viz === "soon") return 20;
  if (viz === "blocked") return 30;
  if (viz === "waiting") return 40;
  if (viz === "later") return 50;
  return 0;
}

function getSectionOrder(section) {
  const s = (section ?? "").toLowerCase();
  if (s === "in progress") return 1;
  if (s === "review") return 2;
  if (s === "blocked") return 3;
  if (s === "backlog") return 4;
  return 99;
}

const vaultName = dv.app.vault.getName();

// ===== BATCH LOAD FILE CONTENTS =====
const fileContents = new Map();
for (let t of allTasks) {
  try {
    const fileContent = await dv.io.load(t.file.path);
    fileContents.set(t.file.path, fileContent);
  } catch (e) {
    fileContents.set(t.file.path, null);
  }
}

// ===== PARENT-CHILD RELATIONSHIP FUNCTIONS =====

function buildParentChildMap(tasks) {
  const parentMap = new Map();
  const tasksByFile = new Map();
  
  for (let t of tasks) {
    const filename = t.file.name;
    tasksByFile.set(filename, t);
  }
  
  for (let t of tasks) {
    let parentFile = clean(t.parent_task);
    if (parentFile) {
      // Strip .md extension if present
      if (parentFile.endsWith('.md')) {
        parentFile = parentFile.slice(0, -3);
      }
      if (!parentMap.has(parentFile)) {
        parentMap.set(parentFile, []);
      }
      parentMap.get(parentFile).push(t);
    }
  }
  
  for (let [parent, children] of parentMap.entries()) {
    children.sort((a, b) => a.file.ctime - b.file.ctime);
  }
  
  return { parentMap, tasksByFile };
}

function identifyOrphans(tasks, tasksByFile) {
  const orphans = new Set();
  for (let t of tasks) {
    let parentFile = clean(t.parent_task);
    if (parentFile) {
      // Strip .md extension if present
      if (parentFile.endsWith('.md')) {
        parentFile = parentFile.slice(0, -3);
      }
      if (!tasksByFile.has(parentFile)) {
        orphans.add(t.file.name);
      }
    }
  }
  return orphans;
}

function isNonOrphanedChild(task, tasksByFile, orphans) {
  let parentFile = clean(task.parent_task);
  if (!parentFile) return false;
  if (orphans.has(task.file.name)) return false;
  // Strip .md extension if present
  if (parentFile.endsWith('.md')) {
    parentFile = parentFile.slice(0, -3);
  }
  return tasksByFile.has(parentFile);
}

// ===== TASK CARD HTML BUILDER (returns string instead of rendering) =====
function buildTaskCardHTML(t, parentMap, orphans, parentIndent, today, vaultName, level = 0) {
  const status = clean(t.status) || "—";
  const priority = clean(t.priority);
  const due = formatDate(t.due_date);
  const taskHistory = parseTaskHistory(fileContents.get(t.file.path));
  const phase = clean(t.phase);
  const effort = clean(t.effort);
  const otherTags = formatTags(t.other_tags);
  const assignee = clean(t.assignee);
  
  const isOrphan = orphans.has(t.file.name);
  
  const vizLabel = t.viz ? getVizLabel(t.viz) : "";
  
  const statusClass = normStatus(status).replace(/[^a-z0-9]+/g, "—") || "unknown";
  const vizBg = getVizBackground(t.viz);
  const vizBorderColor = getVizBorderColor(t.viz);
  
  let totalIndent;
  if (level === 0) {
    totalIndent = getVizIndent(t.viz);
  } else {
    totalIndent = parentIndent + 10;
  }
  
  // Get subtasks from file.tasks (body checkboxes)
  const fileTasks = t.file.tasks?.values ?? [];
  const subtaskSummary = getSubtaskSummary(fileTasks);
  let subtaskBadge = "";
  if (subtaskSummary) {
    const summaryStyle = subtaskSummary.complete === subtaskSummary.total
      ? "background: rgba(34, 197, 94, 0.15); color: #166534; border: 1px solid rgba(34, 197, 94, 0.3);"
      : "background: rgba(99, 102, 241, 0.1); color: #4338ca; border: 1px solid rgba(99, 102, 241, 0.2);";
    
    subtaskBadge = `<span class="dashboard-badge subtasks" style="
      padding: 5px 12px; 
      border-radius: 6px; 
      font-size: 0.85em; 
      font-weight: 500;
      ${summaryStyle}
      box-shadow: 0 1px 3px rgba(0,0,0,0.1);
    ">📋 ${subtaskSummary.text}</span>`;
  }
  
  let detailsHtml = "";
  const hasDueDate = t.due_date !== null && t.due_date !== undefined;
  
  if (assignee !== "" || hasDueDate) {
    detailsHtml = '<div class="dashboard-details" style="margin-top: 12px;">';
    
    if (assignee !== "") {
      detailsHtml += `<p style="margin: 0; color: #555; font-size: 0.95em;"><strong>Assignee:</strong> ${assignee}</p>`;
    }
    
    if (hasDueDate) {
      const isComplete = normStatus(status) === "complete";
      
      if (isComplete) {
        // Show completion status instead of due date calculations
        const badgeStyle = "background: rgba(34, 197, 94, 0.15); color: #166534; border: 1px solid rgba(34, 197, 94, 0.3);";
        detailsHtml += `<p style="margin: 4px 0 0 0;"><span style="padding: 6px 12px; border-radius: 6px; font-size: 0.9em; font-weight: 500; display: inline-block; ${badgeStyle}">✓ Completed (was due: ${due})</span></p>`;
      } else {
        // Calculate overdue/upcoming for active tasks
        const dueDate = new Date(t.due_date);
        const daysDiff = Math.floor((dueDate - today) / (1000 * 60 * 60 * 24));
        
        let daysText = "";
        if (daysDiff < 0) {
          daysText = `${Math.abs(daysDiff)} days overdue`;
        } else if (daysDiff === 0) {
          daysText = "due today";
        } else {
          daysText = `${daysDiff} days until due`;
        }
        
        const isOverdue = daysDiff < 0;
        const badgeStyle = isOverdue 
          ? "background: rgba(239, 68, 68, 0.2); color: #991b1b; border: 1px solid rgba(239, 68, 68, 0.3);"
          : "background: rgba(59, 130, 246, 0.15); color: #1e40af; border: 1px solid rgba(59, 130, 246, 0.3);";
        
        detailsHtml += `<p style="margin: 4px 0 0 0;"><span style="padding: 6px 12px; border-radius: 6px; font-size: 0.9em; font-weight: 500; display: inline-block; ${badgeStyle}">📅 Due: ${due} (${daysText})</span></p>`;
      }
    }
    
    detailsHtml += '</div>';
    detailsHtml += '<hr style="border: none; border-top: 1px solid rgba(0,0,0,0.08); margin: 15px 0;">';
  }
  
  const orphanWarning = isOrphan ? `<span class="dashboard-badge orphan-warning" style="
    padding: 5px 12px; 
    border-radius: 6px; 
    font-size: 0.85em; 
    font-weight: 500;
    background: rgba(234, 179, 8, 0.2);
    color: #854d0e;
    box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  ">⚠️ Parent not found</span>` : "";
  
  const subtasksHtml = renderSubtasksHTML(fileTasks, t.file.path, vaultName);
  
  let html = `
<div class="dashboard-card" style="
  background: ${vizBg}; 
  margin-left: ${totalIndent}px; 
  margin-bottom: 20px;
  border-radius: 8px;
  border-left: 5px solid ${vizBorderColor};
  box-shadow: 0 2px 8px rgba(0,0,0,0.08), 0 1px 3px rgba(0,0,0,0.06);
  padding: 18px;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
">
  <div class="dashboard-card-header" style="margin-bottom: 12px;">
    <a href="obsidian://open?vault=${encodeURIComponent(vaultName)}&file=${encodeURIComponent(t.file.path)}" style="
      font-size: 1.15em; 
      font-weight: 600; 
      color: #1a1a1a;
      line-height: 1.4;
      text-decoration: none;
      cursor: pointer;
    " class="dashboard-title">${t.title}</a>
  </div>
  <div class="dashboard-meta" style="
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
  <div class="dashboard-update" style="margin-top: 15px;">
    <p style="margin: 0; line-height: 1.6; color: #333;">
      <strong style="color: #1a1a1a; font-size: 0.95em;">📋 Task History:</strong><br>
      <span style="margin-top: 8px; display: block; font-size: 0.95em;">${taskHistory}</span>
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
  
  // Check if any task in this project has viz = 11
  const hasNow = projectTasks.some(t => t.viz === "now");
  
  // Build parent-child relationships for this project
  const { parentMap, tasksByFile } = buildParentChildMap(projectTasks);
  const orphans = identifyOrphans(projectTasks, tasksByFile);
  
  // Get project status
  const projectStatus = projectStatuses[projectGroup.key];
  const statusEmoji = projectStatus ? getStatusEmoji(projectStatus) : "";
  const statusText = projectStatus ? ` • ${statusEmoji} ${projectStatus}` : "";
  
  // Get project summary
  const summary = projectSummaries[projectGroup.key];
  const summaryHtml = summary 
    ? `<div style="font-size: 0.9em; color: #555; padding: 0 0 12px 0; margin-bottom: 8px; border-bottom: 1px solid rgba(0,0,0,0.08);">${summary}</div>`
    : "";
  
  // Build all content for this project (sections and tasks)
  let projectContentHtml = "";
  
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
  
  // Build the complete details element HTML with red dot indicator for viz-11
  const nowIndicator = hasNow ? '<span style="color: #ff4444; margin-left: 8px; font-size: 0.9em;">●</span>' : '';
  const projectHeaderHtml = `📁 ${projectGroup.key} (${projectTasks.length} tasks)${statusText}${nowIndicator}`;
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
      ${summaryHtml}
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
```
