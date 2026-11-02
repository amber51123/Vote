<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2027年夜飯投票</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: #f7f9fc;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            box-sizing: border-box;
        }
        .container {
            background: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
            width: 100%;
            max-width: 400px;
        }
        h2 {
            text-align: center;
            color: #333;
            margin-top: 0;
            margin-bottom: 25px;
        }
        .form-group {
            margin-bottom: 20px;
        }
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #444;
        }
        input[type="text"] {
            width: 100%;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 16px;
            outline: none;
        }
        input[type="text"]:focus {
            border-color: #007bff;
        }
        .radio-options {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 10px;
        }
        .radio-options label {
            font-weight: normal;
            display: flex;
            align-items: center;
            gap: 10px;
            cursor: pointer;
            background: #f8f9fa;
            padding: 12px;
            border-radius: 6px;
            border: 1px solid #eee;
        }
        button {
            width: 100%;
            padding: 14px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        button:hover {
            background-color: #0056b3;
        }
        .msg {
            margin-top: 15px;
            text-align: center;
            font-size: 15px;
            color: #28a745;
            font-weight: bold;
        }
        .admin-section {
            margin-top: 30px;
            border-top: 1px dashed #ddd;
            padding-top: 20px;
            text-align: center;
        }
        .admin-btn {
            background-color: #6c757d;
            font-size: 14px;
            padding: 10px;
        }
        .admin-btn:hover {
            background-color: #5a6268;
        }
        #adminPanel {
            margin-top: 15px;
            text-align: left;
            font-size: 14px;
            background: #f1f3f5;
            padding: 12px;
            border-radius: 6px;
            display: none;
            max-height: 250px;
            overflow-y: auto;
        }
    </style>
</head>
<body>

<div class="container">
    <div id="voteForm">
        <h2>🍽️ 2027年夜飯投票</h2>
        
        <div class="form-group">
            <label for="userName">您的姓名：</label>
            <input type="text" id="userName" placeholder="請輸入您的名字">
        </div>
        
        <div class="form-group">
            <label>請選擇餐廳：</label>
            <div class="radio-options">
                <label><input type="radio" name="restaurant" value="白金花"> 1. 白金花</label>
                <label><input type="radio" name="restaurant" value="豪鼎"> 2. 豪鼎</label>
            </div>
        </div>
        
        <button id="submitBtn" onclick="submitVote()">送出投票</button>
        <div id="resultMsg" class="msg"></div>
    </div>

    <!-- 管理者專屬按鈕與後台區塊 -->
    <div class="admin-section">
        <button class="admin-btn" onclick="showAdminResults()">🔐 查看投票結果後台</button>
        <div id="adminPanel">載入中...</div>
    </div>
</div>

<script>
    // ⚠️ 請把下面引號內的網址，換成你的 Google Apps Script 網頁應用程式網址
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbxZwGyKFGv_izJLq7hUjQk5jiC0_v-1LVZRyLGn30WvbcKoRSSgs120fO42pjtWgSg7tQ/exec";

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

        const btn = document.getElementById('submitBtn');
        btn.innerText = '投票送出中...';
        btn.disabled = true;

        const formData = {
            action: "vote",
            name: name,
            choice: selected.value
        };

        fetch(SCRIPT_URL, {
            method: 'POST',
            mode: 'no-cors',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(formData)
        })
        .then(() => {
            document.getElementById('resultMsg').innerText = '投票成功！感謝您的參與 🎉';
            document.getElementById('userName').disabled = true;
            document.querySelectorAll('input[name="restaurant"]').forEach(el => el.disabled = true);
            btn.style.display = 'none';
        })
        .catch(error => {
            alert('投票發生錯誤，請稍後再試！');
            btn.innerText = '送出投票';
            btn.disabled = false;
        });
    }

    function showAdminResults() {
        const password = prompt('請輸入管理者密碼：');
        if (password !== 'admin123') {
            alert('密碼錯誤！');
            return;
        }

        const panel = document.getElementById('adminPanel');
        panel.style.display = 'block';
        panel.innerHTML = '正在讀取雲端結果...';

        // 透過 GET 請求從 Google 試算表抓取投票資料
        fetch(SCRIPT_URL + "?action=getResults")
        .then(response => response.json())
        .then(data => {
            let html = '<b>📊 投票明細：</b><ul style="padding-left: 20px; margin: 5px 0;">';
            let counts = { '白金花': 0, '豪鼎': 0 };

            if (data.length === 0) {
                html += '<li>目前尚無人投票</li>';
            } else {
                data.forEach(row => {
                    html += `<li>${row.name} 投給：<b>${row.choice}</b></li>`;
                    if (counts[row.choice] !== undefined) {
                        counts[row.choice]++;
                    }
                });
            }

            html += '</ul><hr style="border:0; border-top:1px solid #ddd; margin:8px 0;">';
            html += `<b>📈 總票數統計：</b><br>`;
            html += `1. 白金花：${counts['白金花']} 票<br>`;
            html += `2. 豪鼎：${counts['豪鼎']} 票`;

            panel.innerHTML = html;
        })
        .catch(error => {
            panel.innerHTML = '讀取失敗，請確認網路或指令碼設定。';
        });
    }
</script>

</body>
</html>
