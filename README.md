<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>鉄道ポータル - クラウド同期</title>
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { font-family: -apple-system, sans-serif; background: #f0f2f5; margin: 0; padding-bottom: 80px; }
        header { background: #333; color: white; padding: 20px; text-align: center; }
        nav { display: flex; justify-content: center; gap: 8px; background: white; padding: 10px; border-bottom: 1px solid #ddd; position: sticky; top: 0; z-index: 1000; }
        .nav-btn { border: 1px solid #ddd; background: white; padding: 8px 18px; border-radius: 20px; cursor: pointer; font-weight: bold; }
        .nav-btn.active { background: #e50914; color: white; border-color: #e50914; }
        .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }

        /* 管理・設定パネル */
        #edit-panel { display: none; background: #fffbe6; padding: 20px; border: 2px dashed #ffe58f; border-radius: 12px; margin-bottom: 20px; }
        .panel-section { margin-bottom: 20px; padding-bottom: 15px; border-bottom: 1px solid #eee; }
        .panel-section:last-child { border: none; }
        h4 { margin: 0 0 10px 0; color: #856404; font-size: 14px; }
        
        input { width: 100%; padding: 12px; margin-bottom: 10px; border: 1px solid #ccc; border-radius: 6px; box-sizing: border-box; font-size: 15px; }
        .btn { width: 100%; padding: 12px; border: none; border-radius: 6px; font-weight: bold; cursor: pointer; color: white; margin-bottom: 5px; }
        .btn-blue { background: #007bff; }
        .btn-green { background: #28a745; }
        .btn-gray { background: #777; }

        .link-item { background: white; border: 1px solid #eee; padding: 15px; border-radius: 10px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 1px 3px rgba(0,0,0,0.05); }
        .open-btn { background: #e50914; color: white; text-decoration: none; padding: 8px 15px; border-radius: 6px; font-size: 13px; font-weight: bold; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
    </style>
</head>
<body>

<header><h1>鉄道動画ポータル</h1></header>

<nav>
    <button onclick="changeTab('keio')" id="btn-keio" class="nav-btn">京王線</button>
    <button onclick="changeTab('jr')" id="btn-jr" class="nav-btn">JR</button>
    <button onclick="changeTab('other')" id="btn-other" class="nav-btn">その他</button>
    <button onclick="askPassword()" id="btn-lock" class="nav-btn">⚙️</button>
</nav>

<div class="container">
    <div id="edit-panel">
        <!-- 【追加】同期キー設定セクション -->
        <div class="panel-section">
            <h4>🔄 デバイス同期（合言葉を入力）</h4>
            <input type="text" id="sync-key" placeholder="例：123456789wo">
            <button onclick="connectSync()" class="btn btn-blue">この合言葉で同期する</button>
            <p style="font-size: 11px; color: #856404; margin: 5px 0 0;">※同じ合言葉を入れたデバイス同士でリストが共有されます。</p>
        </div>

        <!-- 追加セクション -->
        <div class="panel-section">
            <h4>➕ 新規追加</h4>
            <input type="text" id="new-title" placeholder="タイトル">
            <input type="text" id="new-url" placeholder="YouTube URL">
            <button onclick="saveItem()" class="btn btn-green">クラウドに保存</button>
        </div>

        <button onclick="resetData()" class="btn btn-gray">全データ初期化</button>
    </div>

    <div id="keio" class="tab-content"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    var currentTab = 'keio';
    var isAdmin = false;
    var database = null;
    var currentSyncKey = "";
    var localData = { keio: [], jr: [], other: [] };

    // Firebase設定（以前のあなたの設定を使用）
    const config = {
        apiKey: "AIzaSyAe_KxKH-06cxEOJ0GCtCEnM2xqjMcr-Rc",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo"
    };

    function initApp() {
        if (typeof firebase !== 'undefined') {
            firebase.initializeApp(config);
            database = firebase.database();
            
            // 最後に使った同期キーがあれば自動接続
            const savedKey = localStorage.getItem('last_sync_key');
            if (savedKey) {
                document.getElementById('sync-key').value = savedKey;
                connectSync(true);
            }
        } else {
            setTimeout(initApp, 500);
        }
        changeTab('keio');
    }

    // 【重要】合言葉でクラウドに接続する関数
    function connectSync(isInitial = false) {
        const key = document.getElementById('sync-key').value.trim();
        if (!key) return alert("合言葉を入力してください");

        currentSyncKey = key;
        localStorage.setItem('last_sync_key', key);

        // その合言葉専用の場所を監視する
        database.ref('user_sync/' + key).on('value', function(snapshot) {
            localData = snapshot.val() || { keio: [], jr: [], other: [] };
            render();
            if (!isInitial) console.log("同期されました: " + key);
        });
        
        if (!isInitial) alert("合言葉「" + key + "」で同期を開始しました！");
    }

    function saveItem() {
        if (!currentSyncKey) return alert("先に同期（合言葉）を設定してください");
        var t = document.getElementById('new-title').value.trim();
        var u = document.getElementById('new-url').value.trim();
        if(!t || !u) return alert("入力してください");

        localData[currentTab].push({title: t, url: u});
        database.ref('user_sync/' + currentSyncKey).set(localData).then(function() {
            document.getElementById('new-title').value = '';
            document.getElementById('new-url').value = '';
            alert("保存しました！");
        });
    }

    function render() {
        ['keio', 'jr', 'other'].forEach(function(tab) {
            var container = document.getElementById(tab);
            container.innerHTML = '';
            if (localData[tab]) {
                localData[tab].forEach(function(item, index) {
                    container.innerHTML += `
                        <div class="link-item">
                            <span style="font-weight:bold;">${item.title}</span>
                            <div style="display:flex; gap:10px; align-items:center;">
                                <a href="${item.url}" target="_blank" class="open-btn">開く</a>
                                ${isAdmin ? `<button onclick="deleteItem('${tab}',${index})" style="border:none; color:red; background:none; cursor:pointer; font-size:18px;">×</button>` : ''}
                            </div>
                        </div>`;
                });
            }
        });
    }

    function deleteItem(tab, idx) {
        if (!confirm("削除しますか？")) return;
        localData[tab].splice(idx, 1);
        database.ref('user_sync/' + currentSyncKey).set(localData);
    }

    function resetData() {
        if (!confirm("クラウドのデータをすべて消去しますか？")) return;
        localData = { keio: [], jr: [], other: [] };
        database.ref('user_sync/' + currentSyncKey).set(localData);
    }

    function changeTab(id) {
        currentTab = id;
        document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('active'));
        document.getElementById('btn-' + id).classList.add('active');
    }

    function askPassword() {
        if (isAdmin) { isAdmin = false; document.getElementById('edit-panel').style.display = 'none'; return; }
        if (prompt("パスワードを入力してください") === "0829") {
            isAdmin = true;
            document.getElementById('edit-panel').style.display = 'block';
            render();
        }
    }

    initApp();
</script>
</body>
</html>
