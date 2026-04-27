<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>tetsudo</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin: 0; padding: 20px; background: #f4f4f4; }
        .main-container { background: white; max-width: 600px; margin: 0 auto; padding: 20px; border: 2px solid #000; min-height: 90vh; }
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; border-bottom: 2px solid #000; padding-bottom: 10px; }
        .logo { font-size: 32px; font-weight: bold; }
        .nav-tabs { display: flex; justify-content: center; gap: 5px; margin-bottom: 20px; flex-wrap: wrap; }
        .nav-tabs button { padding: 10px; border: 2px solid #000; background: #fff; cursor: pointer; flex: 1; min-width: 80px; }
        .nav-tabs button.active { background: #000; color: #fff; }
        
        .content-section { display: none; text-align: left; }
        .content-section.active { display: block; }

        /* 利用ルール */
        #rule-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); display: flex; justify-content: center; align-items: center; z-index: 2000; }
        .rule-box { background: white; padding: 20px; border: 3px solid #000; width: 80%; max-width: 400px; }

        /* 入力系 */
        input, select { width: 100%; padding: 10px; margin: 5px 0 15px 0; border: 2px solid #000; box-sizing: border-box; }
        .btn-black { background: #000; color: #fff; padding: 10px 20px; border: none; cursor: pointer; width: 100%; font-weight: bold; }
        
        /* リスト */
        .url-list { list-style: none; padding: 0; }
        .url-list li { border-bottom: 1px solid #ccc; padding: 10px 0; display: flex; justify-content: space-between; }
        .del-btn { color: red; cursor: pointer; font-weight: bold; display: none; }
        .admin-mode .del-btn { display: inline; }

        /* 閲覧者表示 */
        .visitor-tag { display: inline-block; background: #eee; padding: 3px 8px; border-radius: 5px; margin: 3px; font-size: 12px; border: 1px solid #000; }
    </style>
</head>
<body>

<!-- 利用ルール -->
<div id="rule-overlay">
    <div class="rule-box">
        <h2>ご利用ルール</h2>
        <p>1. 鉄道情報を大切に扱うこと</p>
        <p>2. 誹謗中傷を行わないこと</p>
        <p>3. 正しいURLを共有すること</p>
        <label><input type="checkbox" id="rule-agree"> 同意します</label>
        <button class="btn-black" onclick="acceptRules()">サイトに入る</button>
    </div>
</div>

<div class="main-container">
    <div class="header">
        <div class="logo">tetsudo</div>
        <div id="user-display" style="font-size: 11px; text-align: right;"></div>
    </div>

    <div class="nav-tabs">
        <button onclick="showTab('keio')" id="tab-keio">京王</button>
        <button onclick="showTab('jr')" id="tab-jr">JR</button>
        <button onclick="showTab('other')" id="tab-other">その他</button>
        <button onclick="showTab('setting')" id="tab-setting">設定/管理</button>
    </div>

    <!-- コンテンツエリア -->
    <div id="keio" class="content-section"><h3>京王 一覧</h3><ul class="url-list" id="list-keio"></ul></div>
    <div id="jr" class="content-section"><h3>JR 一覧</h3><ul class="url-list" id="list-jr"></ul></div>
    <div id="other" class="content-section"><h3>その他 一覧</h3><ul class="url-list" id="list-other"></ul></div>

    <!-- 設定・管理画面 -->
    <div id="setting" class="content-section">
        <div id="account-section">
            <h3>アカウント登録</h3>
            <input type="email" id="acc-email" placeholder="メールアドレスを入力">
            <input type="text" id="acc-name" placeholder="表示名を入力">
            <button class="btn-black" onclick="register()">登録する</button>
        </div>

        <div id="logout-section" style="display:none;">
            <p id="welcome-msg"></p>
            <button onclick="logout()" style="color: grey; cursor: pointer; border: none; background: none; text-decoration: underline;">ログアウト</button>
        </div>

        <hr style="margin: 20px 0;">

        <div id="admin-auth">
            <h3>管理者用 (パスワード: 0829)</h3>
            <input type="password" id="admin-pw" placeholder="パスワードを入力">
            <button class="btn-black" onclick="authAdmin()">管理者ログイン</button>
        </div>

        <div id="admin-panel" style="display:none; background: #fffde7; padding: 15px; border: 2px dashed #000;">
            <h4>現在の観覧者（履歴）</h4>
            <div id="visitor-list"></div>
            <hr>
            <h4>URL追加</h4>
            <select id="add-cat"><option value="keio">京王</option><option value="jr">JR</option><option value="other">その他</option></select>
            <input type="text" id="add-title" placeholder="タイトル">
            <input type="text" id="add-url" placeholder="URL">
            <button class="btn-black" onclick="addUrl()">追加</button>
            <button onclick="closeAdmin()" style="margin-top:10px; font-size:12px;">管理者モードを閉じる</button>
        </div>
    </div>
</div>

<script>
    // ルール同意
    function acceptRules() {
        if(document.getElementById('rule-agree').checked) {
            document.getElementById('rule-overlay').style.display = 'none';
            logVisit();
        }
    }

    // タブ切り替え
    function showTab(tabId) {
        document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
        document.querySelectorAll('.nav-tabs button').forEach(b => b.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        document.getElementById('tab-' + tabId).classList.add('active');
    }

    // アカウント登録
    function register() {
        const email = document.getElementById('acc-email').value;
        const name = document.getElementById('acc-name').value;
        if(!email.includes('@') || !name) return alert("正しく入力してください");
        
        localStorage.setItem('tetsudo_user', JSON.stringify({email, name}));
        alert("登録完了しました");
        location.reload();
    }

    // ログアウト
    function logout() {
        localStorage.removeItem('tetsudo_user');
        alert("ログアウトしました");
        location.reload();
    }

    // 訪問記録（擬似リアルタイム用）
    function logVisit() {
        const user = JSON.parse(localStorage.getItem('tetsudo_user') || '{"name":"ゲスト"}');
        let visitors = JSON.parse(localStorage.getItem('v_logs') || '[]');
        // 重複排除して最新にする
        visitors = visitors.filter(v => v.name !== user.name);
        visitors.push({name: user.name, time: new Date().toLocaleTimeString()});
        localStorage.setItem('v_logs', JSON.stringify(visitors.slice(-10))); // 直近10名保存
    }

    // 管理者認証
    function authAdmin() {
        if(document.getElementById('admin-pw').value === "0829") {
            document.getElementById('admin-panel').style.display = 'block';
            document.getElementById('admin-auth').style.display = 'none';
            document.body.classList.add('admin-mode');
            renderVisitors();
        } else { alert("パスワードが違います"); }
    }

    function closeAdmin() {
        document.getElementById('admin-panel').style.display = 'none';
        document.getElementById('admin-auth').style.display = 'block';
        document.body.classList.remove('admin-mode');
    }

    function renderVisitors() {
        const visitors = JSON.parse(localStorage.getItem('v_logs') || '[]');
        document.getElementById('visitor-list').innerHTML = visitors.map(v => `<span class="visitor-tag">${v.name} (${v.time})</span>`).join('');
    }

    // URL管理
    function addUrl() {
        const cat = document.getElementById('add-cat').value;
        const title = document.getElementById('add-title').value;
        const url = document.getElementById('add-url').value;
        if(!title || !url) return;

        const list = JSON.parse(localStorage.getItem('t_urls') || '[]');
        list.push({id: Date.now(), cat, title, url});
        localStorage.setItem('t_urls', JSON.stringify(list));
        renderUrls();
    }

    function delUrl(id) {
        let list = JSON.parse(localStorage.getItem('t_urls') || '[]');
        list = list.filter(item => item.id !== id);
        localStorage.setItem('t_urls', JSON.stringify(list));
        renderUrls();
    }

    function renderUrls() {
        const list = JSON.parse(localStorage.getItem('t_urls') || '[]');
        ['keio', 'jr', 'other'].forEach(c => {
            const el = document.getElementById('list-' + c);
            el.innerHTML = "";
            list.filter(i => i.cat === c).forEach(i => {
                el.innerHTML += `<li><a href="${i.url}" target="_blank">${i.title}</a> <span class="del-btn" onclick="delUrl(${i.id})"> [削除]</span></li>`;
            });
        });
    }

    // 初期化
    const savedUser = JSON.parse(localStorage.getItem('tetsudo_user'));
    if(savedUser) {
        document.getElementById('account-section').style.display = 'none';
        document.getElementById('logout-section').style.display = 'block';
        document.getElementById('welcome-msg').innerText = `ようこそ、${savedUser.name}さん`;
        document.getElementById('user-display').innerText = `ログイン：${savedUser.name}`;
    }

    renderUrls();
    showTab('keio');
</script>

</body>
</html>

