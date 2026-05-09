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
        
        /* ナビゲーション */
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; justify-content: center; overflow-x: auto; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 15px; cursor: pointer; border-radius: 20px; white-space: nowrap; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }
        
        /* 検索バー */
        .search-container { max-width: 500px; margin: 15px auto 5px; padding: 0 15px; }
        .search-bar { width: 100%; padding: 12px 15px; border-radius: 25px; border: 1px solid #ddd; box-sizing: border-box; font-size: 16px; outline: none; background: white; }

        /* リストアイテム */
        section { display: none; max-width: 500px; margin: 10px auto 20px; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); text-align: left; }
        section.active { display: block; }
        
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 5px; color: white; vertical-align: middle; }
        .tag.KEIO { background: var(--keio-color); }
        .tag.JR { background: var(--jr-color); }
        .tag.FILES { background: var(--files-color); }
        .tag.OTHERS { background: var(--others-color); }
        
        .url-item { padding: 12px 0; border-bottom: 1px solid #f0f0f0; }
        .url-info { display: flex; justify-content: space-between; align-items: flex-start; }
        .url-info b { -webkit-user-select: text; user-select: text; font-size: 16px; }
        .url-desc { font-size: 12px; color: #666; margin-top: 4px; padding-left: 8px; border-left: 3px solid #eee; }
        
        /* 管理機能 */
        .is-secret { border-left: 5px solid #ff5722 !important; background-color: #fff9f8; }
        .secret-badge { font-size: 10px; background: #ff5722; color: white; padding: 2px 4px; border-radius: 3px; margin-left: 5px; }
        #admin-controls { display: none; margin-top: 20px; padding-top: 20px; border-top: 2px dashed #ddd; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; margin-top: 10px; }
        input, select, textarea { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        a { text-decoration: none; color: #333; }
        
        /* バージョン情報 */
        .version-info { margin-top: 30px; padding-top: 15px; border-top: 1px solid #eee; color: #bbb; font-size: 11px; text-align: center; }
    </style>
</head>
<body>
    <header><h1>tetsudo-site</h1></header>
    
    <nav>
        <button onclick="showPage('all')" id="nav-all" class="active">すべて</button>
        <button onclick="showPage('keio')" id="nav-keio">京王</button>
        <button onclick="showPage('jr')" id="nav-jr">JR線</button>
        <button onclick="showPage('files')" id="nav-files">資料</button>
        <button onclick="showPage('others')" id="nav-others">その他</button>
        <button onclick="showPage('settings')" id="nav-settings">設定</button>
    </nav>

    <!-- 検索バー -->
    <div class="search-container" id="search-box">
        <input type="text" id="search-input" class="search-bar" placeholder="🔍 キーワードで検索..." oninput="render()">
    </div>

    <section id="all" class="active"><h2>すべてのリスト</h2><div id="list-all"></div></section>
    <section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>
    <section id="files"><h2>資料</h2><div id="list-files"></div></section>
    <section id="others"><h2>その他</h2><div id="list-others"></div></section>

    <section id="settings">
        <h2>同期と管理</h2>
        <button class="btn-main" style="background:#4caf50;" onclick="syncLoad()">クラウドから読込 (受信)</button>
        <hr>
        <div id="password-area">
            <!-- パスワードを伏せ字(type="password")に変更 -->
            <input type="password" id="admin-pw" placeholder="パスワード(0829)">
            <button class="btn-main" onclick="unlockAdmin()">ロック解除</button>
        </div>
        
        <div id="admin-controls">
            <select id="new-cat">
                <option value="keio">京王</option>
                <option value="jr">JR</option>
                <option value="files">資料</option>
                <option value="others">その他</option>
            </select>
            <input type="text" id="new-title" placeholder="タイトル">
            <input type="text" id="new-url" placeholder="URL">
            <textarea id="new-desc" placeholder="説明" rows="2"></textarea>
            <label style="font-size:14px; display:block; margin: 10px 0; text-align: left;">
                <input type="checkbox" id="new-secret" style="width:auto; vertical-align: middle;"> 管理者のみ表示する (非公開設定)
            </label>
            <button class="btn-main" onclick="addItem()">リストに追加</button>
            <button class="btn-main" id="btn-save" onclick="syncSave()">現在の状態を保存 (送信)</button>
            <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">全削除</button>
        </div>

        <div class="version-info">
            App Version: 1.0.2<br>
            © tetsudo-site Project
        </div>
    </section>

<script>
    const SECRET_PASSWORD = "0829"; 
    const API_URL = "https://google.com";
    
    let railData = JSON.parse(localStorage.getItem('railData') || '[]');
    let isAdmin = false;

    function showPage(pageId) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        document.getElementById(pageId).classList.add('active');
        document.getElementById('nav-' + pageId).classList.add('active');
        
        // 設定ページでは検索バーを隠す
        document.getElementById('search-box').style.display = (pageId === 'settings') ? 'none' : 'block';
    }

    function unlockAdmin() {
        if(document.getElementById('admin-pw').value === SECRET_PASSWORD) {
            isAdmin = true;
            document.getElementById('password-area').style.display = 'none';
            document.getElementById('admin-controls').style.display = 'block';
            render();
        } else { alert("パスワード違います"); }
    }

    function addItem() {
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        const desc = document.getElementById('new-desc').value;
        const secret = document.getElementById('new-secret').checked;
        if(!title || !url) return alert("タイトルとURLを入力してください");
        railData.push({title, url, cat, desc, secret});
        saveLocal(); render();
        document.getElementById('new-title').value = "";
        document.getElementById('new-url').value = "";
        document.getElementById('new-desc').value = "";
    }

    function deleteItem(index) {
        if(confirm("削除しますか？")) {
            railData.splice(index, 1);
            saveLocal(); render();
        }
    }

    function render() {
        const query = document.getElementById('search-input').value.toLowerCase();
        const ids = ['all', 'keio', 'jr', 'files', 'others'];
        ids.forEach(id => document.getElementById('list-' + id).innerHTML = "");

        railData.forEach((item, index) => {
            if (item.secret && !isAdmin) return;
            
            // 検索フィルタ
            if (!item.title.toLowerCase().includes(query) && !item.desc.toLowerCase().includes(query)) return;

            const deleteBtn = isAdmin ? `<button onclick="deleteItem(${index})" style="color:red; border:none; background:none; cursor:pointer;">[削除]</button>` : '';
            const secretBadge = item.secret ? `<span class="secret-badge">非公開</span>` : '';
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

            document.getElementById('list-all').insertAdjacentHTML('beforeend', html);
            if(document.getElementById('list-' + item.cat)) {
                document.getElementById('list-' + item.cat).insertAdjacentHTML('beforeend', html);
            }
        });
    }

    function saveLocal() { localStorage.setItem('railData', JSON.stringify(railData)); }
    function clearAll() { if(confirm("全削除しますか？")) { railData = []; saveLocal(); render(); } }

    // クラウド保存 (POST)
    async function syncSave() {
        const btn = document.getElementById('btn-save');
        btn.innerText = "保存中...";
        try {
            await fetch(API_URL, { 
                method: 'POST', 
                body: JSON.stringify(railData),
                mode: 'no-cors' // 保存はこれでOK
            });
            alert("保存リクエスト送信完了（反映には数秒かかります）");
        } catch(e) { alert("保存失敗"); }
        btn.innerText = "現在の状態を保存 (送信)";
    }

    // クラウド読込 (GET) - 通信エラー対策版
    async function syncLoad() {
        try {
            const res = await fetch(API_URL, {
                method: 'GET',
                redirect: 'follow'
            });
            if (!res.ok) throw new Error('サーバーエラー');
            const data = await res.json();
            railData = data;
            saveLocal();
            render();
            alert("クラウドからデータを読み込みました");
        } catch(e) {
            console.error(e);
            alert("受信失敗：GASの公開設定が『全員(Anyone)』になっているか、最新のURLか確認してください。");
        }
    }

    render();
</script>
</body>
</html>
