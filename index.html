<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <style>
        body { font-family: sans-serif; background-color: #0074be; margin: 0; display: flex; flex-direction: column; align-items: center; padding-bottom: 50px; }
        header { width: 100%; text-align: center; color: white; padding: 20px 0; }
        nav ul { list-style: none; display: inline-flex; background: white; padding: 5px; border-radius: 25px; gap: 5px; margin: 0; overflow-x: auto; max-width: 90vw; }
        nav li { color: #333; padding: 8px 15px; cursor: pointer; font-size: 14px; font-weight: bold; border-radius: 20px; white-space: nowrap; }
        nav li.active { background-color: #0074be; color: white; }
        main { width: 95%; max-width: 600px; margin-top: 20px; }
        .card { background: white; border-radius: 10px; padding: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        h2 { margin-top: 0; font-size: 18px; border-bottom: 2px solid #eee; padding-bottom: 10px; margin-bottom: 15px; }
        .search-input { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 20px; font-size: 14px; box-sizing: border-box; outline: none; margin-bottom: 15px; }
        .list-item { display: flex; align-items: flex-start; padding: 12px 0; border-bottom: 1px solid #eee; text-decoration: none; color: #333; }
        .item-info { flex: 1; }
        .item-title { font-weight: bold; display: block; font-size: 15px; }
        .item-desc { font-size: 12px; color: #666; display: block; margin-top: 2px; }
        .badge { color: white; font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 10px; min-width: 40px; text-align: center; margin-top: 2px; }
        .badge.京王 { background: #dd004b; }
        .badge.JR { background: #008000; }
        .badge.資料 { background: #666; }
        .badge.その他 { background: #999; }
        .admin-section { display: none; flex-direction: column; gap: 10px; }
        .admin-section.active { display: flex; }
        input, select, textarea, button { padding: 12px; border-radius: 6px; border: 1px solid #ddd; width: 100%; box-sizing: border-box; }
        .btn-blue { background: #0074be; color: white; border: none; cursor: pointer; font-weight: bold; }
        .btn-green { background: #4caf50; color: white; border: none; cursor: pointer; font-weight: bold; }
        .btn-delete { background: #ff4d4d; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; width: auto; font-size: 11px; margin-top: 5px; }
        .form-box { border: 2px dashed #ccc; padding: 15px; border-radius: 8px; margin-top: 15px; }
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
        <input type="text" id="search-query" class="search-input" placeholder="タイトルや詳細で検索...">
        <div id="list-content"></div>
        <div id="admin-view" class="admin-section">
            <div id="login-box">
                <input type="password" id="pass-input" placeholder="パスワード (0829)">
                <button class="btn-blue" id="unlock-btn" style="margin-top:10px;">ロック解除</button>
            </div>
            <div id="admin-controls" style="display:none;">
                <button class="btn-green" id="sync-btn" style="margin-bottom:10px;">クラウドから読込 (受信)</button>
                <button class="btn-blue" id="save-btn">クラウドへ保存 (送信)</button>
                <div class="form-box">
                    <h3>新規項目追加</h3>
                    <input type="text" id="new-name" placeholder="タイトル" style="margin-bottom:8px;">
                    <textarea id="new-desc" placeholder="詳細説明" style="margin-bottom:8px;"></textarea>
                    <input type="text" id="new-url" placeholder="URL (https://...)" style="margin-bottom:8px;">
                    <select id="new-cat" style="margin-bottom:8px;">
                        <option value="京王">京王</option>
                        <option value="JR">JR</option>
                        <option value="資料">資料</option>
                        <option value="その他">その他</option>
                    </select>
                    <button class="btn-blue" id="add-btn">追加する</button>
                </div>
                <div id="manage-list" style="margin-top:15px;"></div>
            </div>
        </div>
    </div>
</main>

<script type="module">
    import { initializeApp } from "https://gstatic.com";
    import { getFirestore, doc, setDoc, getDoc } from "https://gstatic.com";

    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "tetsudo.firebaseapp.com",
        databaseURL: "https://tetsudo-default-rtdb.firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:be06cc22e0d5cdb642ef9c",
        measurementId: "G-TP0SLTH0ER"
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const DOC_REF = doc(db, "data", "main_list");

    let items = [];
    let currentTab = 'すべて';
    let isUnlocked = false;

    const render = () => {
        const query = document.getElementById('search-query').value.toLowerCase();
        const listContent = document.getElementById('list-content');
        const adminView = document.getElementById('admin-view');
        const manageList = document.getElementById('manage-list');
        const searchInput = document.getElementById('search-query');
        const title = document.getElementById('view-title');

        listContent.innerHTML = ''; manageList.innerHTML = '';
        title.innerText = currentTab === '設定' ? '同期と管理' : currentTab + 'のリスト';

        if (currentTab === '設定') {
            adminView.classList.add('active'); searchInput.style.display = 'none';
            if (isUnlocked) {
                items.forEach((item, index) => {
                    const div = document.createElement('div');
                    div.style.padding = "10px 0"; div.style.borderBottom = "1px solid #eee";
                    div.innerHTML = `<strong>[${item.cat}] ${item.name}</strong><br><button class="btn-delete">削除</button>`;
                    div.querySelector('.btn-delete').onclick = () => { items.splice(index, 1); render(); };
                    manageList.appendChild(div);
                });
            }
        } else {
            adminView.classList.remove('active'); searchInput.style.display = 'block';
            const filtered = items.filter(i => {
                const matchTab = (currentTab === 'すべて' || i.cat === currentTab);
                const matchSearch = i.name.toLowerCase().includes(query) || (i.desc && i.desc.toLowerCase().includes(query));
                return matchTab && matchSearch;
            });
            filtered.forEach(item => {
                const a = document.createElement('a'); a.className = 'list-item'; a.href = item.url; a.target = "_blank";
                a.innerHTML = `<span class="badge ${item.cat}">${item.cat}</span><div class="item-info"><span class="item-title">${item.name}</span><span class="item-desc">${item.desc || ''}</span></div>`;
                listContent.appendChild(a);
            });
        }
    };

    document.querySelectorAll('#tabs li').forEach(li => {
        li.onclick = () => {
            document.querySelectorAll('#tabs li').forEach(t => t.classList.remove('active'));
            li.classList.add('active'); currentTab = li.getAttribute('data-tab');
            document.getElementById('search-query').value = ''; render();
        };
    });

    document.getElementById('search-query').oninput = render;
    document.getElementById('unlock-btn').onclick = () => {
        if(document.getElementById('pass-input').value === '0829') {
            isUnlocked = true; document.getElementById('login-box').style.display = 'none';
            document.getElementById('admin-controls').style.display = 'block'; render();
        } else { alert('パスワードが違います'); }
    };

    document.getElementById('add-btn').onclick = () => {
        const name = document.getElementById('new-name').value;
        const desc = document.getElementById('new-desc').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        if(name && url) {
            items.push({ name, desc, url, cat }); render();
            document.getElementById('new-name').value = ''; document.getElementById('new-desc').value = ''; document.getElementById('new-url').value = '';
        }
    };

    document.getElementById('save-btn').onclick = async () => {
        try { await setDoc(DOC_REF, { list: items }); alert("クラウドに保存しました！"); } catch (e) { alert("保存失敗: " + e); }
    };

    document.getElementById('sync-btn').onclick = async () => {
        try { const snap = await getDoc(DOC_REF); if (snap.exists()) { items = snap.data().list || []; render(); alert("読み込みました"); } } catch (e) { alert("同期失敗: " + e); }
    };

    window.onload = async () => { try { const snap = await getDoc(DOC_REF); if (snap.exists()) { items = snap.data().list || []; render(); } } catch (e) { render(); } };
</script>
</body>
</html>
