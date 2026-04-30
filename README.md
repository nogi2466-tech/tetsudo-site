<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <link rel="manifest" href="manifest.json">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="theme-color" content="#0078d4">
    <title>tetsudo-site</title>
    
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="apple-touch-icon" href="https://icons8.com">
    <link rel="icon" href="https://icons8.com">

    <style>
        :root { 
            --primary-blue: #0078d4; 
            --bg-gray: #f9f9f9; 
            --keio-color: #e60012; 
            --jr-color: #008000; 
            --files-color: #607d8b; 
            --others-color: #ff8c00; 
        }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); text-align: center; -webkit-user-select: none; user-select: none; }
        header { background: linear-gradient(135deg, #0078d4, #005a9e); color: white; padding: 25px 10px; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 5px; color: white; }
        .tag.KEIO { background: var(--keio-color); }
        .tag.JR { background: var(--jr-color); }
        .tag.FILES { background: var(--files-color); }
        .tag.OTHERS { background: var(--others-color); }
        
        /* 管理者用：非公開中や資料タブの特別な見た目 */
        .is-secret { border-left: 5px solid #ff5722 !important; background-color: #fff9f8; }
        .secret-badge { font-size: 10px; background: #ff5722; color: white; padding: 2px 4px; border-radius: 3px; margin-left: 5px; }
        #nav-files { display: none; } /* 初期状態では「資料」ボタンを隠す */

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
        <button onclick="showPage('files')" id="nav-files">資料</button> <!-- 管理者のみ表示 -->
        <button onclick="showPage('others')" id="nav-others">その他</button>
        <button onclick="showPage('settings')" id="nav-settings">同期・管理</button>
    </nav>

    <section id="all" class="active"><h2>すべてのリスト</h2><div id="list-all"></div></section>
    <section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>
    <section id="files"><h2>非公開資料</h2><div id="list-files"></div></section>
    <section id="others"><h2>その他</h2><div id="list-others"></div></section>

    <section id="settings">
        <h2>同期と管理</h2>
        <button class="btn-main" style="background:#4caf50;" onclick="syncLoad()">クラウドから読込 (受信)</button>
        <hr>
        <div id="password-area">
            <input type="password" id="admin-pw" placeholder="パスワード(0829)">
            <button class="btn-main" onclick="unlockAdmin()">ロック解除</button>
        </div>
        <div id="admin-controls">
            <p style="font-size:12px; color:red;">※「資料」カテゴリーは常にパスワード保護されます</p>
            <select id="new-cat">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="files">資料 (基本非公開)</option>
                <option value="others">その他</option>
            </select>
            <input type="text" id="new-title" placeholder="タイトル">
            <input type="text" id="new-url" placeholder="URL">
            <textarea id="new-desc" placeholder="説明" rows="2"></textarea>
            <label id="secret-wrap" style="font-size:14px; display:block; margin: 10px 0; text-align: left;">
                <input type="checkbox" id="new-secret" style="width:auto; vertical-align: middle;"> 公開リストにも表示する (公開設定)
            </label>
            <button class="btn-main" onclick="addItem()">リストに追加</button>
            <button class="btn-main" id="btn-save" onclick="syncSave()">現在の状態を保存 (送信)</button>
            <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">全削除</button>
        </div>
    </section>

<script>
    if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('sw.js').then(() => console.log("SW Registered"));
    }

    const SECRET_PASSWORD = "0829"; 
    const API_URL = "https://google.com";
    
    let railData = JSON.parse(localStorage.getItem('railData') || '[]');
    let isAdmin = false;

    function unlockAdmin() {
        if(document.getElementById('admin-pw').value === SECRET_PASSWORD) {
            isAdmin = true;
            document.getElementById('password-area').style.display = 'none';
            document.getElementById('admin-controls').style.display = 'block';
            document.getElementById('nav-files').style.display = 'inline-block'; // 資料タブを表示
            render();
        } else { alert("パスワード違います"); }
    }

    function addItem() {
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        const desc = document.getElementById('new-desc').value;
        // 「公開設定」にチェックがない ＝ secretプロパティをtrueにする
        const isPublic = document.getElementById('new-secret').checked;
        const secret = !isPublic; 
        
        if(!title || !url) return alert("入力してください");
        
        railData.push({title, url, cat, desc, secret});
        saveLocal(); render();
        
        document.getElementById('new-title').value = "";
        document.getElementById('new-url').value = "";
        document.getElementById('new-desc').value = "";
        document.getElementById('new-secret').checked = false;
    }

    function render() {
        const ids = ['all', 'keio', 'jr', 'files', 'others'];
        ids.forEach(id => {
            const el = document.getElementById('list-' + id);
            if(el) el.innerHTML = "";
        });

        railData.forEach((item, index) => {
            // 資料(files)カテゴリー、または非公開設定(secret)のものは管理者以外には見せない
            const isFilesCat = (item.cat === 'files');
            if ((isFilesCat || item.secret) && !isAdmin) return;

            const deleteBtn = isAdmin ? `<button onclick="deleteItem(${index})" style="color:red; border:none; background:none; cursor:pointer;">[削除]</button>` : '';
            const secretBadge = item.secret ? `<span class="secret-badge">非公開中</span>` : '';
            const secretClass = item.secret ? 'is-secret' : '';

            let catLabel = {keio:'京王', jr:'JR', files:'資料', others:'その他'}[item.cat] || '他';
            
            const html = `
                <div class="url-item ${secretClass}">
                    <div class="url-info">
                        <div>
                            <span class="tag ${item.cat.toUpperCase()}">${catLabel}</span>
                            ${secretBadge}
                            <b><a href="${item.url}" target="_blank">${item.title}</a></b>
                        </div>
                        ${deleteBtn}
                    </div>
                    ${item.desc ? `<div class="url-desc">${item.desc}</div>` : ''}
                </div>`;

            // 「すべて」のリストには公開分のみ（管理者は全部）
            document.getElementById('list-all').innerHTML += html;
            // 各カテゴリーのリストに追加
            const targetList = document.getElementById('list-' + item.cat);
            if(targetList) targetList.innerHTML += html;
        });
    }

    function deleteItem(index) {
        if(!confirm("削除しますか？")) return;
        railData.splice(index, 1);
        saveLocal(); render();
    }

    function saveLocal() { localStorage.setItem('railData', JSON.stringify(railData)); }

    async function syncSave() {
        const btn = document.getElementById('btn-save'); btn.innerText = "送信中...";
        try {
            await fetch(API_URL, { method: "POST", body: JSON.stringify({ action: "save", data: railData }) });
            alert("クラウドに保存しました！");
        } catch (e) { alert("保存失敗"); }
        finally { btn.innerText = "現在の状態を保存 (送信)"; }
    }

    async function syncLoad() {
        try {
            const res = await fetch(API_URL);
            const json = await res.json();
            if (json && Array.isArray(json.data)) { 
                railData = json.data; 
                saveLocal(); 
                // もし読み込んだデータに資料が含まれていても、isAdminがfalseなら表示されない
                render(); 
                alert("クラウドから読込完了！"); 
            }
        } catch (e) { alert("読込失敗"); }
    }

    function clearAll() { if(confirm("全消去？")) { railData = []; saveLocal(); render(); } }

    function showPage(id) {
        document.querySelectorAll('section, nav button').forEach(el => el.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        const navBtn = document.getElementById('nav-' + id);
        if(navBtn) navBtn.classList.add('active');
    }

    window.onload = render;
</script>
</body>
</html>

