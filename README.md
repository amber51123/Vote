
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2027年夜飯投票</title>
    <!-- 引入 Firebase 雲端資料庫 SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            background-color: #f4f6f9;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            background: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 400px;
        }
        h2 {
            text-align: center;
            color: #333;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 20px;
        }
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #555;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 16px;
        }
        .radio-options {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-top: 10px;
        }
        .radio-options label {
            font-weight: normal;
            display: flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
        }
        button {
            width: 100%;
            padding: 12px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        button:hover {
            background-color: #0056b3;
        }
        .msg {
            margin-top: 15px;
            text-align: center;
            font-size: 14px;
            color: #28a745;
        }
        .admin-section {
            margin-top: 30px;
            border-top: 1px solid #eee;
            padding-top: 20px;
        }
        .admin-section h3 {
            font-size: 16px;
            color: #666;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>🍽️ 2027年夜飯投票</h2>
    
    <div id="voteForm">
        <div class="form-group">
            <label for="userName">您的姓名 / 識別名稱：</label>
            <input type="text" id="userName" placeholder="請輸入您的名字（防重複投票）">
        </div>
        
        <div class="form-group">
            <label>請選擇餐廳：</label>
            <div class="radio-options">
                <label><input type="radio" name="restaurant" value="白金花"> 1. 白金花</label>
                <label><input type="radio" name="restaurant" value="豪鼎"> 2. 豪鼎</label>
            </div>
        </div>
        
        <button onclick="submitVote()">送出投票</button>
        <div id="resultMsg" class="msg"></div>
    </div>

    <!-- 建立者專屬後台區塊 -->
    <div class="admin-section">
        <h3>🔐 建立者專屬管理</h3>
        <button onclick="showAdminResults()" style="background-color: #6c757d;">查看投票結果明細</button>
        <div id="adminPanel" style="margin-top: 10px; font-size: 14px; color: #333;"></div>
    </div>
</div>

<script>
    // === 請在此處填入你免費申請的 Firebase 設定 ===
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_AUTH_DOMAIN",
        databaseURL: "YOUR_DATABASE_URL",
        projectId: "YOUR_PROJECT_ID",
        storageBucket: "YOUR_STORAGE_BUCKET",
        messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
        appId: "YOUR_APP_ID"
    };

    // 初始化 Firebase
    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();

    function submitVote() {
        const name = document.getElementById('userName').value.trim();
        const selected = document.querySelector('input[name="restaurant"]:checked');
        
        if (!name) {
            alert('請輸入您的名字！');
            return;
        }
        if (!selected) {
            alert('請選擇一個選項！');
            return;
        }

        // 將名字中的特殊字元過濾（Firebase key 不支援某些符號）
        const safeName = name.replace(/[.#$\/\[\]]/g, '_');

        // 檢查是否投過票
        database.ref('votes/' + safeName).once('value', (snapshot) => {
            if (snapshot.exists()) {
                alert('您已經投過票囉！每人僅限投一次。');
                return;
            }

            // 寫入雲端資料庫
            database.ref('votes/' + safeName).set({
                name: name,
                choice: selected.value,
                time: new Date().toLocaleString()
            }, (error) => {
                if (error) {
                    alert('投票失敗，請稍後再試');
                } else {
                    document.getElementById('resultMsg').innerText = '投票成功！感謝您的參與。';
                    document.getElementById('userName').disabled = true;
                }
            });
        });
    }

    function showAdminResults() {
        const password = prompt('請輸入建立者密碼：');
        if (password !== 'admin123') {
            alert('密碼錯誤！');
            return;
        }

        database.ref('votes').once('value', (snapshot) => {
            let votes = snapshot.val() || {};
            let html = '<b>目前雲端投票明細：</b><br><ul>';
            let counts = { '白金花': 0, '豪鼎': 0 };

            for (let key in votes) {
                let v = votes[key];
                html += `<li>${v.name} 投給：<b>${v.choice}</b></li>`;
                if (counts[v.choice] !== undefined) {
                    counts[v.choice]++;
                }
            }
            html += '</ul><hr>';
            html += `<b>雲端統計結果：</b><br>1. 白金花：${counts['白金花']} 票<br>2. 豪鼎：${counts['豪鼎']} 票`;

            document.getElementById('adminPanel').innerHTML = html;
        });
    }
</script>

</body>
</html>
