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
    </style>
</head>
<body>

<div class="container">
    <h2>🍽️ 2027年夜飯投票</h2>
    
    <div id="voteForm">
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
</div>

<script>
    // ⚠️ 請把下面這行引號內的網址，換成你剛剛從 Google Apps Script 複製的「網頁應用程式網址」
    const SCRIPT_URL = "YOUR_GOOGLE_SCRIPT_URL";

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
            name: name,
            choice: selected.value
        };

        // 發送資料到 Google 試算表
        fetch(SCRIPT_URL, {
            method: 'POST',
            mode: 'no-cors', // 避免跨域阻擋
            headers: {
                'Content-Type': 'application/json',
            },
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
</script>

</body>
</html>
