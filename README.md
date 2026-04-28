<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - GAS Sync Edition</title>
    <style>
        :root { --primary-blue: #0078d4; --bg-gray: #f9f9f9; --accent-green: #4caf50; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); text-align: center; }
        header { background: linear-gradient(135deg, #0078d4, #00b0ff); color: white; padding: 25px 10px; }
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; justify-content: center; overflow-x: auto; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 15px; cursor: pointer; border-radius: 20px; white-space: nowrap; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }
        section { display: none; max-width: 500px; margin: 20px auto; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); text-align: left; }
        section.active { display: block; }
        .url-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px solid #f0f0f0; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; margin-top: 10px; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        .clock { background: #e1f5fe; padding: 15px; border-radius: 10px; text-align: center; margin-bottom: 20px; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 5px; color: white; background: #666; }
    </style>
</head>
<body>
    <header><h1>RailStream</h1></header>
    
    <nav>
        <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
        <button onclick="showPage('keio')" id="nav-keio">京王</button>
        <button onclick="showPage('jr')" id="nav-jr">JR</button>
        <button onclick="showPage('others')" id="nav-others">その他</button>
        <button onclick="showPage('settings')" id="nav-settings">同期・設定</button>
    </nav>

    <section id="all" class="active"><h2>すべての動画</h2><div id="list-all"></div></section>
    <section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>
    <section id="others"><h2>その他</h2><div id="list-others"></div></section>

    <section id="settings">
        <h2>データ追加・同期</h2>
        <div class="clock"><div id="date"></div><div id="time" style="font-size:24px; font-weight:bold;">00:00:00</div></div>
        
        <select id="new-cat">
            <option value="keio">京王</option>
            <option value="jr">JR</option>
            <option value="others">その他</option>
        </select>
        <input type="text" id="new-title" placeholder="タイトルを入力">
        <input type="text" id="new-url" placeholder="URLを貼り付け">
        <button class="btn-main" onclick="addItem()">リストに追加</button>
        
        <hr style="margin:25px 0; border:none; border-top:1px solid #eee;">
        
        <button class="btn-main" onclick="syncSave()">クラウドに保存 (送信)</button>
        <button class="btn-main" style="background:var(--accent-green);" onclick="syncLoad()">クラウドから読込 (受信)</button>
        <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">全データ削除</button>
    </section>

<script>
    // あなたのGAS URLを使用
    const API_URL = "https://google.com";
    
    // ローカルストレージから読み込み
    let railData = JSON.parse(localStorage.getItem('railData') || '[]');

    function addItem() {
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        if(!title || !url) return alert("タイトルとURLを入力してください");

        railData.push({title, url, cat});
        saveLocal();
        render();
        
        document.getElementById('new-title').value = "";
        document.getElementById('new-url').value = "";
        alert("追加しました。「送信」ボタンで同期してください。");
    }

    function render() {
        const ids = ['all', 'keio', 'jr', 'others'];
        ids.forEach(id => document.getElementById('list-' + id).innerHTML = "");

        railData.forEach((item, index) => {
            const html = `
                <div class="url-item">
                    <div>
                        <span class="tag">${item.cat.toUpperCase()}</span>
                        <b><a href="${item.url}" target="_blank" style="text-decoration:none; color:#333;">${item.title}</a></b>
                    </div>
                    <button onclick="deleteItem(${index})" style="color:red; border:none; background:none; cursor:pointer;">削除</button>
                </div>`;
            document.getElementById('list-all').innerHTML += html;
            if(document.getElementById('list-' + item.cat)) {
                document.getElementById('list-' + item.cat).innerHTML += html;
            }
        });
    }

    function deleteItem(index) {
        if(!confirm("削除しますか？")) return;
        railData.splice(index, 1);
        saveLocal();
        render();
    }

    function saveLocal() {
        localStorage.setItem('railData', JSON.stringify(railData));
    }

    // --- 同期機能 (GAS連携) ---
    async function syncSave() {
        const btn = event.target;
        btn.innerText = "送信中...";
        btn.disabled = true;

        try {
            await fetch(API_URL, {
                method: "POST",
                body: JSON.stringify({ action: "save", data: railData })
            });
            alert("クラウドに保存しました！");
        } catch (e) {
            alert("保存に失敗しました。GASの設定を確認してください。");
        } finally {
            btn.innerText = "クラウドに保存 (送信)";
            btn.disabled = false;
        }
    }

    async function syncLoad() {
        const btn = event.target;
        btn.innerText = "受信中...";
        btn.disabled = true;

        try {
            const res = await fetch(API_URL);
            const json = await res.json();
            if (json && Array.isArray(json.data)) {
                railData = json.data;
                saveLocal();
                render();
                alert("最新データを読み込みました！");
            }
        } catch (e) {
            alert("読込に失敗しました。");
        } finally {
            btn.innerText = "クラウドから読込 (受信)";
            btn.disabled = false;
        }
    }

    function clearAll() {
        if(confirm("全データを削除しますか？")) {
            railData = [];
            saveLocal();
            render();
        }
    }

    function showPage(id) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('nav-' + id).classList.add('active');
    }

    function init() {
        render();
        setInterval(() => {
            const n = new Date();
            document.getElementById('date').innerText = n.toLocaleDateString();
            document.getElementById('time').innerText = n.toLocaleTimeString();
        }, 1000);
    }
    window.onload = init;
</script>
</body>
</html>
