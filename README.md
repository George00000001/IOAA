<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IOAA</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #121212;
            color: #e0e0e0;
            margin: 0;
            padding: 0;
        }
        header {
            background: linear-gradient(to right, #673ab7, #512da8);
            color: white;
            padding: 1rem;
            text-align: center;
            font-size: 2rem;
            box-shadow: 0 2px 5px rgba(0,0,0,0.5);
        }
        .menu {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 3rem;
            gap: 1.5rem;
        }
        .menu a {
            background-color: #03a9f4;
            color: white;
            padding: 1rem 2rem;
            border-radius: 8px;
            text-decoration: none;
            font-size: 1.25rem;
            transition: all 0.3s;
            width: 200px;
            text-align: center;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }
        .menu a:hover {
            background-color: #0288d1;
            transform: translateY(-3px);
        }
    </style>
</head>
<body>
    <header>IOAA - Login</header>
    <div class="menu">
        <input type="text" id="username" placeholder="اسم المستخدم" style="padding: 1rem; border-radius: 8px; width: 250px;">
        <input type="password" id="password" placeholder="كلمة المرور" style="padding: 1rem; border-radius: 8px; width: 250px;">
        <button onclick="login()" style="padding: 1rem 2rem; border-radius: 8px; font-size: 1rem; cursor: pointer;">دخول</button>
    </div>
    <script>
        function login() {
            const user = document.getElementById('username').value;
            const pass = document.getElementById('password').value;
            if(user === "admin" && pass === "1234") {
                window.location.href = 'tasks.html';
            } else {
                alert("بيانات دخول غير صحيحة!");
            }
        }
    </script>
</body>
</html>

<!-- tasks.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tasks - IOAA</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #121212; color: #e0e0e0; margin: 0; padding: 0; }
        header { background: linear-gradient(to right, #673ab7, #512da8); color: white; padding: 1rem; text-align: center; font-size: 2rem; box-shadow: 0 2px 5px rgba(0,0,0,0.5); }
        .container { max-width: 1200px; margin: auto; padding: 1rem; background-color: #1e1e1e; margin-top: 1rem; box-shadow: 0 2px 5px rgba(0,0,0,0.3); border-radius: 8px; }
        table { width: 100%; border-collapse: collapse; margin-top: 1rem; }
        th, td { padding: 0.75rem; border: 1px solid #333; text-align: center; }
        th { background-color: #4CAF50; color: white; }
        input[type="text"], select, input[type="date"] { padding: 0.5rem; width: 100%; box-sizing: border-box; border-radius: 5px; border: 1px solid #ccc; margin-bottom: 10px; color: #000; }
        .btn { padding: 0.5rem 1rem; border: none; border-radius: 5px; background-color: #4CAF50; color: white; cursor: pointer; margin-top: 10px; }
        .btn:hover { background-color: #45a049; }
        .form-section { margin-top: 20px; }
    </style>
</head>
<body>
    <header>Tasks</header>
    <div class="container">
        <div class="form-section">
            <h3>إضافة أو تعديل إجراء</h3>
            <input type="hidden" id="taskIndex">
            <input type="text" id="action" placeholder="الاجراء">
            <input type="text" id="company" placeholder="الشركة">
            <select id="importance">
                <option value="عالي">عالي</option>
                <option value="متوسط">متوسط</option>
                <option value="منخفض">منخفض</option>
            </select>
            <input type="text" id="assigned" placeholder="الموكل بالاجراء">
            <input type="date" id="startDate">
            <input type="text" id="status" placeholder="موقف الاجراء">
            <input type="date" id="endDate">
            <input type="text" id="notes" placeholder="ملاحظات">
            <button class="btn" onclick="saveTask()">حفظ</button>
            <button class="btn" onclick="clearForm()">مسح</button>
        </div>
        <div class="form-section">
            <button class="btn" onclick="exportData()">🔄 تصدير البيانات</button>
            <input type="file" id="importFile" onchange="importData()">
        </div>
        <table id="tasksTable">
            <thead>
                <tr>
                    <th>الاجراء</th>
                    <th>الشركة</th>
                    <th>الاهمية</th>
                    <th>الموكل بالاجراء</th>
                    <th>تاريخ البداية</th>
                    <th>موقف الاجراء</th>
                    <th>تاريخ النهاية</th>
                    <th>ملاحظات</th>
                    <th>تعديل</th>
                    <th>حذف</th>
                </tr>
            </thead>
            <tbody id="tableBody"></tbody>
        </table>
        <a class="btn" href="index.html">تسجيل الخروج</a>
    </div>
    <script>
        let tasks = JSON.parse(localStorage.getItem("tasks")) || [];

        function renderTable() {
            const tbody = document.getElementById("tableBody");
            tbody.innerHTML = "";
            tasks.forEach((task, index) => {
                const row = `<tr>
                    <td>${task.action}</td>
                    <td>${task.company}</td>
                    <td>${task.importance}</td>
                    <td>${task.assigned}</td>
                    <td>${task.startDate}</td>
                    <td>${task.status}</td>
                    <td>${task.endDate}</td>
                    <td>${task.notes}</td>
                    <td><button onclick="editTask(${index})">✏️</button></td>
                    <td><button onclick="deleteTask(${index})">🗑️</button></td>
                </tr>`;
                tbody.innerHTML += row;
            });
        }

        function saveTask() {
            const index = document.getElementById("taskIndex").value;
            const task = {
                action: document.getElementById("action").value,
                company: document.getElementById("company").value,
                importance: document.getElementById("importance").value,
                assigned: document.getElementById("assigned").value,
                startDate: document.getElementById("startDate").value,
                status: document.getElementById("status").value,
                endDate: document.getElementById("endDate").value,
                notes: document.getElementById("notes").value
            };
            if(index === "") {
                tasks.push(task);
            } else {
                tasks[index] = task;
            }
            localStorage.setItem("tasks", JSON.stringify(tasks));
            renderTable();
            clearForm();
        }

        function editTask(index) {
            const task = tasks[index];
            document.getElementById("taskIndex").value = index;
            document.getElementById("action").value = task.action;
            document.getElementById("company").value = task.company;
            document.getElementById("importance").value = task.importance;
            document.getElementById("assigned").value = task.assigned;
            document.getElementById("startDate").value = task.startDate;
            document.getElementById("status").value = task.status;
            document.getElementById("endDate").value = task.endDate;
            document.getElementById("notes").value = task.notes;
        }

        function deleteTask(index) {
            if(confirm("هل أنت متأكد من الحذف؟")) {
                tasks.splice(index, 1);
                localStorage.setItem("tasks", JSON.stringify(tasks));
                renderTable();
            }
        }

        function clearForm() {
            document.getElementById("taskIndex").value = "";
            document.querySelectorAll('.form-section input, .form-section select').forEach(i => i.value = "");
        }

        function exportData() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(tasks));
            const dlAnchorElem = document.createElement('a');
            dlAnchorElem.setAttribute("href", dataStr);
            dlAnchorElem.setAttribute("download", "tasks.json");
            dlAnchorElem.click();
        }

        function importData() {
            const file = document.getElementById('importFile').files[0];
            const reader = new FileReader();
            reader.onload = function(e) {
                tasks = JSON.parse(e.target.result);
                localStorage.setItem("tasks", JSON.stringify(tasks));
                renderTable();
            };
            reader.readAsText(file);
        }

        renderTable();
    </script>
</body>
</html>
