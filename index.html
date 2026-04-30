<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>tetsudo-site</title>
    
    <!-- PWA（本物のアプリにするための設定） -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="apple-touch-icon" href="https://icons8.com">
    <link rel="icon" href="https://icons8.com">
    <!-- 設計図 manifest.json の読み込み -->
    <link rel="manifest" href="manifest.json">

    <style>
        :root { 
            --primary-blue: #0078d4; 
            --bg-gray: #f9f9f9; 
            --keio-color: #e60012; 
            --jr-color: #008000;   
            --others-color: #ff8c00; 
        }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); text-align: center; -webkit-user-select: none; user-select: none; }
        
        /* ヘッダー：青色 */
        header { background: linear-gradient(135deg, #0078d4, #005a9e); color: white; padding: 25px 10px; }
        
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 5px; color: white; }
        .tag.KEIO { background: var(--keio-color); }
        .tag.JR { background: var(--jr-color); }
        .tag.OTHERS { background: var(--others-color); }
        
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; justify-content: center; overflow-x: auto; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 15px; cursor: pointer; border-radius: 20px; white-space: nowrap; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }
        
        section { display: none; max-width: 500px; margin: 20px auto; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); text-align: left; }
        section.active { display: block; }
        
        .url-item { padding: 12px 0; border-bottom: 1px solid #f0f0f0; }
        .url-info { display: flex; justify-content: space-between; align-items: flex-start; }
        .url-info b { -webkit-user-select: text; user-select: text; }
        .url-desc { font-size: 12px; color: #666; margin-top: 4px; padding-left: 8px; border-left: 3px solid #eee; }
        
        #admin-controls { display: none; margin-top: 20px; padding-top: 20px; border-top: 2px dashed #ddd; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; margin-top: 10px; }
        input, select, textarea { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        
        a { text-decoration: none; color: #333; }
        a:hover { color: var(--primary-blue); }
    </style>
</head>
<body>
    <header><h1>tetsudo-site</h1></header>
    
    <nav>
        <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
        <button onclick="showPage('keio')" id="nav-keio">京王</button>
        <button onclick="showPage('jr')" id="nav-jr">JR線</button>
        <button onclick="showPage('others')" id="nav-others">その他</button>
        <button onclick="showPage('settings')" id="nav-settings">同期・管理</button>
    </nav>

    <section id="all" class="active"><h2>すべての動画</h2><div id="list-all"></div></section>
    <section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>
    <section id="others"><h2>その他</h2><div id="list-others"></div></section>

    <section id="settings">
        <h2>同期と管理</h2>
        <div style="background:#f0f7ff; padding:15px; border-radius:10px; margin-bottom:20px;">
            <p style="font-size:13px; margin:0 0 10px 0; color:#0056b3;">最新のリストに更新：</p>
            <button class="btn-main" style="background:#4caf50; margin:0;" onclick="syncLoad()">クラウドから読込 (受信)</button>
        </div>
        <hr>
        <div id="password-area">
            <h3>管理者メニュー</h3>
            <p style="font-size:13px; color:#666;">パスワード(0829)を入力してロック解除：</p>
            <input type="password" id="admin-pw" placeholder="パスワードを入力">
            <button class="btn-main" onclick="unlockAdmin()">ロック解除</button>
        </div>
        <div id="admin-controls">
            <h3 style="color:var(--primary-blue);">URLを追加</h3>
            <select id="new-cat">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="others">その他</option>
            </select>
            <input type="text" id="new-title" placeholder="動画タイトル">
            <input type="text" id="new-url" placeholder="https://...">
            <textarea id="new-desc" placeholder="動画の説明（任意）" rows="2"></textarea>
            <button class="btn-main" onclick="addItem()">リストに追加</button>
            <hr style="margin:25px 0; border:none; border-top:1px solid #eee;">
            <h3>クラウド保存</h3>
            <button class="btn-main" id="btn-save" onclick="syncSave()">現在の状態を保存 (送信)</button>
            <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">全データ削除</button>
        </div>
    </section>

<script>
    // Service Workerの登録
    if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('sw.js').then(() => {
            console.log("Service Worker Registered");
        });
    }

    const SECRET_PASSWORD = "0829"; 
    const API_URL = "https://script.google.com/macros/s/AKfycbwrCAWMH7NvrVcAErytIutPyt-AM4v5vvDtM_wD3aCZhwY6iNslqzhKI4qccqNoYjle/exec";
    
    let railData = JSON.parse(localStorage.getItem('railData') || '[]');
    let isAdmin = false;

    function unlockAdmin() {
        if(document.getElementById('admin-pw').value === SECRET_PASSWORD) {
            isAdmin = true;
            document.getElementById('password-area').style.display = 'none';
            document.getElementById('admin-controls').style.display = 'block';
            render();
        } else { alert("パスワードが違います"); }
    }

    function addItem() {
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        const desc = document.getElementById('new-desc').value;
        if(!title || !url) return alert("タイトルとURLを入力してください");
        railData.push({title, url, cat, desc});
        saveLocal(); render();
        document.getElementById('new-title').value = "";
        document.getElementById('new-url').value = "";
        document.getElementById('new-desc').value = "";
    }

    function render() {
        const ids = ['all', 'keio', 'jr', 'others'];
        ids.forEach(id => document.getElementById('list-' + id).innerHTML = "");
        railData.forEach((item, index) => {
            const deleteBtn = isAdmin ? `<button onclick="deleteItem(${index})" style="color:red; border:none; background:none; cursor:pointer; font-size:12px;">[削除]</button>` : '';
            let catLabel = item.cat === 'keio' ? '京王' : (item.cat === 'others' ? 'その他' : 'JR');
            const html = `<div class="url-item"><div class="url-info"><div><span class="tag ${item.cat.toUpperCase()}">${catLabel}</span><b><a href="${item.url}" target="_blank">${item.title}</a></b></div>${deleteBtn}</div>${item.desc ? `<div class="url-desc">${item.desc}</div>` : ''}</div>`;
            document.getElementById('list-all').innerHTML += html;
            if(document.getElementById('list-' + item.cat)) document.getElementById('list-' + item.cat).innerHTML += html;
        });
    }

    function deleteItem(index) {
        if(!confirm("削除しますか？")) return;
        railData.splice(index, 1);
        saveLocal(); render();
    }

    function saveLocal() { localStorage.setItem('railData', JSON.stringify(railData)); }

    async function syncSave() {
        const btn = document.getElementById('btn-save');
        btn.innerText = "送信中..."; btn.disabled = true;
        try {
            await fetch(API_URL, { method: "POST", body: JSON.stringify({ action: "save", data: railData }) });
            alert("クラウドに保存しました！");
        } catch (e) { alert("保存に失敗しました。"); }
        finally { btn.innerText = "現在の状態を保存 (送信)"; btn.disabled = false; }
    }

    async function syncLoad() {
        const btn = event.target;
        const oldText = btn.innerText;
        btn.innerText = "受信中...";
        try {
            const res = await fetch(API_URL);
            const json = await res.json();
            if (json && Array.isArray(json.data)) {
                railData = json.data; saveLocal(); render(); alert("読み込み完了！");
            }
        } catch (e) { alert("読込失敗"); }
        finally { btn.innerText = oldText; }
    }

    function clearAll() {
        if(confirm("全消去しますか？")) { railData = []; saveLocal(); render(); }
    }

    function showPage(id) {
        document.querySelectorAll('section, nav button').forEach(el => el.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('nav-' + id).classList.add('active');
    }

    window.onload = render;
</script>
</body>
</html>

