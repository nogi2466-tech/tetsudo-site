<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>tetsudo-site</title>
    <style>
        :root { 
            --primary-blue: #0078d4; 
            --bg-gray: #f9f9f9; 
            --keio-color: #e60012; /* 京王: 赤 */
            --jr-color: #008000;   /* JR: 緑 */
            --others-color: #ff8c00; /* その他: オレンジ */
        }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); text-align: center; }
        header { background: linear-gradient(135deg, #333, #666); color: white; padding: 25px 10px; }
        
        /* 項目別のタグ色 */
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 5px; color: white; }
        .tag.KEIO { background: var(--keio-color); }
        .tag.JR { background: var(--jr-color); }
        .tag.OTHERS { background: var(--others-color); }
        
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); display: flex; justify-content: center; overflow-x: auto; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 4px; padding: 8px 15px; cursor: pointer; border-radius: 20px; white-space: nowrap; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }
        
        section { display: none; max-width: 500px; margin: 20px auto; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); text-align: left; }
        section.active { display: block; }
        
        /* リスト表示 */
        .url-item { padding: 12px 0; border-bottom: 1px solid #f0f0f0; }
        .url-info { display: flex; justify-content: space-between; align-items: flex-start; }
        .url-desc { font-size: 12px; color: #666; margin-top: 4px; padding-left: 8px; border-left: 3px solid #eee; }
        
        /* 管理画面（最初は非表示） */
        #admin-controls { display: none; margin-top: 20px; border-top: 2px dashed #ddd; pt: 20px; }
        .btn-main { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; font-weight: bold; margin-top: 10px; }
        input, select, textarea { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        
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
        <button onclick="showPage('settings')" id="nav-settings">管理・同期</button>
    </nav>

    <section id="all" class="active"><h2>すべての動画</h2><div id="list-all"></div></section>
    <section id="keio"><h2>京王線</h2><div id="list-keio"></div></section>
    <section id="jr"><h2>JR線</h2><div id="list-jr"></div></section>
    <section id="others"><h2>その他</h2><div id="list-others"></div></section>

    <!-- 管理・同期設定セクション -->
    <section id="settings">
        <h2>管理者認証</h2>
        <div id="password-area">
            <p style="font-size:14px; color:#666;">管理操作を行うにはパスワードを入力してください。</p>
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
            <input type="text" id="new-title" placeholder="タイトルを入力">
            <input type="text" id="new-url" placeholder="URLを貼り付け">
            <textarea id="new-desc" placeholder="動画の説明を入力（任意）" rows="2"></textarea>
            <button class="btn-main" onclick="addItem()">リストに追加</button>
            
            <hr style="margin:25px 0; border:none; border-top:1px solid #eee;">
            <h3>クラウド同期</h3>
            <button class="btn-main" onclick="syncSave()">クラウドに保存 (送信)</button>
            <button class="btn-main" style="background:#4caf50;" onclick="syncLoad()">クラウドから読込 (受信)</button>
            <button class="btn-main" style="background:#999; margin-top:20px;" onclick="clearAll()">全データ削除</button>
        </div>
    </section>

<script>
    const SECRET_PASSWORD = "0829"; 
    const API_URL = "https://google.com";
    
    let railData = JSON.parse(localStorage.getItem('railData') || '[]');
    let isAdmin = false; // 管理者状態フラグ

    // 管理画面のロック解除
    function unlockAdmin() {
        const pw = document.getElementById('admin-pw').value;
        if(pw === SECRET_PASSWORD) {
            isAdmin = true;
            document.getElementById('password-area').style.display = 'none';
            document.getElementById('admin-controls').style.display = 'block';
            render(); // 削除ボタンを表示させるために再描画
        } else {
            alert("パスワードが違います");
        }
    }

    function addItem() {
        if(!isAdmin) return;
        const title = document.getElementById('new-title').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        const desc = document.getElementById('new-desc').value;
        
        if(!title || !url) return alert("タイトルとURLを入力してください");

        railData.push({title, url, cat, desc});
        saveLocal();
        render();
        
        document.getElementById('new-title').value = "";
        document.getElementById('new-url').value = "";
        document.getElementById('new-desc').value = "";
        alert("追加しました。");
    }

    function render() {
        const ids = ['all', 'keio', 'jr', 'others'];
        ids.forEach(id => document.getElementById('list-' + id).innerHTML = "");

        railData.forEach((item, index) => {
            // 管理者の時だけ削除ボタンを表示
            const deleteBtn = isAdmin ? `<button onclick="deleteItem(${index})" style="color:red; border:none; background:none; cursor:pointer; font-size:12px;">[削除]</button>` : '';
            
            const html = `
                <div class="url-item">
                    <div class="url-info">
                        <div>
                            <span class="tag ${item.cat.toUpperCase()}">${item.cat.toUpperCase()}</span>
                            <b><a href="${item.url}" target="_blank">${item.title}</a></b>
                        </div>
                        ${deleteBtn}
                    </div>
                    ${item.desc ? `<div class="url-desc">${item.desc}</div>` : ''}
                </div>`;
            document.getElementById('list-all').innerHTML += html;
            if(document.getElementById('list-' + item.cat)) {
                document.getElementById('list-' + item.cat).innerHTML += html;
            }
        });
    }

    function deleteItem(index) {
        if(!isAdmin) return;
        if(!confirm("本当に削除しますか？")) return;
        railData.splice(index, 1);
        saveLocal();
        render();
    }

    function saveLocal() {
        localStorage.setItem('railData', JSON.stringify(railData));
    }

    async function syncSave() {
        if(!isAdmin) return;
        const btn = event.target;
        btn.innerText = "送信中...";
        try {
            await fetch(API_URL, { method: "POST", body: JSON.stringify({ action: "save", data: railData }) });
            alert("クラウドに保存しました！");
        } catch (e) { alert("保存に失敗しました。"); }
        btn.innerText = "クラウドに保存 (送信)";
    }

    async function syncLoad() {
        try {
            const res = await fetch(API_URL);
            const json = await res.json();
            if (json && Array.isArray(json.data)) {
                railData = json.data;
                saveLocal();
                render();
                alert("読み込み完了！");
            }
        } catch (e) { alert("読込失敗"); }
    }

    function clearAll() {
        if(!isAdmin) return;
        if(confirm("全消去しますか？")) {
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

    window.onload = render;
</script>
</body>
</html>

