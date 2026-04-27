<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo</title>
    <style>
        /* 周りの線をなくし、シンプルなデザインに */
        body { font-family: sans-serif; text-align: center; padding: 20px; background: #fff; }
        
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .logo { font-size: 48px; font-weight: bold; }
        .datetime { text-align: right; font-size: 14px; line-height: 1.2; }
        
        /* 項目をボタン形式に */
        .nav-tabs { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        .nav-tabs button { 
            padding: 10px 25px; border: 2px solid #000; background: #fff; 
            cursor: pointer; font-size: 18px; font-weight: bold;
        }
        .nav-tabs button.active { border-color: red; color: red; }
        
        /* コンテンツエリア（枠線なし） */
        .content-section { display: none; padding: 10px; max-width: 500px; margin: 0 auto; text-align: left; }
        .content-section.active { display: block; }

        /* 利用ルール（規約） */
        #rule-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #fff; 
                         display: flex; justify-content: center; align-items: center; z-index: 2000; }
        .rule-box { width: 80%; max-width: 400px; text-align: center; }

        input, select { width: 100%; padding: 10px; margin: 10px 0; border: 2px solid #000; font-size: 16px; }
        .btn-main { background: #eee; border: 2px solid #000; padding: 10px; cursor: pointer; width: 100%; font-weight: bold; }
        
        .url-list { list-style: none; padding: 0; }
        .url-list li { margin: 10px 0; border-bottom: 1px solid #ccc; padding-bottom: 5px; display: flex; justify-content: space-between; }
        .url-list a { text-decoration: none; color: blue; font-weight: bold; }
        .del-btn { color: red; cursor: pointer; font-size: 12px; display: none; }
        .admin-mode .del-btn { display: inline; }
    </style>
</head>
<body>

<!-- 利用ルール（未登録時のみ表示） -->
<div id="rule-overlay" style="display:none;">
    <div class="rule-box">
        <h2>ご利用ルール</h2>
        <p>正しく利用することに同意しますか？</p>
        <label><input type="checkbox" id="rule-agree"> 同意する</label>
        <button class="btn-main" style="margin-top:20px;" onclick="acceptRules()">サイトに入る</button>
    </div>
</div>

<div class="header">
    <div class="logo">tetsudo</div>
    <div class="datetime">
        <div id="date">0000/00/00</div>
        <div id="time">00:00:00</div>
    </div>
</div>

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
    <div id="account-area">
        <h3>アカウント設定</h3>
        <input type="email" id="acc-email" placeholder="メールアドレス">
        <input type="text" id="acc-name" placeholder="名前">
        <button class="btn-main" onclick="register()">アカウント作成</button>
    </div>
    <div id="logout-area" style="display:none;">
        <p id="user-info"></p>
        <button onclick="logout()" style="border:none; background:none; color:gray; text-decoration:underline; cursor:pointer;">ログアウト</button>
    </div>
    
    <hr style="margin: 20px 0; border: none; border-top: 1px solid #000;">

    <div id="admin-auth">
        <h3>管理ログイン</h3>
        <input type="password" id="admin-pw" placeholder="パスワードを入力">
        <button class="btn-main" onclick="authAdmin()">認証</button>
    </div>

    <div id="admin-panel" style="display:none; margin-top: 10px; border: 1px dashed #000; padding: 10px;">
        <h4>URL追加</h4>
        <select id="add-cat"><option value="keio">京王</option><option value="jr">JR</option><option value="other">その他</option></select>
        <input type="text" id="add-title" placeholder="タイトル">
        <input type="text" id="add-url" placeholder="URL">
        <button class="btn-main" onclick="addUrl()">データベースに追加</button>
    </div>
</div>

<!-- Firebase SDK (同期に必要) -->
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
        document.getElementById('date').innerText = now.toLocaleDateString();
        document.getElementById('time').innerText = now.toLocaleTimeString();
    }
    setInterval(updateClock, 1000);
    updateClock();

    // ログイン済みなら規約を隠す
    const savedUser = JSON.parse(localStorage.getItem('tetsudo_user'));
    if (!savedUser) {
        document.getElementById('rule-overlay').style.display = 'flex';
    } else {
        document.getElementById('account-area').style.display = 'none';
        document.getElementById('logout-area').style.display = 'block';
        document.getElementById('user-info').innerText = `ログイン中: ${savedUser.name}`;
    }

    function acceptRules() {
        if(document.getElementById('rule-agree').checked) {
            document.getElementById('rule-overlay').style.display = 'none';
        }
    }

    function showTab(id) {
        document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-tabs button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('btn-' + id).classList.add('active');
    }

    function register() {
        const email = document.getElementById('acc-email').value;
        const name = document.getElementById('acc-name').value;
        if(!email.includes('@') || !name) return alert("正しく入力してください");
        localStorage.setItem('tetsudo_user', JSON.stringify({email, name}));
        location.reload();
    }

    function logout() { localStorage.removeItem('tetsudo_user'); location.reload(); }

    function authAdmin() {
        if(document.getElementById('admin-pw').value === "0829") {
            document.getElementById('admin-panel').style.display = 'block';
            document.getElementById('admin-auth').style.display = 'none';
            document.body.classList.add('admin-mode');
        } else { alert("パスワードが違います"); }
    }

    // URL追加
    function addUrl() {
        const cat = document.getElementById('add-cat').value;
        const title = document.getElementById('add-title').value;
        const url = document.getElementById('add-url').value;
        if(!title || !url) return alert("入力してください");

        db.ref('urls').push({cat, title, url})
            .then(() => {
                alert("追加完了");
                document.getElementById('add-title').value = "";
                document.getElementById('add-url').value = "";
            })
            .catch(err => alert("エラー: " + err.message));
    }

    function delUrl(key) { if(confirm("削除しますか？")) db.ref('urls/' + key).remove(); }

    // データのリアルタイム読み込み
    db.ref('urls').on('value', (snapshot) => {
        const data = snapshot.val();
        ['keio', 'jr', 'other'].forEach(c => document.getElementById('list-' + c).innerHTML = "");
        if(!data) return;
        Object.keys(data).forEach(key => {
            const item = data[key];
            const li = `<li><a href="${item.url}" target="_blank">${item.title}</a> <span class="del-btn" onclick="delUrl('${key}')">[削除]</span></li>`;
            document.getElementById('list-' + item.cat).innerHTML += li;
        });
    });

    showTab('keio');
</script>
</body>
</html>

