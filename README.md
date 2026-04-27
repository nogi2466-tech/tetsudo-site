<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - 鉄道動画集</title>
    <!-- Firebaseライブラリ -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>

    <style>
        :root { --primary-blue: #0078d4; --bg-gray: #f9f9f9; --accent-red: #ff5252; --keio-red: #e60012; --jr-green: #008000; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); color: #333; }
        
        header { background: linear-gradient(135deg, #0078d4, #00b0ff); color: white; padding: 25px 10px; text-align: center; }
        h1 { margin: 0; font-size: 24px; letter-spacing: 2px; }

        /* 5つのナビゲーションボタン */
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); text-align: center; overflow-x: auto; white-space: nowrap; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 12px; cursor: pointer; color: #555; border-radius: 20px; transition: 0.3s; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }

        section { display: none; max-width: 500px; margin: 20px auto 40px; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); }
        section.active { display: block; }
        
        h2 { border-left: 5px solid var(--primary-blue); padding-left: 15px; color: var(--primary-blue); margin-bottom: 25px; font-size: 20px; }

        /* URLリスト */
        .url-item { display: flex; justify-content: space-between; align-items: center; padding: 15px 0; border-bottom: 1px solid #f0f0f0; }
        .url-item a { color: var(--primary-blue); text-decoration: none; font-weight: bold; font-size: 16px; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; background: #eee; margin-right: 8px; vertical-align: middle; }
        .del-text { color: var(--accent-red); font-size: 12px; cursor: pointer; border: none; background: none; }

        /* 入力フォーム */
        .input-area { background: #fdfdfd; padding: 20px; border-radius: 10px; border: 1px solid #eee; margin-top: 20px; }
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

    <!-- すべて表示タブ -->
    <section id="all" class="active">
        <h2>すべての動画</h2>
        <div id="list-all"></div>
    </section>

    <!-- 京王タブ -->
    <section id="keio">
        <h2 style="border-left-color: var(--keio-red); color: var(--keio-red);">京王線</h2>
        <div id="list-keio"></div>
    </section>

    <!-- JRタブ -->
    <section id="jr">
        <h2 style="border-left-color: var(--jr-green); color: var(--jr-green);">JR線</h2>
        <div id="list-jr"></div>
    </section>

    <!-- その他タブ -->
    <section id="other">
        <h2 style="border-left-color: #333; color: #333;">その他</h2>
        <div id="list-other"></div>
    </section>

    <!-- 設定タブ -->
    <section id="settings">
        <h2>設定・同期</h2>
        <div class="clock-container">
            <div id="date" style="font-size:12px;"></div>
            <div id="time" style="font-size:24px; font-weight:bold; color:var(--primary-blue);">00:00:00</div>
        </div>

        <div class="input-area">
            <h3>動画を追加</h3>
            <select id="new-cat">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="other">その他</option>
            </select>
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
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        let myRailItems = JSON.parse(localStorage.getItem('railItems') || '[]');

        function showPage(id) {
            document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            document.getElementById('nav-' + id).classList.add('active');
        }

        function updateClock() {
            const now = new Date();
            document.getElementById('date').innerText = now.toLocaleDateString('ja-JP');
            document.getElementById('time').innerText = now.toLocaleTimeString('ja-JP');
        }
        setInterval(updateClock, 1000); updateClock();

        function render() {
            const lists = { all: 'list-all', keio: 'list-keio', jr: 'list-jr', other: 'list-other' };
            Object.values(lists).forEach(id => document.getElementById(id).innerHTML = "");

            myRailItems.forEach((item, index) => {
                const html = `
                    <div class="url-item">
                        <div>
                            <span class="tag">${item.cat.toUpperCase()}</span>
                            <a href="${item.url}" target="_blank">${item.title}</a>
                        </div>
                        <button class="del-text" onclick="delItem(${index})">[削除]</button>
                    </div>`;
                
                document.getElementById('list-all').innerHTML += html;
                if (lists[item.cat]) document.getElementById(lists[item.cat]).innerHTML += html;
            });
        }

        function addItem() {
            const title = document.getElementById('new-title').value;
            const url = document.getElementById('new-url').value;
            const cat = document.getElementById('new-cat').value;
            if(!title || !url) return alert("入力してください");
            myRailItems.push({title, url, cat});
            saveLocal();
            document.getElementById('new-title').value = "";
            document.getElementById('new-url').value = "";
        }

        function delItem(i) {
            if(confirm("削除しますか？")) { myRailItems.splice(i, 1); saveLocal(); }
        }

        function saveLocal() {
            localStorage.setItem('railItems', JSON.stringify(myRailItems));
            render();
        }

        function syncSave() {
            db.ref('rail_sync').set(myRailItems).then(() => alert("クラウドに送信しました"));
        }

        function syncLoad() {
            db.ref('rail_sync').once('value').then(snap => {
                const data = snap.val();
                if(data) { myRailItems = data; saveLocal(); alert("クラウドから読み込みました"); }
            });
        }

        function clearAll() {
            if(confirm("すべて消去しますか？")) { myRailItems = []; saveLocal(); }
        }

        window.onload = render;
    </script>
</body>
</html>
