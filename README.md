<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>鉄道ポータル - クラウド同期</title>
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { font-family: "Helvetica Neue", Arial, sans-serif; background-color: #f8f9fa; margin: 0; padding-bottom: 50px; color: #333; }
        header { background: #0099e5; color: white; padding: 20px 0; text-align: center; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        h1 { margin: 0; font-size: 20px; }
        nav { display: flex; justify-content: center; gap: 10px; background: white; padding: 10px 0; border-bottom: 1px solid #dee2e6; position: sticky; top: 0; z-index: 100; }
        .nav-btn { border: 1px solid #dee2e6; background: #fff; padding: 8px 20px; border-radius: 5px; cursor: pointer; font-weight: bold; }
        .nav-btn.active { background: #e50914; color: white; border-color: #e50914; }
        .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }
        .sync-card { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); margin-bottom: 25px; }
        .card-title { border-left: 5px solid #0099e5; padding-left: 15px; margin-bottom: 20px; font-size: 18px; color: #0076b3; font-weight: bold; }
        .btn-large { width: 100%; padding: 14px; border: none; border-radius: 8px; font-size: 15px; font-weight: bold; cursor: pointer; margin-bottom: 12px; }
        .btn-blue { background-color: #007bff; color: white; }
        .btn-green { background-color: #28a745; color: white; }
        .btn-gray { background-color: #a0a0a0; color: white; }
        .input-group { margin-bottom: 15px; }
        input { width: 100%; padding: 12px; border: 1px solid #ced4da; border-radius: 6px; box-sizing: border-box; margin-bottom: 10px; }
        .link-item { background: white; border: 1px solid #dee2e6; padding: 15px; border-radius: 8px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; }
        .open-btn { background: #e50914; color: white; text-decoration: none; padding: 8px 15px; border-radius: 5px; font-size: 13px; font-weight: bold; }
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
    <div class="sync-card">
        <div class="card-title">クラウド同期</div>
        <button onclick="pushToCloud()" class="btn-large btn-blue">今のリストを保存 (送信)</button>
        <button onclick="pullFromCloud()" class="btn-large btn-green">最新のリストを読込 (受信)</button>
    </div>

    <div id="edit-panel" class="sync-card" style="display:none; background: #fffde6;">
        <div class="card-title" style="border-color: #f1c40f; color: #856404;">新規追加</div>
        <div class="input-group">
            <input type="text" id="new-title" placeholder="タイトルを入力">
            <input type="text" id="new-url" placeholder="YouTube URLを入力">
            <button onclick="addToList()" class="btn-large btn-blue" style="background:#444;">リストに仮追加</button>
        </div>
        <button onclick="resetData()" class="btn-large btn-gray">全データ初期化</button>
    </div>

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
            
            // 【重要】リアルタイム監視：他デバイスで更新されたら自動で画面を書き換える
            database.ref('links').on('value', function(snapshot) {
                localData = snapshot.val() || { keio: [], jr: [], other: [] };
                render();
                console.log("クラウドから自動同期しました");
            });

            changeTab('keio');
        } else {
            setTimeout(initApp, 500);
        }
    }

    // クラウドから強制受信
    function pullFromCloud() {
        database.ref('links').once('value').then(function(s) {
            localData = s.val() || { keio: [], jr: [], other: [] };
            render();
            alert("同期完了！");
        });
    }

    // クラウドへ送信
    function pushToCloud() {
        if (!isAdmin) return alert("送信は管理者モード（⚙️）のみ可能です。");
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
        if(!t || !u) return alert("入力してください");
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
                            <span class="link-text">${item.title}</span>
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
