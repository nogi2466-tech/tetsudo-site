<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RailStream - hayabee Style</title>
    <!-- Firebaseライブラリ (安定版) -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>

    <style>
        :root { --primary-blue: #0078d4; --light-blue: #e1f5fe; --text-dark: #333; --bg-gray: #f9f9f9; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; padding: 0; background: var(--bg-gray); color: var(--text-dark); }
        
        /* ヘッダー: hayabee29風グラデーション */
        header { background: linear-gradient(135deg, #0078d4, #00b0ff); color: white; padding: 30px 10px; text-align: center; }
        h1 { margin: 0; font-size: 26px; letter-spacing: 2px; }

        /* ナビゲーション: 追従型 */
        nav { background: white; padding: 10px 0; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 5px rgba(0,0,0,0.05); text-align: center; }
        nav button { background: none; border: none; font-size: 14px; margin: 0 5px; padding: 8px 15px; cursor: pointer; color: #555; border-radius: 20px; transition: 0.3s; }
        nav button.active { background: var(--primary-blue); color: white; font-weight: bold; }

        /* メインセクション */
        section { display: none; max-width: 500px; margin: 20px auto 40px; padding: 20px; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.05); }
        section.active { display: block; }
        
        h2 { border-left: 5px solid var(--primary-blue); padding-left: 15px; color: var(--primary-blue); margin-bottom: 20px; font-size: 20px; }

        /* 時計エリア */
        .clock-container { background: var(--light-blue); padding: 15px; border-radius: 10px; text-align: center; margin-bottom: 20px; }
        .clock-time { font-size: 28px; font-weight: bold; color: var(--primary-blue); font-family: monospace; }

        /* 同期ボタン */
        .btn-blue { background: var(--primary-blue); color: white; border: none; padding: 14px; border-radius: 8px; cursor: pointer; width: 100%; margin-top: 10px; font-weight: bold; font-size: 16px; transition: 0.2s; }
        .btn-blue:active { opacity: 0.8; transform: scale(0.98); }
        .btn-green { background: #4caf50; }

        /* URLカード */
        .url-card { border: 1px solid #eee; padding: 12px; border-radius: 8px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; background: #fff; }
        .url-card a { color: var(--primary-blue); text-decoration: none; font-weight: bold; font-size: 15px; }
        .tag { font-size: 10px; padding: 2px 6px; border-radius: 4px; background: #f0f0f0; color: #666; margin-right: 8px; }

        /* 入力フォーム */
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
    </style>
</head>
<body>

    <header><h1>RailStream</h1></header>

    <nav>
        <button onclick="showPage('home')" id="nav-home" class="active">ホーム</button>
        <button onclick="showPage('urls')" id="nav-urls">鉄道リスト</button>
        <button onclick="showPage('sync')" id="nav-sync">同期・設定</button>
    </nav>

    <!-- ホーム: 時計と概要 -->
    <section id="home" class="active">
        <h2>現在の時刻</h2>
        <div class="clock-container">
            <div id="current-date" style="font-size: 14px; color: #666;">----年--月--日</div>
            <div id="current-time" class="clock-time">00:00:00</div>
        </div>
        <p style="font-size: 14px; color: #888; text-align: center;">鉄道動画を管理・同期できます。</p>
    </section>

    <!-- 鉄道リスト -->
    <section id="urls">
        <h2>鉄道動画リスト</h2>
        <div id="url-container">
            <!-- ここにリストが表示されます -->
        </div>
        <hr style="margin: 25px 0; border: none; border-top: 1px dashed #ccc;">
        <h3>動画を追加</h3>
        <select id="new-cat">
            <option value="keio">京王</option>
            <option value="jr">JR</option>
            <option value="other">その他</option>
        </select>
        <input type="text" id="new-title" placeholder="動画のタイトル">
        <input type="text" id="new-url" placeholder="動画のURL (https://...)">
        <button class="btn-blue" onclick="addUrlLocal()">リストに追加</button>
    </section>

    <!-- 同期・設定 -->
    <section id="sync">
        <h2>クラウド同期</h2>
        <p style="font-size: 13px; color: #666; margin-bottom: 20px;">他のデバイスとデータを共有します。</p>
        <button class="btn-blue" onclick="syncSave()">今のリストを保存 (送信)</button>
        <button class="btn-blue btn-green" onclick="syncLoad()">最新のリストを読込 (受信)</button>
        <hr style="margin: 30px 0; border: none; border-top: 1px solid #eee;">
        <button class="btn-blue" style="background:#999;" onclick="clearLocal()">全データ初期化</button>
    </section>

    <script>
        // 1. Firebaseの設定 (あなたの設定を使用)
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

        // 2. ローカルデータの管理
        let railList = JSON.parse(localStorage.getItem('railList') || '[]');

        function showPage(pageId) {
            document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
            document.getElementById('nav-' + pageId).classList.add('active');
        }

        function updateClock() {
            const now = new Date();
            document.getElementById('current-date').innerText = now.toLocaleDateString('ja-JP', { year: 'numeric', month: 'long', day: 'numeric', weekday: 'short' });
            document.getElementById('current-time').innerText = now.toLocaleTimeString('ja-JP');
        }
        setInterval(updateClock, 1000);
        updateClock();

        // 3. URL操作
        function renderList() {
            const container = document.getElementById('url-container');
            container.innerHTML = railList.length === 0 ? '<p style="color:#999; text-align:center;">リストが空です</p>' : '';
            railList.forEach((item, index) => {
                container.innerHTML += `
                    <div class="url-card">
                        <div>
                            <span class="tag">${item.cat.toUpperCase()}</span>
                            <a href="${item.url}" target="_blank">${item.title}</a>
                        </div>
                        <button onclick="deleteUrl(${index})" style="border:none; background:none; color:#ccc; cursor:pointer;">×</button>
                    </div>
                `;
            });
        }

        function addUrlLocal() {
            const title = document.getElementById('new-title').value;
            const url = document.getElementById('new-url').value;
            const cat = document.getElementById('new-cat').value;
            if(!title || !url) return alert("入力してください");

            railList.push({title, url, cat});
            saveToLocal();
            renderList();
            document.getElementById('new-title').value = "";
            document.getElementById('new-url').value = "";
            alert("リストに追加しました");
        }

        function deleteUrl(index) {
            if(confirm("削除しますか？")) {
                railList.splice(index, 1);
                saveToLocal();
                renderList();
            }
        }

        function saveToLocal() {
            localStorage.setItem('railList', JSON.stringify(railList));
        }

        function clearLocal() {
            if(confirm("ローカルのデータをすべて消去しますか？")) {
                localStorage.removeItem('railList');
                railList = [];
                renderList();
            }
        }

        // 4. 同期機能 (Firebaseを使用)
        function syncSave() {
            db.ref('sync_rail_data').set(railList)
                .then(() => alert("クラウドに送信（保存）しました！"))
                .catch(e => alert("エラー: " + e.message));
        }

        function syncLoad() {
            db.ref('sync_rail_data').once('value').then((snap) => {
                const data = snap.val();
                if(data) {
                    railList = data;
                    saveToLocal();
                    renderList();
                    alert("クラウドから受信（読込）しました！");
                } else {
                    alert("クラウドにデータがありません");
                }
            });
        }

        // 初期実行
        renderList();
    </script>
</body>
</html>
