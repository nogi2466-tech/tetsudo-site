<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tetsudo Cloud Link</title>
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { font-family: sans-serif; margin: 0; background: #f0f2f5; padding-bottom: 50px; }
        header { background: #333; color: white; padding: 15px 0; position: sticky; top: 0; z-index: 100; text-align: center; }
        nav { display: flex; justify-content: center; gap: 8px; margin-top: 10px; }
        button { padding: 10px 15px; cursor: pointer; border: none; border-radius: 5px; background: #444; color: white; font-weight: bold; }
        button.active { background: #e50914; }
        
        .container { max-width: 600px; margin: 20px auto; padding: 0 15px; }
        
        #edit-panel { display: none; background: #fffbe6; padding: 15px; border: 2px dashed #ffe58f; border-radius: 8px; margin-bottom: 20px; }
        .input-group { display: flex; flex-direction: column; gap: 10px; margin-top: 10px; }
        input { padding: 12px; border: 1px solid #ccc; border-radius: 4px; font-size: 16px; }

        .tab-content { display: none; flex-direction: column; gap: 12px; }
        
        /* リンクカードのデザイン */
        .link-card { background: white; padding: 18px; border-radius: 10px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); display: flex; align-items: center; justify-content: space-between; gap: 15px; }
        .link-title { font-weight: bold; color: #333; flex: 1; font-size: 16px; line-height: 1.4; }
        
        .open-btn { background: #e50914; color: white; padding: 10px 20px; border-radius: 6px; text-decoration: none; font-size: 14px; font-weight: bold; white-space: nowrap; }
        .del-btn { background: #666; color: white; border: none; padding: 10px; border-radius: 6px; cursor: pointer; font-size: 12px; }
    </style>
</head>
<body>

<header>
    <div style="font-size: 20px; font-weight: bold;">鉄道動画ポータル</div>
    <nav>
        <button onclick="showTab('keio')" id="btn-keio">京王線</button>
        <button onclick="showTab('jr')" id="btn-jr">JR</button>
        <button onclick="showTab('other')" id="btn-other">その他</button>
        <button onclick="unlockAdmin()" id="btn-lock">🔒 設定</button>
    </nav>
</header>

<div class="container">
    <div id="edit-panel">
        <strong>【<span id="label">京王線</span>】にリンクを追加</strong>
        <div class="input-group">
            <input type="text" id="new-title" placeholder="タイトル（例：京王8000系）">
            <input type="text" id="new-url" placeholder="YouTubeのURL">
            <button onclick="pushToCloud()" style="background: #28a745; padding: 12px; color:white; border:none; border-radius:6px; font-weight:bold; cursor:pointer;">クラウドに保存</button>
        </div>
    </div>

    <div id="keio" class="tab-content"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    // --- あなたのFirebase情報をセット済みです ---
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxEOJ0GCtCEnM2xqjMcr-Rc",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    let currentTab = 'keio';
    let isAdmin = false;

    // データのリアルタイム読み込み
    db.ref('links').on('value', (s) => render(s.val() || {}));

    function pushToCloud() {
        const t = document.getElementById('new-title').value.trim();
        const u = document.getElementById('new-url').value.trim();
        if(!t || !u) return alert("タイトルとURLを両方入力してください");

        db.ref('links/' + currentTab).once('value', (s) => {
            let list = s.val() || [];
            list.push({title: t, url: u});
            db.ref('links/' + currentTab).set(list);
            document.getElementById('new-title').value = '';
            document.getElementById('new-url').value = '';
        });
    }

    function deleteLink(tab, index) {
        if(!confirm("この項目を削除しますか？")) return;
        db.ref('links/' + tab).once('value', (s) => {
            let list = s.val() || [];
            list.splice(index, 1);
            db.ref('links/' + tab).set(list);
        });
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
                            <div style="display:flex; gap:8px; align-items:center;">
                                <a href="${item.url}" target="_blank" class="open-btn">開く</a>
                                ${isAdmin ? `<button onclick="deleteLink('${tab}', ${index})" class="del-btn">削除</button>` : ''}
                            </div>
                        </div>`;
                });
            }
        });
    }

    function showTab(t) {
        currentTab = t;
        document.querySelectorAll('.tab-content').forEach(el => el.style.display = 'none');
        document.querySelectorAll('nav button').forEach(el => el.classList.remove('active'));
        document.getElementById(t).style.display = 'flex';
        document.getElementById('btn-'+t).classList.add('active');
        document.getElementById('label').innerText = document.getElementById('btn-'+t).innerText;
    }

    function unlockAdmin() {
        if (isAdmin || prompt("パスワード(0829)を入力") === "0829") {
            isAdmin = !isAdmin;
            document.getElementById('edit-panel').style.display = isAdmin ? 'block' : 'none';
            document.getElementById('btn-lock').innerText = isAdmin ? '🔓 編集終了' : '🔒 設定';
            db.ref('links').once('value', s => render(s.val() || {}));
        }
    }

    showTab('keio');
</script>
</body>
</html>
