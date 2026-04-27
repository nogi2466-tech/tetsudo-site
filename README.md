<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin: 0; padding: 0; background: #fff; }
        
        /* 上部に色を追加 */
        .top-bar { background: #333; color: #fff; padding: 20px; display: flex; justify-content: space-between; align-items: center; }
        .logo { font-size: 32px; font-weight: bold; }
        .datetime { text-align: right; font-size: 14px; font-family: monospace; }
        
        .container { padding: 20px; }
        .nav-tabs { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        .nav-tabs button { padding: 10px 20px; border: 2px solid #000; background: #fff; cursor: pointer; font-size: 16px; font-weight: bold; }
        .nav-tabs button.active { border-color: red; color: red; }
        
        .content-section { display: none; padding: 10px; max-width: 500px; margin: 0 auto; text-align: left; }
        .content-section.active { display: block; }

        /* 利用ルール */
        #rule-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #fff; display: flex; justify-content: center; align-items: center; z-index: 2000; }
        
        input, select { width: 100%; padding: 10px; margin: 10px 0; border: 2px solid #000; box-sizing: border-box; }
        .btn-main { background: #eee; border: 2px solid #000; padding: 10px; cursor: pointer; width: 100%; font-weight: bold; }
        
        .url-list { list-style: none; padding: 0; }
        .url-list li { border-bottom: 1px solid #ccc; padding: 10px 0; display: flex; justify-content: space-between; align-items: center; }
        .url-list a { text-decoration: none; color: blue; font-weight: bold; }
        
        /* 管理モード時のみ削除ボタンを表示 */
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

<!-- 上部の色付きヘッダー -->
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

    <div id="keio" class="content-section"><h2>京王</h2><ul class="url-list" id="list-keio"></ul></div>
    <div id="jr" class="content-section"><h2>JR</h2><ul class="url-list" id="list-jr"></ul></div>
    <div id="other" class="content-section"><h2>その他</h2><ul class="url-list" id="list-other"></ul></div>

    <div id="setting" class="content-section">
        <div id="account-area">
            <h3>アカウント登録</h3>
            <input type="email" id="acc-email" placeholder="メールアドレス">
            <input type="text" id="acc-name" placeholder="名前">
            <button class="btn-main" onclick="register()">登録</button>
        </div>
        <div id="logout-area" style="display:none;">
            <p id="user-info"></p>
            <button onclick="logout()">ログアウト</button>
        </div>
        <hr>
        <div id="admin-auth">
            <h3>管理ログイン</h3>
            <input type="password" id="admin-pw" placeholder="パスワードを入力">
            <button class="btn-main" onclick="authAdmin()">認証</button>
        </div>
        <div id="admin-panel" style="display:none; border: 1px dashed #000; padding: 10px; margin-top:10px;">
            <h4>URL追加</h4>
            <select id="add-cat"><option value="keio">京王</option><option value="jr">JR</option><option value="other">その他</option></select>
            <input type="text" id="add-title" placeholder="タイトル">
            <input type="text" id="add-url" placeholder="URL">
            <button class="btn-main" onclick="addUrl()">保存</button>
        </div>
    </div>
</div>

<script src="https://gstatic.com"></script>
<script src="https://gstatic.com"></script>

<script>
    // ★ここにご自身のFirebase設定を貼り付けてください
    const firebaseConfig = {
        apiKey: "YOUR_API_KEY",
        authDomain: "YOUR_://firebaseapp.com",
        databaseURL: "https://YOUR_://firebaseio.com",
        projectId: "YOUR_PROJECT",
        storageBucket: "YOUR_://appspot.com",
        messagingSenderId: "000000000000",
        appId: "1:000000000000:web:xxxx"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // 日時表示
    function updateClock() {
        const now = new Date();
        document.getElementById('date').innerText = now.toLocaleDateString('ja-JP');
        document.getElementById('time').innerText = now.toLocaleTimeString('ja-JP');
    }
    setInterval(updateClock, 1000);
    updateClock();

    // ログインチェックと規約表示
    const savedUser = JSON.parse(localStorage.getItem('tetsudo_user'));
    if (!savedUser) {
        document.getElementById('rule-overlay').style.display = 'flex';
    } else {
        document.getElementById('account-area').style.display = 'none';
        document.getElementById('logout-area').style.display = 'block';
        document.getElementById('user-info').innerText = `ログイン中: ${savedUser.name}`;
    }

    function acceptRules() {
        if(document.getElementById('rule-agree').checked) document.getElementById('rule-overlay').style.display = 'none';
    }

    function showTab(id) {
        document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-tabs button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('btn-' + id).classList.add('active');
    }

    function register() {
        const name = document.getElementById('acc-name').value;
        if(!name) return alert("名前を入れてください");
        localStorage.setItem('tetsudo_user', JSON.stringify({name}));
        location.reload();
    }

    function logout() { localStorage.removeItem('tetsudo_user'); location.reload(); }

    function authAdmin() {
        if(document.getElementById('admin-pw').value === "0829") {
            document.getElementById('admin-panel').style.display = 'block';
            document.getElementById('admin-auth').style.display = 'none';
            document.body.classList.add('admin-mode');
        } else { alert("失敗"); }
    }

    // URL追加（Firebase）
    function addUrl() {
        const cat = document.getElementById('add-cat').value;
        const title = document.getElementById('add-title').value;
        const url = document.getElementById('add-url').value;
        if(!title || !url) return alert("入力してください");

        db.ref('urls').push({cat, title, url})
            .then(() => { alert("追加成功"); document.getElementById('add-title').value = ""; document.getElementById('add-url').value = ""; })
            .catch(e => alert("保存に失敗しました。Firebaseの「Rules」を確認してください。"));
    }

    // URL削除（Firebase）
    function delUrl(key) { if(confirm("消去しますか？")) db.ref('urls/' + key).remove(); }

    // データの読み込み
    db.ref('urls').on('value', (snapshot) => {
        const data = snapshot.val();
        ['keio', 'jr', 'other'].forEach(c => document.getElementById('list-' + c).innerHTML = "");
        if(!data) return;
        Object.keys(data).forEach(key => {
            const item = data[key];
            const li = `<li><a href="${item.url}" target="_blank">${item.title}</a> <button class="del-btn" onclick="delUrl('${key}')">削除</button></li>`;
            document.getElementById('list-' + item.cat).innerHTML += li;
        });
    });

    showTab('keio');
</script>
</body>
</html>

