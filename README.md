<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream</title>

    <!-- Firebaseライブラリ (最優先で読み込み) -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>

    <style>
        :root { --primary-blue: #0078d4; --bg-gray: #f9f9f9; --accent-red: #ff5252; --keio-red: #e60012; --jr-green: #008000; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); color: #333; }
        
        header { background: linear-gradient(135deg, #0078d4, #00b0ff); color: white; padding: 25px 10px; text-align: center; }
        h1 { margin: 0; font-size: 24px; letter-spacing: 2px; }

        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); text-align: center; overflow-x: auto; white-space: nowrap; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 12px; cursor: pointer; color: #555; border-radius: 20px; transition: 0.3s; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }

        section { display: none; max-width: 500px; margin: 20px auto 40px; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); }
        section.active { display: block; }
        
        h2 { border-left: 5px solid var(--primary-blue); padding-left: 15px; color: var(--primary-blue); margin-bottom: 25px; font-size: 20px; }

        .url-item { display: flex; justify-content: space-between; align-items: center; padding: 15px 0; border-bottom: 1px solid #f0f0f0; text-align: left; }
        .url-item a { color: var(--primary-blue); text-decoration: none; font-weight: bold; font-size: 16px; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; background: #eee; margin-right: 8px; }
        .del-text { color: var(--accent-red); font-size: 12px; cursor: pointer; border: none; background: none; }

        .input-area { background: #fdfdfd; padding: 20px; border-radius: 10px; border: 1px solid #eee; margin-top: 20px; text-align: left; }
        input, select { width: 100%; padding: 12px; margin-bottom: 10px; border: 1px solid #ddd; border-radius: 5px; box-sizing: border-box; font-size: 14px; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 12px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; font-size: 16px; }

        .clock-container { background: #e1f5fe; padding: 15px; border-radius: 10px; text-align: center; margin-bottom: 20px; }
    </style>
</head>
<body>

    <header><h1>RailStream</h1></header>

    <nav>
        <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
        <button onclick="showPage('keio')" id="nav-keio">京王</button>
        <button onclick="showPage('jr')" id="nav-jr">JR</button>
        <button onclick="showPage('other')" id="nav-other">その他</button>
        <button onclick="showPage('settings')" id="nav-settings">設定</button>
    </nav>

    <section id="all" class="active"><h2>すべての動画</h2><div id="list-all"></div></section>
    <section id="keio"><h2 style="border-left-color: var(--keio-red); color: var(--keio-red);">京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2 style="border-left-color: var(--jr-green); color: var(--jr-green);">JR線</h2><div id="list-jr"></div></section>
    <section id="other"><h2>その他</h2><div id="list-other"></div></section>

    <section id="settings">
        <h2>設定・同期</h2>
        <div class="clock-container">
            <div id="date" style="font-size:12px; color:#666;"></div>
            <div id="time" style="font-size:24px; font-weight:bold; color:var(--primary-blue);">00:00:00</div>
        </div>
        <div class="input-area">
            <h3>動画を追加</h3>
            <select id="new-cat"><option value="keio">京王</option><option value="jr">JR</option><option value="other">その他</option></select>
            <input type="text" id="new-title" placeholder="動画のタイトル">
            <input type="text" id="new-url" placeholder="https://...">
            <button class="btn-main" onclick="addItem()">リストに追加</button>
        </div>
        <hr style="margin:30px 0; border:none; border-top:1px solid #eee;">
        <button class="btn-main" style="background:#4caf50;" onclick="syncSave()">今のデータを保存 (送信)</button>
        <button class="btn-main" style="background:#666; margin-top:10px;" onclick="syncLoad()">最新のデータを読込 (受信)</button>
        <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">初期化</button>
    </section>

<script>
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

    // Firebase接続を確実にするためのループ
    function connectFirebase() {
        if (typeof firebase !== 'undefined') {
            if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
            db = firebase.database();
            console.log("Firebase Ready");
            render(); 
        } else {
            setTimeout(connectFirebase, 1000);
        }
    }
    connectFirebase();

    function updateClock() {
        const now = new Date();
        const d = document.getElementById('date'), t = document.getElementById('time');
        if(d) d.innerText = now.toLocaleDateString('ja-JP');
        if(t) t.innerText = now.toLocaleTimeString('ja-JP');
    }
    setInterval(updateClock, 1000); updateClock();

    function showPage(id) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('nav-' + id).classList.add('active');
    }

    function render() {
        const lists = { all: 'list-all', keio: 'list-keio', jr: 'list-jr', other: 'list-other' };
        Object.values(lists).forEach(id => { if(document.getElementById(id)) document.getElementById(id).innerHTML = ""; });

        myRailItems.forEach((item, index) => {
            const html = `<div class="url-item"><div><span class="tag">${item.cat.toUpperCase()}</span><a href="${item.url}" target="_blank">${item.title}</a></div><button class="del-text" onclick="delItem(${index})">[削除]</button></div>`;
            if(document.getElementById('list-all')) document.getElementById('list-all').innerHTML += html;
            if(lists[item.cat] && document.getElementById(lists[item.cat])) document.getElementById(lists[item.cat]).innerHTML += html;
        });
    }

    function addItem() {
        const title = document.getElementById('new-title').value, url = document.getElementById('new-url').value, cat = document.getElementById('new-cat').value;
        if(!title || !url) return alert("入力してください");
        myRailItems.push({title, url, cat});
        saveLocal();
        document.getElementById('new-title').value = ""; document.getElementById('new-url').value = "";
    }

    function delItem(i) { if(confirm("削除しますか？")) { myRailItems.splice(i, 1); saveLocal(); } }
    function saveLocal() { localStorage.setItem('railItems', JSON.stringify(myRailItems)); render(); }

    function syncSave() {
        if(!db) return alert("接続中です。数秒待ってから押し直してください。");
        db.ref('rail_sync').set(myRailItems).then(() => alert("クラウドに送信しました"));
    }

    function syncLoad() {
        if(!db) return alert("接続中です。");
        db.ref('rail_sync').once('value').then(snap => {
            const data = snap.val();
            if(data) { myRailItems = data; saveLocal(); alert("クラウドから読み込みました"); }
        });
    }

    function clearAll() { if(confirm("すべて消去しますか？")) { myRailItems = []; saveLocal(); } }
</script>
</body>
</html>
