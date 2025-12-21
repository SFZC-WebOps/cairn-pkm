# 📋 Vault Dashboard

## 📊 Vault Stats

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

<details open>
<summary style="cursor: pointer; font-size: 1.1em; font-weight: 600; padding: 8px 0;">
This Week's Tasks
</summary>

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

<details open>
<summary style="cursor: pointer; font-size: 1.1em; font-weight: 600; padding: 8px 0;">
Capture Inbox (<span id="inbox-count">loading...</span> items)
</summary>

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

- [ ] Show all viz tags

<details id="main-tasks-view">
<summary style="cursor: pointer; font-size: 1.2em; font-weight: 600; padding: 12px 0; margin-bottom: 16px;">
Tasks by Project/Viz (<span id="task-count">loading...</span> tasks)
</summary>

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
