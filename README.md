<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - クラウド同期</title>
    <!-- Firebaseライブラリ -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        :root { --main-blue: #007bff; --main-green: #28a745; --bg-gray: #f8f9fa; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--bg-gray); margin: 0; color: #333; }
        header { background: #1a2a3a; color: white; padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; }
        .container { max-width: 700px; margin: 20px auto; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 4px 20px rgba(0,0,0,0.08); }
        
        h2 { border-left: 5px solid var(--main-blue); padding-left: 15px; margin-bottom: 25px; color: #1a2a3a; }

        /* 同期ボタンのデザイン（参考にされたサイト風） */
        .sync-btn { width: 100%; padding: 15px; border: none; border-radius: 8px; color: white; font-weight: bold; font-size: 16px; cursor: pointer; margin-bottom: 12px; transition: 0.2s; }
        .btn-send { background: var(--main-blue); }
        .btn-receive { background: var(--main-green); }
        .btn-delete { background: #6c757d; margin-top: 20px; }
        .sync-btn:active { transform: scale(0.98); opacity: 0.9; }

        /* 入力エリア */
        .input-group { background: #fdfdfd; padding: 20px; border: 1px solid #eee; border-radius: 10px; margin-bottom: 30px; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        
        /* リスト表示 */
        .video-list { margin-top: 20px; }
        .video-item { padding: 15px; border-bottom: 1px solid #eee; display: flex; justify-content: space-between; align-items: center; }
        .video-item a { color: var(--main-blue); text-decoration: none; font-weight: bold; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; background: #eee; margin-right: 8px; }

        .clock { font-family: monospace; font-size: 0.9rem; text-align: right; }
    </style>
</head>
<body>

<header>
    <div style="font-weight:bold; font-size:1.2rem;">RailStream</div>
    <div class="clock"><div id="date">--/--/--</div><div id="time">--:--:--</div></div>
</header>

<div class="container">
    <h2>クラウド同期</h2>

    <!-- 同期アクション -->
    <button class="sync-btn btn-send" onclick="sendData()">今のデータを保存 (送信)</button>
    <button class="sync-btn btn-receive" onclick="receiveData()">最新のデータを読込 (受信)</button>

    <hr style="margin:30px 0; border:0; border-top:1px solid #eee;">

    <div class="input-group">
        <h3>動画を追加</h3>
        <select id="cat"><option value="keio">京王</option><option value="jr">JR</option><option value="other">その他</option></select>
        <input type="text" id="title" placeholder="タイトルを入力">
        <input type="text" id="url" placeholder="URLを貼り付け">
        <button class="sync-btn" style="background:#333;" onclick="addToList()">リストに追加</button>
    </div>

    <div class="video-list" id="display-list">
        <!-- ここに一時的なリストが表示されます -->
    </div>

    <button class="sync-btn btn-delete" onclick="clearAll()">全データ初期化</button>
</div>

<script>
    // 1. 基本機能（時計）
    function clock() {
        const n = new Date();
        document.getElementById('date').innerText = n.toLocaleDateString();
        document.getElementById('time').innerText = n.toLocaleTimeString();
    }
    setInterval(clock, 1000); clock();

    // 2. Firebase設定
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxE0JOGCtCEnM2xqjMcr-Rc",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo",
        storageBucket: "tetsudo.firebasestorage.app",
        messagingSenderId: "91814902933",
        appId: "1:91814902933:web:f9a8a3bce73470b842ef9c"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // 3. ローカルでのデータ管理
    let currentList = JSON.parse(localStorage.getItem('rail_data')) || [];
    render();

    function addToList() {
        const title = document.getElementById('title').value;
        const url = document.getElementById('url').value;
        const cat = document.getElementById('cat').value;
        if(!title || !url) return alert("入力してください");

        currentList.push({title, url, cat});
        saveLocal();
        render();
        document.getElementById('title').value = "";
        document.getElementById('url').value = "";
    }

    function render() {
        const listDiv = document.getElementById('display-list');
        listDiv.innerHTML = "<h3>現在のリスト</h3>";
        currentList.forEach((item, index) => {
            listDiv.innerHTML += `
                <div class="video-item">
                    <div>
                        <span class="tag">${item.cat.toUpperCase()}</span>
                        <a href="${item.url}" target="_blank">${item.title}</a>
                    </div>
                    <button onclick="removeItem(${index})" style="border:none; background:none; cursor:pointer;">❌</button>
                </div>
            `;
        });
    }

    function removeItem(index) {
        currentList.splice(index, 1);
        saveLocal();
        render();
    }

    function saveLocal() {
        localStorage.setItem('rail_data', JSON.stringify(currentList));
    }

    function clearAll() {
        if(confirm("すべて削除しますか？")) {
            currentList = [];
            saveLocal();
            render();
        }
    }

    // --- 4. 同期機能 (Firebase) ---

    // 送信: 今のリストをクラウドに保存
    function sendData() {
        if(!db) return alert("接続エラー");
        db.ref('sync_data').set(currentList)
            .then(() => alert("クラウドに保存しました！"))
            .catch(e => alert("失敗: " + e.message));
    }

    // 受信: クラウドから最新データを取ってくる
    function receiveData() {
        db.ref('sync_data').once('value').then((snap) => {
            const data = snap.val();
            if(data) {
                currentList = data;
                saveLocal();
                render();
                alert("最新データを読み込みました！");
            } else {
                alert("データが見つかりませんでした");
            }
        });
    }
</script>
</body>
</html>
