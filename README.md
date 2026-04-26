<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>鉄道ポータル</title>
    <style>
        body { font-family: -apple-system, sans-serif; background: #f0f2f5; margin: 0; padding-bottom: 80px; color: #333; }
        header { background: #333; color: white; padding: 20px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        h1 { margin: 0; font-size: 20px; }
        nav { display: flex; justify-content: center; gap: 8px; background: white; padding: 10px; border-bottom: 1px solid #ddd; position: sticky; top: 0; z-index: 1000; }
        .nav-btn { border: 1px solid #ddd; background: white; padding: 8px 18px; border-radius: 20px; cursor: pointer; font-weight: bold; }
        .nav-btn.active { background: #e50914; color: white; border-color: #e50914; }
        .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }

        /* 同期カード */
        .sync-card { background: white; padding: 20px; border-radius: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); margin-bottom: 20px; }
        .card-title { border-left: 5px solid #333; padding-left: 10px; font-weight: bold; margin-bottom: 15px; font-size: 18px; }
        .btn-large { width: 100%; padding: 15px; border: none; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; margin-bottom: 10px; color: white; }
        .btn-sync { background-color: #28a745; } /* 送信（URL生成） */

        .link-item { background: white; border: 1px solid #eee; padding: 15px; border-radius: 10px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
        .open-btn { background: #e50914; color: white; text-decoration: none; padding: 8px 15px; border-radius: 6px; font-size: 13px; font-weight: bold; }

        #edit-panel { display: none; background: #fffbe6; padding: 20px; border: 2px dashed #ffe58f; border-radius: 12px; margin-bottom: 20px; }
        input { width: 100%; padding: 12px; margin-bottom: 12px; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
    </style>
</head>
<body>

<header>
    <h1>鉄道動画ポータル</h1>
</header>

<nav>
    <button onclick="changeTab('keio')" id="btn-keio" class="nav-btn">京王線</button>
    <button onclick="changeTab('jr')" id="btn-jr" class="nav-btn">JR</button>
    <button onclick="changeTab('other')" id="btn-other" class="nav-btn">その他</button>
    <button onclick="askPassword()" id="btn-lock" class="nav-btn">⚙️</button>
</nav>

<div class="container">
    <!-- 同期用エリア -->
    <div class="sync-card">
        <div class="card-title">デバイス同期</div>
        <button onclick="generateSyncUrl()" class="btn-large btn-sync">同期用URLを発行（コピー）</button>
        <p style="font-size: 11px; color: #666;">※このURLを他のデバイスで開くとリストが同期されます。</p>
    </div>

    <!-- 管理者パネル -->
    <div id="edit-panel">
        <div class="card-title" style="border-color: #f1c40f;">新規追加</div>
        <input type="text" id="new-title" placeholder="タイトル">
        <input type="text" id="new-url" placeholder="YouTube URL">
        <button onclick="addToList()" class="btn-large" style="background:#444;">追加</button>
        <button onclick="resetData()" class="btn-large" style="background:#777;">全初期化</button>
    </div>

    <div id="keio" class="tab-content"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    var currentTab = 'keio';
    var isAdmin = false;
    var localData = { keio: [], jr: [], other: [] };

    // 起動時に保存されたデータまたはURLからのデータを確認
    function init() {
        const params = new URLSearchParams(window.location.search);
        const sharedData = params.get('d');
        
        if (sharedData) {
            try {
                // URLからデータを受信
                localData = JSON.parse(decodeURIComponent(atob(sharedData)));
                saveToStorage();
                // URLをきれいにする（パラメータを消す）
                window.history.replaceState({}, "", window.location.pathname);
                alert("同期が完了しました！");
            } catch (e) {
                console.error("同期失敗", e);
            }
        } else {
            const saved = localStorage.getItem('tetsudo_data');
            if (saved) localData = JSON.parse(saved);
        }
        render();
        changeTab('keio');
    }

    // 同期用URLを生成してクリップボードにコピー
    function generateSyncUrl() {
        const dataStr = btoa(encodeURIComponent(JSON.stringify(localData)));
        const syncUrl = window.location.origin + window.location.pathname + '?d=' + dataStr;
        
        navigator.clipboard.writeText(syncUrl).then(() => {
            alert("同期用URLをコピーしました！他のデバイスでこのURLを開いてください。");
        });
    }

    function addToList() {
        var t = document.getElementById('new-title').value.trim();
        var u = document.getElementById('new-url').value.trim();
        if(!t || !u) return;
        localData[currentTab].push({title: t, url: u});
        saveToStorage();
        render();
        document.getElementById('new-title').value = '';
        document.getElementById('new-url').value = '';
    }

    function saveToStorage() {
        localStorage.setItem('tetsudo_data', JSON.stringify(localData));
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
        localData[tab].splice(idx, 1);
        saveToStorage();
        render();
    }

    function resetData() {
        if (confirm("全データを消去しますか？")) {
            localData = { keio: [], jr: [], other: [] };
            saveToStorage();
            render();
        }
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
        // 指定されたパスワード「123456789wo」で管理画面を開く
        if (prompt("パスワード") === "123456789wo") {
            isAdmin = true;
            document.getElementById('edit-panel').style.display = 'block';
            render();
        }
    }

    init();
</script>
</body>
</html>
