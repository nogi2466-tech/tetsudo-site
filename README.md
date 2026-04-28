<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - 自動同期</title>
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
        <button id="add-btn" class="btn-main" disabled>接続中...</button>
        <button id="clear-btn" class="btn-main" style="background:#999; margin-top:20px;">全削除</button>
    </section>

<script type="module">
    import { initializeApp } from "https://gstatic.com";
    import { getDatabase, ref, onValue, set, get } from "https://gstatic.com";

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

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const dbRef = ref(db, 'rail_auto_sync');

    onValue(dbRef, (snapshot) => {
        const val = snapshot.val();
        // データベースが「0」や空の場合でも配列として扱う
        const data = (Array.isArray(val)) ? val : [];
        render(data);
        document.getElementById('add-btn').disabled = false;
        document.getElementById('add-btn').innerText = "データを追加して同期";
    });

    window.autoAdd = async () => {
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        if(!title || !url) return alert("入力してください");

        const snapshot = await get(dbRef);
        const val = snapshot.val();
        const list = (Array.isArray(val)) ? val : [];
        list.push({ title, url, cat });
        await set(dbRef, list);
        
        document.getElementById('new-title').value = "";
        document.getElementById('new-url').value = "";
    };

    window.autoDelete = async (index) => {
        if(!confirm("削除しますか？")) return;
        const snapshot = await get(dbRef);
        const list = snapshot.val() || [];
        list.splice(index, 1);
        await set(dbRef, list);
    };

    document.getElementById('add-btn').onclick = window.autoAdd;
    document.getElementById('clear-btn').onclick = () => { if(confirm("全消去？")) set(dbRef, []); };

    function render(data) {
        ['all', 'keio', 'jr', 'others'].forEach(id => document.getElementById('list-' + id).innerHTML = "");
        data.forEach((item, index) => {
            if(!item.title) return; // 不正データ飛ばし
            const html = `
                <div class="url-item">
                    <div><b>[${item.cat.toUpperCase()}] ${item.title}</b></div>
                    <button onclick="autoDelete(${index})" style="color:red; border:none; background:none; cursor:pointer;">削除</button>
                </div>`;
            document.getElementById('list-all').innerHTML += html;
            if(document.getElementById('list-' + item.cat)) document.getElementById('list-' + item.cat).innerHTML += html;
        });
    }

    window.showPage = (id) => {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('nav-' + id).classList.add('active');
    };

    setInterval(() => {
        const n = new Date();
        document.getElementById('date').innerText = n.toLocaleDateString();
        document.getElementById('time').innerText = n.toLocaleTimeString();
    }, 1000);
</script>
</body>
</html>
