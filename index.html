<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo - 認証システム</title>
    <style>
        /* 基本スタイル */
        body { font-family: sans-serif; text-align: center; margin: 0; padding: 20px; }
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .logo { font-size: 40px; font-weight: bold; }
        .nav-tabs { display: flex; justify-content: center; gap: 5px; margin-bottom: 20px; }
        .nav-tabs button { padding: 8px 15px; border: 2px solid #000; background: #fff; cursor: pointer; }
        .nav-tabs button.active { border-color: red; color: red; }
        .content-section { display: none; border: 2px solid #000; min-height: 300px; padding: 20px; max-width: 500px; margin: 0 auto; }
        .content-section.active { display: block; }

        /* 利用ルール（オーバーレイ） */
        #rule-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); 
                         display: flex; justify-content: center; align-items: center; z-index: 1000; }
        .rule-box { background: white; padding: 30px; border-radius: 10px; max-width: 400px; text-align: left; }
        
        /* 管理画面用スタイル */
        .admin-box { background: #f9f9f9; padding: 15px; border: 1px dashed #333; margin-top: 10px; }
        .user-badge { display: inline-block; background: #e0f7fa; padding: 2px 8px; border-radius: 10px; margin: 2px; font-size: 12px; }
        .del-btn { color: red; cursor: pointer; margin-left: 10px; font-weight: bold; }
    </style>
</head>
<body>

<!-- 1. 利用ルールモーダル -->
<div id="rule-overlay">
    <div class="rule-box">
        <h2>ご利用ルール</h2>
        <p>・不適切なURLを投稿しないこと</p>
        <p>・ルールを守って利用すること</p>
        <label><input type="checkbox" id="rule-check"> ルールに同意します</label>
        <br><br>
        <button onclick="agreeRules()" style="width: 100%; padding: 10px;">サイトへ入る</button>
    </div>
</div>

<div class="header">
    <div class="logo">tetsudo</div>
    <div id="login-status" style="font-size: 12px;"></div>
</div>

<div class="nav-tabs">
    <button onclick="showTab('keio')" id="btn-keio">京王</button>
    <button onclick="showTab('jr')" id="btn-jr">JR</button>
    <button onclick="showTab('setting')" id="btn-setting">設定/管理</button>
</div>

<!-- メインコンテンツ -->
<div id="keio" class="content-section">
    <h2>京王 一覧</h2>
    <ul id="list-keio" style="text-align: left;"></ul>
</div>

<div id="jr" class="content-section">
    <h2>JR 一覧</h2>
    <ul id="list-jr" style="text-align: left;"></ul>
</div>

<!-- 設定・管理画面 -->
<div id="setting" class="content-section">
    <h3>アカウント設定</h3>
    <input type="text" id="user-name" placeholder="あなたの名前を入力">
    <button onclick="saveAccount()">登録</button>
    
    <hr>
    
    <h3>URL管理（パスワード 0829）</h3>
    <div id="password-area">
        <input type="password" id="pw-input" placeholder="パスワードを入力">
        <button onclick="checkPassword()">管理モード起動</button>
    </div>

    <div id="admin-controls" style="display:none;" class="admin-box">
        <p><strong>現在の閲覧者:</strong></p>
        <div id="online-users"></div> <!-- ここに登録済みの人が表示される -->
        
        <hr>
        <h4>URL追加</h4>
        <select id="cat-select"><option value="keio">京王</option><option value="jr">JR</option></select>
        <input type="text" id="url-title" placeholder="タイトル">
        <input type="text" id="url-link" placeholder="URL">
        <button onclick="addUrl()">追加</button>
    </div>
</div>

<script>
    // 利用ルールの同意
    function agreeRules() {
        if (document.getElementById('rule-check').checked) {
            document.getElementById('rule-overlay').style.display = 'none';
            trackUser(); // 閲覧開始を記録
        } else {
            alert("同意チェックを入れてください");
        }
    }

    // タブ切り替え
    function showTab(id) {
        document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    // アカウント登録
    function saveAccount() {
        const name = document.getElementById('user-name').value;
        if (!name) return alert("名前を入力してください");
        localStorage.setItem('tetsudo_user', name);
        updateStatus();
        trackUser();
        alert("登録しました");
    }

    // 閲覧者の記録（簡易版：localStorageに名前を保存）
    function trackUser() {
        const name = localStorage.getItem('tetsudo_user') || "名無しさん";
        let users = JSON.parse(localStorage.getItem('online_users') || '[]');
        if (!users.includes(name)) {
            users.push(name);
            localStorage.setItem('online_users', JSON.stringify(users));
        }
        renderOnlineUsers();
    }

    // オンラインユーザー表示
    function renderOnlineUsers() {
        const users = JSON.parse(localStorage.getItem('online_users') || '[]');
        const container = document.getElementById('online-users');
        container.innerHTML = users.map(u => `<span class="user-badge">${u}</span>`).join('');
    }

    // パスワードチェック
    function checkPassword() {
        if (document.getElementById('pw-input').value === "0829") {
            document.getElementById('admin-controls').style.display = 'block';
            document.getElementById('password-area').style.display = 'none';
            renderOnlineUsers();
        } else {
            alert("パスワードが違います");
        }
    }

    // URL追加・表示・削除の基本機能
    function addUrl() {
        const cat = document.getElementById('cat-select').value;
        const title = document.getElementById('url-title').value;
        const link = document.getElementById('url-link').value;
        if(!title || !link) return;

        const urls = JSON.parse(localStorage.getItem('urls') || '[]');
        urls.push({ id: Date.now(), cat, title, link });
        localStorage.setItem('urls', JSON.stringify(urls));
        renderUrls();
    }

    function deleteUrl(id) {
        let urls = JSON.parse(localStorage.getItem('urls') || '[]');
        urls = urls.filter(u => u.id !== id);
        localStorage.setItem('urls', JSON.stringify(urls));
        renderUrls();
    }

    function renderUrls() {
        const urls = JSON.parse(localStorage.getItem('urls') || '[]');
        ['keio', 'jr'].forEach(cat => {
            const list = document.getElementById('list-' + cat);
            list.innerHTML = "";
            urls.filter(u => u.cat === cat).forEach(u => {
                list.innerHTML += `<li><a href="${u.link}" target="_blank">${u.title}</a> <span class="del-btn" onclick="deleteUrl(${u.id})">[×]</span></li>`;
            });
        });
    }

    function updateStatus() {
        const name = localStorage.getItem('tetsudo_user');
        document.getElementById('login-status').innerText = name ? `ログイン中: ${name}` : "未登録";
    }

    // 初期化
    updateStatus();
    renderUrls();
    showTab('keio');
</script>

</body>
</html>

