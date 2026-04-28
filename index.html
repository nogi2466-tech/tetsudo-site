<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - 自動同期</title>
    <!-- ★FirebaseライブラリのURLを修正しました -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        :root { --primary-blue: #0078d4; --bg-gray: #f9f9f9; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); text-align: center; }
        header { background: linear-gradient(135deg, #0078d4, #00b0ff); color: white; padding: 25px 10px; }
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 15px; cursor: pointer; border-radius: 20px; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }
        section { display: none; max-width: 500px; margin: 20px auto; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); text-align: left; }
        section.active { display: block; }
        .url-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px solid #f0f0f0; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; margin-top: 10px; }
        .btn-main:disabled { background: #ccc; cursor: not-allowed; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        .clock { background: #e1f5fe; padding: 15px; border-radius: 10px; text-align: center; margin-bottom: 20px; }
    </style>
</head>
<body>
    <header><h1>RailStream</h1></header>
    <nav>
        <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
        <button onclick="showPage('keio')" id="nav-keio">京王</button>
        <button onclick="showPage('jr')" id="nav-jr">JR</button>
        <button onclick="showPage('others')" id="nav-others">その他</button>
        <button onclick="showPage('settings')" id="nav-settings">設定</button>
    </nav>

    <section id="all" class="active"><h2>すべての動画</h2><div id="list-all"></div></section>
    <section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>
    <section id="others"><h2>その他</h2><div id="list-others"></div></section>

    <section id="settings">
        <h2>自動同期・追加</h2>
        <div class="clock"><div id="date"></div><div id="time" style="font-size:24px; font-weight:bold;">00:00:00</div></div>
        <select id="new-cat"><option value="keio">京王</option><option value="jr">JR</option><option value="others">その他</option></select>
        <input type="text" id="new-title" placeholder="タイトルを入力">
        <input type="text" id="new-url" placeholder="URLを貼り付け">
        <button id="add-btn" class="btn-main" onclick="autoAdd()" disabled>接続中...</button>
        <button class="btn-main" style="background:#999; margin-top:20px;" onclick="autoClear()">全削除</button>
    </section>

<script>
    // ★Firebaseの設定値をあなたのプロジェクト専用に修正しました
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "tetsudo.firebaseapp.com",
        databaseURL: "https://tetsudo-default-rtdb.firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:f9a8a3bce73470b842ef9c",
        measurementId: "G-MEZNHLIE80"
    };

    let db = null;

    function init() {
        if (typeof firebase !== 'undefined' && firebase.database) {
            if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
            db = firebase.database();
            
            db.ref('rail_auto_sync').on('value', (snap) => {
                const val = snap.val();
                let data = [];
                // データベースが「0」や空の場合の対策
                if (Array.isArray(val)) data = val;
                else if (val && typeof val === 'object') data = Object.values(val);
                
                render(data);
                const btn = document.getElementById('add-btn');
                if(btn) {
                    btn.disabled = false;
                    btn.innerText = "データを追加して同期";
                }
            }, (err) => {
                console.error("同期エラー:", err);
            });
            console.log("Firebase Ready");
        } else {
            setTimeout(init, 500);
        }
    }
    window.onload = init;

    function autoAdd() {
        if (!db) return;
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        if(!title || !url) return alert("入力してください");

        db.ref('rail_auto_sync').once('value').then((snap) => {
            const val = snap.val();
            let list = [];
            if (Array.isArray(val)) list = val;
            else if (val && typeof val === 'object') list = Object.values(val);
            
            list.push({title, url, cat});
            db.ref('rail_auto_sync').set(list).then(() => {
                document.getElementById('new-title').value = "";
                document.getElementById('new-url').value = "";
            });
        });
    }

    function render(data) {
        ['all', 'keio', 'jr', 'others'].forEach(id => {
            const el = document.getElementById('list-' + id);
            if(el) el.innerHTML = "";
        });
        data.forEach((item, index) => {
            // itemが文字列（初期値0など）の場合はスキップ
            if(!item || typeof item !== 'object' || !item.title) return;
            const html = `
                <div class="url-item">
                    <div><b>[${item.cat ? item.cat.toUpperCase() : '???'}] ${item.title}</b></div>
                    <button onclick="autoDelete(${index})" style="color:red; border:none; background:none; cursor:pointer;">削除</button>
                </div>`;
            document.getElementById('list-all').innerHTML += html;
            const target = document.getElementById('list-' + item.cat);
            if(target) target.innerHTML += html;
        });
    }

    function autoDelete(i) {
        if(!confirm("削除しますか？")) return;
        db.ref('rail_auto_sync').once('value').then(s => {
            const val = s.val();
            let l = [];
            if (Array.isArray(val)) l = val;
            else if (val && typeof val === 'object') l = Object.values(val);
            
            l.splice(i, 1); 
            db.ref('rail_auto_sync').set(l);
        });
    }

    function autoClear() { if(confirm("全消去しますか？")) db.ref('rail_auto_sync').set([]); }
    
    function showPage(id) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        const target = document.getElementById(id);
        const nav = document.getElementById('nav-' + id);
        if(target) target.classList.add('active');
        if(nav) nav.classList.add('active');
    }

    setInterval(() => {
        const n = new Date();
        const d = document.getElementById('date');
        const t = document.getElementById('time');
        if(d) d.innerText = n.toLocaleDateString();
        if(t) t.innerText = n.toLocaleTimeString();
    }, 1000);
</script>
</body>
</html>
