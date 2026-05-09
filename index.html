<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>tetsudo-site | 詳細表示対応</title>
    <style>
        /* --- デザイン (CSS) --- */
        body { font-family: sans-serif; background-color: #0074be; margin: 0; display: flex; flex-direction: column; align-items: center; padding-bottom: 50px; }
        header { width: 100%; text-align: center; color: white; padding: 20px 0; }
        nav ul { list-style: none; display: inline-flex; background: white; padding: 5px; border-radius: 25px; gap: 5px; margin: 0; overflow-x: auto; max-width: 95vw; }
        nav li { color: #333; padding: 8px 15px; cursor: pointer; font-size: 14px; font-weight: bold; border-radius: 20px; white-space: nowrap; }
        nav li.active { background-color: #0074be; color: white; }
        
        main { width: 95%; max-width: 600px; margin-top: 20px; }
        .card { background: white; border-radius: 10px; padding: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        h2 { margin-top: 0; font-size: 20px; border-bottom: 2px solid #eee; padding-bottom: 10px; margin-bottom: 15px; }

        .search-container { margin-bottom: 20px; }
        .search-input { width: 100%; padding: 12px 15px; border: 1px solid #ddd; border-radius: 20px; font-size: 14px; box-sizing: border-box; outline: none; }
        
        /* リスト項目のスタイル改善 */
        .list-item { display: flex; align-items: flex-start; padding: 15px 0; border-bottom: 1px solid #eee; text-decoration: none; color: #333; }
        .item-info { flex: 1; }
        .item-title { font-weight: bold; display: block; margin-bottom: 4px; font-size: 16px; }
        .item-desc { font-size: 12px; color: #666; display: block; line-height: 1.4; }
        
        .badge { color: white; font-size: 10px; padding: 2px 6px; border-radius: 4px; margin-right: 12px; margin-top: 3px; min-width: 40px; text-align: center; }
        .badge.京王 { background: #dd004b; }
        .badge.JR { background: #008000; }
        .badge.資料 { background: #666; }
        .badge.その他 { background: #999; }
        
        /* 管理画面 */
        .admin-section { display: none; flex-direction: column; gap: 15px; }
        .admin-section.active { display: flex; }
        input, select, textarea, button { padding: 12px; border-radius: 6px; border: 1px solid #ddd; width: 100%; box-sizing: border-box; font-size: 14px; }
        textarea { resize: vertical; min-height: 60px; font-family: inherit; }
        .btn-blue { background: #0074be; color: white; border: none; cursor: pointer; font-weight: bold; }
        .btn-green { background: #4caf50; color: white; border: none; cursor: pointer; font-weight: bold; }
        .btn-delete { background: #ff4d4d; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; font-size: 11px; width: auto; margin-top: 10px; }
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

        <div class="search-container" id="search-box">
            <input type="text" class="search-input" id="search-query" placeholder="タイトルや詳細から検索...">
        </div>

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
                    <input type="text" id="new-name" placeholder="タイトル (例: 2000系)" style="margin-bottom:10px;">
                    <textarea id="new-desc" placeholder="詳細 (例: 新宿→笹塚 走行音など)" style="margin-bottom:10px;"></textarea>
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
    import { initializeApp } from "https://gstatic.com";
    import { getFirestore, doc, setDoc, getDoc } from "https://gstatic.com";

    // Firebaseの設定
    const firebaseConfig = { apiKey: "YOUR_API_KEY", projectId: "YOUR_PROJECT_ID" };
    let db;
    try { if (firebaseConfig.apiKey !== "YOUR_API_KEY") { const app = initializeApp(firebaseConfig); db = getFirestore(app); } } catch (e) {}

    const DOC_ID = "main_list";
    let items = [
        { name: "2000系", desc: "新宿駅から笹塚駅までの前面展望動画", cat: "京王", url: "https://example.com" },
        { name: "E233系7000番台", desc: "埼京線開業40周年ラッピング車両の記録", cat: "JR", url: "https://example.com" }
    ];
    let isUnlocked = false;

    function render(tabName) {
        const query = document.getElementById('search-query').value.toLowerCase();
        const content = document.getElementById('list-content');
        const adminView = document.getElementById('admin-view');
        const manageList = document.getElementById('manage-list');
        const searchBox = document.getElementById('search-box');
        const title = document.getElementById('view-title');
        
        content.innerHTML = '';
        manageList.innerHTML = '';
        title.innerText = tabName === '設定' ? '同期と管理' : tabName + 'のリスト';

        if (tabName === '設定') {
            adminView.classList.add('active');
            searchBox.style.display = 'none';
            if (isUnlocked) {
                items.forEach((item, index) => {
                    const div = document.createElement('div');
                    div.style.padding = "10px 0";
                    div.style.borderBottom = "1px solid #eee";
                    div.innerHTML = `
                        <strong>[${item.cat}] ${item.name}</strong><br>
                        <small style="color: #666;">${item.desc || '(詳細なし)'}</small><br>
                        <button class="btn-delete" data-index="${index}">削除</button>
                    `;
                    div.querySelector('.btn-delete').onclick = () => { items.splice(index, 1); render('設定'); };
                    manageList.appendChild(div);
                });
            }
        } else {
            adminView.classList.remove('active');
            searchBox.style.display = 'block';
            
            const filtered = items.filter(i => {
                const matchTab = (tabName === 'すべて' || i.cat === tabName);
                const matchSearch = i.name.toLowerCase().includes(query) || 
                                    (i.desc && i.desc.toLowerCase().includes(query)) || 
                                    i.cat.toLowerCase().includes(query);
                return matchTab && matchSearch;
            });

            filtered.forEach(item => {
                const a = document.createElement('a');
                a.className = 'list-item';
                a.href = item.url;
                a.target = "_blank";
                a.innerHTML = `
                    <span class="badge ${item.cat}">${item.cat}</span>
                    <div class="item-info">
                        <span class="item-title">${item.name}</span>
                        <span class="item-desc">${item.desc || ''}</span>
                    </div>
                `;
                content.appendChild(a);
            });
        }
    }

    // 検索・タブイベント
    document.getElementById('search-query').addEventListener('input', () => render(document.querySelector('#tabs .active').getAttribute('data-tab')));
    document.querySelectorAll('#tabs li').forEach(tab => {
        tab.addEventListener('click', () => {
            document.querySelector('#tabs .active').classList.remove('active');
            tab.classList.add('active');
            document.getElementById('search-query').value = '';
            render(tab.getAttribute('data-tab'));
        });
    });

    // 管理系ボタン
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
        const desc = document.getElementById('new-desc').value;
        const url = document.getElementById('new-url').value;
        const cat = document.getElementById('new-cat').value;
        if(name && url) {
            items.push({ name, desc, cat, url });
            render('設定');
            document.getElementById('new-name').value = '';
            document.getElementById('new-desc').value = '';
            document.getElementById('new-url').value = '';
        } else { alert('タイトルとURLは必須です'); }
    };

    document.getElementById('save-btn').onclick = async () => {
        if (!db) return alert("Firebase設定が必要です");
        try { await setDoc(doc(db, "tetsudo", DOC_ID), { list: items }); alert("保存完了"); } catch (e) { alert("保存失敗"); }
    };

    document.getElementById('sync-btn').onclick = async () => {
        if (!db) return alert("Firebase設定が必要です");
        try { 
            const docSnap = await getDoc(doc(db, "tetsudo", DOC_ID));
            if (docSnap.exists()) { items = docSnap.data().list || []; render('すべて'); }
            alert("同期しました");
        } catch (e) { alert("同期失敗"); }
    };

    render('すべて');
</script>

</body>
</html>
