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
        body { font-family: -apple-system, sans-serif; background: #fafafa; margin: 0; padding-bottom: 60px; }
        header { background: #fff; border-bottom: 1px solid #ddd; padding: 15px; position: sticky; top: 0; text-align: center; z-index: 1000; }
        nav { display: flex; justify-content: center; gap: 5px; margin-top: 10px; }
        .nav-btn { border: 1px solid #ddd; background: #fff; padding: 8px 15px; border-radius: 20px; cursor: pointer; font-size: 14px; font-weight: bold; }
        .nav-btn.active { background: #e50914; color: #fff; border-color: #e50914; }
        .container { max-width: 500px; margin: auto; padding: 15px; }
        .tab-content { display: none; flex-direction: column; gap: 10px; }
        .tab-content.active { display: flex; }
        .link-card { background: #fff; border: 1px solid #eee; padding: 15px; border-radius: 10px; display: flex; align-items: center; justify-content: space-between; margin-bottom: 10px; }
        .link-title { font-weight: bold; font-size: 15px; flex: 1; }
        .open-btn { background: #e50914; color: #fff; text-decoration: none; padding: 8px 15px; border-radius: 6px; font-size: 13px; font-weight: bold; }
        #edit-panel { display: none; background: #fffbe6; padding: 15px; border: 2px dashed #ffe58f; border-radius: 10px; margin-bottom: 20px; }
        input { width: 100%; padding: 12px; margin-bottom: 10px; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box; }
    </style>
</head>
<body>

<header>
    <strong>鉄道ポータル</strong>
    <nav id="top-nav">
        <button id="btn-keio" class="nav-btn">京王線</button>
        <button id="btn-jr" class="nav-btn">JR</button>
        <button id="btn-other" class="nav-btn">その他</button>
        <button id="btn-lock" class="nav-btn">⚙️</button>
    </nav>
</header>

<div class="container">
    <div id="edit-panel">
        <strong id="target-label">京王線に追加</strong>
        <div style="margin-top:10px;">
            <input type="text" id="new-title" placeholder="タイトル">
            <input type="text" id="new-url" placeholder="YouTube URL">
            <button id="save-btn" style="width:100%; background:#28a745; color:#fff; padding:12px; border:none; border-radius:5px; font-weight:bold; cursor:pointer;">クラウドに保存</button>
        </div>
    </div>
    <div id="keio" class="tab-content active"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    // ブラウザがHTMLを読み込み終わってから実行
    window.addEventListener('DOMContentLoaded', function() {
        var currentTab = 'keio';
        var isAdmin = false;
        var database;

        // Firebase初期化
        const config = {
            apiKey: "AIzaSyAe_KxKH-06cxEOJ0GCtCEnM2xqjMcr-Rc",
            databaseURL: "https://firebaseio.com",
            projectId: "tetsudo"
        };
        firebase.initializeApp(config);
        database = firebase.database();

        // データの読み込み
        database.ref('links').on('value', function(snapshot) {
            var data = snapshot.val() || {};
            ['keio', 'jr', 'other'].forEach(function(tab) {
                var container = document.getElementById(tab);
                container.innerHTML = '';
                if (data[tab]) {
                    data[tab].forEach(function(item, index) {
                        var card = document.createElement('div');
                        card.className = 'link-card';
                        card.innerHTML = '<span class="link-title">' + item.title + '</span>' +
                                       '<div style="display:flex; gap:10px; align-items:center;">' +
                                       '<a href="' + item.url + '" target="_blank" class="open-btn">開く</a>' +
                                       (isAdmin ? '<button class="del-trigger" data-tab="'+tab+'" data-idx="'+index+'" style="border:none; color:red; background:none; cursor:pointer;">× 削除</button>' : '') +
                                       '</div>';
                        container.appendChild(card);
                    });
                }
            });
        });

        // 保存ボタン（onclickを使わない方法）
        document.getElementById('save-btn').addEventListener('click', function() {
            var t = document.getElementById('new-title').value.trim();
            var u = document.getElementById('new-url').value.trim();
            if(!t || !u) return alert("入力してね");

            database.ref('links/' + currentTab).once('value', function(s) {
                var list = s.val() || [];
                list.push({title: t, url: u});
                database.ref('links/' + currentTab).set(list).then(function() {
                    document.getElementById('new-title').value = '';
                    document.getElementById('new-url').value = '';
                    alert("クラウドに保存しました！");
                });
            });
        });

        // タブ切り替え（onclickを使わない方法）
        ['keio', 'jr', 'other'].forEach(function(tabId) {
            document.getElementById('btn-' + tabId).addEventListener('click', function() {
                currentTab = tabId;
                document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
                document.getElementById(tabId).classList.add('active');
                document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('active'));
                this.classList.add('active');
                document.getElementById('target-label').innerText = this.innerText + 'に追加';
            });
        });

        // 設定ボタン
        document.getElementById('btn-lock').addEventListener('click', function() {
            if (isAdmin || prompt("パスワード") === "0829") {
                isAdmin = !isAdmin;
                document.getElementById('edit-panel').style.display = isAdmin ? 'block' : 'none';
                this.innerText = isAdmin ? '🔓' : '⚙️';
            }
        });

        // 削除機能（動的に作られたボタンに対応）
        document.addEventListener('click', function(e) {
            if(e.target && e.target.className == 'del-trigger'){
                if(!confirm("消す？")) return;
                var tab = e.target.getAttribute('data-tab');
                var idx = e.target.getAttribute('data-idx');
                database.ref('links/' + tab).once('value', function(s) {
                    var list = s.val() || [];
                    list.splice(idx, 1);
                    database.ref('links/' + tab).set(list);
                });
            }
        });
    });
</script>
</body>
</html>
