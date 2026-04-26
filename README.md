<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Cloud Portal</title>
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { font-family: -apple-system, sans-serif; background: #fafafa; margin: 0; padding-bottom: 60px; }
        header { background: #fff; border-bottom: 1px solid #ddd; padding: 15px; position: sticky; top: 0; text-align: center; }
        nav { display: flex; justify-content: center; gap: 5px; margin-top: 10px; }
        button { border: 1px solid #ddd; background: #fff; padding: 8px 12px; border-radius: 20px; cursor: pointer; font-size: 14px; }
        button.active { background: #007aff; color: #fff; border-color: #007aff; }
        .container { max-width: 500px; margin: auto; padding: 15px; }
        .link-card { background: #fff; border: 1px solid #eee; padding: 15px; border-radius: 10px; display: flex; align-items: center; justify-content: space-between; margin-bottom: 10px; }
        .link-title { font-weight: 500; font-size: 15px; }
        .open-btn { color: #007aff; text-decoration: none; font-weight: 600; font-size: 14px; }
        #edit-panel { display: none; background: #f0f0f0; padding: 15px; border-radius: 10px; margin-bottom: 20px; }
        input { width: 100%; padding: 10px; margin-bottom: 10px; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box; }
    </style>
</head>
<body>

<header>
    <strong>ポータルサイト</strong>
    <nav>
        <button onclick="showTab('keio')" id="btn-keio">京王線</button>
        <button onclick="showTab('jr')" id="btn-jr">JR</button>
        <button onclick="showTab('other')" id="btn-other">その他</button>
        <button onclick="unlockAdmin()" id="btn-lock">⚙️</button>
    </nav>
</header>

<div class="container">
    <div id="edit-panel">
        <input type="text" id="new-title" placeholder="タイトル">
        <input type="text" id="new-url" placeholder="URL">
        <button onclick="pushToCloud()" style="width:100%; background:#007aff; color:#fff;">保存</button>
    </div>
    <div id="keio" class="tab-content"></div>
    <div id="jr" class="tab-content"></div>
    <div id="other" class="tab-content"></div>
</div>

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyAe_KxKH-06cxEOJ0GCtCEnM2xqjMcr-Rc",
        databaseURL: "https://firebaseio.com",
        projectId: "tetsudo"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    let currentTab = 'keio';
    let isAdmin = false;

    db.ref('links').on('value', (s) => render(s.val() || {}));

    function pushToCloud() {
        const t = document.getElementById('new-title').value;
        const u = document.getElementById('new-url').value;
        if(!t || !u) return;
        db.ref('links/' + currentTab).once('value', (s) => {
            let list = s.val() || [];
            list.push({title: t, url: u});
            db.ref('links/' + currentTab).set(list);
            document.getElementById('new-title').value = '';
            document.getElementById('new-url').value = '';
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
                            <div style="display:flex; gap:15px; align-items:center;">
                                <a href="${item.url}" target="_blank" class="open-btn">開く</a>
                                ${isAdmin ? `<button onclick="deleteLink('${tab}', ${index})" style="border:none; color:red; font-size:12px;">× 削除</button>` : ''}
                            </div>
                        </div>`;
                });
            }
        });
    }

    function deleteLink(tab, index) {
        db.ref('links/' + tab).once('value', (s) => {
            let list = s.val() || [];
            list.splice(index, 1);
            db.ref('links/' + tab).set(list);
        });
    }

    function showTab(t) {
        currentTab = t;
        document.querySelectorAll('.tab-content').forEach(el => el.style.display = 'none');
        document.querySelectorAll('nav button').forEach(el => el.classList.remove('active'));
        document.getElementById(t).style.display = 'block';
        document.getElementById('btn-'+t).classList.add('active');
    }

    function unlockAdmin() {
        if (isAdmin || prompt("Pass?") === "0829") {
            isAdmin = !isAdmin;
            document.getElementById('edit-panel').style.display = isAdmin ? 'block' : 'none';
        }
    }
    showTab('keio');
</script>
</body>
</html>
