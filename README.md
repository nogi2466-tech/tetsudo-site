<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - 鉄道動画コレクション</title>
    <style>
        :root { --main-color: #2c3e50; --accent-color: #e74c3c; --bg-color: #f4f7f6; }
        body { font-family: 'Helvetica Neue', Arial, sans-serif; background: var(--bg-color); margin: 0; color: #333; }
        
        /* ヘッダー */
        header { background: var(--main-color); color: white; padding: 1rem 2rem; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        .logo { font-size: 1.5rem; font-weight: bold; letter-spacing: 1px; }
        .clock { text-align: right; font-family: monospace; font-size: 0.9rem; }

        /* メインコンテンツ */
        .container { max-width: 900px; margin: 20px auto; padding: 0 15px; }
        
        /* タブ・フィルタ */
        .filter-bar { display: flex; justify-content: center; gap: 10px; margin-bottom: 25px; flex-wrap: wrap; }
        .filter-btn { padding: 10px 25px; border: none; background: white; border-radius: 30px; cursor: pointer; font-weight: bold; box-shadow: 0 2px 4px rgba(0,0,0,0.1); transition: 0.3s; }
        .filter-btn.active { background: var(--accent-color); color: white; }

        /* リスト表示 */
        .video-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 20px; }
        .video-card { background: white; border-radius: 12px; padding: 15px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); display: flex; flex-direction: column; justify-content: space-between; position: relative; border-left: 5px solid #ccc; }
        .card-keio { border-left-color: #e60012; }
        .card-jr { border-left-color: #008000; }
        .card-other { border-left-color: #333; }
        .video-card h3 { margin: 0 0 10px 0; font-size: 1.1rem; color: var(--main-color); }
        .video-card a { color: #3498db; text-decoration: none; font-weight: bold; font-size: 0.9rem; }
        .video-card a:hover { text-decoration: underline; }

        /* 管理用ボタン */
        .del-badge { position: absolute; top: -10px; right: -10px; background: red; color: white; border: none; width: 25px; height: 25px; border-radius: 50%; cursor: pointer; display: none; font-size: 12px; }
        .admin-mode .del-badge { display: block; }

        /* 設定エリア */
        .settings-panel { background: white; padding: 30px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); margin-top: 30px; }
        input, select { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        .btn-action { width: 100%; padding: 15px; border: none; border-radius: 8px; background: var(--main-color); color: white; font-weight: bold; cursor: pointer; margin-top: 10px; }
        .btn-action:hover { opacity: 0.9; }

        @media (max-width: 600px) { .video-grid { grid-template-columns: 1fr; } }
    </style>
</head>
<body>

<header>
    <div class="logo">RailStream</div>
    <div class="clock">
        <div id="date">----/--/--</div>
        <div id="time">--:--:--</div>
    </div>
</header>

<div class="container">
    <!-- タブ -->
    <div class="filter-bar">
        <button class="filter-btn active" onclick="filterCat('all', this)">すべて</button>
        <button class="filter-btn" onclick="filterCat('keio', this)">京王</button>
        <button class="filter-btn" onclick="filterCat('jr', this)">JR</button>
        <button class="filter-btn" onclick="filterCat('other', this)">その他</button>
        <button class="filter-btn" onclick="toggleSettings()">⚙ 設定</button>
    </div>

    <!-- 動画一覧 -->
    <div class="video-grid" id="main-list">
        <!-- ここに動画が追加されます -->
    </div>

    <!-- 設定パネル（初期非表示） -->
    <div id="settings-area" class="settings-panel" style="display:none;">
        <div id="admin-login">
            <h3>管理者ログイン</h3>
            <input type="password" id="pass" placeholder="パスワード (0829)">
            <button class="btn-action" onclick="login()">ログイン</button>
        </div>
        <div id="admin-tools" style="display:none;">
            <h3>新しい動画を追加</h3>
            <select id="cat"><option value="keio">京王</option><option value="jr">JR</option><option value="other">その他</option></select>
            <input type="text" id="title" placeholder="動画タイトル">
            <input type="text" id="url" placeholder="URL (YouTube等)">
            <button class="btn-action" style="background: #27ae60;" onclick="addVideo()">保存する</button>
            <button class="btn-action" style="background: #ccc; margin-top:20px;" onclick="location.reload()">ログアウト</button>
        </div>
    </div>
</div>

<!-- Firebase v8 -->
<script src="https://gstatic.com"></script>
<script src="https://gstatic.com"></script>

<script>
    // 1. Firebase設定（あなたの設定を反映済み）
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "tetsudo.firebaseapp.com",
        databaseURL: "https://tetsudo-default-rtdb.firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:f9a8a3bce73470b842ef9c"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // 2. 時計
    function updateTime() {
        const n = new Date();
        document.getElementById('date').innerText = n.toLocaleDateString();
        document.getElementById('time').innerText = n.toLocaleTimeString();
    }
    setInterval(updateTime, 1000);
    updateTime();

    // 3. 動画読み込み
    let allData = {};
    db.ref('urls').on('value', (snap) => {
        allData = snap.val() || {};
        render('all');
    });

    function render(filter) {
        const list = document.getElementById('main-list');
        list.innerHTML = "";
        Object.keys(allData).forEach(key => {
            const item = allData[key];
            if(filter !== 'all' && item.cat !== filter) return;
            
            const card = document.createElement('div');
            card.className = `video-card card-${item.cat}`;
            card.innerHTML = `
                <h3>${item.title}</h3>
                <a href="${item.url}" target="_blank">▶ 動画を見る</a>
                <button class="del-badge" onclick="delVideo('${key}')">×</button>
            `;
            list.appendChild(card);
        });
    }

    // 4. 操作
    function filterCat(cat, btn) {
        document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        document.getElementById('settings-area').style.display = 'none';
        render(cat);
    }

    function toggleSettings() {
        const area = document.getElementById('settings-area');
        area.style.display = area.style.display === 'none' ? 'block' : 'none';
    }

    function login() {
        if(document.getElementById('pass').value === "0829") {
            document.getElementById('admin-login').style.display = 'none';
            document.getElementById('admin-tools').style.display = 'block';
            document.body.classList.add('admin-mode');
        } else { alert("失敗"); }
    }

    function addVideo() {
        const cat = document.getElementById('cat').value;
        const title = document.getElementById('title').value;
        const url = document.getElementById('url').value;
        if(!title || !url) return alert("入力してください");
        db.ref('urls').push({cat, title, url}).then(() => {
            alert("追加完了");
            document.getElementById('title').value = "";
            document.getElementById('url').value = "";
        });
    }

    function delVideo(key) {
        if(confirm("削除しますか？")) db.ref('urls/' + key).remove();
    }
</script>
</body>
</html>
