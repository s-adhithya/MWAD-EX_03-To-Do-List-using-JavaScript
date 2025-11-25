# MWAD-EX_03-To-Do-List-using-JavaScript
## Date:

## AIM
To create a To-do Application with all features using JavaScript.

## ALGORITHM
### STEP 1
Build the HTML structure (index.html).

### STEP 2
Style the App (style.css).

### STEP 3
Plan the features the To-Do App should have.

### STEP 4
Create a To-do application using Javascript.

### STEP 5
Add functionalities.

### STEP 6
Test the App.

### STEP 7
Open the HTML file in a browser to check layout and functionality.

### STEP 8
Fix styling issues and refine content placement.

### STEP 9
Deploy the website.

### STEP 10
Upload to GitHub Pages for free hosting.

## PROGRAM
### INDEX.HTML:
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Ex.03 — Todo Application</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="container">
    <header>
      <h1>Ex.03 — Todo Application</h1>
    </header>

    <section class="card">
      <div class="toolbar">
        <input id="taskTitle" type="text" placeholder="Task title" />
        <input id="taskDue" type="date" />
        <select id="taskPriority">
          <option value="low">Low</option>
          <option value="medium" selected>Medium</option>
          <option value="high">High</option>
        </select>
        <button id="addBtn">Add</button>
      </div>

      <div class="toolbar">
        <input id="search" type="text" placeholder="Search..." />
        <select id="filter">
          <option value="all">All</option>
          <option value="active">Active</option>
          <option value="completed">Completed</option>
        </select>
        <button id="clearCompleted">Clear Completed</button>
      </div>

      <div id="taskList" class="list"></div>

      <footer>
        <p>Name: <strong>ADHITHYA S</strong> | Register No: <strong>212222240003</strong></p>
      </footer>
    </section>
  </div>
  <script src="script.js"></script>
</body>
</html>
```
### STYLE.CSS:
```
:root {
  --bg: #0f1724;
  --card: #1a2234;
  --accent: #38bdf8;
  --success: #10b981;
  --danger: #ef4444;
  --text: #e6eef6;
  --muted: #9aa4b2;
}

* { box-sizing: border-box; }
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: var(--bg);
  color: var(--text);
}

.container {
  max-width: 700px;
  margin: 30px auto;
  padding: 20px;
}

header h1 {
  text-align: center;
  margin-bottom: 20px;
}

.card {
  background: var(--card);
  padding: 16px;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.4);
}

.toolbar {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

input, select, button {
  padding: 8px;
  border-radius: 8px;
  border: none;
}

input, select {
  flex: 1;
  background: #111827;
  color: var(--text);
}

button {
  background: var(--accent);
  color: #04263b;
  cursor: pointer;
}

button:hover {
  opacity: 0.9;
}

.list {
  margin-top: 10px;
}

.task {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 8px;
  background: #111827;
  border-radius: 8px;
}

.task.done h3 {
  text-decoration: line-through;
  color: var(--muted);
}

.task h3 {
  margin: 0;
  font-size: 16px;
}

.actions button {
  margin-left: 6px;
  background: transparent;
  border: 1px solid var(--muted);
  color: var(--text);
  padding: 4px 6px;
  border-radius: 6px;
}
.actions button:hover {
  background: var(--muted);
}

footer {
  text-align: center;
  margin-top: 10px;
  color: var(--muted);
}
```
### SCRIPT.CSS:
```
const STORAGE_KEY = "ex03_todos";
let tasks = [];

const taskList = document.getElementById("taskList");
const addBtn = document.getElementById("addBtn");
const titleIn = document.getElementById("taskTitle");
const dueIn = document.getElementById("taskDue");
const priorityIn = document.getElementById("taskPriority");
const searchIn = document.getElementById("search");
const filterEl = document.getElementById("filter");
const clearCompleted = document.getElementById("clearCompleted");

function uid() {
  return Date.now().toString(36) + "-" + Math.random().toString(36).slice(2, 8);
}

function load() {
  const data = localStorage.getItem(STORAGE_KEY);
  if (data) tasks = JSON.parse(data);
}
function save() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks));
}

function render() {
  let filtered = tasks;

  // search
  const s = searchIn.value.toLowerCase();
  if (s) filtered = filtered.filter(t => t.title.toLowerCase().includes(s));

  // filter
  if (filterEl.value === "active") filtered = filtered.filter(t => !t.done);
  if (filterEl.value === "completed") filtered = filtered.filter(t => t.done);

  // display
  taskList.innerHTML = "";
  if (filtered.length === 0) {
    taskList.innerHTML = `<p style="color:var(--muted);text-align:center;">No tasks found</p>`;
    return;
  }

  filtered.forEach(t => {
    const div = document.createElement("div");
    div.className = "task" + (t.done ? " done" : "");
    div.innerHTML = `
      <div>
        <h3>${t.title}</h3>
        <small>${t.due ? "Due: " + t.due : ""} | ${t.priority}</small>
      </div>
      <div class="actions">
        <button onclick="toggleTask('${t.id}')">${t.done ? "Undo" : "Done"}</button>
        <button onclick="editTask('${t.id}')">Edit</button>
        <button onclick="deleteTask('${t.id}')">Delete</button>
      </div>
    `;
    taskList.appendChild(div);
  });
}

function addTask() {
  const title = titleIn.value.trim();
  if (!title) {
    alert("Please enter a task title");
    return;
  }
  tasks.unshift({
    id: uid(),
    title,
    due: dueIn.value || null,
    priority: priorityIn.value,
    done: false
  });
  save();
  render();
  titleIn.value = "";
  dueIn.value = "";
}

function toggleTask(id) {
  const t = tasks.find(x => x.id === id);
  if (t) t.done = !t.done;
  save();
  render();
}

function editTask(id) {
  const t = tasks.find(x => x.id === id);
  if (!t) return;
  const newTitle = prompt("Edit title", t.title);
  if (newTitle !== null) t.title = newTitle.trim() || t.title;
  save();
  render();
}

function deleteTask(id) {
  if (!confirm("Delete this task?")) return;
  tasks = tasks.filter(t => t.id !== id);
  save();
  render();
}

function clearCompletedTasks() {
  tasks = tasks.filter(t => !t.done);
  save();
  render();
}

// Event bindings
addBtn.addEventListener("click", addTask);
titleIn.addEventListener("keydown", e => { if (e.key === "Enter") addTask(); });
searchIn.addEventListener("input", render);
filterEl.addEventListener("change", render);
clearCompleted.addEventListener("click", clearCompletedTasks);

// init
load();
render();
```

## OUTPUT
<img width="1919" height="967" alt="Screenshot 2025-11-25 211915" src="https://github.com/user-attachments/assets/43006475-2060-4354-a3b3-1e684ecd4602" />


## RESULT
The program for creating To-do list using JavaScript is executed successfully.
