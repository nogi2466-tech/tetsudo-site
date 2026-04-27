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
        
        .content-section { display: none; border: 2px solid #000; min-height: 400px; padding: 20px; margin: 0 auto; max-width: 500px; }
        .content-section.active { display: block; }

        input, select { padding: 10px; width: 80%; border: 2px solid #000; font-size: 16px; margin-bottom: 10px; }
        .btn-main { padding: 10px 40px; background: #eee; border: 2px solid #000; cursor: pointer; font-weight: bold; }
        
        .url-list { list-style: none; padding: 0; text-align: left; }
        .url-list li { margin: 10px 0; border-bottom: 1px solid #ccc; padding-bottom: 8px; display: flex; justify-content: space-between; align-items: center; }
        .url-list a { text-decoration: none; color: blue; font-weight: bold; }
        
        /* 削除ボタン（管理画面のみ表示） */
        .del-btn { color: white; background: red; border: none; padding: 2px 8px; cursor: pointer; font-size: 12px; display: none; }
        .admin-mode .del-btn { display: inline-block; }
    </style>
</head>
<body class="public-mode"> <!-- 初期状態は一般モード -->

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

    <!-- URL一覧エリア（常に表示） -->
    <div id="keio" class="content-section active"><h2>京王 URL一覧</h2><ul class="url-list" id="list-keio"></ul></div>
    <div id="jr" class="content-section"><h2>JR URL一覧</h2><ul class="url-list" id="list-jr"></ul></div>
    <div id="other" class="content-section"><h2>その他 URL一覧</h2><ul class="url-list" id="list-other"></ul></div>

    <!-- 設定エリア -->
    <div id="setting" class="content-section">
        <!-- 認証前 -->
        <div id="password-area">
            <p>パスワードを入力して管理モードへ</p>
            <input type="password" id="pw-input">
            <button class="btn-main" onclick="checkPassword()">認証</button>
        </div>
        
        <!-- 認証後 -->
        <div id="add-url-area" style="display:none;">
            <h3>URLを追加</h3>
            <select id="category-select">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="other">その他</option>
            </select>
            <input type="text" id="title-input" placeholder="タイトルを入力">
            <input type="text" id="url-input" placeholder="URLを入力">
            <button class="btn-main" onclick="addUrl()">追加</button>
            <hr style="margin: 20px 0;">
            <button onclick="logout()" style="border:none; background:none; cursor:pointer; color:gray; text-decoration: underline;">管理モードを終了</button>
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
            if (document.getElementById('pw-input').value === "0829") {
                document.body.classList.replace('public-mode', 'admin-mode'); // 削除ボタンを表示させる
                document.getElementById('password-area').style.display = 'none';
                document.getElementById('add-url-area').style.display = 'block';
            } else {
                alert("パスワードが違います");
            }
        }

        function logout() {
            document.body.classList.replace('admin-mode', 'public-mode');
            document.getElementById('pw-input').value = "";
            document.getElementById('password-area').style.display = 'block';
            document.getElementById('add-url-area').style.display = 'none';
        }

        // URL追加
        function addUrl() {
            const cat = document.getElementById('category-select').value;
            const title = document.getElementById('title-input').value;
            const url = document.getElementById('url-input').value;

            if (!title || !url) return alert("入力してください");

            const data = JSON.parse(localStorage.getItem('tetsudo_urls') || '[]');
            data.push({ id: Date.now(), cat, title, url }); // 削除用にIDを付与
            localStorage.setItem('tetsudo_urls', JSON.stringify(data));

            document.getElementById('title-input').value = "";
            document.getElementById('url-input').value = "";
            renderUrls();
            alert("追加しました。各タブで確認・削除ができます。");
        }

        // URL削除
        function deleteUrl(id) {
            if (!confirm("本当に削除しますか？")) return;
            let data = JSON.parse(localStorage.getItem('tetsudo_urls') || '[]');
            data = data.filter(item => item.id !== id);
            localStorage.setItem('tetsudo_urls', JSON.stringify(data));
            renderUrls();
        }

        // 一覧表示
        function renderUrls() {
            const data = JSON.parse(localStorage.getItem('tetsudo_urls') || '[]');
            const lists = { keio: 'list-keio', jr: 'list-jr', other: 'list-other' };
            
            Object.values(lists).forEach(id => document.getElementById(id).innerHTML = "");

            data.forEach(item => {
                const li = document.createElement('li');
                li.innerHTML = `
                    <a href="${item.url}" target="_blank">${item.title}</a>
                    <button class="del-btn" onclick="deleteUrl(${item.id})">削除</button>
                `;
                document.getElementById(lists[item.cat]).appendChild(li);
            });
        }

        renderUrls();
    </script>
</body>
</html>
