<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tetsudo Portal</title>
    <!-- 【重要】Firebaseの本体を先に読み込む -->
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
        .link-card { background: #fff; border: 1px solid #eee; padding: 15px; border-radius: 10px; display: flex; align-items: center; justify-content: space-between; margin-bottom: 10px; box-shadow: 0 1px 3px rgba(0,0,0,0.05); }
        .link-title { font-weight: bold; font-size: 15px; flex: 1; }
        .open-btn { background: #e50914; color: #fff; text-decoration: none; padding: 8px 15px; border-radius: 6px; font-size: 13px; font-weight: bold; }
        #edit-panel { display: none; background: #fffbe6; padding: 15px; border: 2px dashed #ffe58f; border-radius: 10px; margin-bottom: 20px; }
        input { width: 100%; padding: 12px; margin-bottom: 10px; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box; font-size: 16px; }
    </style>
</head>
<body>

<header>
    <strong>鉄道ポータル</strong>
    <nav>
        <button onclick="showTab('keio')" id="btn-keio" class="nav-btn">京王線</button>
        <button onclick="showTab('jr')" id="btn-jr" class="nav-btn">JR</button>
        <button onclick="showTab('other')" id="btn-other" class="nav-btn">その他</button>
        <button onclick="unlockAdmin()" id="btn-lock" class="nav-btn">⚙️</button>
    </nav>
</header>

<div class="container">
    <div id="edit-panel">
        <strong id="target-label">京王線に追加</strong>
        <div style="margin-top:10px;">
            <input type="text" id="new-title" placeholder="タイトル">
            <input type="text" id="new-url" placeholder="YouTube URL">
            <button onclick="pushToCloud()" style="width:100%; background:#28a745; color:#fff; padding:12px; border:none; border-radius:5px; font-weight:bold; cursor:pointer;">クラウドに保存</button>
        </div>
    </div>
    <div id="keio" class="tab-content active"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    // 変数を一番上で宣言する（エラー回避のため）
    let currentTab = 'keio';
    let isAdmin = false;

    // Firebaseの設定
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxEOJ0GCtCEnM2xqjMcr-Rc",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo"
    };

    // Firebaseの初期化（ここで読み込みエラーを防ぐ）
    if (typeof firebase !== 'undefined') {
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        // データの受信
        db.ref('links').on('value', (s) => render(s.val() || {}));

        // 保存機能
        window.pushToCloud = function() {
            const t = document.getElementById('new-title').value.trim();
            const u = document.getElementById('new-url').value.trim();
            if(!t || !u) return alert("入力してください");
            db.ref('links/' + currentTab).once('value', (s) => {
                let list = s.val() || [];
                list.push({title: t, url: u});
                db.ref('links/' + currentTab).set(list);
                document.getElementById('new-title').value = '';
                document.getElementById('new-url').value = '';
            });
        };

        // 削除機能
        window.deleteLink = function(tab, index) {
            if(!confirm("削除しますか？")) return;
            db.ref('links/' + tab).once('value', (s) => {
                let list = s.val() || [];
                list.splice(index, 1);
                db.ref('links/' + tab).set(list);
            });
        };
    }

    function showTab(tabId) {
        currentTab = tabId;
        document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        document.querySelectorAll('.nav-btn').forEach(btn => btn.classList.remove('active'));
        document.getElementById('btn-' + tabId).classList.add('active');
        document.getElementById('target-label').innerText = document.getElementById('btn-'+tabId).innerText + 'に追加';
    }

    function render(data) {
        ['keio', 'jr', 'other'].forEach(tab => {
            const container = document.getElementById(tab);
            container.innerHTML = '';
            if (data && data[tab]) {
                data[tab].forEach((item, index) => {
                    container.innerHTML += `
                        <div class="link-card">
                            <span class="link-title">${item.title}</span>
                            <div style="display:flex; gap:10px; align-items:center;">
                                <a href="${item.url}" target="_blank" class="open-btn">開く</a>
                                ${isAdmin ? `<button onclick="deleteLink('${tab}', ${index})" style="border:none; color:#dc3545; background:none; cursor:pointer; font-size:12px;">× 削除</button>` : ''}
                            </div>
                        </div>`;
                });
            }
        });
    }

    function unlockAdmin() {
        if (isAdmin || prompt("パスワード") === "0829") {
            isAdmin = !isAdmin;
            document.getElementById('edit-panel').style.display = isAdmin ? 'block' : 'none';
            if (typeof firebase !== 'undefined') {
                firebase.database().ref('links').once('value', s => render(s.val() || {}));
            }
        }
    }

    // 起動時に初期表示
    showTab('keio');
</script>
</body>
</html>
