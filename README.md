
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2027年夜飯投票</title>
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
                <label><input type="radio" name="restaurant" value="白金花"> 白金花</label>
                <label><input type="radio" name="restaurant" value="豪鼎"> 豪鼎</label>
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
    // 模擬後端儲存（實際應用中可串接 Firebase 或後端資料庫）
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

        // 讀取現有投票資料
        let votes = JSON.parse(localStorage.getItem('votes_2027') || '{}');
        
        if (votes[name]) {
            alert('您已經投過票囉！每人僅限投一次。');
            return;
        }

        // 記錄投票
        votes[name] = selected.value;
        localStorage.setItem('votes_2027', JSON.stringify(votes));

        document.getElementById('resultMsg').innerText = '投票成功！感謝您的參與。';
        document.getElementById('userName').disabled = true;
    }

    function showAdminResults() {
        const password = prompt('請輸入建立者密碼（預設密碼請自行修改）：');
        if (password !== 'admin123') {
            alert('密碼錯誤！');
            return;
        }

        let votes = JSON.parse(localStorage.getItem('votes_2027') || '{}');
        let html = '<b>目前投票明細：</b><br><ul>';
        let counts = { '白金花': 0, '豪鼎': 0 };

        for (let user in votes) {
            html += `<li>${user} 投給：<b>${votes[user]}</b></li>`;
            if (counts[votes[user]] !== undefined) {
                counts[votes[user]]++;
            }
        }
        html += '</ul><hr>';
        html += `<b>統計結果：</b><br>1. 白金花：${counts['白金花']} 票<br>2. 豪鼎：${counts['豪鼎']} 票`;

        document.getElementById('adminPanel').innerHTML = html;
    }
</script>

</body>
</html>
