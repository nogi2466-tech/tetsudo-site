<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - 鉄道動画同期システム</title>
    <!-- Firebaseライブラリを最優先で読み込み -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        :root { --primary-blue: #0078d4; --bg-gray: #f9f9f9; --keio-red: #e60012; --jr-green: #008000; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); color: #333; }
        header { background: linear-gradient(135deg, #0078d4, #00b0ff); color: white; padding: 25px 10px; text-align: center; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); text-align: center; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 15px; cursor: pointer; color: #555; border-radius: 20px; transition: 0.3s; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }
        section { display: none; max-width: 500px; margin: 20px auto 40px; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); }
        section.active { display: block; }
        h2 { border-left: 5px solid var(--primary-blue); padding-left: 15px; color: var(--primary-blue); margin-bottom: 20px; font-size: 20px; }
        .url-item { display: flex; justify-content: space-between; align-items: center; padding: 15px 0; border-bottom: 1px solid #f0f0f0; text-align: left; }
        .url-item a { color: var(--primary-blue); text-decoration: none; font-weight: bold; font-size: 16px; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; font-size: 16px; margin-top: 10px; transition: 0.2s; }
        .btn-main:disabled { background: #ccc !important; cursor: not-allowed; opacity: 0.7; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; background: #eee; margin-right: 8px; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        .clock-container { background: #e1f5fe; padding: 15px; border-radius: 10px; text-align: center; margin-bottom: 20px; }
    </style>
</head>
<body>

<header><h1>RailStream</h1></header>

<nav>
    <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
    <button onclick="showPage('keio')" id="nav-keio">京王</button>
    <button onclick="showPage('jr')" id="nav-jr">JR</button>
    <button onclick="showPage('settings')" id="nav-settings">同期・設定</button>
</nav>

<section id="all" class="active"><h2>すべての動画</h2><div id="list-all"></div></section>
<section id="keio"><h2 style="border-left-color: var(--keio-red); color: var(--keio-red);">京王線</h2><div id="list-keio"></div></section>
<section id="jr"><h2 style="border-left-color: var(--jr-green); color: var(--jr-green);">JR線</h2><div id="list-jr"></div></section>

<section id="settings">
    <h2>設定・同期</h2>
    <div class="clock-container">
        <div id="date" style="font-size:12px; color:#666;"></div>
        <div id="time" style="font-size:24px; font-weight:bold; color:var(--primary-blue);">00:00:00</div>
    </div>

    <div style="background:#fdfdfd; padding:20px; border-radius:10px; border:1px solid #eee;">
        <h3>動画を追加</h3>
        <select id="new-cat"><option value="keio">京王</option><option value="jr">JR</option></select>
        <input type="text" id="new-title" placeholder="タイトルを入力">
        <input type="text" id="new-url" placeholder="URL (https://...)">
        <button class="btn-main" onclick="addItem()">リストに追加</button>
    </div>

    <hr style="margin:30px 0; border:none; border-top:1px solid #eee;">
    <button id="sync-s" class="btn-main" style="background:#4caf50;" onclick="syncSave()" disabled>接続中...</button>
    <button id="sync-l" class="btn-main" style="background:#666;" onclick="syncLoad()" disabled>接続中...</button>
    <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">初期化</button>
</section>

<script>
    // 1. Firebase設定
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:f9a8a3bce73470b842ef9c"
    };

    let db = null;
    let myRailItems = JSON.parse(localStorage.getItem('railItems') || '[]');

    // 2. 起動処理：ライブラリが読み込まれるまで待機し、確実にボタンを有効化する
    function startApp() {
        if (typeof firebase !== 'undefined' && firebase.database) {
            if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
            db = firebase.database();
            
            // ボタンのロック解除
            const sBtn = document.getElementById('sync-s');
            const lBtn = document.getElementById('sync-l');
            if(sBtn) { sBtn.disabled = false; sBtn.innerText = "今のデータを保存 (送信)"; }
            if(lBtn) { lBtn.disabled = false; lBtn.innerText = "最新のデータを読込 (受信)"; }
            
            render();
            console.log("System Ready");
        } else {
            setTimeout(startApp, 1000); // 1秒ごとにチェック
        }
    }
    window.onload = startApp;

    // 3. 時計機能
    function updateClock() {
        const now = new Date();
        const d = document.getElementById('date'), t = document.getElementById('time');
        if(d) d.innerText = now.toLocaleDateString('ja-JP');
        if(t) t.innerText = now.toLocaleTimeString('ja-JP');
    }
    setInterval(updateClock, 1000); updateClock();

    // 4. 画面切り替え
    function showPage(id) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('nav-' + id).classList.add('active');
    }

    // 5. リスト表示
    function render() {
        ['all', 'keio', 'jr'].forEach(id => {
            const el = document.getElementById('list-' + id);
            if(el) el.innerHTML = "";
        });

        myRailItems.forEach((item, index) => {
            const html = `
                <div class="url-item">
                    <div>
                        <span class="tag">${item.cat.toUpperCase()}</span>
                        <a href="${item.url}" target="_blank">${item.title}</a>
                    </div>
                    <button onclick="delItem(${index})" style="color:#ff5252; border:none; background:none; cursor:pointer;">[削除]</button>
                </div>`;
            
            document.getElementById('list-all').innerHTML += html;
            const target = document.getElementById('list-' + item.cat);
            if(target) target.innerHTML += html;
        });
    }

    // 6. データ操作
    function addItem() {
        const title = document.getElementById('new-title').value, url = document.getElementById('new-url').value, cat = document.getElementById('new-cat').value;
        if(!title || !url) return alert("入力してください");
        myRailItems.push({title, url, cat});
        saveLocal();
        document.getElementById('new-title').value = ""; document.getElementById('new-url').value = "";
        alert("リストに追加しました（送信ボタンで同期してください）");
    }

    function delItem(i) { if(confirm("削除しますか？")) { myRailItems.splice(i, 1); saveLocal(); } }
    function saveLocal() { localStorage.setItem('railItems', JSON.stringify(myRailItems)); render(); }
    function clearAll() { if(confirm("初期化しますか？")) { myRailItems = []; saveLocal(); } }

    // 7. 同期機能
    function syncSave() {
        if(!db) return;
        db.ref('rail_sync').set(myRailItems)
            .then(() => alert("クラウドに送信（保存）しました！"))
            .catch(e => alert("送信エラー: " + e.message));
    }

    function syncLoad() {
        if(!db) return;
        db.ref('rail_sync').once('value').then(snap => {
            const data = snap.val();
            if(data) {
                myRailItems = data;
                saveLocal();
                alert("クラウドから受信（読込）しました！");
            } else {
                alert("クラウドにデータがありません");
            }
        }).catch(e => alert("受信エラー: " + e.message));
    }
</script>
</body>
</html>
