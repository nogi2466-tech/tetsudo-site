<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>鉄道ポータル</title>
    <!-- Firebaseの読み込み -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { font-family: -apple-system, sans-serif; background: #f0f2f5; margin: 0; padding-bottom: 60px; color: #333; }
        
        /* 前の配色：黒ヘッダー */
        header { background: #333; color: white; padding: 20px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        h1 { margin: 0; font-size: 20px; }
        
        /* ナビゲーション */
        nav { display: flex; justify-content: center; gap: 8px; background: white; padding: 10px; border-bottom: 1px solid #ddd; position: sticky; top: 0; z-index: 1000; }
        .nav-btn { border: 1px solid #ddd; background: white; padding: 8px 18px; border-radius: 20px; cursor: pointer; font-weight: bold; }
        
        /* 前の配色：アクティブは赤 */
        .nav-btn.active { background: #e50914; color: white; border-color: #e50914; }
        
        .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }

        /* カードデザイン */
        .sync-card { background: white; padding: 20px; border-radius: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); margin-bottom: 20px; }
        .card-title { border-left: 5px solid #333; padding-left: 10px; font-weight: bold; color: #333; margin-bottom: 20px; font-size: 18px; }

        /* 大きな同期ボタン */
        .btn-large { width: 100%; padding: 15px; border: none; border-radius: 8px; font-size: 16px; font-weight: bold; cursor: pointer; margin-bottom: 12px; color: white; transition: 0.2s; }
        .btn-large:disabled { opacity: 0.5; cursor: not-allowed; }
        
        /* 前の配色：送信は緑、受信は青 */
        .btn-send { background-color: #28a745; }
        .btn-recv { background-color: #007bff; }
        .btn-gray { background-color: #777; margin-top: 5px; }

        /* リストアイテム */
        .link-item { background: white; border: 1px solid #eee; padding: 15px; border-radius: 10px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 1px 3px rgba(0,0,0,0.05); }
        .link-title { font-weight: bold; font-size: 15px; flex: 1; text-align: left; }
        
        /* 前の配色：開くボタンは赤 */
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
    <!-- クラウド同期カード（大きなボタン） -->
    <div class="sync-card">
        <div class="card-title">クラウド同期</div>
        <button id="s-btn" onclick="pushToCloud()" class="btn-large btn-send" disabled>準備中...</button>
        <button id="r-btn" onclick="pullFromCloud()" class="btn-large btn-recv" disabled>準備中...</button>
    </div>

    <!-- 管理者パネル -->
    <div id="edit-panel">
        <div class="card-title" style="border-color: #f1c40f;">新規追加</div>
        <input type="text" id="new-title" placeholder="タイトルを入力">
        <input type="text" id="new-url" placeholder="YouTube URLを入力">
        <button onclick="addToList()" class="btn-large" style="background:#444;">リストに仮追加</button>
        <button onclick="resetData()" class="btn-large btn-gray">全データ初期化</button>
    </div>

    <!-- 各タブの表示エリア -->
    <div id="keio" class="tab-content"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    var currentTab = 'keio';
    var isAdmin = false;
    var database = null;
    var localData = { keio: [], jr: [], other: [] };

    const config = {
        apiKey: "AIzaSyAe_KxKH-06cxEOJ0GCtCEnM2xqjMcr-Rc",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo"
    };

    function initApp() {
        if (typeof firebase !== 'undefined') {
            firebase.initializeApp(config);
            database = firebase.database();
            
            // ボタンを有効化
            document.getElementById('s-btn').innerText = "今のリストを保存 (送信)";
            document.getElementById('s-btn').disabled = false;
            document.getElementById('r-btn').innerText = "最新のリストを読込 (受信)";
            document.getElementById('r-btn').disabled = false;

            // リアルタイム同期の設定：他デバイスの変更を自動キャッチ
            database.ref('links').on('value', function(snapshot) {
                localData = snapshot.val() || { keio: [], jr: [], other: [] };
                render();
            });

            changeTab('keio');
        } else {
            setTimeout(initApp, 500);
        }
    }

    function pullFromCloud() {
        database.ref('links').once('value').then(function(s) {
            localData = s.val() || { keio: [], jr: [], other: [] };
            render();
            alert("同期完了！");
        });
    }

    function pushToCloud() {
        if (!isAdmin) return alert("保存（送信）は管理者モード（⚙️）のみ可能です。");
        database.ref('links').set(localData).then(function() {
            alert("クラウドへ保存しました！");
        });
    }

    function resetData() {
        if (confirm("全データを消去しますか？")) {
            localData = { keio: [], jr: [], other: [] };
            database.ref('links').set(localData).then(function() {
                render();
                alert("初期化完了");
            });
        }
    }

    function addToList() {
        var t = document.getElementById('new-title').value.trim();
        var u = document.getElementById('new-url').value.trim();
        if(!t || !u) return alert("タイトルとURLを入力してください");
        localData[currentTab].push({title: t, url: u});
        render();
        document.getElementById('new-title').value = '';
        document.getElementById('new-url').value = '';
    }

    function render() {
        ['keio', 'jr', 'other'].forEach(function(tab) {
            var container = document.getElementById(tab);
            container.innerHTML = '';
            if (localData[tab]) {
                localData[tab].forEach(function(item, index) {
                    container.innerHTML += `
                        <div class="link-item">
                            <span class="link-title">${item.title}</span>
                            <div style="display:flex; gap:10px; align-items:center;">
                                <a href="${item.url}" target="_blank" class="open-btn">開く</a>
                                ${isAdmin ? `<button onclick="deleteLocal('${tab}',${index})" style="border:none; color:red; background:none; cursor:pointer; font-size:18px;">×</button>` : ''}
                            </div>
                        </div>`;
                });
            }
        });
    }

    function deleteLocal(tab, idx) {
        localData[tab].splice(idx, 1);
        render();
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
        if (prompt("パスワード") === "0829") {
            isAdmin = true;
            document.getElementById('edit-panel').style.display = 'block';
            render();
        }
    }

    initApp();
</script>
</body>
</html>
