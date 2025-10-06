# Ex03 To-Do List using JavaScript
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
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Todo Application (Vanilla JS)</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--muted:#9aa4b2;--accent:#60a5fa;--success:#34d399;--danger:#fb7185}
    *{box-sizing:border-box}
    body{font-family:Inter, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial; margin:0;background:linear-gradient(180deg,#071129 0%, #071627 100%);color:#e6eef6;min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px}
    .app{width:100%;max-width:920px}
    header{display:flex;align-items:baseline;gap:12px;margin-bottom:16px}
    header h1{font-size:22px;margin:0}
    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:12px;padding:16px;box-shadow:0 6px 24px rgba(2,6,23,0.6);}
    .controls{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
    .input, .select, .btn{padding:10px 12px;border-radius:8px;background:#07111a;border:1px solid rgba(255,255,255,0.03);color:inherit}
    .input{flex:1;min-width:180px}
    .btn{cursor:pointer}
    .btn:hover{opacity:0.95}
    .todo-list{margin-top:12px;display:flex;flex-direction:column;gap:8px}
    .todo{display:flex;align-items:center;gap:12px;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.005));}
    .todo.dragging{opacity:0.6;transform:scale(.995)}
    .checkbox{width:18px;height:18px;border-radius:4px;border:1px solid rgba(255,255,255,0.08);display:inline-flex;align-items:center;justify-content:center;cursor:pointer}
    .checkbox.checked{background:var(--success);border-color:transparent}
    .content{flex:1;min-width:0}
    .title{font-size:15px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
    .title.completed{text-decoration:line-through;color:var(--muted)}
    .meta{font-size:12px;color:var(--muted);margin-top:4px}
    .actions{display:flex;gap:8px}
    .small{font-size:13px;padding:6px 8px}
    .filters{display:flex;gap:6px;align-items:center}
    .badge{padding:6px 8px;border-radius:999px;background:rgba(255,255,255,0.02);font-size:13px}
    .search{min-width:180px}
    .due{font-weight:600}
    .hidden{display:none}
    footer{display:flex;justify-content:space-between;align-items:center;margin-top:12px;color:var(--muted);font-size:13px}
    @media (max-width:640px){header{flex-direction:column;align-items:flex-start} .controls{flex-direction:column}}
    input[type="date"], input[type="time"]{padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit}
    textarea{resize:none}
    .editable{background:transparent;border:none;color:inherit;font-size:15px;width:100%}
    .importfile{display:none}
  </style>
</head>
<body>
  <div class="app">
    <header>
      <h1> Todo Application (Vanilla JS)</h1>
      <div class="badge">Features: add · edit · delete · complete · reorder · filter · search · due dates · persistence · import/export</div>
    </header>

    <div class="card" role="application" aria-label="Todo application">
      <div class="controls">
        <input id="newTodo" class="input" placeholder="Add a new todo and press Enter..." aria-label="New task" />
        <input id="dueDate" type="date" title="Optional due date" />
        <select id="priority" class="select" title="Priority">
          <option value="normal">Normal priority</option>
          <option value="high">High priority</option>
          <option value="low">Low priority</option>
        </select>
        <button id="addBtn" class="btn">Add</button>

        <input id="search" class="input search" placeholder="Search tasks (press / to focus)" aria-label="Search tasks" />
        <div class="filters">
          <button class="btn small" data-filter="all">All</button>
          <button class="btn small" data-filter="active">Active</button>
          <button class="btn small" data-filter="completed">Completed</button>
        </div>

        <div style="margin-left:auto;display:flex;gap:8px;align-items:center">
          <button id="clearCompleted" class="btn small">Clear completed</button>
          <button id="exportBtn" class="btn small">Export</button>
          <label class="btn small" for="importFile">Import</label>
          <input id="importFile" class="importfile" type="file" accept="application/json" />
        </div>
      </div>

      <div id="todoList" class="todo-list" aria-live="polite"></div>

      <footer>
        <div><span id="count">0</span> tasks</div>
        <div>Tip: Press <kbd>Enter</kbd> to add · <kbd>/</kbd> to focus search</div>
      </footer>
    </div>
  </div>

  <template id="todoTemplate">
    <div class="todo" draggable="true">
      <div class="checkbox" role="checkbox" tabindex="0" aria-checked="false"></div>
      <div class="content">
        <div class="title" tabindex="0"></div>
        <div class="meta"></div>
      </div>
      <div class="actions">
        <button class="btn small edit">Edit</button>
        <button class="btn small delete">Delete</button>
      </div>
    </div>
  </template>

  <script>
    const STORAGE_KEY = 'ex03_todos_v1';

    function loadTodos(){
      try{
        const raw = localStorage.getItem(STORAGE_KEY);
        return raw ? JSON.parse(raw) : [];
      }catch(e){
        console.error('Failed to load todos', e); return [];
      }
    }
    function saveTodos(data){
      localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
    }

    function uid(){return Date.now().toString(36) + Math.random().toString(36).slice(2,8)}
    function formatDate(d){ if(!d) return ''; const dt=new Date(d); return dt.toLocaleDateString() + (d.includes('T')? ' ' + dt.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : ''); }

    let state = {
      todos: loadTodos(),
      filter: 'all',
      query: ''
    };

    const todoList = document.getElementById('todoList');
    const newTodo = document.getElementById('newTodo');
    const dueDate = document.getElementById('dueDate');
    const priority = document.getElementById('priority');
    const addBtn = document.getElementById('addBtn');
    const search = document.getElementById('search');
    const countEl = document.getElementById('count');
    const clearCompleted = document.getElementById('clearCompleted');
    const exportBtn = document.getElementById('exportBtn');
    const importFile = document.getElementById('importFile');

    function render(){
      const list = state.todos.filter(t=>{
        if(state.filter==='active' && t.completed) return false;
        if(state.filter==='completed' && !t.completed) return false;
        if(state.query){
          const q=state.query.toLowerCase();
          return t.text.toLowerCase().includes(q) || (t.note||'').toLowerCase().includes(q);
        }
        return true;
      });

      const priorityOrder = {high:0, normal:1, low:2};
      list.sort((a,b)=>{
        if(a.completed !== b.completed) return a.completed?1:-1;
        if(priorityOrder[a.priority] !== priorityOrder[b.priority]) return priorityOrder[a.priority] - priorityOrder[b.priority];
        if(a.due && b.due) return new Date(a.due) - new Date(b.due);
        if(a.due && !b.due) return -1;
        if(!a.due && b.due) return 1;
        return (a.position || 0) - (b.position || 0);
      });

      todoList.innerHTML = '';
      const template = document.getElementById('todoTemplate');

      list.forEach(todo=>{
        const el = template.content.firstElementChild.cloneNode(true);
        el.dataset.id = todo.id;
        const checkbox = el.querySelector('.checkbox');
        const title = el.querySelector('.title');
        const meta = el.querySelector('.meta');
        const editBtn = el.querySelector('.edit');
        const delBtn = el.querySelector('.delete');

        checkbox.tabIndex = 0;
        checkbox.setAttribute('role','checkbox');
        checkbox.setAttribute('aria-checked', todo.completed ? 'true' : 'false');

        if(todo.completed) checkbox.classList.add('checked');
        title.textContent = todo.text;
        if(todo.completed) title.classList.add('completed');

        meta.innerHTML = `<span class="muted">${todo.priority}</span> ${todo.due? ' · due ' + formatDate(todo.due) : ''}`;
        if(todo.note) meta.innerHTML += ` · ${todo.note}`;

        // events
        checkbox.addEventListener('click', ()=> toggleComplete(todo.id));
        checkbox.addEventListener('keydown', e=>{ if(e.key === ' ' || e.key === 'Enter'){ e.preventDefault(); toggleComplete(todo.id); } });

        editBtn.addEventListener('click', ()=> openEditor(el, todo));
        delBtn.addEventListener('click', ()=> deleteTodo(todo.id));

        // double click to edit title
        title.addEventListener('dblclick', ()=> openEditor(el, todo));

        // drag and drop
        el.addEventListener('dragstart', dragStart);
        el.addEventListener('dragend', dragEnd);
        el.addEventListener('dragover', dragOver);
        el.addEventListener('drop', drop);

        todoList.appendChild(el);
      });

      countEl.textContent = state.todos.length;
      saveTodos(state.todos);
    }

    function addTodo(text, opts={}){
      if(!text || !text.trim()) return;
      const t = {
        id: uid(),
        text: text.trim(),
        note: opts.note || '',
        created: new Date().toISOString(),
        due: opts.due || '',
        priority: opts.priority || 'normal',
        completed: false,
        position: state.todos.length
      };
      state.todos.push(t);
      render();
    }

    function toggleComplete(id){
      const t = state.todos.find(x=>x.id===id); if(!t) return; t.completed = !t.completed; render();
    }

    function deleteTodo(id){
      state.todos = state.todos.filter(x=>x.id !== id);
      render();
    }

    function openEditor(el, todo){
      // create an inline editing UI
      const content = el.querySelector('.content');
      content.innerHTML = '';
      const titleInput = document.createElement('input');
      titleInput.className = 'editable';
      titleInput.value = todo.text;
      const noteInput = document.createElement('input');
      noteInput.className = 'editable';
      noteInput.placeholder = 'Optional note';
      noteInput.value = todo.note || '';
      const dueInput = document.createElement('input');
      dueInput.type = 'date';
      dueInput.value = todo.due ? todo.due.split('T')[0] : '';
      const saveBtn = document.createElement('button'); saveBtn.className='btn small'; saveBtn.textContent='Save';
      const cancelBtn = document.createElement('button'); cancelBtn.className='btn small'; cancelBtn.textContent='Cancel';

      content.appendChild(titleInput);
      content.appendChild(noteInput);
      content.appendChild(dueInput);
      content.appendChild(saveBtn);
      content.appendChild(cancelBtn);

      titleInput.focus();

      saveBtn.addEventListener('click', ()=>{
        todo.text = titleInput.value.trim() || todo.text;
        todo.note = noteInput.value.trim();
        todo.due = dueInput.value ? new Date(dueInput.value).toISOString() : '';
        render();
      });
      cancelBtn.addEventListener('click', render);
    }

    let dragSrc = null;
    function dragStart(e){
      this.classList.add('dragging');
      dragSrc = this;
      e.dataTransfer.effectAllowed = 'move';
      try{ e.dataTransfer.setData('text/plain', this.dataset.id); }catch(e){}
    }
    function dragEnd(){ this.classList.remove('dragging'); }
    function dragOver(e){ e.preventDefault(); e.dataTransfer.dropEffect = 'move'; }
    function drop(e){
      e.preventDefault();
      const srcId = e.dataTransfer.getData('text/plain') || (dragSrc && dragSrc.dataset.id);
      const targetId = this.dataset.id;
      if(!srcId || srcId === targetId) return;
      const srcIndex = state.todos.findIndex(t=>t.id===srcId);
      const tgtIndex = state.todos.findIndex(t=>t.id===targetId);
      if(srcIndex<0||tgtIndex<0) return;
      const [moved] = state.todos.splice(srcIndex,1);
      state.todos.splice(tgtIndex,0,moved);
      // reassign positions
      state.todos.forEach((t,i)=>t.position = i);
      render();
    }

    document.querySelectorAll('[data-filter]').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        document.querySelectorAll('[data-filter]').forEach(b=>b.classList.remove('active'));
        btn.classList.add('active');
        state.filter = btn.dataset.filter;
        render();
      });
    });

    search.addEventListener('input', ()=>{ state.query = search.value; render(); });

    exportBtn.addEventListener('click', ()=>{
      const data = JSON.stringify(state.todos, null, 2);
      const blob = new Blob([data], {type:'application/json'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'todos.json'; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });

    importFile.addEventListener('change', async (e)=>{
      const f = e.target.files[0];
      if(!f) return;
      try{
        const text = await f.text();
        const imported = JSON.parse(text);
        if(Array.isArray(imported)){
          const map = new Map(state.todos.map(t=>[t.id,t]));
          imported.forEach(it=>{ if(!map.has(it.id)) map.set(it.id,it); });
          state.todos = Array.from(map.values());
          render();
        }else alert('Import file must be an array of todos');
      }catch(err){ alert('Failed to import: ' + err.message); }
      importFile.value = '';
    });

    clearCompleted.addEventListener('click', ()=>{
      state.todos = state.todos.filter(t=>!t.completed);
      render();
    });

    addBtn.addEventListener('click', ()=>{
      addTodo(newTodo.value, {due: dueDate.value ? new Date(dueDate.value).toISOString() : '', priority: priority.value});
      newTodo.value=''; dueDate.value=''; priority.value='normal'; newTodo.focus();
    });

    newTodo.addEventListener('keydown', e=>{
      if(e.key === 'Enter'){ addBtn.click(); }
    });

    window.addEventListener('keydown', e=>{
      if(e.key === '/' && document.activeElement !== search){ e.preventDefault(); search.focus(); }
    });


   
    render();

    window.__ex03 = { state, render };
  </script>
</body>
</html>
```
## OUTPUT
<img width="1917" height="895" alt="image" src="https://github.com/user-attachments/assets/1c1e41bb-743c-4363-a031-1cc5c05cb433" />



## RESULT
The program for creating To-do list using JavaScript is executed successfully.
