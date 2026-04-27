<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin: 0; padding: 0; background: #fff; }
        
        /* ヘッダー */
        .top-bar { background: #333; color: #fff; padding: 20px; display: flex; justify-content: space-between; align-items: center; }
        .logo { font-size: 32px; font-weight: bold; }
        .datetime { text-align: right; font-size: 16px; font-family: 'Courier New', monospace; line-height: 1.2; }
        
        .container { padding: 20px; }
        .nav-tabs { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
        .nav-tabs button { padding: 10px 20px; border: 2px solid #000; background: #fff; cursor: pointer; font-size: 16px; font-weight: bold; }
        .nav-tabs button.active { border-color: red; color: red; }
        
        .content-section { display: none; padding: 10px; max-width: 600px; margin: 0 auto; text-align: left; }
        .content-section.active { display: block; }

        /* 利用ルール */
        #rule-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #fff; display: flex; justify-content: center; align-items: center; z-index: 2000; }
        
        input, select { width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #000; box-sizing: border-box; font-size: 16px; }
        .btn-main { background: #eee; border: 2px solid #000; padding: 12px; cursor: pointer; width: 100%; font-weight: bold; font-size: 16px; }
        .btn-main:hover { background: #ddd; }
        
        .url-list { list-style: none; padding: 0; }
        .url-list li { border-bottom: 1px solid #ccc; padding: 12px 0; display: flex; justify-content: space-between; align-items: center; }
        .url-list a { text-decoration: none; color: blue; font-weight: bold; font-size: 18px; }
        
        /* 削除ボタン（管理モード時のみ表示） */
        .del-btn { color: white; background: red; border: none; padding: 6px 12px; cursor: pointer; font-size: 12px; border-radius: 4px; display: none; }
        .admin-mode .del-btn { display: inline-block; }
    </style>
</head>
<body>

<!-- 利用ルールオーバーレイ -->
<div id="rule-overlay" style="display:none;">
    <div style="text-align:center; padding: 20px;">
        <h2>ご利用ルール</h2>
        <p>正しく利用することに同意しますか？</p>
        <label style="font-size: 18px;"><input type="checkbox" id="rule-agree"> 同意する</label><br><br>
        <button class="btn-main" onclick="acceptRules()">サイトに入る</button>
    </div>
</div>

<!-- ヘッダー -->
<div class="top-bar">
    <div class="logo">tetsudo</div>
    <div class="datetime">
        <div id="date">0000/00/00</div>
        <div id="time">00:00:00</div>
    </div>
</div>

<div class="container">
    <!-- タブボタン -->
    <div class="nav-tabs">
        <button onclick="showTab('keio')" id="btn-keio">京王</button>
        <button onclick="showTab('jr')" id="btn-jr">JR</button>
        <button onclick="showTab('other')" id="btn-other">その他</button>
        <button onclick="showTab('setting')" id="btn-setting">設定</button>
    </div>

    <!-- 各セクション -->
    <div id="keio" class="content-section"><h2>京王 一覧</h2><ul class="url-list" id="list-keio"></ul></div>
    <div id="jr" class="content-section"><h2>JR 一覧</h2><ul class="url-list" id="list-jr"></ul></div>
    <div id="other" class="content-section"><h2>その他 一覧</h2><ul class="url-list" id="list-other"></ul></div>

    <div id="setting" class="content-section">
        <!-- 管理ログイン -->
        <div id="admin-auth">
            <h3>管理ログイン</h3>
            <p style="font-size: 12px; color: #666;">動画を追加・削除するにはパスワードを入力してください。</p>
            <input type="password" id="admin-pw" placeholder="パスワードを入力">
            <button class="btn-main" onclick="authAdmin()">認証</button>
        </div>

        <!-- 追加パネル（初期状態は非表示） -->
        <div id="admin-panel" style="display:none; border: 2px dashed #000; padding: 20px; margin-top:10px; background: #f9f9f9;">
            <h4>動画を追加する</h4>
            <label>カテゴリー:</label>
            <select id="add-cat">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="other">その他</option>
            </select>
            <input type="text" id="add-title" placeholder="動画のタイトル">
            <input type="text" id="add-url" placeholder="動画のURL (https://...)">
            <button class="btn-main" style="background: #aaffaa;" onclick="addUrl()">データを保存して追加</button>
            <p style="font-size: 12px; margin-top:10px;">※保存すると全員の一覧に反映されます。</p>
        </div>

        <hr style="margin: 30px 0;">
        <button class="btn-main" onclick="logout()" style="background: #ffcccc;">ログアウト（規約再表示）</button>
    </div>
</div>

<!-- Firebase 本体ライブラリ（確実に動作するv8を使用） -->
<script src="https://gstatic.com"></script>
<script src="https://gstatic.com"></script>

<script>
    // 1. 時計機能（Firebaseの読み込み成否に関わらず動作）
    function updateClock() {
        const now = new Date();
        const d = document.getElementById('date');
        const t = document.getElementById('time');
        if(d) d.innerText = now.getFullYear() + "/" + String(now.getMonth() + 1).padStart(2, '0') + "/" + String(now.getDate()).padStart(2, '0');
        if(t) t.innerText = now.toLocaleTimeString('ja-JP');
    }
    setInterval(updateClock, 1000);
    updateClock();

    // 2. Firebase初期化（画像の設定値を反映）
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "tetsudo.firebaseapp.com",
        databaseURL: "https://tetsudo-default-rtdb.firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:f9a8a3bce73470b842ef9c"
    };
    
    // Firebaseが正しく読み込まれているか確認してから初期化
    if (typeof firebase !== 'undefined') {
        firebase.initializeApp(firebaseConfig);
        var db = firebase.database();
    } else {
        alert("Firebaseの読み込みに失敗しました。ネット接続を確認してください。");
    }

    // 3. ログイン・規約チェック
    if (!localStorage.getItem('tetsudo_agreed')) {
        document.getElementById('rule-overlay').style.display = 'flex';
    }

    function acceptRules() {
        if(document.getElementById('rule-agree').checked) {
            localStorage.setItem('tetsudo_agreed', 'true');
            document.getElementById('rule-overlay').style.display = 'none';
        } else {
            alert("同意チェックを入れてください");
        }
    }

    function logout() {
        localStorage.removeItem('tetsudo_agreed');
        location.reload();
    }

    // 4. タブ切り替え
    function showTab(id) {
        document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-tabs button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('btn-' + id).classList.add('active');
    }

    // 5. 管理者認証
    function authAdmin() {
        if(document.getElementById('admin-pw').value === "0829") {
            document.getElementById('admin-panel').style.display = 'block';
            document.getElementById('admin-auth').style.display = 'none';
            document.body.classList.add('admin-mode');
            alert("管理者認証に成功しました。一覧に削除ボタンが表示されます。");
        } else {
            alert("パスワードが違います。");
        }
    }

    // 6. Firebaseデータ追加
    function addUrl() {
        const cat = document.getElementById('add-cat').value;
        const title = document.getElementById('add-title').value;
        const url = document.getElementById('add-url').value;
        if(!title || !url) return alert("タイトルとURLを入力してください");

        db.ref('urls').push({cat, title, url})
            .then(() => {
                alert("保存しました！");
                document.getElementById('add-title').value = "";
                document.getElementById('add-url').value = "";
            })
            .catch(e => alert("保存エラー: Firebaseの『ルール』が公開(true)になっているか確認してください。"));
    }

    // 7. Firebaseデータ削除
    function delUrl(key) {
        if(confirm("この項目を完全に消去しますか？")) {
            db.ref('urls/' + key).remove();
        }
    }

    // 8. リアルタイムデータ読み込み
    if(db) {
        db.ref('urls').on('value', (snapshot) => {
            const data = snapshot.val();
            ['keio', 'jr', 'other'].forEach(c => document.getElementById('list-' + c).innerHTML = "");
            if(!data) return;
            Object.keys(data).forEach(key => {
                const item = data[key];
                const li = `<li>
                    <a href="${item.url}" target="_blank">${item.title}</a>
                    <button class="del-btn" onclick="delUrl('${key}')">削除</button>
                </li>`;
                const targetList = document.getElementById('list-' + item.cat);
                if(targetList) targetList.innerHTML += li;
            });
        });
    }

    // 初期表示
    showTab('keio');
</script>
</body>
</html>
