<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Gestor de Deberes</title>
  <style>
    :root {
      --bg: #000;
      --card: rgba(17, 17, 17, 0.85);
      --glass: rgba(30, 30, 40, 0.7);
      --text: #eee;
      --accent: #00d4ff;
      --accent-glow: #00d4ff30;
      --border: #333;
      --hover: #00a8cc;
      --danger: #ff4444;
      --warning: #ffaa00;
      --neon-red: #ff0044;
      --neon-glow: 
        0 0 5px #ff0044,
        0 0 10px #ff0044,
        0 0 15px #ff0044,
        0 0 25px #ff0044,
        0 0 35px #ff0044;
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      padding: 2rem;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      background-image: 
        radial-gradient(circle at 20% 80%, #0a1a2f 0%, transparent 50%),
        radial-gradient(circle at 80% 20%, #1a0033 0%, transparent 50%);
    }

    .container {
      width: 100%;
      max-width: 600px;
      background: var(--card);
      border-radius: 20px;
      overflow: hidden;
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6);
      border: 1px solid var(--border);
      backdrop-filter: blur(12px);
    }

    header {
      padding: 1.5rem;
      text-align: center;
      background: linear-gradient(135deg, rgba(0,212,255,0.1), transparent);
      border-bottom: 1px solid var(--border);
    }

    h1 {
      font-weight: 700;
      font-size: 1.9rem;
      background: linear-gradient(90deg, #00d4ff, #00ff88);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      letter-spacing: 1px;
    }

    /* === PESTAÑAS === */
    .tabs {
      display: flex;
      overflow-x: auto;
      background: var(--glass);
      backdrop-filter: blur(10px);
      border-bottom: 1px solid var(--border);
      padding: 0.6rem 0.8rem;
      gap: 0.5rem;
    }

    .tab {
      min-width: 130px;
      padding: 0.85rem 1.1rem;
      background: rgba(40, 40, 50, 0.6);
      color: #ccc;
      border: none;
      border-radius: 14px;
      cursor: pointer;
      font-weight: 500;
      font-size: 0.95rem;
      position: relative;
      overflow: hidden;
      transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 8px;
      backdrop-filter: blur(6px);
    }

    .tab::before {
      content: '';
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      background: linear-gradient(135deg, var(--accent), var(--hover));
      opacity: 0;
      transition: opacity 0.4s ease;
      z-index: -1;
    }

    .tab:hover {
      transform: translateY(-3px);
      box-shadow: 0 8px 20px var(--accent-glow);
      color: white;
    }

    .tab:hover::before {
      opacity: 0.15;
    }

    .tab.active {
      background: linear-gradient(135deg, rgba(0,212,255,0.25), rgba(0,212,255,0.15));
      color: var(--accent);
      box-shadow: 0 0 20px var(--accent-glow);
      border: 1px solid var(--accent);
      transform: translateY(-2px);
    }

    .tab.active::before {
      opacity: 0.3;
    }

    .tab .close {
      font-size: 1.3rem;
      opacity: 0.7;
      transition: all 0.3s ease;
      transform: scale(0.8);
    }

    .tab:hover .close {
      opacity: 1;
      transform: scale(1);
      color: var(--danger);
    }

    .add-subject {
      padding: 1rem;
      display: flex;
      gap: 0.5rem;
      background: var(--glass);
      backdrop-filter: blur(8px);
    }

    #subjectInput, .task-input {
      flex: 1;
      padding: 0.9rem 1rem;
      background: rgba(30, 30, 30, 0.7);
      border: 1px solid var(--border);
      border-radius: 12px;
      color: var(--text);
      font-size: 1rem;
      outline: none;
      transition: all 0.3s ease;
    }

    #subjectInput:focus, .task-input:focus {
      border-color: var(--accent);
      box-shadow: 0 0 0 3px var(--accent-glow);
      transform: scale(1.01);
    }

    button {
      padding: 0.9rem 1.3rem;
      background: var(--accent);
      color: #000;
      border: none;
      border-radius: 12px;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.3s ease;
      font-size: 0.95rem;
    }

    button:hover {
      background: var(--hover);
      transform: translateY(-2px) scale(1.03);
      box-shadow: 0 8px 20px var(--accent-glow);
    }

    /* === CONTENIDO === */
    .tab-content {
      padding: 1.5rem;
    }

    .task-input-group {
      display: flex;
      gap: 0.5rem;
      margin-bottom: 1.5rem;
      align-items: center;
    }

    .task-input-group input[type="date"] {
      padding: 0.8rem;
      background: rgba(30, 30, 30, 0.7);
      border: 1px solid var(--border);
      border-radius: 10px;
      color: var(--text);
      font-size: 0.9rem;
      min-width: 140px;
    }

    .task-list {
      list-style: none;
      max-height: 500px;
      overflow-y: auto;
      padding-right: 0.5rem;
    }

    .task-item {
      background: rgba(30, 30, 30, 0.6);
      padding: 1rem;
      margin-bottom: 0.75rem;
      border-radius: 12px;
      border: 1px solid var(--border);
      display: flex;
      align-items: center;
      gap: 0.75rem;
      transition: all 0.4s ease;
      animation: slideIn 0.5s ease forwards;
      backdrop-filter: blur(5px);
    }

    .task-item:hover {
      transform: translateX(6px);
      border-color: var(--accent);
      box-shadow: 0 5px 15px var(--accent-glow);
    }

    /* === CHECKBOX NÍTIDO === */
    .checkbox-container {
      position: relative;
      width: 22px;
      height: 22px;
      cursor: pointer;
    }

    .checkbox-container input {
      opacity: 0;
      width: 0;
      height: 0;
    }

    .checkmark {
      position: absolute;
      top: 0;
      left: 0;
      height: 22px;
      width: 22px;
      background-color: rgba(50, 50, 50, 0.8);
      border: 2px solid #555;
      border-radius: 6px;
      transition: all 0.3s ease;
    }

    .checkbox-container:hover .checkmark {
      border-color: var(--accent);
      box-shadow: 0 0 10px var(--accent-glow);
    }

    .checkmark:after {
      content: "";
      position: absolute;
      display: none;
      left: 6px;
      top: 2px;
      width: 5px;
      height: 10px;
      border: solid white;
      border-width: 0 2.5px 2.5px 0;
      transform: rotate(45deg);
      transition: all 0.2s ease;
    }

    .checkbox-container input:checked ~ .checkmark {
      background-color: var(--accent);
      border-color: var(--accent);
      box-shadow: 0 0 12px var(--accent-glow);
    }

    .checkbox-container input:checked ~ .checkmark:after {
      display: block;
    }

    /* === TEXTO COMPLETADO: ROJO NEÓN CON GLOW FUERTE === */
    .task-text {
      flex: 1;
      font-size: 1rem;
      word-break: break-word;
      transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .task-item.completed .task-text {
      color: var(--neon-red);
      text-shadow: var(--neon-glow);
      animation: neonPulse 1.8s ease-in-out infinite alternate;
      font-weight: 600;
    }

    @keyframes neonPulse {
      0% {
        text-shadow: 
          0 0 5px #ff0044,
          0 0 10px #ff0044,
          0 0 15px #ff0044;
      }
      100% {
        text-shadow: 
          0 0 10px #ff0044,
          0 0 20px #ff0044,
          0 0 30px #ff0044,
          0 0 40px #ff0044,
          0 0 50px #ff0044;
      }
    }

    .due-date {
      font-size: 0.85rem;
      color: var(--warning);
      font-weight: 500;
      padding: 0.3rem 0.6rem;
      background: rgba(255, 170, 0, 0.15);
      border-radius: 8px;
      display: flex;
      align-items: center;
      gap: 4px;
      white-space: nowrap;
    }

    .task-actions {
      display: flex;
      gap: 0.5rem;
    }

    .btn {
      background: none;
      border: none;
      font-size: 1.1rem;
      cursor: pointer;
      padding: 0.3rem;
      border-radius: 6px;
      transition: all 0.2s ease;
    }

    .btn.edit { color: #ffd700; }
    .btn.delete { color: var(--danger); }
    .btn:hover { background: rgba(255, 255, 255, 0.1); transform: scale(1.1); }

    .empty-state {
      text-align: center;
      padding: 2.5rem;
      color: #666;
      font-style: italic;
      font-size: 1.1rem;
    }

    @keyframes slideIn {
      from { opacity: 0; transform: translateY(20px) scale(0.95); }
      to { opacity: 1; transform: translateY(0) scale(1); }
    }

    @media (max-width: 480px) {
      .container { padding: 0; border-radius: 16px; }
      .add-subject, .task-input-group { flex-direction: column; }
      button, #addSubjectBtn { width: 100%; }
      .tab { min-width: 110px; font-size: 0.9rem; padding: 0.7rem 0.9rem; }
      .due-date { font-size: 0.8rem; }
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>Gestor de Deberes</h1>
    </header>

    <div class="tabs" id="tabs"></div>

    <div class="add-subject">
      <input type="text" id="subjectInput" placeholder="Nueva asignatura..." maxlength="25" />
      <button id="addSubjectBtn">+ Asignatura</button>
    </div>

    <div id="tabContent" class="tab-content">
      <!-- Contenido dinámico -->
    </div>
  </div>

  <script>
    const tabsContainer = document.getElementById('tabs');
    const tabContent = document.getElementById('tabContent');
    const subjectInput = document.getElementById('subjectInput');
    const addSubjectBtn = document.getElementById('addSubjectBtn');

    let subjects = [];
    let activeSubjectId = null;

    document.addEventListener('DOMContentLoaded', () => {
      loadSubjects();
      if (subjects.length === 0) {
        showEmptyState();
      } else {
        switchToSubject(subjects[0].id);
      }
    });

    addSubjectBtn.addEventListener('click', addSubject);
    subjectInput.addEventListener('keypress', e => e.key === 'Enter' && addSubject());

    function addSubject() {
      const name = subjectInput.value.trim();
      if (!name) return;

      const subject = { id: Date.now(), name, tasks: [] };
      subjects.push(subject);
      createTab(subject);
      saveSubjects();
      subjectInput.value = '';
      switchToSubject(subject.id);
    }

    function createTab(subject) {
      const tab = document.createElement('button');
      tab.className = 'tab';
      tab.innerHTML = `${escapeHtml(subject.name)} <span class="close" title="Eliminar">×</span>`;
      tab.dataset.id = subject.id;

      tab.addEventListener('click', e => {
        if (!e.target.classList.contains('close')) {
          switchToSubject(subject.id);
        }
      });

      tab.querySelector('.close').addEventListener('click', e => {
        e.stopPropagation();
        deleteSubject(subject.id);
      });

      tabsContainer.appendChild(tab);
    }

    function switchToSubject(id) {
      activeSubjectId = id;
      document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
      const tab = document.querySelector(`.tab[data-id="${id}"]`);
      if (tab) tab.classList.add('active');

      const subject = subjects.find(s => s.id === id);
      renderSubjectContent(subject);
    }

    function renderSubjectContent(subject) {
      tabContent.innerHTML = '';

      const inputGroup = document.createElement('div');
      inputGroup.className = 'task-input-group';

      const taskInput = document.createElement('input');
      taskInput.type = 'text';
      taskInput.className = 'task-input';
      taskInput.placeholder = 'Nuevo deber...';

      const dateInput = document.createElement('input');
      dateInput.type = 'date';
      dateInput.value = new Date().toISOString().split('T')[0];

      const addBtn = document.createElement('button');
      addBtn.textContent = 'Añadir';

      inputGroup.append(taskInput, dateInput, addBtn);
      tabContent.appendChild(inputGroup);

      const taskList = document.createElement('ul');
      taskList.className = 'task-list';
      tabContent.appendChild(taskList);

      const addTask = () => {
        const text = taskInput.value.trim();
        const due = dateInput.value;
        if (!text || !due) return;

        const task = { id: Date.now(), text, due, completed: false };
        subject.tasks.push(task);
        createTaskElement(task, taskList, subject);
        saveSubjects();
        taskInput.value = '';
      };

      addBtn.addEventListener('click', addTask);
      taskInput.addEventListener('keypress', e => e.key === 'Enter' && addTask());

      subject.tasks.forEach(t => createTaskElement(t, taskList, subject));
      checkEmptyTasks(taskList);
    }

    function createTaskElement(task, list, subject) {
      const li = document.createElement('li');
      li.className = `task-item ${task.completed ? 'completed' : ''}`;
      li.dataset.id = task.id;

      const due = new Date(task.due);
      const formatted = due.toLocaleDateString('es', { day: 'numeric', month: 'short' });

      li.innerHTML = `
        <label class="checkbox-container">
          <input type="checkbox" ${task.completed ? 'checked' : ''}>
          <span class="checkmark"></span>
        </label>
        <span class="task-text">${escapeHtml(task.text)}</span>
        <span class="due-date">Entrega: ${formatted}</span>
        <div class="task-actions">
          <button class="btn edit" title="Editar">Edit</button>
          <button class="btn delete" title="Eliminar">Delete</button>
        </div>
      `;

      const checkbox = li.querySelector('input[type="checkbox"]');
      checkbox.addEventListener('change', function() {
        li.classList.toggle('completed');
        task.completed = this.checked;
        saveSubjects();
      });

      li.querySelector('.btn.edit').addEventListener('click', () => editTask(task, li, subject));
      li.querySelector('.btn.delete').addEventListener('click', () => {
        li.style.animation = 'slideIn 0.4s reverse';
        setTimeout(() => {
          li.remove();
          subject.tasks = subject.tasks.filter(t => t.id !== task.id);
          saveSubjects();
          checkEmptyTasks(list);
        }, 400);
      });

      list.appendChild(li);
    }

    function editTask(task, li, subject) {
      const textSpan = li.querySelector('.task-text');
      const dueSpan = li.querySelector('.due-date');
      const currentText = textSpan.textContent;
      const currentDue = task.due;

      const input = document.createElement('input');
      input.type = 'text';
      input.value = currentText;
      input.className = 'task-input';
      input.style.flex = '1';

      const dateInput = document.createElement('input');
      dateInput.type = 'date';
      dateInput.value = currentDue;
      dateInput.style.width = '130px';

      const save = () => {
        const newText = input.value.trim();
        const newDue = dateInput.value;
        if (newText && newDue) {
          textSpan.textContent = escapeHtml(newText);
          task.text = newText;
          task.due = newDue;
          const d = new Date(newDue);
          dueSpan.textContent = `Entrega: ${d.toLocaleDateString('es', { day: 'numeric', month: 'short' })}`;
          saveSubjects();
        }
        input.replaceWith(textSpan);
        dateInput.replaceWith(dueSpan);
      };

      input.addEventListener('blur', save);
      dateInput.addEventListener('change', save);
      input.addEventListener('keypress', e => e.key === 'Enter' && save());

      textSpan.replaceWith(input);
      dueSpan.replaceWith(dateInput);
      input.focus();
    }

    function checkEmptyTasks(list) {
      let empty = list.parentElement.querySelector('.empty-state');
      if (list.children.length === 0 && !empty) {
        empty = document.createElement('div');
        empty.className = 'empty-state';
        empty.textContent = 'Sin deberes. ¡Añade uno!';
        list.parentElement.appendChild(empty);
      } else if (empty && list.children.length > 0) {
        empty.remove();
      }
    }

    function deleteSubject(id) {
      if (!confirm('¿Eliminar asignatura y todos sus deberes?')) return;
      subjects = subjects.filter(s => s.id !== id);
      document.querySelector(`.tab[data-id="${id}"]`)?.remove();
      saveSubjects();
      if (subjects.length === 0) {
        showEmptyState();
      } else {
        switchToSubject(subjects[0].id);
      }
    }

    function showEmptyState() {
      tabContent.innerHTML = '<div class="empty-state">Crea una asignatura para empezar.</div>';
      document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
    }

    function saveSubjects() {
      localStorage.setItem('subjects', JSON.stringify(subjects));
    }

    function loadSubjects() {
      const data = localStorage.getItem('subjects');
      if (data) {
        subjects = JSON.parse(data);
        subjects.forEach(createTab);
      }
    }

    function escapeHtml(text) {
      const div = document.createElement('div');
      div.textContent = text;
      return div.innerHTML;
    }
  </script>
</body>
</html>
