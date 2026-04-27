<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo</title>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 20px; }
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .logo { font-size: 48px; font-weight: bold; }
        .datetime { text-align: right; font-size: 14px; }
        .nav-tabs { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        .nav-tabs button { padding: 10px 20px; border: 2px solid #000; background: #fff; cursor: pointer; font-size: 18px; }
        .nav-tabs button.active { border-color: red; color: red; }
        
        .content-section { display: none; border: 2px solid #000; min-height: 300px; padding: 20px; margin: 0 auto; max-width: 500px; }
        .content-section.active { display: block; }

        .setting-form { display: flex; flex-direction: column; gap: 15px; align-items: center; }
        input { padding: 10px; width: 80%; border: 2px solid #000; font-size: 16px; }
        .btn-add { padding: 10px 40px; background: #eee; border: 2px solid #000; cursor: pointer; }
        
        .url-list { list-style: none; padding: 0; text-align: left; }
        .url-list li { margin: 10px 0; border-bottom: 1px solid #ccc; padding-bottom: 5px; }
        .url-list a { text-decoration: none; color: blue; }
    </style>
</head>
<body>

    <div class="header">
        <div class="logo">tetsudo</div>
        <div class="datetime">
            <div id="date">日付を表示</div>
            <div id="time">時間を表示</div>
        </div>
    </div>

    <div class="nav-tabs">
        <button onclick="showTab('keio')" id="btn-keio">京王</button>
        <button onclick="showTab('jr')" id="btn-jr">JR</button>
        <button onclick="showTab('other')" id="btn-other">その他</button>
        <button onclick="showTab('setting')" id="btn-setting">設定</button>
    </div>

    <!-- 各カテゴリの表示エリア -->
    <div id="keio" class="content-section active"><h2>京王 URL一覧</h2><ul class="url-list" id="list-keio"></ul></div>
    <div id="jr" class="content-section"><h2>JR URL一覧</h2><ul class="url-list" id="list-jr"></ul></div>
    <div id="other" class="content-section"><h2>その他 URL一覧</h2><ul class="url-list" id="list-other"></ul></div>

    <!-- 設定エリア（パスワードが必要） -->
    <div id="setting" class="content-section">
        <div id="password-area">
            <p>パスワードを入力</p>
            <input type="password" id="pw-input">
            <button class="btn-add" style="margin-top:10px" onclick="checkPassword()">認証</button>
        </div>
        
        <div id="add-url-area" style="display:none;">
            <h3>URLを追加</h3>
            <select id="category-select" style="margin-bottom:10px; padding:5px;">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="other">その他</option>
            </select>
            <input type="text" id="title-input" placeholder="タイトルを入力">
            <input type="text" id="url-input" placeholder="URLを入力">
            <button class="btn-add" onclick="addUrl()">追加</button>
            <button onclick="logout()" style="margin-top:20px; border:none; background:none; cursor:pointer; color:gray;">ログアウト</button>
        </div>
    </div>

    <script>
        // 日時更新
        function updateTime() {
            const now = new Date();
            document.getElementById('date').innerText = now.toLocaleDateString();
            document.getElementById('time').innerText = now.toLocaleTimeString();
        }
        setInterval(updateTime, 1000);
        updateTime();

        // タブ切り替え
        function showTab(tabId) {
            document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.nav-tabs button').forEach(b => b.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
            document.getElementById('btn-' + tabId).classList.add('active');
        }

        // パスワードチェック
        function checkPassword() {
            const pw = document.getElementById('pw-input').value;
            if (pw === "0829") {
                document.getElementById('password-area').style.display = 'none';
                document.getElementById('add-url-area').style.display = 'block';
            } else {
                alert("パスワードが違います");
            }
        }

        function logout() {
            document.getElementById('pw-input').value = "";
            document.getElementById('password-area').style.display = 'block';
            document.getElementById('add-url-area').style.display = 'none';
        }

        // URL追加と保存
        function addUrl() {
            const cat = document.getElementById('category-select').value;
            const title = document.getElementById('title-input').value;
            const url = document.getElementById('url-input').value;

            if (!title || !url) return alert("入力してください");

            const data = JSON.parse(localStorage.getItem('tetsudo_urls') || '[]');
            data.push({ cat, title, url });
            localStorage.setItem('tetsudo_urls', JSON.stringify(data));

            document.getElementById('title-input').value = "";
            document.getElementById('url-input').value = "";
            renderUrls();
            alert("追加しました");
        }

        // 一覧表示
        function renderUrls() {
            const data = JSON.parse(localStorage.getItem('tetsudo_urls') || '[]');
            const lists = { keio: 'list-keio', jr: 'list-jr', other: 'list-other' };
            
            // 一旦クリア
            Object.values(lists).forEach(id => document.getElementById(id).innerHTML = "");

            data.forEach(item => {
                const li = document.createElement('li');
                li.innerHTML = `<a href="${item.url}" target="_blank">${item.title}</a>`;
                document.getElementById(lists[item.cat]).appendChild(li);
            });
        }

        // 初期表示
        renderUrls();
        showTab('keio');
    </script>
</body>
</html>
