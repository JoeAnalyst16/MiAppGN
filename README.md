<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Formularios Analytics - Sistema de Usuarios</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { 
            margin: 0; 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #f8fafc;
        }
        .card { 
            background: white; 
            border-radius: 12px; 
            box-shadow: 0 2px 6px rgba(0,0,0,0.05); 
            border: 1px solid #002338;
        }
        .btn-primary {
            background: #002338;
            color: white;
            padding: 12px 24px;
            border-radius: 8px;
            border: none;
            cursor: pointer;
            font-weight: 500;
            transition: all 0.3s;
        }
        .btn-primary:hover { background: #77c25c; }
        .btn-secondary {
            background: #f8fafc;
            color: #002338;
            padding: 8px 16px;
            border-radius: 6px;
            border: 1px solid #002338;
            cursor: pointer;
            transition: all 0.3s;
        }
        .btn-secondary:hover { background: #f1f5f9; }
        .btn-danger {
            background: #ef4444;
            color: white;
            padding: 8px 16px;
            border-radius: 6px;
            border: none;
            cursor: pointer;
            transition: all 0.3s;
        }
        .btn-danger:hover { background: #dc2626; }
        .input {
            width: 100%;
            padding: 12px;
            border: 1px solid #002338;
            border-radius: 8px;
            font-size: 16px;
        }
        .input:focus {
            outline: none;
            border-color: #77c25c;
            box-shadow: 0 0 0 3px rgba(119, 194, 92, 0.3);
        }
        .user-status-active { color: #16a34a; }
        .user-status-inactive { color: #dc2626; }
        .user-performance-high { background: #dcfce7; color: #15803d; }
        .user-performance-medium { background: #fef3c7; color: #d97706; }
        .user-performance-low { background: #fee2e2; color: #dc2626; }
        @media (max-width: 768px) {
            .container { padding: 16px; }
            .grid-cols-2 { grid-template-columns: 1fr; }
        }
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1000;
        }
        .modal.show { display: flex; }
        .modal-content {
            background: white;
            margin: auto;
            padding: 20px;
            border-radius: 12px;
            max-width: 500px;
            width: 90%;
        }
    </style>
</head>
<body>

<div id="auth-section" class="min-h-screen flex items-center justify-center bg-[#002338] text-white p-6">
    <div class="w-full max-w-md bg-white rounded-xl shadow-lg p-8 text-[#002338]">
        <h2 class="text-2xl font-semibold mb-6 text-center">Iniciar Sesión</h2>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Nombre de Usuario</label>
            <input type="text" id="login-username" class="input" placeholder="usuario123">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Contraseña</label>
            <input type="password" id="login-password" class="input" placeholder="••••••••">
        </div>
        <button onclick="login()" class="btn-primary w-full mt-4">Ingresar</button>
        
        <div class="text-center mt-6">
            <p class="text-sm text-gray-600 mb-2">¿No tienes cuenta?</p>
            <button onclick="showRegisterModal()" class="btn-secondary w-full">Crear Cuenta</button>
        </div>
    </div>
</div>

<div id="register-modal" class="modal">
    <div class="modal-content">
        <h3 class="text-xl font-semibold mb-4">Crear Nueva Cuenta</h3>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Nombre de Usuario</label>
            <input type="text" id="register-username" class="input" placeholder="Ingresa tu usuario">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Contraseña</label>
            <input type="password" id="register-password" class="input" placeholder="Ingresa tu contraseña">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Nombre Completo</label>
            <input type="text" id="register-fullname" class="input" placeholder="Tu nombre completo">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Email</label>
            <input type="email" id="register-email" class="input" placeholder="tu@email.com">
        </div>
        <div class="flex gap-3">
            <button onclick="registerUser()" class="btn-primary flex-1">Crear Cuenta</button>
            <button onclick="hideRegisterModal()" class="btn-secondary">Cancelar</button>
        </div>
    </div>
</div>

<div id="app" class="min-h-screen hidden">
    <div class="container mx-auto p-6 max-w-6xl">
        <div class="mb-8 flex justify-between items-center">
            <div>
                <h1 class="text-3xl font-semibold text-[#002338] mb-2">📋 Sistema de Formularios</h1>
                <p class="text-[#77c25c]">Crea formularios y analiza el rendimiento de tus usuarios</p>
            </div>
            <div class="flex items-center gap-4">
                <span class="text-sm text-gray-600">👋 <span id="current-user-name"></span></span>
                <button onclick="logout()" class="btn-secondary">Cerrar Sesión</button>
            </div>
        </div>

        <div id="notification-area" class="mb-6 hidden">
            </div>

        <div class="flex gap-2 mb-6 overflow-x-auto">
            <button onclick="showTab('forms')" id="tab-forms" class="btn-primary">Formularios</button>
            <button onclick="showTab('create')" id="tab-create" class="btn-secondary">Crear Nuevo</button>
            <button onclick="showTab('analytics')" id="tab-analytics" class="btn-secondary">📊 Analytics</button>
            <button onclick="showTab('users')" id="tab-users" class="btn-secondary hidden">👥 Usuarios</button>
            <button onclick="showTab('preview')" id="tab-preview" class="btn-secondary">👁️ Vista Previa</button>
        </div>

        <div id="content-forms" class="content-section">
            <div class="grid gap-6 md:grid-cols-2 lg:grid-cols-3" id="forms-list">
                </div>
        </div>

        <div id="content-create" class="content-section hidden">
            <div class="card p-6 border border-[#002338]">
                <h2 class="text-2xl font-semibold text-[#002338] mb-4">🆕 Crear Nuevo Formulario</h2>
                
                <div class="mb-4">
                    <label class="block text-sm font-semibold text-[#002338] mb-2">Nombre del Formulario</label>
                    <input type="text" id="form-name" class="input" placeholder="Ejemplo: Registro de Cliente">
                </div>

                <div class="mb-4">
                    <div class="flex justify-between items-center mb-4">
                        <label class="block text-sm font-semibold text-[#002338]">Campos</label>
                        <button onclick="addField()" class="btn-primary">➕ Agregar Campo</button>
                    </div>
                    <div id="fields-container">
                        </div>
                </div>

                <div class="flex gap-3">
                    <button onclick="saveForm()" class="btn-primary">💾 Guardar Formulario</button>
                    <button onclick="clearForm()" class="btn-secondary">🗑️ Limpiar</button>
                </div>
            </div>
        </div>

        <div id="content-analytics" class="content-section hidden">
            <div class="grid gap-6">
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">📝</div>
                        <div class="text-2xl font-bold" id="total-submissions">0</div>
                        <div class="text-sm text-[#77c25c]">Total Envíos</div>
                    </div>
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">✅</div>
                        <div class="text-2xl font-bold" id="completion-rate">0%</div>
                        <div class="text-sm text-[#77c25c]">Tasa Completación</div>
                    </div>
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">⏱️</div>
                        <div class="text-2xl font-bold" id="avg-time">0s</div>
                        <div class="text-sm text-[#77c25c]">Tiempo Promedio</div>
                    </div>
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">👥</div>
                        <div class="text-2xl font-bold" id="unique-users">0</div>
                        <div class="text-sm text-[#77c25c]">Usuarios Únicos</div>
                    </div>
                </div>

                <div class="card p-6 border border-[#002338]">
                    <h3 class="text-xl font-semibold text-[#002338] mb-4">📋 Envíos Recientes</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-sm">
                            <thead>
                                <tr class="border-b">
                                    <th class="text-left p-2">Usuario</th>
                                    <th class="text-left p-2">Formulario</th>
                                    <th class="text-left p-2">Fecha</th>
                                    <th class="text-left p-2">Tiempo</th>
                                    <th class="text-left p-2">Estado</th>
                                </tr>
                            </thead>
                            <tbody id="submissions-table">
                                </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>

        <div id="content-users" class="content-section hidden">
            <div class="grid gap-6">
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">👥</div>
                        <div class="text-2xl font-bold" id="total-users">0</div>
                        <div class="text-sm text-[#77c25c]">Total Usuarios</div>
                    </div>
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">🟢</div>
                        <div class="text-2xl font-bold" id="active-users">0</div>
                        <div class="text-sm text-[#77c25c]">Usuarios Activos</div>
                    </div>
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">📈</div>
                        <div class="text-2xl font-bold" id="avg-user-submissions">0</div>
                        <div class="text-sm text-[#77c25c]">Promedio Envíos</div>
                    </div>
                    <div class="card p-4 text-center border border-[#002338]">
                        <div class="text-2xl mb-2">⭐</div>
                        <div class="text-2xl font-bold" id="top-performer">-</div>
                        <div class="text-sm text-[#77c25c]">Top Performer</div>
                    </div>
                </div>

                <div class="card p-6 border border-[#002338]">
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="text-xl font-semibold text-[#002338]">👥 Gestión de Usuarios</h3>
                        <button onclick="showCreateUserModal()" class="btn-primary">➕ Crear Usuario</button>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-sm">
                            <thead>
                                <tr class="border-b">
                                    <th class="text-left p-2">Usuario</th>
                                    <th class="text-left p-2">Nombre</th>
                                    <th class="text-left p-2">Email</th>
                                    <th class="text-left p-2">Rol</th>
                                    <th class="text-left p-2">Envíos</th>
                                    <th class="text-left p-2">Rendimiento</th>
                                    <th class="text-left p-2">Estado</th>
                                    <th class="text-left p-2">Acciones</th>
                                </tr>
                            </thead>
                            <tbody id="users-table">
                                </tbody>
                        </table>
                    </div>
                </div>

                <div class="card p-6 border border-[#002338]">
                    <h3 class="text-xl font-semibold text-[#002338] mb-4">📊 Rendimiento de Usuarios</h3>
                    <div id="user-performance-chart">
                        </div>
                </div>
            </div>
        </div>

        <div id="content-preview" class="content-section hidden">
            <div class="mb-4">
                <label class="block text-sm font-semibold text-[#002338] mb-2">Seleccionar Formulario</label>
                <select id="preview-form-select" class="input" onchange="loadPreview()">
                    <option value="">Selecciona un formulario...</option>
                </select>
            </div>
            <div id="preview-container">
                </div>
        </div>
    </div>
</div>

<div id="create-user-modal" class="modal">
    <div class="modal-content">
        <h3 class="text-xl font-semibold mb-4">Crear Nuevo Usuario</h3>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Nombre de Usuario</label>
            <input type="text" id="create-username" class="input" placeholder="usuario123">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Contraseña</label>
            <input type="password" id="create-password" class="input" placeholder="••••••••">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Nombre Completo</label>
            <input type="text" id="create-fullname" class="input" placeholder="Nombre completo">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Email</label>
            <input type="email" id="create-email" class="input" placeholder="email@ejemplo.com">
        </div>
        <div class="mb-4">
            <label class="block text-sm font-semibold mb-2">Rol</label>
            <select id="create-role" class="input">
                <option value="user">Usuario</option>
                <option value="admin">Administrador</option>
            </select>
        </div>
        <div class="flex gap-3">
            <button onclick="createUser()" class="btn-primary flex-1">Crear Usuario</button>
            <button onclick="hideCreateUserModal()" class="btn-secondary">Cancelar</button>
        </div>
    </div>
</div>

<script>
// Variables globales
let currentUser = null;
let users = JSON.parse(localStorage.getItem('users') || '[]');
let forms = JSON.parse(localStorage.getItem('forms') || '[]');
let submissions = JSON.parse(localStorage.getItem('submissions') || '[]');
let fieldCounter = 0;

// Inicializar usuarios por defecto si no existen
if (users.length === 0) {
    users = [
        {
            id: 1,
            username: 'admin',
            password: 'admin123',
            fullName: 'Administrador',
            email: 'admin@sistema.com',
            role: 'admin',
            created: new Date().toISOString(),
            lastLogin: null,
            active: true
        },
        {
            id: 2,
            username: 'user1',
            password: 'user123',
            fullName: 'Usuario Demo',
            email: 'user@demo.com',
            role: 'user',
            created: new Date().toISOString(),
            lastLogin: null,
            active: true
        }
    ];
    localStorage.setItem('users', JSON.stringify(users));
}

// Authentication
function login() {
    const username = document.getElementById('login-username').value.trim();
    const password = document.getElementById('login-password').value;

    if (!username || !password) {
        alert("Ingresa usuario y contraseña");
        return;
    }

    const user = users.find(u => u.username === username && u.password === password && u.active);
    
    if (!user) {
        alert("Usuario o contraseña incorrectos");
        return;
    }

    currentUser = user;
    user.lastLogin = new Date().toISOString();
    localStorage.setItem('users', JSON.stringify(users));
    localStorage.setItem('currentUser', JSON.stringify(currentUser));
    
    document.getElementById('auth-section').style.display = 'none';
    document.getElementById('app').style.display = 'block';
    document.getElementById('current-user-name').textContent = user.fullName;
    
    // Show admin tabs if admin
    if (user.role === 'admin') {
        document.getElementById('tab-users').classList.remove('hidden');
    }
    
    showTab('forms');
    loadForms();
    updateAnalytics();
    updatePreviewSelect();
    if (user.role === 'admin') {
        loadUsers();
    }
    displayNotifications(); // Call notification function on login
}

function logout() {
    currentUser = null;
    localStorage.removeItem('currentUser');
    document.getElementById('auth-section').style.display = 'flex';
    document.getElementById('app').style.display = 'none';
    document.getElementById('login-username').value = '';
    document.getElementById('login-password').value = '';
    document.getElementById('tab-users').classList.add('hidden'); // Hide admin tab on logout
    document.getElementById('notification-area').classList.add('hidden'); // Hide notifications on logout
}

function showRegisterModal() {
    document.getElementById('register-modal').classList.add('show');
}

function hideRegisterModal() {
    document.getElementById('register-modal').classList.remove('show');
    clearRegisterForm();
}

function clearRegisterForm() {
    document.getElementById('register-username').value = '';
    document.getElementById('register-password').value = '';
    document.getElementById('register-fullname').value = '';
    document.getElementById('register-email').value = '';
}

function registerUser() {
    const username = document.getElementById('register-username').value.trim();
    const password = document.getElementById('register-password').value;
    const fullName = document.getElementById('register-fullname').value.trim();
    const email = document.getElementById('register-email').value.trim();

    if (!username || !password || !fullName || !email) {
        alert('Por favor completa todos los campos');
        return;
    }

    if (users.find(u => u.username === username)) {
        alert('El nombre de usuario ya existe');
        return;
    }

    if (users.find(u => u.email === email)) {
        alert('El email ya está registrado');
        return;
    }

    const newUser = {
        id: Date.now(),
        username: username,
        password: password,
        fullName: fullName,
        email: email,
        role: 'user',
        created: new Date().toISOString(),
        lastLogin: null,
        active: true
    };

    users.push(newUser);
    localStorage.setItem('users', JSON.stringify(users));
    
    hideRegisterModal();
    alert('✅ Usuario creado correctamente. Ya puedes iniciar sesión.');
}

// User Management (Admin only)
function showCreateUserModal() {
    document.getElementById('create-user-modal').classList.add('show');
}

function hideCreateUserModal() {
    document.getElementById('create-user-modal').classList.remove('show');
    clearCreateUserForm();
}

function clearCreateUserForm() {
    document.getElementById('create-username').value = '';
    document.getElementById('create-password').value = '';
    document.getElementById('create-fullname').value = '';
    document.getElementById('create-email').value = '';
    document.getElementById('create-role').value = 'user';
}

function createUser() {
    if (currentUser.role !== 'admin') {
        alert('Solo los administradores pueden crear usuarios');
        return;
    }

    const username = document.getElementById('create-username').value.trim();
    const password = document.getElementById('create-password').value;
    const fullName = document.getElementById('create-fullname').value.trim();
    const email = document.getElementById('create-email').value.trim();
    const role = document.getElementById('create-role').value;

    if (!username || !password || !fullName || !email) {
        alert('Por favor completa todos los campos');
        return;
    }

    if (users.find(u => u.username === username)) {
        alert('El nombre de usuario ya existe');
        return;
    }

    if (users.find(u => u.email === email)) {
        alert('El email ya está registrado');
        return;
    }

    const newUser = {
        id: Date.now(),
        username: username,
        password: password,
        fullName: fullName,
        email: email,
        role: role,
        created: new Date().toISOString(),
        lastLogin: null,
        active: true
    };

    users.push(newUser);
    localStorage.setItem('users', JSON.stringify(users));
    
    hideCreateUserModal();
    loadUsers();
    alert('✅ Usuario creado correctamente');
}

function toggleUserStatus(userId) {
    const user = users.find(u => u.id === userId);
    if (user) {
        user.active = !user.active;
        localStorage.setItem('users', JSON.stringify(users));
        loadUsers();
    }
}

function deleteUser(userId) {
    if (userId === currentUser.id) {
        alert('No puedes eliminar tu propia cuenta');
        return;
    }

    if (confirm('¿Estás seguro de eliminar este usuario?')) {
        users = users.filter(u => u.id !== userId);
        localStorage.setItem('users', JSON.stringify(users));
        loadUsers();
    }
}

function loadUsers() {
    if (currentUser.role !== 'admin') return;

    const tbody = document.getElementById('users-table');
    tbody.innerHTML = '';

    // Update user stats
    const totalUsers = users.length;
    const activeUsers = users.filter(u => u.active).length;
    const userSubmissions = {};
    
    submissions.forEach(sub => {
        const user = users.find(u => u.username === sub.userId); // Correctly match username
        if (user) {
            userSubmissions[user.id] = (userSubmissions[user.id] || 0) + 1;
        }
    });

    const avgSubmissions = Math.round(Object.values(userSubmissions).reduce((a, b) => a + b, 0) / totalUsers || 0);
    const topPerformer = users.find(u => u.id === Object.keys(userSubmissions).reduce((a, b) => userSubmissions[a] > userSubmissions[b] ? a : b, users[0]?.id));

    document.getElementById('total-users').textContent = totalUsers;
    document.getElementById('active-users').textContent = activeUsers;
    document.getElementById('avg-user-submissions').textContent = avgSubmissions;
    document.getElementById('top-performer').textContent = topPerformer?.username || '-';

    users.forEach(user => {
        const userSubs = userSubmissions[user.id] || 0;
        const completedSubs = submissions.filter(s => s.userId === user.username && s.completed).length; // Filter by user.username
        const performance = userSubs > 10 ? 'Alto' : userSubs > 5 ? 'Medio' : 'Bajo';
        const performanceClass = userSubs > 10 ? 'user-performance-high' : userSubs > 5 ? 'user-performance-medium' : 'user-performance-low';

        const row = document.createElement('tr');
        row.className = 'border-b hover:bg-gray-50';
        row.innerHTML = `
            <td class="p-2 font-medium">${user.username}</td>
            <td class="p-2">${user.fullName}</td>
            <td class="p-2">${user.email}</td>
            <td class="p-2">
                <span class="px-2 py-1 rounded-full text-xs ${user.role === 'admin' ? 'bg-purple-100 text-purple-800' : 'bg-blue-100 text-blue-800'}">
                    ${user.role === 'admin' ? '👑 Admin' : '👤 Usuario'}
                </span>
            </td>
            <td class="p-2">${userSubs}</td>
            <td class="p-2">
                <span class="px-2 py-1 rounded-full text-xs ${performanceClass}">
                    ${performance}
                </span>
            </td>
            <td class="p-2">
                <span class="text-sm ${user.active ? 'user-status-active' : 'user-status-inactive'}">
                    ${user.active ? '🟢 Activo' : '🔴 Inactivo'}
                </span>
            </td>
            <td class="p-2">
                <div class="flex gap-1">
                    <button onclick="toggleUserStatus(${user.id})" class="btn-secondary text-xs">
                        ${user.active ? 'Desactivar' : 'Activar'}
                    </button>
                    ${user.id !== currentUser.id ? `<button onclick="deleteUser(${user.id})" class="btn-danger text-xs">Eliminar</button>` : ''}
                    <button onclick="viewUserReport(${user.id})" class="btn-secondary text-xs">📄 Reporte</button>
                </div>
            </td>
        `;
        tbody.appendChild(row);
    });
}

function viewUserReport(userId) {
    if (currentUser.role !== 'admin') {
        alert('Solo los administradores pueden ver reportes de usuario.');
        return;
    }
    const user = users.find(u => u.id === userId);
    if (!user) {
        alert('Usuario no encontrado.');
        return;
    }
    const userSubs = submissions.filter(s => s.userId === user.username);
    const totalSubmissions = userSubs.length;
    const avgTime = totalSubmissions > 0 ? Math.round(userSubs.reduce((acc, s) => acc + s.timeSpent, 0) / totalSubmissions) : 0;
    const lastSubmission = userSubs.length ? new Date(userSubs[userSubs.length - 1].timestamp).toLocaleString() : 'N/A';

    const report = `
        📄 Reporte de Usuario: ${user.fullName} (${user.username})\n
        ------------------------------------------
        Total de Formularios Enviados: ${totalSubmissions}
        Tiempo Promedio por Formulario: ${avgTime}s
        Último Envío: ${lastSubmission}
        Rol: ${user.role === 'admin' ? 'Administrador' : 'Usuario'}
        Estado: ${user.active ? 'Activo' : 'Inactivo'}
        Email: ${user.email}
    `;
    alert(report);
}


// Tab Navigation
function showTab(tabName) {
    if (tabName === "analytics" && (!currentUser || currentUser.role !== "admin")) {
        alert("Solo los administradores pueden ver el análisis de rendimiento");
        return;
    }
    
    if (tabName === "users" && (!currentUser || currentUser.role !== "admin")) {
        alert("Solo los administradores pueden gestionar usuarios");
        return;
    }

    // Ocultar todas las secciones
    document.querySelectorAll('.content-section').forEach(section => {
        section.classList.add('hidden');
    });
    
    // Resetear estilos de tabs
    document.querySelectorAll('[id^="tab-"]').forEach(tab => {
        tab.className = 'btn-secondary';
    });
    
    // Mostrar sección activa
    document.getElementById('content-' + tabName).classList.remove('hidden');
    document.getElementById('tab-' + tabName).className = 'btn-primary';
    
    // Load specific content
    if (tabName === 'users' && currentUser.role === 'admin') {
        loadUsers();
    }
}

// Form Management
function addField() {
    fieldCounter++;
    const container = document.getElementById('fields-container');
    const fieldDiv = document.createElement('div');
    fieldDiv.className = 'card p-4 mb-4';
    fieldDiv.innerHTML = `
        <div class="grid grid-cols-12 gap-4 items-end">
            <div class="col-span-12 md:col-span-4">
                <label class="block text-xs font-medium mb-1">Etiqueta</label>
                <input type="text" class="input text-sm" placeholder="Nombre del campo">
            </div>
            <div class="col-span-6 md:col-span-3">
                <label class="block text-xs font-medium mb-1">Tipo</label>
                <select class="input text-sm">
                    <option value="text">Texto</option>
                    <option value="email">Email</option>
                    <option value="tel">Teléfono</option>
                    <option value="number">Número</option>
                    <option value="textarea">Área de Texto</option>
                </select>
            </div>
            <div class="col-span-4 md:col-span-3">
                <label class="flex items-center gap-2 text-xs">
                    <input type="checkbox"> Requerido
                </label>
            </div>
            <div class="col-span-2">
                <button onclick="this.closest('.card').remove()" class="btn-secondary text-red-600">🗑️</button>
            </div>
        </div>
    `;
    container.appendChild(fieldDiv);
}

function saveForm() {
    const name = document.getElementById('form-name').value.trim();
    if (!name) {
        alert('Por favor ingresa un nombre para el formulario');
        return;
    }

    const fieldElements = document.querySelectorAll('#fields-container .card');
    if (fieldElements.length === 0) {
        alert('Agrega al menos un campo al formulario');
        return;
    }

    const fields = [];
    fieldElements.forEach((fieldEl, index) => {
        const label = fieldEl.querySelector('input[type="text"]').value.trim();
        const type = fieldEl.querySelector('select').value;
        const required = fieldEl.querySelector('input[type="checkbox"]').checked;
        
        if (label) {
            fields.push({
                id: index + 1,
                label: label,
                type: type,
                required: required
            });
        }
    });

    if (fields.length === 0) {
        alert('Ingresa al menos un campo válido');
        return;
    }

    const form = {
        id: Date.now(),
        name: name,
        fields: fields,
        created: new Date().toISOString(), // Store as ISO string for easier comparison
        createdBy: currentUser.username,
        status: 'active'
    };

    forms.push(form);
    localStorage.setItem('forms', JSON.stringify(forms));
    
    clearForm();
    loadForms();
    updatePreviewSelect();
    alert('✅ Formulario guardado correctamente');
    displayNotifications(); // Update notifications after new form is created
}

function clearForm() {
    document.getElementById('form-name').value = '';
    document.getElementById('fields-container').innerHTML = '';
    fieldCounter = 0;
}

function loadForms() {
    const container = document.getElementById('forms-list');
    container.innerHTML = '';
    
    if (forms.length === 0) {
        container.innerHTML = `
            <div class="col-span-full text-center py-12">
                <div class="text-6xl mb-4">📝</div>
                <h3 class="text-lg font-medium mb-2">No hay formularios</h3>
                <p class="text-[#77c25c] mb-4">Crea tu primer formulario para empezar</p>
                <button onclick="showTab('create')" class="btn-primary">🆕 Crear Formulario</button>
            </div>
        `;
        return;
    }

    forms.forEach(form => {
        const formSubmissions = submissions.filter(s => s.formId === form.id);
        const completedSubmissions = formSubmissions.filter(s => s.completed);
        const completionRate = formSubmissions.length > 0 
            ? Math.round((completedSubmissions.length / formSubmissions.length) * 100) 
            : 0;

        const formCard = document.createElement('div');
        formCard.className = 'card p-6';
        formCard.innerHTML = `
            <div class="flex justify-between items-start mb-4">
                <h3 class="text-xl font-semibold text-[#002338]">${form.name}</h3>
                <span class="px-2 py-1 rounded-full text-xs bg-green-100 text-[#77c25c]">Activo</span>
            </div>
            
            <div class="space-y-2 text-sm text-[#77c25c] mb-4">
                <p>📋 Campos: ${form.fields.length}</p>
                <p>📅 Creado: ${new Date(form.created).toLocaleDateString()}</p>
                <p>👤 Por: ${form.createdBy || 'Sistema'}</p>
                <p>📊 Envíos: ${formSubmissions.length}</p>
                <p>✅ Completación: ${completionRate}%</p>
            </div>

            <div class="flex gap-2">
                <button onclick="previewForm(${form.id})" class="btn-secondary">👁️ Ver</button>
                ${currentUser && (currentUser.role === 'admin' || form.createdBy === currentUser.username) ? 
                    `<button onclick="deleteForm(${form.id})" class="btn-secondary text-red-600">🗑️</button>` : ''}
            </div>
        `;
        container.appendChild(formCard);
    });
}

function deleteForm(formId) {
    const form = forms.find(f => f.id === formId);
    if (currentUser.role !== 'admin' && form.createdBy !== currentUser.username) {
        alert('Solo puedes eliminar tus propios formularios');
        return;
    }

    if (confirm('¿Estás seguro de eliminar este formulario?')) {
        forms = forms.filter(f => f.id !== formId);
        localStorage.setItem('forms', JSON.stringify(forms));
        loadForms();
        updatePreviewSelect();
        displayNotifications(); // Update notifications after form is deleted
    }
}

function previewForm(formId) {
    document.getElementById('preview-form-select').value = formId;
    loadPreview();
    showTab('preview');
}

// Preview functionality
function updatePreviewSelect() {
    const select = document.getElementById('preview-form-select');
    select.innerHTML = '<option value="">Selecciona un formulario...</option>';
    
    forms.forEach(form => {
        const option = document.createElement('option');
        option.value = form.id;
        option.textContent = form.name;
        select.appendChild(option);
    });
}

function loadPreview() {
    const formId = parseInt(document.getElementById('preview-form-select').value);
    const container = document.getElementById('preview-container');
    
    if (!formId) {
        container.innerHTML = `
            <div class="card p-12 text-center border border-[#002338]">
                <div class="text-4xl mb-4">👁️</div>
                <p class="text-gray-500">Selecciona un formulario para ver la vista previa</p>
            </div>
        `;
        return;
    }

    const form = forms.find(f => f.id === formId);
    if (!form) return;

    const startTime = Date.now();
    let formData = {};

    container.innerHTML = `
        <div class="card p-6 border border-[#002338]">
            <h3 class="text-2xl font-semibold text-[#002338] mb-6">${form.name}</h3>
            <div class="space-y-4" id="form-fields">
                ${form.fields.map(field => `
                    <div>
                        <label class="block text-sm font-semibold text-[#002338] mb-2">
                            ${field.label}
                            ${field.required ? '<span class="text-red-500">*</span>' : ''}
                        </label>
                        ${field.type === 'textarea' 
                            ? `<textarea class="input" rows="4" data-field-id="${field.id}"></textarea>`
                            : `<input type="${field.type}" class="input" data-field-id="${field.id}">`
                        }
                    </div>
                `).join('')}
            </div>
            <button onclick="submitPreviewForm(${form.id}, ${startTime})" class="btn-primary w-full mt-6">
                📤 Enviar Formulario
            </button>
        </div>
    `;

    // Agregar listeners para tracking
    container.querySelectorAll('input, textarea').forEach(input => {
        input.addEventListener('input', (e) => {
            formData[e.target.dataset.fieldId] = e.target.value;
        });
    });
}

function submitPreviewForm(formId, startTime) {
    const form = forms.find(f => f.id === formId);
    const formData = {};
    let isValid = true;

    // Recopilar datos y validar
    document.querySelectorAll('#preview-container input, #preview-container textarea').forEach(input => {
        const fieldId = parseInt(input.dataset.fieldId);
        const field = form.fields.find(f => f.id === fieldId);
        
        formData[fieldId] = input.value;
        
        if (field.required && !input.value.trim()) {
            isValid = false;
            input.style.borderColor = '#ef4444';
        } else {
            input.style.borderColor = '#d1d5db';
        }
    });

    if (!isValid) {
        alert('⚠️ Por favor completa todos los campos requeridos');
        return;
    }

    const timeSpent = Math.round((Date.now() - startTime) / 1000);
    
    const submission = {
        id: Date.now(),
        formId: formId,
        userId: currentUser.username,
        userFullName: currentUser.fullName,
        timestamp: new Date().toISOString(),
        timeSpent: timeSpent,
        completed: true,
        data: formData
    };

    submissions.push(submission);
    localStorage.setItem('submissions', JSON.stringify(submissions));
    
    updateAnalytics();
    loadForms();
    if (currentUser.role === 'admin') {
        loadUsers();
    }
    
    alert('✅ Formulario enviado correctamente!\n⏱️ Tiempo: ' + timeSpent + ' segundos');
    
    // Limpiar formulario
    document.querySelectorAll('#preview-container input, #preview-container textarea').forEach(input => {
        input.value = '';
    });
}

// Analytics
function updateAnalytics() {
    const totalSubmissions = submissions.length;
    const completedSubmissions = submissions.filter(s => s.completed).length;
    const completionRate = totalSubmissions > 0 ? Math.round((completedSubmissions / totalSubmissions) * 100) : 0;
    const avgTime = totalSubmissions > 0 ? Math.round(submissions.reduce((acc, s) => acc + s.timeSpent, 0) / totalSubmissions) : 0;
    const uniqueUsers = new Set(submissions.map(s => s.userId)).size;

    document.getElementById('total-submissions').textContent = totalSubmissions;
    document.getElementById('completion-rate').textContent = completionRate + '%';
    document.getElementById('avg-time').textContent = avgTime + 's';
    document.getElementById('unique-users').textContent = uniqueUsers;

    // Actualizar tabla de submissions
    const tbody = document.getElementById('submissions-table');
    tbody.innerHTML = '';
    
    submissions.slice(-10).reverse().forEach(submission => {
        const form = forms.find(f => f.id === submission.formId);
        const user = users.find(u => u.username === submission.userId);
        const row = document.createElement('tr');
        row.className = 'border-b hover:bg-gray-50';
        row.innerHTML = `
            <td class="p-2">${user ? user.fullName : submission.userId}</td>
            <td class="p-2">${form ? form.name : 'Formulario eliminado'}</td>
            <td class="p-2">${new Date(submission.timestamp).toLocaleString()}</td>
            <td class="p-2">${submission.timeSpent}s</td>
            <td class="p-2">
                <span class="px-2 py-1 rounded-full text-xs ${
                    submission.completed 
                        ? 'bg-green-100 text-[#77c25c]' 
                        : 'bg-yellow-100 text-yellow-800'
                }">
                    ${submission.completed ? '✅ Completado' : '⏳ Incompleto'}
                </span>
            </td>
        `;
        tbody.appendChild(row);
    });
}

// Notifications
function displayNotifications() {
    const notificationArea = document.getElementById('notification-area');
    notificationArea.innerHTML = ''; // Clear previous notifications
    notificationArea.classList.add('hidden'); // Hide by default

    if (currentUser) {
        // Example: Notify about new forms created in the last 7 days
        const sevenDaysAgo = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000);
        const newForms = forms.filter(f => new Date(f.created) > sevenDaysAgo);

        if (newForms.length > 0) {
            notificationArea.innerHTML = `
                <div class="p-4 rounded-lg bg-green-100 text-green-800">
                    📢 Hay ${newForms.length} formulario(s) nuevo(s) creado(s) esta semana. ¡Échales un vistazo!
                </div>
            `;
            notificationArea.classList.remove('hidden');
        } else {
            // If no new forms, maybe a general positive message
            notificationArea.innerHTML = `
                <div class="p-4 rounded-lg bg-blue-100 text-blue-800">
                    👍 ¡Todo al día! No hay formularios nuevos esta semana.
                </div>
            `;
            notificationArea.classList.remove('hidden');
        }

        // Add more notification logic here if needed (e.g., low performance, high submissions)
    }
}

// Función para exportar datos
function exportData() {
    if (currentUser.role !== 'admin') {
        alert('Solo los administradores pueden exportar datos');
        return;
    }

    const data = {
        users: users.map(u => ({...u, password: '***'})), // Hide passwords
        forms: forms,
        submissions: submissions,
        exported: new Date().toISOString(),
        exportedBy: currentUser.username
    };
    
    const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'sistema-formularios-' + new Date().toISOString().split('T')[0] + '.json';
    a.click();
    URL.revokeObjectURL(url);
}

// Initialize app
document.addEventListener('DOMContentLoaded', function() {
    // Check if user is logged in
    const storedUser = localStorage.getItem('currentUser');
    if (storedUser) {
        currentUser = JSON.parse(storedUser);
        // Verify user still exists and is active
        const user = users.find(u => u.id === currentUser.id && u.active);
        if (user) {
            document.getElementById('auth-section').style.display = 'none';
            document.getElementById('app').style.display = 'block';
            document.getElementById('current-user-name').textContent = user.fullName;
            
            if (user.role === 'admin') {
                document.getElementById('tab-users').classList.remove('hidden');
            }
            
            loadForms();
            updateAnalytics();
            updatePreviewSelect();
            showTab('forms');
            displayNotifications(); // Display notifications on page load if logged in
        } else {
            logout(); // Log out if user no longer exists or is inactive
            alert("Debe iniciar sesión para acceder al sistema.");
            document.getElementById('app').style.display = 'none';
            document.getElementById('auth-section').style.display = 'flex';
        }
    } else {
        document.getElementById('auth-section').style.display = 'flex';
        document.getElementById('app').style.display = 'none';
    }

    // Add export button to analytics section
    const analyticsSection = document.getElementById('content-analytics');
    if (analyticsSection && currentUser && currentUser.role === 'admin') {
        const exportBtn = document.createElement('div');
        exportBtn.className = 'text-center mt-6';
        exportBtn.innerHTML = `
            <button onclick="exportData()" class="btn-secondary">
                📊 Exportar Datos
            </button>
        `;
        analyticsSection.appendChild(exportBtn);
    }
});
</script>
</body>
</html>
