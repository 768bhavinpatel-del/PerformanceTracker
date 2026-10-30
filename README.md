<!DOCTYPE html>
<html lang="gu">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Employee Performance & Task Management</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #3498db;
            --success-color: #2ecc71;
            --danger-color: #e74c3c;
            --light-bg: #f8f9fa;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f6f9;
        }

        .header {
            background-color: var(--primary-color);
            color: white;
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .lang-switch select {
            padding: 5px 10px;
            border-radius: 4px;
            border: none;
            font-weight: bold;
        }

        .container {
            padding: 20px;
            max-width: 1400px;
            margin: 0 auto;
        }

        .card {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }

        .login-card {
            max-width: 400px;
            margin: 80px auto;
        }

        .form-group {
            margin-bottom: 15px;
        }

        .form-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
        }

        .form-group input, .form-group select {
            width: 100%;
            padding: 8px 12px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }

        button {
            background-color: var(--secondary-color);
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 4px;
            cursor: pointer;
            font-weight: bold;
        }

        button:hover {
            opacity: 0.9;
        }

        .btn-danger {
            background-color: var(--danger-color);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            overflow-x: auto;
            display: block;
        }

        th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: left;
            min-width: 140px;
        }

        th {
            background-color: #e9ecef;
            position: sticky;
            top: 0;
        }

        .permissions-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 10px;
            margin-top: 10px;
        }

        .hidden {
            display: none !important;
        }

        .nav-tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            border-bottom: 2px solid #ccc;
            padding-bottom: 5px;
        }

        .nav-tabs button {
            background: none;
            color: #333;
            border: none;
            padding: 10px 20px;
            cursor: pointer;
        }

        .nav-tabs button.active {
            border-bottom: 3px solid var(--secondary-color);
            color: var(--secondary-color);
            font-weight: bold;
        }
    </style>
</head>
<body>

    <!-- Header Section -->
    <div class="header">
        <h2 id="app-title">Performance Sheet Portal</h2>
        <div style="display: flex; gap: 15px; align-items: center;">
            <div class="lang-switch">
                <label for="languageSelect" style="color:white; margin-right:5px;">🌐</label>
                <select id="languageSelect" onchange="changeLanguage(this.value)">
                    <option value="gu">ગુજરાતી</option>
                    <option value="en">English</option>
                    <option value="hi">हिंदी</option>
                </select>
            </div>
            <button id="logoutBtn" class="hidden btn-danger" onclick="logout()" data-i18n="logout">Logout</button>
        </div>
    </div>

    <div class="container">
        
        <!-- Login Form -->
        <div id="loginSection" class="card login-card">
            <h3 data-i18n="login">Login</h3>
            <div class="form-group">
                <label data-i18n="username">Username</label>
                <input type="text" id="loginUsername" placeholder="Enter Username">
            </div>
            <div class="form-group">
                <label data-i18n="password">Password</label>
                <input type="password" id="loginPassword" placeholder="Enter Password">
            </div>
            <button style="width: 100%;" onclick="login()" data-i18n="login">Login</button>
            <p id="loginError" style="color:red; margin-top:10px; display:none;" data-i18n="invalid_login">Invalid Username or Password</p>
        </div>

        <!-- Dashboard Section -->
        <div id="dashboardSection" class="hidden">
            <h3 id="welcomeMsg">Welcome</h3>

            <!-- Navigation Tabs (Only visible to Admin) -->
            <div id="adminNav" class="nav-tabs hidden">
                <button class="active" onclick="switchTab('userMgmt')" data-i18n="tab_users">User Management & Rights</button>
                <button onclick="switchTab('taskMgmt')" data-i18n="tab_tasks">Master Task List (Col G)</button>
                <button onclick="switchTab('dataSheet')" data-i18n="tab_data">Performance Data Sheet</button>
            </div>

            <!-- TAB 1: User Management & Rights (Admin) -->
            <div id="userMgmtTab" class="tab-content hidden">
                <div class="card">
                    <h4 data-i18n="add_user">Add New User & Assign Rights</h4>
                    <div style="display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px;">
                        <input type="text" id="newUsername" placeholder="Username (Col A Name)">
                        <input type="password" id="newPassword" placeholder="Password">
                        <select id="newUserRole">
                            <option value="employee">Employee</option>
                            <option value="admin">Admin</option>
                        </select>
                    </div>

                    <h5 style="margin-top: 15px;" data-i18n="select_visible_cols">Select Editable/Visible Columns:</h5>
                    <div class="permissions-grid" id="colPermissions">
                        <!-- Checkboxes generated via JS -->
                    </div>

                    <button style="margin-top: 15px;" onclick="createUser()" data-i18n="btn_create_user">Save User Rights</button>
                </div>
            </div>

            <!-- TAB 2: Task List Management (Col G Master) -->
            <div id="taskMgmtTab" class="tab-content hidden">
                <div class="card">
                    <h4 data-i18n="add_task">Add Master Task (Col G)</h4>
                    <div style="display:flex; gap:10px;">
                        <input type="text" id="newTaskName" placeholder="Task Name">
                        <button onclick="addMasterTask()" data-i18n="btn_add_task">Add Task</button>
                    </div>
                    <ul id="masterTaskList" style="margin-top: 15px;"></ul>
                </div>
            </div>

            <!-- TAB 3: Data Sheet (Viewable by Employee and Admin) -->
            <div id="dataSheetTab" class="tab-content card">
                <h4 data-i18n="performance_sheet">Performance Entry Sheet</h4>
                
                <!-- Admin Task & Salary Management Box -->
                <div id="adminActionBox" class="card hidden" style="background: #eef6fc;">
                    <h5 data-i18n="admin_action_title">Admin Panel: Assign Daily Task & Salary</h5>
                    <div style="display:flex; gap:10px; flex-wrap: wrap; align-items: center;">
                        <label>Date:</label>
                        <input type="date" id="inputDate" style="width: 140px;">
                        <select id="assignUserSelect"></select>
                        <input type="number" id="inputSalary" placeholder="Salary (Col B)" style="width: 130px;">
                        <select id="assignTaskSelect"></select>
                        <button onclick="assignDailyTask()" data-i18n="btn_assign">Add Daily Entry</button>
                    </div>
                </div>

                <!-- Main Data Table -->
                <div style="overflow-x:auto;">
                    <table id="performanceTable">
                        <thead>
                            <tr id="tableHeader">
                                <!-- Headers built dynamically -->
                            </tr>
                        </thead>
                        <tbody id="tableBody">
                            <!-- Rows generated via JS -->
                        </tbody>
                    </table>
                </div>
                <button style="margin-top: 15px;" onclick="saveSheetData()" data-i18n="btn_save_data">Save Sheet Data</button>
            </div>

        </div>
    </div>

    <script>
        // Language Translations
        const translations = {
            gu: {
                app_title: "પર્ફોર્મન્સ શીટ પોર્ટલ",
                login: "લોગિન",
                username: "યુઝરનેમ",
                password: "પાસવર્ડ",
                invalid_login: "ખોટો યુઝરનેમ અથવા પાસવર્ડ!",
                logout: "લોગઆઉટ",
                welcome: "જી આયા નૂ, ",
                tab_users: "યુઝર મેનેજમેન્ટ અને રાઈટ્સ",
                tab_tasks: "માસ્ટર ટાસ્ક લિસ્ટ (કોલમ G)",
                tab_data: "પર્ફોર્મન્સ ડેટા શીટ",
                add_user: "નવો યુઝર અને રાઈટ્સ ઉમેરો",
                select_visible_cols: "એક્સેસ આપવાની હોય તે કોલમ પસંદ કરો:",
                btn_create_user: "યુઝર સેટિંગ્સ સેવ કરો",
                add_task: "માસ્ટર ટાસ્ક ઉમેરો (કોલમ G)",
                btn_add_task: "ટાસ્ક એડ કરો",
                performance_sheet: "પર્ફોર્મન્સ ડેટા એન્ટ્રી શીટ",
                admin_action_title: "એડમિન પેનલ: તારીખ સાથે કર્મચારીને સેલેરી અને ટાસ્ક એસાઇન કરો",
                btn_assign: "રોજની એન્ટ્રી ઉમેરો",
                btn_save_data: "ડેટા સેવ કરો",
                cols: {
                    dateCol: "Date (તારીખ)",
                    colA: "A: Name (નામ)",
                    colB: "B: Salary (સેલેરી)",
                    colC: "C: 10% Incentive",
                    colD: "D: Per Day Incentive",
                    colE: "E: 1% Indiscipline Incentive",
                    colF: "F: 1% Over Time Incentive",
                    colG: "G: Task Name (ટાસ્ક)",
                    colH: "H: Completed/Incomplete",
                    colI: "I: Incentive Amount according to status",
                    colJ: "J: Total Point",
                    colK: "K: Per Point Incentive",
                    colL: "L: Total Incentive",
                    colM: "M: Incentive Eligible to Receive",
                    colN: "N: Same Day No Submission (-50% Per Day)",
                    colO: "O: Next Day No Report (-50% Per Day)"
                }
            },
            en: {
                app_title: "Performance Sheet Portal",
                login: "Login",
                username: "Username",
                password: "Password",
                invalid_login: "Invalid Username or Password!",
                logout: "Logout",
                welcome: "Welcome, ",
                tab_users: "User Management & Rights",
                tab_tasks: "Master Task List (Col G)",
                tab_data: "Performance Data Sheet",
                add_user: "Add New User & Set Rights",
                select_visible_cols: "Select Visible/Editable Columns:",
                btn_create_user: "Save User Rights",
                add_task: "Add Master Task (Col G)",
                btn_add_task: "Add Task",
                performance_sheet: "Performance Entry Sheet",
                admin_action_title: "Admin Panel: Assign Daily Task & Salary with Date",
                btn_assign: "Add Daily Entry",
                btn_save_data: "Save Data",
                cols: {
                    dateCol: "Date",
                    colA: "A: Name",
                    colB: "B: Salary",
                    colC: "C: 10% Incentive",
                    colD: "D: Per Day Incentive",
                    colE: "E: 1% Indiscipline Incentive",
                    colF: "F: 1% Over Time Incentive",
                    colG: "G: Task Name",
                    colH: "H: Completed/Incomplete",
                    colI: "I: Incentive Amount according to status",
                    colJ: "J: Total Point",
                    colK: "K: Per Point Incentive",
                    colL: "L: Total Incentive",
                    colM: "M: Incentive Eligible to Receive",
                    colN: "N: Same Day No Submission (-50% Per Day)",
                    colO: "O: Next Day No Report (-50% Per Day)"
                }
            },
            hi: {
                app_title: "परफॉरमेंस शीट पोर्टल",
                login: "लॉगिन",
                username: "यूज़रनेम",
                password: "पासवर्ड",
                invalid_login: "गलत यूज़रनेम या पासवर्ड!",
                logout: "लॉगआउट",
                welcome: "स्वागत है, ",
                tab_users: "यूज़र मैनेजमेंट और राइट्स",
                tab_tasks: "मास्टर टास्क लिस्ट (कॉलम G)",
                tab_data: "परफॉरमेंस डेटा शीट",
                add_user: "नया यूज़र और राइट्स जोड़ें",
                select_visible_cols: "एक्सेस देने वाले कॉलम चुनें:",
                btn_create_user: "यूज़र सेटिंग्स सेव करें",
                add_task: "मास्टर टास्क जोड़ें (कॉलम G)",
                btn_add_task: "टास्क जोड़ें",
                performance_sheet: "परफॉरमेंस डेटा एंट्री शीट",
                admin_action_title: "एडमिन पैनल: तारीख के साथ कर्मचारी को सैलरी और टास्क असाइन करें",
                btn_assign: "दैनिक एंट्री जोड़ें",
                btn_save_data: "डेटा सेव करें",
                cols: {
                    dateCol: "Date (तारीख)",
                    colA: "A: Name (नाम)",
                    colB: "B: Salary (सैलेरी)",
                    colC: "C: 10% Incentive",
                    colD: "D: Per Day Incentive",
                    colE: "E: 1% Indiscipline Incentive",
                    colF: "F: 1% Over Time Incentive",
                    colG: "G: Task Name (टास्क)",
                    colH: "H: Completed/Incomplete",
                    colI: "I: Incentive Amount according to status",
                    colJ: "J: Total Point",
                    colK: "K: Per Point Incentive",
                    colL: "L: Total Incentive",
                    colM: "M: Incentive Eligible to Receive",
                    colN: "N: Same Day No Submission (-50% Per Day)",
                    colO: "O: Next Day No Report (-50% Per Day)"
                }
            }
        };

        const colKeys = ['dateCol', 'colA', 'colB', 'colC', 'colD', 'colE', 'colF', 'colG', 'colH', 'colI', 'colJ', 'colK', 'colL', 'colM', 'colN', 'colO'];

        let currentLang = 'gu';
        let currentUser = null;

        let users = JSON.parse(localStorage.getItem('app_users')) || [
            { username: 'admin', password: '123', role: 'admin', rights: colKeys },
            { username: 'Demo', password: '123', role: 'employee', rights: ['dateCol', 'colA', 'colG', 'colH'] }
        ];

        let masterTasks = JSON.parse(localStorage.getItem('app_tasks')) || ["Task 1", "Task 2", "Task 3"];
        
        let sheetData = JSON.parse(localStorage.getItem('app_sheet_data')) || [
            { dateCol: '2026-06-07', colA: 'Demo', colB: 15000, colC: 1500, colD: 50, colE: 150, colF: 150, colG: 'Task 1', colH: 'Incomplete', colI: 0, colJ: 0, colK: 0, colL: 0, colM: 0, colN: 0, colO: 0 }
        ];

        function changeLanguage(lang) {
            currentLang = lang;
            document.querySelectorAll('[data-i18n]').forEach(el => {
                const key = el.getAttribute('data-i18n');
                if (translations[lang][key]) {
                    el.innerText = translations[lang][key];
                }
            });
            renderPermissionsCheckboxes();
            renderSheetTable();
        }

        function login() {
            const u = document.getElementById('loginUsername').value;
            const p = document.getElementById('loginPassword').value;

            const found = users.find(user => user.username === u && user.password === p);
            if (found) {
                currentUser = found;
                document.getElementById('loginSection').classList.add('hidden');
                document.getElementById('dashboardSection').classList.remove('hidden');
                document.getElementById('logoutBtn').classList.remove('hidden');
                document.getElementById('welcomeMsg').innerText = translations[currentLang].welcome + currentUser.username;

                // Set today's date by default in admin input
                const today = new Date().toISOString().split('T')[0];
                const dateInput = document.getElementById('inputDate');
                if(dateInput) dateInput.value = today;

                if (currentUser.role === 'admin') {
                    document.getElementById('adminNav').classList.remove('hidden');
                    document.getElementById('adminActionBox').classList.remove('hidden');
                    switchTab('userMgmt');
                } else {
                    document.getElementById('adminNav').classList.add('hidden');
                    document.getElementById('adminActionBox').classList.add('hidden');
                    switchTab('dataSheet');
                }
                populateTaskAssignOptions();
                renderSheetTable();
            } else {
                document.getElementById('loginError').style.display = 'block';
            }
        }

        function logout() {
            currentUser = null;
            document.getElementById('loginSection').classList.remove('hidden');
            document.getElementById('dashboardSection').classList.add('hidden');
            document.getElementById('logoutBtn').classList.add('hidden');
        }

        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.nav-tabs button').forEach(el => el.classList.remove('active'));

            if (tabName === 'userMgmt') {
                document.getElementById('userMgmtTab').classList.remove('hidden');
            } else if (tabName === 'taskMgmt') {
                document.getElementById('taskMgmtTab').classList.remove('hidden');
                renderMasterTaskList();
            } else if (tabName === 'dataSheet') {
                document.getElementById('dataSheetTab').classList.remove('hidden');
                renderSheetTable();
            }
        }

        function renderPermissionsCheckboxes() {
            const container = document.getElementById('colPermissions');
            container.innerHTML = '';
            colKeys.forEach(key => {
                const div = document.createElement('div');
                div.innerHTML = `
                    <input type="checkbox" id="chk_${key}" value="${key}" checked>
                    <label for="chk_${key}">${translations[currentLang].cols[key]}</label>
                `;
                container.appendChild(div);
            });
        }

        function createUser() {
            const u = document.getElementById('newUsername').value;
            const p = document.getElementById('newPassword').value;
            const r = document.getElementById('newUserRole').value;

            if (!u || !p) return alert("Please fill username and password");

            const selectedRights = [];
            colKeys.forEach(key => {
                if (document.getElementById(`chk_${key}`).checked) {
                    selectedRights.push(key);
                }
            });

            const existingIdx = users.findIndex(user => user.username === u);
            if (existingIdx >= 0) {
                users[existingIdx] = { username: u, password: p, role: r, rights: selectedRights };
            } else {
                users.push({ username: u, password: p, role: r, rights: selectedRights });
            }

            localStorage.setItem('app_users', JSON.stringify(users));
            alert("User and Rights saved successfully!");
            populateTaskAssignOptions();
        }

        function addMasterTask() {
            const t = document.getElementById('newTaskName').value;
            if (t) {
                masterTasks.push(t);
                localStorage.setItem('app_tasks', JSON.stringify(masterTasks));
                document.getElementById('newTaskName').value = '';
                renderMasterTaskList();
                populateTaskAssignOptions();
            }
        }

        function renderMasterTaskList() {
            const list = document.getElementById('masterTaskList');
            list.innerHTML = '';
            masterTasks.forEach((t, i) => {
                list.innerHTML += `<li>${t} <button class="btn-danger" style="padding:2px 5px;" onclick="removeTask(${i})">X</button></li>`;
            });
        }

        function removeTask(index) {
            masterTasks.splice(index, 1);
            localStorage.setItem('app_tasks', JSON.stringify(masterTasks));
            renderMasterTaskList();
            populateTaskAssignOptions();
        }

        function populateTaskAssignOptions() {
            const uSelect = document.getElementById('assignUserSelect');
            const tSelect = document.getElementById('assignTaskSelect');
            if(!uSelect || !tSelect) return;

            uSelect.innerHTML = '';
            users.filter(u => u.role === 'employee').forEach(u => {
                uSelect.innerHTML += `<option value="${u.username}">${u.username}</option>`;
            });

            tSelect.innerHTML = '';
            masterTasks.forEach(t => {
                tSelect.innerHTML += `<option value="${t}">${t}</option>`;
            });
        }

        function assignDailyTask() {
            const dt = document.getElementById('inputDate').value;
            const u = document.getElementById('assignUserSelect').value;
            const sal = parseFloat(document.getElementById('inputSalary').value) || 0;
            const t = document.getElementById('assignTaskSelect').value;

            if (!dt) return alert("Please select a date!");

            let c = sal * 0.10;
            sheetData.push({
                dateCol: dt,
                colA: u, 
                colB: sal, 
                colC: c, 
                colD: (c/30).toFixed(2), 
                colE: sal * 0.01, 
                colF: sal * 0.01, 
                colG: t, 
                colH: 'Incomplete', 
                colI: 0, 
                colJ: 0, 
                colK: 0, 
                colL: 0, 
                colM: 0, 
                colN: 0, 
                colO: 0
            });

            localStorage.setItem('app_sheet_data', JSON.stringify(sheetData));
            alert("Daily Task & Entry Added Successfully!");
            renderSheetTable();
        }

        function renderSheetTable() {
            const headerTr = document.getElementById('tableHeader');
            const bodyTb = document.getElementById('tableBody');
            headerTr.innerHTML = '';
            bodyTb.innerHTML = '';

            const userRights = currentUser.role === 'admin' ? colKeys : currentUser.rights;

            colKeys.forEach(colKey => {
                if (userRights.includes(colKey)) {
                    headerTr.innerHTML += `<th>${translations[currentLang].cols[colKey]}</th>`;
                }
            });

            const visibleRows = currentUser.role === 'admin' ? sheetData : sheetData.filter(row => row.colA === currentUser.username);

            visibleRows.forEach((row, rowIndex) => {
                let tr = document.createElement('tr');
                colKeys.forEach(colKey => {
                    if (userRights.includes(colKey)) {
                        let td = document.createElement('td');
                        
                        let isEditable = true;
                        if (currentUser.role === 'employee') {
                            // Employees can only update column H (Completed/Incomplete) for their rows
                            if (colKey !== 'colH') {
                                isEditable = false; 
                            }
                        }

                        if (!isEditable) {
                            td.innerText = row[colKey] || '';
                        } else {
                            if (colKey === 'colH') {
                                // Dropdown for Completed / Incomplete
                                td.innerHTML = `
                                    <select onchange="updateCellData(${rowIndex}, '${colKey}', this.value)">
                                        <option value="Incomplete" ${row[colKey] === 'Incomplete' ? 'selected' : ''}>Incomplete</option>
                                        <option value="Completed" ${row[colKey] === 'Completed' ? 'selected' : ''}>Completed</option>
                                    </select>
                                `;
                            } else {
                                td.innerHTML = `<input type="text" value="${row[colKey] || ''}" onchange="updateCellData(${rowIndex}, '${colKey}', this.value)">`;
                            }
                        }
                        tr.appendChild(td);
                    }
                });
                bodyTb.appendChild(tr);
            });
        }

        function updateCellData(rowIndex, colKey, value) {
            const visibleRows = currentUser.role === 'admin' ? sheetData : sheetData.filter(row => row.colA === currentUser.username);
            visibleRows[rowIndex][colKey] = value;
            
            let r = visibleRows[rowIndex];
            if (colKey === 'colB') {
                let x = parseFloat(r.colB) || 0;
                r.colC = x * 0.10;
                r.colD = (r.colC / 30).toFixed(2);
                r.colE = x * 0.01;
                r.colF = x * 0.01;
            }

            localStorage.setItem('app_sheet_data', JSON.stringify(sheetData));
        }

        function saveSheetData() {
            localStorage.setItem('app_sheet_data', JSON.stringify(sheetData));
            alert("Data saved successfully!");
        }

        renderPermissionsCheckboxes();
    </script>
</body>
</html># PerformanceTracker
