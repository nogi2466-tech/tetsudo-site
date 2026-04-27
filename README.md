<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin: 0; padding: 0; background: #fff; }
        .top-bar { background: #333; color: #fff; padding: 20px; display: flex; justify-content: space-between; align-items: center; }
        .logo { font-size: 32px; font-weight: bold; }
        .datetime { text-align: right; font-size: 14px; font-family: monospace; }
        .container { padding: 20px; }
        .nav-tabs { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
        .nav-tabs button { padding: 10px 20px; border: 2px solid #000; background: #fff; cursor: pointer; font-size: 16px; font-weight: bold; }
        .nav-tabs button.active { border-color: red; color: red; }
        .content-section { display: none; padding: 10px; max-width: 500px; margin: 0 auto; text-align: left; }
        .content-section.active { display: block; }
        #rule-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #fff; display: flex; justify-content: center; align-items: center; z-index: 2000; }
        input, select { width: 100%; padding: 10px; margin: 10px 0; border: 2px solid #000; box-sizing: border-box; }
        .btn-main { background: #eee; border: 2px solid #000; padding: 10px; cursor: pointer; width: 100%; font-weight: bold; }
        .url-list { list-style: none; padding: 0; }
        .url-list li { border-bottom: 1px solid #ccc; padding: 10px 0; display: flex; justify-content: space-between; align-items: center; }
        .url-list a { text-decoration: none; color: blue; font-weight: bold; }
        .del-btn { color: white; background: red; border: none; padding: 5px 10px; cursor: pointer; font-size: 12px; display: none; }
        .admin-mode .del-btn { display: inline-block; }
    </style>
</head>
<body>

<div id="rule-overlay" style="display:none;">
    <div style="text-align:center;">
        <h2>ご利用ルール</h2>
        <p>正しく利用することに同意します</p>
        <label><input type="checkbox" id="rule-agree"> 同意する</label><br><br>
        <button class="btn-main" onclick="acceptRules()">サイトに入る</button>
    </div>
</div>

<div class="top-bar">
    <div class="logo">tetsudo</div>
    <div class="datetime">
        <div id="date">----/--/--</div>
        <div id="time">--:--:--</div>
    </div>
</div>

<div class="container">
    <div class="nav-tabs">
        <button onclick="showTab('keio')" id="btn-keio">京王</button>
        <button onclick="showTab('jr')" id="btn-jr">JR</button>
        <button onclick="showTab('other')" id="btn-other">その他</button>
        <button onclick="showTab('setting')" id="btn-setting">設定</button>
    </div>

    <div id="keio" class="content-section"><h2>京王 一覧</h2><ul class="url-list" id="list-keio"></ul></div>
    <div id="jr" class="content-section"><h2>JR 一覧</h2><ul class="url-list" id="list-jr"></ul></div>
    <div id="other" class="content-section"><h2>その他 一覧</h2><ul class="url-list" id="list-other"></ul></div>

    <div id="setting" class="content-section">
        <div id="admin-auth">
            <h3>管理ログイン (追加・削除)</h3>
            <input type="password" id="admin-pw" placeholder="パスワード(0829)">
            <button class="btn-main" onclick="authAdmin()">認証</button>
        </div>
        <div id="admin-panel" style="display:none; border: 2px dashed #333; padding: 15px; margin-top:10px;">
            <h4>動画URLの追加</h4>
            <label>追加先：</label>
            <select id="add-cat">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="other">その他</option>
            </select>
            <input type="text" id="add-title" placeholder="動画のタイトル">
            <input type="text" id="add-url" placeholder="https://youtube.com...">
            <button class="btn-main" style="background:#ccffcc;" onclick="addUrl()">保存して一覧に追加</button>
        </div>
        <hr>
        <div id="logout-area">
            <button onclick="logout()">ログアウト</button>
        </div>
    </div>
</div>

<!-- Firebase ライブラリ (修正版) -->
<script src="https://gstatic.com"></script>
<script src="https://gstatic.com"></script>

<script>
    // ★ここをご自身のFirebaseの「プロジェクト設定」にある内容に必ず書き換えてください
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_PROJECT.firebaseapp.com",
        databaseURL: "https://YOUR_PROJECT.firebaseio.com",
        projectId: "YOUR_PROJECT",
        storageBucket: "YOUR_PROJECT.appspot.com",
        messagingSenderId: "YOUR_ID",
        appId: "YOUR_APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    function updateClock() {
        const now = new Date();
        document.getElementById('date').innerText = now.toLocaleDateString('ja-JP');
        document.getElementById('time').innerText = now.toLocaleTimeString('ja-JP');
    }
    setInterval(updateClock, 1000);
    updateClock();

    function showTab(id) {
        document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-tabs button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('btn-' + id).classList.add('active');
    }

    function authAdmin() {
        if(document.getElementById('admin-pw').value === "0829") {
            document.getElementById('admin-panel').style.display = 'block';
            document.getElementById('admin-auth').style.display = 'none';
            document.body.classList.add('admin-mode');
            alert("管理者として認証されました");
        } else { alert("パスワードが違います"); }
    }

    function addUrl() {
        const cat = document.getElementById('add-cat').value;
        const title = document.getElementById('add-title').value;
        const url = document.getElementById('add-url').value;
        if(!title || !url) return alert("タイトルとURLを入力してください");

        db.ref('urls').push({cat, title, url})
            .then(() => { 
                alert("追加しました！"); 
                document.getElementById('add-title').value = ""; 
                document.getElementById('add-url').value = ""; 
            })
            .catch(e => alert("エラー: FirebaseのRealtime Databaseの設定を確認してください。"));
    }

    function delUrl(key) { if(confirm("消去しますか？")) db.ref('urls/' + key).remove(); }

    db.ref('urls').on('value', (snapshot) => {
        const data = snapshot.val();
        ['keio', 'jr', 'other'].forEach(c => document.getElementById('list-' + c).innerHTML = "");
        if(!data) return;
        Object.keys(data).forEach(key => {
            const item = data[key];
            const li = `<li><a href="${item.url}" target="_blank">${item.title}</a> <button class="del-btn" onclick="delUrl('${key}')">削除</button></li>`;
            const listElement = document.getElementById('list-' + item.cat);
            if(listElement) listElement.innerHTML += li;
        });
    });

    showTab('keio');
</script>
</body>
</html>
