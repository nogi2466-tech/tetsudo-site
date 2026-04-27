<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - 自動同期</title>
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
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; font-size: 16px; margin-top: 10px; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; background: #eee; margin-right: 8px; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        .clock-container { background: #e1f5fe; padding: 15px; border-radius: 10px; text-align: center; margin-bottom: 20px; }
        .status-badge { font-size: 11px; color: #4caf50; font-weight: bold; }
    </style>
</head>
<body>

<header><h1>RailStream</h1></header>

<nav>
    <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
    <button onclick="showPage('keio')" id="nav-keio">京王</button>
    <button onclick="showPage('jr')" id="nav-jr">JR</button>
    <button onclick="showPage('settings')" id="nav-settings">追加・設定</button>
</nav>

<section id="all" class="active"><h2>すべての動画 <span id="status-all" class="status-badge"></span></h2><div id="list-all"></div></section>
<section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
<section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>

<section id="settings">
    <h2>動画を追加 (自動同期)</h2>
    <div class="clock-container">
        <div id="date" style="font-size:12px; color:#666;"></div>
        <div id="time" style="font-size:24px; font-weight:bold; color:var(--primary-blue);">00:00:00</div>
    </div>

    <div style="background:#fdfdfd; padding:20px; border-radius:10px; border:1px solid #eee;">
        <select id="new-cat"><option value="keio">京王</option><option value="jr">JR</option></select>
        <input type="text" id="new-title" placeholder="タイトルを入力">
        <input type="text" id="new-url" placeholder="URL (https://...)">
        <button id="add-btn" class="btn-main" onclick="autoAdd()" disabled>接続中...</button>
    </div>

    <hr style="margin:30px 0; border:none; border-top:1px solid #eee;">
    <p style="font-size:12px; color:#999; text-align:center;">※このサイトでは、追加した内容は自動で全デバイスに同期されます。</p>
    <button class="btn-main" style="background:#999; margin-top:20px;" onclick="autoClear()">全データを初期化</button>
</section>

<script>
       const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "://firebaseapp.com",
        // ★ここを画像にあるURLと完全に一致させました
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:f9a8a3bce73470b842ef9c"
    };


    let db = null;

    // --- 自動同期の仕組み ---
    function startAutoSync() {
        if (typeof firebase !== 'undefined' && firebase.database) {
            if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
            db = firebase.database();

            // クラウドのデータに変更があったら、自動で画面を書き換える命令
            db.ref('auto_rail_list').on('value', (snapshot) => {
                const data = snapshot.val() || [];
                render(data);
                console.log("Auto-synced with Cloud");
                
                // ボタンを有効化
                const addBtn = document.getElementById('add-btn');
                if(addBtn) { addBtn.disabled = false; addBtn.innerText = "データを追加して同期"; }
                document.getElementById('status-all').innerText = "● 同期済み";
            });
        } else {
            setTimeout(startAutoSync, 1000);
        }
    }
    window.onload = startAutoSync;

    // 動画を追加（追加した瞬間にクラウドへ送信）
    function autoAdd() {
        const title = document.getElementById('new-title').value, url = document.getElementById('new-url').value, cat = document.getElementById('new-cat').value;
        if(!title || !url) return alert("入力してください");

        // 今のデータを一旦取得して新しいのを混ぜる
        db.ref('auto_rail_list').once('value').then((snap) => {
            const list = snap.val() || [];
            list.push({title, url, cat});
            // クラウドへ上書き（これで全デバイスが自動更新される）
            db.ref('auto_rail_list').set(list).then(() => {
                document.getElementById('new-title').value = "";
                document.getElementById('new-url').value = "";
                alert("追加しました！他のデバイスも自動更新されます。");
            });
        });
    }

    // 動画を削除（削除した瞬間にクラウドへ反映）
    function autoDelete(index) {
        if(!confirm("削除しますか？")) return;
        db.ref('auto_rail_list').once('value').then((snap) => {
            const list = snap.val() || [];
            list.splice(index, 1);
            db.ref('auto_rail_list').set(list);
        });
    }

    function autoClear() {
        if(confirm("全データを削除しますか？")) db.ref('auto_rail_list').set([]);
    }

    // --- 画面表示 ---
    function render(data) {
        ['all', 'keio', 'jr'].forEach(id => {
            const el = document.getElementById('list-' + id);
            if(el) el.innerHTML = "";
        });

        data.forEach((item, index) => {
            const html = `
                <div class="url-item">
                    <div>
                        <span class="tag">${item.cat.toUpperCase()}</span>
                        <a href="${item.url}" target="_blank">${item.title}</a>
                    </div>
                    <button onclick="autoDelete(${index})" style="color:#ff5252; border:none; background:none; cursor:pointer;">[削除]</button>
                </div>`;
            
            document.getElementById('list-all').innerHTML += html;
            const target = document.getElementById('list-' + item.cat);
            if(target) target.innerHTML += html;
        });
    }

    function showPage(id) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('nav-' + id).classList.add('active');
    }

    function updateClock() {
        const now = new Date();
        const d = document.getElementById('date'), t = document.getElementById('time');
        if(d) d.innerText = now.toLocaleDateString('ja-JP');
        if(t) t.innerText = now.toLocaleTimeString('ja-JP');
    }
    setInterval(updateClock, 1000); updateClock();
</script>
</body>
</html>
