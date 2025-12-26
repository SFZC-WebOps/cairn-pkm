## 🔊 All Projects/Tasks

```dataviewjs
// ===== 🔊 ALL PROJECTS & TASKS =====
// ===== PROJECT FILTER DROPDOWN =====

const container = dv.container;

// Simple dropdown - no fancy styling
const filterDiv = container.createEl("div", {
  attr: {
    style: "margin-bottom: 20px; padding: 15px; background: #667eea; border-radius: 8px;"
  }
});

filterDiv.createEl("label", {
  text: "Select Project/Area: ",
  attr: {
    style: "color: white; font-weight: bold; margin-right: 10px;"
  }
});

const dropdown = filterDiv.createEl("select", {
  attr: {
    id: "project-filter-dropdown",
    style: "padding: 8px 12px; font-size: 1em; border: none; border-radius: 4px; background: white; width: 300px; height: 40px; line-height: 1.5;"
  }
});

// Add default option
dropdown.createEl("option", {
  text: "-- Select --",
  attr: { value: "" }
});

// Load project summaries from home docs in Tracks AND System
const trackHomeDocs = dv.pages('"Tracks"')
  .where(p => p.file.name.endsWith('-home'));

const systemHomeDoc = dv.page('"System/_system-home"');

const projectIds = [];

// Add tracks (projects and areas)
for (let ps of trackHomeDocs) {
  // Use project_id if available, otherwise extract from filename
  // e.g., _area-webops-home.md -> area-webops
  // e.g., _p013-wasb-migr-home.md -> p013-wasb-migr
  let id = ps.project_id || ps.file.name.replace(/^_/, '').replace(/-home$/, '');
  if (id) projectIds.push(id);
}

// Add System if it exists
if (systemHomeDoc) {
  projectIds.push("system");
}

// Sort and add to dropdown
projectIds.sort();
for (let id of projectIds) {
  dropdown.createEl("option", {
    text: id,
    attr: { value: id }
  });
}

// Load saved selection
const savedSelection = sessionStorage.getItem("dashboard-selected-project");
if (savedSelection) {
  dropdown.value = savedSelection;
}

// Content container
const contentDiv = container.createEl("div");

// ===== HELPER FUNCTIONS =====

function clean(v) {
  if (v === null || v === undefined) return "";
  if (v === "null" || v === "NULL") return "";
  return v;
}

function formatDate(d) {
  if (!d) return "—";
  const dateStr = String(d);
  if (dateStr.includes('T')) return dateStr.split('T')[0];
  return dateStr;
}

function formatTags(t) {
  if (!t) return "";
  if (Array.isArray(t)) return t.join(", ");
  return t;
}

function formatTaskHistory(history) {
  if (!history) return "—";
  if (Array.isArray(history)) {
    return history.slice(0, 3).map(entry => `• ${entry}`).join('<br><br>');
  }
  return `• ${history}`;
}

function getSubtaskSummary(tasks) {
  if (!tasks || tasks.length === 0) return null;
  const complete = tasks.filter(t => t.completed).length;
  return { complete, total: tasks.length, text: `${complete}/${tasks.length} complete` };
}

function getStatusBadgeStyle(status) {
  const s = clean(status).toLowerCase();
  const styles = {
    "active": "background: #22c55e20; color: #166534; border: 1px solid #22c55e40;",
    "blocked": "background: #ef444420; color: #991b1b; border: 1px solid #ef444440;",
    "complete": "background: #a855f720; color: #6b21a8; border: 1px solid #a855f740;",
    "scheduled": "background: #3b82f620; color: #1e40af; border: 1px solid #3b82f640;",
    "waiting": "background: #f59e0b20; color: #92400e; border: 1px solid #f59e0b40;",
    "deferred": "background: #9ca3af20; color: #4b5563; border: 1px solid #9ca3af40;",
    "onhold": "background: #fbbf2420; color: #92400e; border: 1px solid #fbbf2440;"
  };
  return styles[s] || "background: #6b728020; color: #374151; border: 1px solid #6b728040;";
}

function getStatusEmoji(status) {
  const s = clean(status).toLowerCase();
  const emojis = {
    "planning": "📋", "active": "🚀", "blocked": "🚫", "review": "👀",
    "complete": "✅", "archived": "📦", "scheduled": "📅", "waiting": "⏳",
    "deferred": "⏸️", "onhold": "🔶"
  };
  return emojis[s] || "📌";
}

function getPriorityBadgeStyle(priority) {
  const p = clean(priority).toLowerCase();
  const styles = {
    "critical": "background: #dc262620; color: #991b1b; border: 1px solid #dc262640;",
    "high": "background: #ea580c20; color: #9a3412; border: 1px solid #ea580c40;",
    "medium": "background: #f59e0b20; color: #92400e; border: 1px solid #f59e0b40;",
    "low": "background: #3b82f620; color: #1e40af; border: 1px solid #3b82f640;"
  };
  return styles[p] || "background: #6b728020; color: #374151; border: 1px solid #6b728040;";
}

function getVizBackground(viz) {
  const backgrounds = {
    "11": "linear-gradient(135deg, #ffe5e5 0%, #ffd5d5 100%)",
    "eyeson": "linear-gradient(135deg, #fff9e5 0%, #ffedd5 100%)",
    "backlog": "linear-gradient(135deg, #e5f9f5 0%, #d5f0eb 100%)",
    "scheduled": "linear-gradient(135deg, #f0ecf9 0%, #e5dff5 100%)",
    "system": "linear-gradient(135deg, #f0f0f2 0%, #e5e5e8 100%)",
    "reviewing": "linear-gradient(135deg, #fff0eb 0%, #ffe5d9 100%)"
  };
  return backgrounds[viz] || "linear-gradient(135deg, #f5f5f5 0%, #ececec 100%)";
}

function getVizBorderColor(viz) {
  const colors = {
    "11": "#dc2626", "eyeson": "#ea580c", "backlog": "#059669",
    "scheduled": "#7c3aed", "system": "#6b7280", "reviewing": "#f97316"
  };
  return colors[viz] || "#9ca3af";
}

// ===== RENDERING FUNCTION =====
function renderProjectContent(selectedProject) {
  contentDiv.innerHTML = "";
  
  if (!selectedProject) {
    contentDiv.createEl("div", {
      text: "👆 Please select a project or area from the dropdown above.",
      attr: { style: "padding: 40px; text-align: center; color: #6b7280;" }
    });
    return;
  }

  const originalContainer = dv.container;
  dv.container = contentDiv;

  const vaultName = dv.app.vault.getName();
  const today = new Date();
  today.setHours(0, 0, 0, 0);

  // Load tasks from Tracks/*/tasks AND System/tasks folders
  const trackTasks = dv.pages('"Tracks"')
    .where(p => p.file.folder.includes('/tasks'))
    .where(p => p.title)
    .where(p => clean(p.status).toLowerCase() !== "complete");

  const systemTasks = dv.pages('"System/tasks"')
    .where(p => p.title)
    .where(p => clean(p.status).toLowerCase() !== "complete");

  // Combine all tasks
  const allTasks = [...trackTasks, ...systemTasks];

  // Filter by selected project
  // For system tasks, match if project is "system" or empty/missing
  const filteredTasks = allTasks.filter(p => {
    const taskProject = clean(p.project).toLowerCase();
    const selected = selectedProject.toLowerCase();
    
    if (selected === "system") {
      return taskProject === "system" || taskProject === "" || p.file.folder.includes("System/tasks");
    }
    return taskProject === selected;
  });

  // Load project summary from home doc
  let homeDoc = null;
  let projectSummary = null;
  let projectStatus = null;
  let projectProgress = null;
  
  if (selectedProject.toLowerCase() === "system") {
    // System home doc is at System/_system-home.md
    homeDoc = dv.page('"System/_system-home"');
  } else {
    // Look for home doc in Tracks by project_id or by filename pattern
    homeDoc = dv.pages('"Tracks"')
      .where(p => {
        if (!p.file.name.endsWith('-home')) return false;
        // Match by project_id if available
        if (p.project_id === selectedProject) return true;
        // Match by filename pattern: _area-webops-home -> area-webops
        const extractedId = p.file.name.replace(/^_/, '').replace(/-home$/, '');
        return extractedId === selectedProject;
      })
      .first();
  }
  
  if (homeDoc) {
    projectSummary = homeDoc.summary || null;
    projectStatus = homeDoc.status || null;
    projectProgress = homeDoc.progress || null;
  }

  // Build parent-child map
  const tasksByFile = new Map();
  const parentMap = new Map();
  
  for (let task of filteredTasks) {
    tasksByFile.set(task.file.name, task);
  }
  
  for (let task of filteredTasks) {
    const parentFile = clean(task.parent_task);
    if (parentFile) {
      if (!parentMap.has(parentFile)) {
        parentMap.set(parentFile, []);
      }
      parentMap.get(parentFile).push(task);
    }
  }

  // Identify orphans
  const orphans = new Set();
  for (let task of filteredTasks) {
    const parentFile = clean(task.parent_task);
    if (parentFile && !tasksByFile.has(parentFile)) {
      orphans.add(task.file.name);
    }
  }

  // Get top-level tasks
  const topLevelTasks = filteredTasks.filter(t => {
    const parentFile = clean(t.parent_task);
    if (!parentFile) return true;
    if (orphans.has(t.file.name)) return true;
    return !tasksByFile.has(parentFile);
  });

  // Render subtasks HTML (using file.tasks from body checkboxes)
  function renderSubtasks(fileTasks, filePath) {
    if (!fileTasks || fileTasks.length === 0) return "";

    const items = fileTasks.map(task => {
      const checkbox = task.completed ? "☑" : "☐";
      const textStyle = task.completed 
        ? "text-decoration: line-through; color: #555;" 
        : "color: #1a1a1a;";
      const linkUrl = `obsidian://open?vault=${encodeURIComponent(vaultName)}&file=${encodeURIComponent(filePath)}`;
      
      return `
        <div style="padding: 6px 8px; margin: 4px 0; background: rgba(255,255,255,0.9); border-left: 3px solid ${task.completed ? "#a855f7" : "#6b7280"}; border-radius: 4px; border: 1px solid rgba(0,0,0,0.08);">
          <span style="${textStyle}">${checkbox} ${task.text}</span>
          <a href="${linkUrl}" style="margin-left: 8px; font-size: 0.8em; color: #4338ca; text-decoration: none;">↗</a>
        </div>
      `;
    }).join('');

    return `
      <hr style="border: none; border-top: 1px solid rgba(0,0,0,0.15); margin: 15px 0;">
      <div style="margin-top: 10px;">
        <div style="font-weight: 600; margin-bottom: 8px; color: #1a1a1a;">📋 Subtasks:</div>
        ${items}
      </div>
    `;
  }

  // Render task with children
  function renderTask(t, indent = 0) {
    const title = clean(t.title) || "Untitled";
    const status = clean(t.status) || "—";
    const priority = clean(t.priority);
    const viz = clean(t.viz);
    const assignee = clean(t.assignee);
    const dueDate = formatDate(t.due_date);
    const createdDate = formatDate(t.created_date);
    const taskHistory = formatTaskHistory(t.last_update);

    const vizBg = getVizBackground(viz);
    const vizBorder = getVizBorderColor(viz);

    // Get subtasks from file.tasks (body checkboxes)
    const fileTasks = t.file.tasks?.values ?? [];
    const subtaskSummary = getSubtaskSummary(fileTasks);
    const subtaskBadge = subtaskSummary 
      ? `<span style="padding: 4px 8px; border-radius: 4px; font-size: 0.85em; background: #6366f120; color: #4338ca;">📋 ${subtaskSummary.text}</span>`
      : "";

    const orphanWarning = orphans.has(t.file.name)
      ? `<span style="padding: 4px 8px; border-radius: 4px; font-size: 0.85em; background: #ef444420; color: #991b1b;">⚠️ Parent not found</span>`
      : "";

    const subtasksHtml = renderSubtasks(fileTasks, t.file.path);

    dv.el("div", `
<div style="background: ${vizBg}; margin-left: ${indent}px; margin-bottom: 15px; border-radius: 8px; border-left: 4px solid ${vizBorder}; padding: 15px;">
  <div style="margin-bottom: 10px;">
    <a href="obsidian://open?vault=${encodeURIComponent(vaultName)}&file=${encodeURIComponent(t.file.path)}" style="font-size: 1.1em; font-weight: 600; color: #1a1a1a; text-decoration: none;">${title}</a>
  </div>
  <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
    ${orphanWarning}
    ${status !== "—" ? `<span style="padding: 4px 8px; border-radius: 4px; font-size: 0.85em; ${getStatusBadgeStyle(status)}">${getStatusEmoji(status)} ${status}</span>` : ""}
    ${priority ? `<span style="padding: 4px 8px; border-radius: 4px; font-size: 0.85em; ${getPriorityBadgeStyle(priority)}">🎯 ${priority}</span>` : ""}
    ${viz ? `<span style="padding: 4px 8px; border-radius: 4px; font-size: 0.85em; background: #00000010; color: #333;">${viz}</span>` : ""}
    ${subtaskBadge}
  </div>
  <div style="font-size: 0.9em; color: #333; margin-top: 8px;">
    ${assignee ? `👤 ${assignee}` : ""} ${dueDate !== "—" ? `• 📅 Due: ${dueDate}` : ""} ${createdDate !== "—" ? `• 🆕 ${createdDate}` : ""}
  </div>
  <div style="margin-top: 10px; padding: 10px; background: rgba(255,255,255,0.85); border-radius: 4px; border: 1px solid rgba(0,0,0,0.1);">
    <strong style="color: #1a1a1a;">📋 Task History:</strong><br>
    <span style="font-size: 0.9em; color: #333;">${taskHistory}</span>
  </div>
  ${subtasksHtml}
</div>
    `);

    // Render children
    const children = parentMap.get(t.file.name) || [];
    for (let child of children) {
      renderTask(child, indent + 30);
    }
  }

  // Main render
  const title = homeDoc?.title || selectedProject;
  const statusEmoji = projectStatus ? getStatusEmoji(projectStatus) : "";

  dv.el("div", `🏗 ${selectedProject} - ${title} (${topLevelTasks.length} tasks)`, {
    attr: { style: "font-size: 1.4em; font-weight: 700; margin: 20px 0 10px 0; border-bottom: 2px solid #ddd; padding-bottom: 8px;" }
  });

  // Project info box
  if (projectStatus || projectProgress !== null || projectSummary) {
    let infoHtml = '<div style="background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%); padding: 15px; border-radius: 8px; border-left: 4px solid #0ea5e9; margin-bottom: 20px;">';
    
    // Status and Progress on same line
    if (projectStatus || projectProgress !== null) {
      infoHtml += '<div style="display: flex; gap: 15px; margin-bottom: 10px; flex-wrap: wrap;">';
      
      if (projectStatus) {
        const statusStyle = getStatusBadgeStyle(projectStatus);
        infoHtml += `<span style="padding: 6px 12px; border-radius: 6px; font-size: 0.95em; font-weight: 600; ${statusStyle}">${statusEmoji} ${projectStatus}</span>`;
      }
      
      if (projectProgress !== null) {
        const progressPercent = projectProgress || 0;
        const progressColor = progressPercent >= 75 ? '#059669' : progressPercent >= 50 ? '#0ea5e9' : progressPercent >= 25 ? '#f59e0b' : '#6b7280';
        infoHtml += `
          <div style="display: flex; align-items: center; gap: 8px;">
            <span style="font-weight: 600; color: #334155;">Progress:</span>
            <div style="width: 200px; height: 20px; background: rgba(0,0,0,0.1); border-radius: 10px; overflow: hidden;">
              <div style="width: ${progressPercent}%; height: 100%; background: ${progressColor}; transition: width 0.3s;"></div>
            </div>
            <span style="font-weight: 600; color: ${progressColor};">${progressPercent}%</span>
          </div>
        `;
      }
      
      infoHtml += '</div>';
    }
    
    // Summary
    if (projectSummary) {
      infoHtml += `<div style="color: #334155; line-height: 1.6; font-size: 0.95em;"><strong>Summary:</strong> ${projectSummary}</div>`;
    }
    
    infoHtml += '</div>';
    
    dv.el("div", infoHtml);
  }

  if (topLevelTasks.length === 0) {
    dv.el("div", "No tasks for this project", {
      attr: { style: "padding: 20px; background: #f5f5f5; border-radius: 8px; color: #6b7280; font-style: italic;" }
    });
  } else {
    // Group by section
    const tasksBySection = new Map();
    for (let task of topLevelTasks) {
      const section = clean(task.section) || "No Section";
      if (!tasksBySection.has(section)) tasksBySection.set(section, []);
      tasksBySection.get(section).push(task);
    }

    const sectionOrder = ["In Progress", "Review", "Blocked", "Backlog", "Complete", "No Section"];
    const sections = Array.from(tasksBySection.keys()).sort((a, b) => {
      const ia = sectionOrder.indexOf(a);
      const ib = sectionOrder.indexOf(b);
      if (ia === -1 && ib === -1) return a.localeCompare(b);
      if (ia === -1) return 1;
      if (ib === -1) return -1;
      return ia - ib;
    });

    for (let section of sections) {
      const tasks = tasksBySection.get(section);
      dv.el("div", `${section} (${tasks.length})`, {
        attr: { style: "font-size: 1.1em; font-weight: 600; margin: 20px 0 10px 0; padding: 8px; background: #f5f5f5; border-left: 4px solid #6366f1; border-radius: 4px;" }
      });
      
      tasks.sort((a, b) => b.file.ctime - a.file.ctime);
      for (let task of tasks) {
        renderTask(task, 0);
      }
    }
  }

  dv.container = originalContainer;
}

// Initial render
renderProjectContent(dropdown.value);

// Handle dropdown change
dropdown.addEventListener("change", (e) => {
  const selected = e.target.value;
  if (selected) {
    sessionStorage.setItem("dashboard-selected-project", selected);
  } else {
    sessionStorage.removeItem("dashboard-selected-project");
  }
  renderProjectContent(selected);
});
```
