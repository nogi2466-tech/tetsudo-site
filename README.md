<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>tetsudo-site | 同期機能付き</title>
    <style>
        /* --- デザイン設定 (CSS) --- */
        body { font-family: sans-serif; background-color: #0074be; margin: 0; display: flex; flex-direction: column; align-items: center; padding-bottom: 50px; }
        header { width: 100%; text-align: center; color: white; padding: 20px 0; }
        nav ul { list-style: none; display: inline-flex; background: white; padding: 5px; border-radius: 25px; gap: 5px; margin: 0; }
        nav li { color: #333; padding: 8px 15px; cursor: pointer; font-size: 14px; font-weight: bold; border-radius: 20px; }
        nav li.active { background-color: #0074be; color: white; }
        
        main { width: 90%; max-width: 600px; margin-top: 20px; }
        .card { background: white; border-radius: 10px; padding: 25px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        h2 { margin-top: 0; font-size: 20px; border-bottom: 2px solid #eee; padding-bottom: 10px; }
        
        .list-item { display: flex; align-items: center; padding: 12px 0; border-bottom: 1px solid #eee; text-decoration: none; color: #333; font-weight: bold; }
        .badge { color: white; font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 10px; min-width: 40px; text-align: center; }
        .badge.京王 { background: #dd004b; }
        .badge.JR { background: #008000; }
        .badge.資料 { background: #666; }
        .badge.その他 { background: #999; }
        .url-text { font-size: 11px; color: #888; font-weight: normal; margin-left: auto; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; max-width: 200px; }
        
        /* 管理・設定画面用 */
        .admin-section { display: none; flex-direction: column; gap: 15px; }
        .admin-section.active { display: flex; }
        input, select, button { padding: 12px; border-radius: 6px; border: 1px solid #ddd; width: 100%; box-sizing: border-box; }
        .btn-blue { background: #0074be; color: white; border: none; cursor: pointer; font-weight: bold; }
        .btn-green { background: #4caf50; color: white; border: none; cursor: pointer; font-weight: bold; }
        .btn-delete { background: #ff4d4d; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; font-size: 11px; margin-left: 10px; width: auto; }
        .form-box { border: 2px dashed #ccc; padding: 15px; margin-top: 20px; border-radius: 8px; }
    </style>
</head>
<body>

<header>
    <h1>tetsudo-site</h1>
    <nav>
        <ul id="tabs">
            <li class="active" data-tab="すべて">すべて</li>
            <li data-tab="京王">京王</li>
            <li data-tab="JR">JR</li>
            <li data-tab="資料">資料</li>
            <li data-tab="その他">その他</li>
            <li data-tab="設定">設定</li>
        </ul>
    </nav>
</header>

<main>
    <div class="card">
        <h2 id="view-title">すべてのリスト</h2>
        <div id="list-content"></div>

        <!-- 設定セクション -->
        <div id="admin-view" class="admin-section">
            <div id="login-box">
                <input type="password" id="pass-input" placeholder="パスワード (0829)">
                <button class="btn-blue" id="unlock-btn" style="margin-top:10px;">ロック解除</button>
            </div>
            
            <div id="admin-controls" style="display:none;">
                <button class="btn-green" id="sync-btn" style="margin-bottom:10px;">クラウドから読込 (受信)</button>
                <button class="btn-blue" id="save-btn">クラウドへ保存 (送信)</button>

                <div class="form-box">
                    <h3>項目追加</h3>
                    <input type="text" id="new-name" placeholder="名称" style="margin-bottom:10px;">
                    <input type="text" id="new-url" placeholder="URL (https://...)" style="margin-bottom:10px;">
                    <select id="new-cat" style="margin-bottom:10px;">
                        <option value="京王">京王</option>
                        <option value="JR">JR</option>
                        <option value="資料">資料</option>
                        <option value="その他">その他</option>
                    </select>
                    <button class="btn-blue" id="add-btn">追加する</button>
                </div>
                <div id="manage-list" style="margin-top:20px;"></div>
            </div>
        </div>
    </div>
</main>

<script type="module">
    // --- Firebase設定 ---
    import { initializeApp } from "https://gstatic.com";
    import { getFirestore, doc, setDoc, getDoc } from "https://gstatic.com";

    const firebaseConfig = {
        apiKey: "ここにあなたのAPI_KEYを入力",
        authDomain: "://firebaseapp.com",
        projectId: "あなたのプロジェクトID",
        storageBucket: "://appspot.com",
        messagingSenderId: "...",
        appId: "..."
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const DOC_ID = "main_list";

    // --- 状態管理 ---
    let items = [];
    let isUnlocked = false;

    // --- 関数: クラウドからデータ取得 ---
    async function syncFromCloud() {
        try {
            const docSnap = await getDoc(doc(db, "tetsudo", DOC_ID));
            if (docSnap.exists()) {
                items = docSnap.data().list || [];
                render(document.querySelector('#tabs .active').getAttribute('data-tab'));
            }
        } catch (e) { console.error("Sync Error:", e); }
    }

    // --- 関数: クラウドへ保存 ---
    async function saveToCloud() {
        if (!isUnlocked) return;
        try {
            await setDoc(doc(db, "tetsudo", DOC_ID), { list: items });
            alert("クラウドに保存しました！");
        } catch (e) { alert("保存失敗: " + e); }
    }

    // --- 関数: 描画 ---
    function render(tabName) {
        const content = document.getElementById('list-content');
        const adminView = document.getElementById('admin-view');
        const manageList = document.getElementById('manage-list');
        const title = document.getElementById('view-title');
        
        content.innerHTML = '';
        manageList.innerHTML = '';
        title.innerText = tabName === '設定' ? '同期と管理' : tabName + 'のリスト';

        if (tabName === '設定') {
            adminView.classList.add('active');
            if (isUnlocked) {
                items.forEach((item, index) => {
                    const div = document.createElement('div');
                    div.className = 'list-item';
                    div.innerHTML = `<span class="badge ${item.cat}">${item.cat}</span> ${item.name} <button class="btn-delete" data-index="${index}">削除</button>`;
                    div.querySelector('.btn-delete').onclick = () => { items.splice(index, 1); render('設定'); };
                    manageList.appendChild(div);
                });
            }
        } else {
            adminView.classList.remove('active');
            const filtered = tabName === 'すべて' ? items : items.filter(i => i.cat === tabName);
            filtered.forEach(item => {
                const a = document.createElement('a');
                a.className = 'list-item';
                a.href = item.url;
                a.target = "_blank";
                a.innerHTML = `<span class="badge ${item.cat}">${item.cat}</span> ${item.name} <span class="url-text">${item.url}</span>`;
                content.appendChild(a);
            });
        }
    }

    // --- イベント設定 ---
    document.querySelectorAll('#tabs li').forEach(tab => {
        tab.addEventListener('click', () => {
            document.querySelector('#tabs .active').classList.remove('active');
            tab.classList.add('active');
            render(tab.getAttribute('data-tab'));
        });
    });

    document.getElementById('unlock-btn').onclick = () => {
        if(document.getElementById('pass-input').value === '0829') {
            isUnlocked = true;
            document.getElementById('login-box').style.display = 'none';
            document.getElementById('admin-controls').style.display = 'block';
            render('設定');
        } else { alert('パスワードが違います'); }
    };

    document.getElementById('add-btn').onclick = () => {
        const name = document.getElementById('new-name').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        if(name && url) {
            items.push({ name, cat, url });
            render('設定');
            document.getElementById('new-name').value = '';
            document.getElementById('new-url').value = '';
        }
    };

    document.getElementById('save-btn').onclick = saveToCloud;
    document.getElementById('sync-btn').onclick = syncFromCloud;

    // 初回読み込み
    syncFromCloud();
</script>

</body>
</html>
