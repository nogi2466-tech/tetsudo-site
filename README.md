<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>tetsudo-site6</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
  body {
    margin: 0;
    font-family: system-ui, sans-serif;
    background: #f5f5f5;
  }

  header {
    background: #1976d2;
    color: #fff;
    padding: 12px;
    text-align: center;
  }

  /* ▼ タブ（横並び固定＋横スクロール） */
  .tabs {
    display: flex;
    overflow-x: auto;
    white-space: nowrap;
    gap: 6px;
    padding: 10px;
  }

  .tab {
    padding: 8px 14px;
    border-radius: 18px;
    cursor: pointer;
    color: #fff;
    font-size: 14px;
  }

  .tab.active {
    background: #fff;
    color: #1976d2;
    font-weight: bold;
  }

  .tab[data-tab="京王"] { background:#b4007f; }
  .tab[data-tab="JR"] { background:#4caf50; }
  .tab[data-tab="大手私鉄"] { background:#ffd54f; color:#333; }
  .tab[data-tab="地下鉄"] { background:#ff9800; }
  .tab[data-tab="その他"] { background:#9e9e9e; }
  .tab[data-tab="資料"] { background:#ba68c8; }
  .tab[data-tab="画像"] { background:#42a5f5; }
  .tab[data-tab="同期・管理"] { background:#000; }

  .search-bar { padding: 10px; background:#e3f2fd; }
  .search-bar input {
    width: 100%;
    padding: 8px;
    border-radius: 6px;
    border: 1px solid #bbb;
  }

  .card-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
    max-height: 70vh;
    overflow-y: auto;
    padding: 10px;
  }

  .card {
    background: #fff;
    border-radius: 12px;
    padding: 16px;
    border-left: 6px solid #ccc;
  }

  .cat-京王 { border-left-color:#b4007f; }
  .cat-JR { border-left-color:#4caf50; }
  .cat-大手私鉄 { border-left-color:#ffd54f; }
  .cat-地下鉄 { border-left-color:#ff9800; }
  .cat-その他 { border-left-color:#9e9e9e; }
  .cat-資料 { border-left-color:#ba68c8; }
  .cat-画像 { border-left-color:#42a5f5; }

  .card-category { font-size:13px; font-weight:bold; }
  .card-title { font-size:22px; font-weight:bold; margin-top:6px; }
  .card-detail { white-space:pre-wrap; margin-top:6px; }

  /* ▼ 編集画面のボタンだけデザイン変更（小さめ） */
  .main-btn {
    background:#1976d2;
    color:#fff;
    border:none;
    padding:10px 14px;
    font-size:15px;
    border-radius:6px;
    cursor:pointer;
    width: 60%;
    margin: 12px auto;
    display:block;
  }
  .main-btn:hover { background:#0f5bb5; }

  /* ▼ 他のボタンは前のまま */
  .edit-btn { background:#1976d2; color:#fff; padding:6px 10px; border:none; border-radius:6px; }
  .delete-btn { background:#d32f2f; color:#fff; padding:6px 10px; border:none; border-radius:6px; }

  .settings { max-width:500px; margin:0 auto; padding:10px; }
  .settings input, .settings textarea, .settings select {
    width:100%; padding:8px; border-radius:6px; border:1px solid #bbb;
  }

  .settings textarea {
    min-height:120px;
    max-height:120px;
    resize:none;
  }

  .hidden { display:none; }
</style>
</head>

<body>

<header><h1>tetsudo-site6</h1></header>

<div class="tabs" id="tabs">
  <div class="tab active" data-tab="すべて">すべて</div>
  <div class="tab" data-tab="京王">京王</div>
  <div class="tab" data-tab="JR">JR</div>
  <div class="tab" data-tab="大手私鉄">大手私鉄</div>
  <div class="tab" data-tab="地下鉄">地下鉄</div>
  <div class="tab" data-tab="その他">その他</div>
  <div class="tab" data-tab="資料">資料</div>
  <div class="tab" data-tab="画像">画像</div>
  <div class="tab" data-tab="同期・管理">同期・管理</div>
</div>

<div class="search-bar">
  <input id="searchInput" placeholder="検索…">
</div>

<div id="listSection">
  <div class="card-list" id="cardList"></div>
</div>

<div id="settingsSection" class="hidden">
  <div class="settings">

    <div id="passwordBlock">
      <label>編集パスワード</label>
      <input type="password" id="adminPass">
      <button id="passSubmit" class="main-btn">送信</button>
    </div>

    <div id="formBlock" class="hidden">
      <button id="backToList" class="edit-btn">◀ 戻る</button>

      <label>タイトル</label>
      <input id="newTitle">

      <label>URL</label>
      <input id="newURL">

      <label>詳細</label>
      <textarea id="newDetail"></textarea>

      <label>カテゴリ</label>
      <select id="newCategory">
        <option value="京王">京王</option>
        <option value="JR">JR</option>
        <option value="大手私鉄">大手私鉄</option>
        <option value="地下鉄">地下鉄</option>
        <option value="その他">その他</option>
        <option value="資料">資料</option>
        <option value="画像">画像</option>
      </select>

      <!-- ▼ この2つだけデザイン変更 -->
      <button id="addSubmit" class="main-btn">追加</button>

      <hr>
      <button id="cloudLoad" class="edit-btn">クラウド受信</button>
      <button id="cloudSave" class="edit-btn">クラウド保存</button>
    </div>

  </div>
</div>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-app.js";
  import { getDatabase, ref, set, onValue } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-database.js";
  import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-auth.js";

  const firebaseConfig = {
    apiKey: "AIzaSyD55Pawag1UichGwM-Uxddivb8lFr7QOU8",
    authDomain: "tetsudo-site6.firebaseapp.com",
    databaseURL: "https://tetsudo-site6-default-rtdb.firebaseio.com",
    projectId: "tetsudo-site6",
    storageBucket: "tetsudo-site6.appspot.com",
    messagingSenderId: "563943849207",
    appId: "1:563943849207:web:1c813365201cb431d6e7f2"
  };

  const PASSWORD = "0829";

  const app = initializeApp(firebaseConfig);
  const db = getDatabase();
  const auth = getAuth();

  let items = [];
  let currentTab = "すべて";
  let adminMode = false;
  let editIndex = null;

  const tabsEl = document.getElementById("tabs");
  const cardList = document.getElementById("cardList");
  const searchInput = document.getElementById("searchInput");

  const adminPass = document.getElementById("adminPass");
  const passSubmit = document.getElementById("passSubmit");
  const passwordBlock = document.getElementById("passwordBlock");
  const formBlock = document.getElementById("formBlock");

  const newTitle = document.getElementById("newTitle");
  const newURL = document.getElementById("newURL");
  const newDetail = document.getElementById("newDetail");
  const newCategory = document.getElementById("newCategory");
  const addSubmit = document.getElementById("addSubmit");
  const backToList = document.getElementById("backToList");

  const cloudLoad = document.getElementById("cloudLoad");
  const cloudSave = document.getElementById("cloudSave");

  signInAnonymously(auth).then(loadFromFirebase);

  function loadFromFirebase() {
    onValue(ref(db, "urlData"), snap => {
      items = snap.val() || [];
      render();
    });
  }

  function saveToFirebase() {
    set(ref(db, "urlData"), items);
  }

  function render() {
    cardList.innerHTML = "";

    const filtered = items.filter(item => {
      if (currentTab === "すべて") {
        return !["資料", "画像"].includes(item.category);
      }
      return item.category === currentTab;
    });

    filtered.forEach(item => {
      const card = document.createElement("div");
      card.className = "card cat-" + item.category;

      card.innerHTML = `
        <div class="card-category">${item.category}</div>
        <div class="card-title">${item.title}</div>
        <div class="card-detail">${item.detail}</div>
      `;

      card.onclick = () => window.open(item.url, "_blank");

      if (adminMode) {
        const editBtn = document.createElement("button");
        editBtn.className = "edit-btn";
        editBtn.textContent = "編集";
        editBtn.onclick = e => { e.stopPropagation(); startEdit(item); };

        const delBtn = document.createElement("button");
        delBtn.className = "delete-btn";
        delBtn.textContent = "削除";
        delBtn.onclick = e => {
          e.stopPropagation();
          items.splice(items.indexOf(item), 1);
          saveToFirebase();
          render();
        };

        card.appendChild(editBtn);
        card.appendChild(delBtn);
      }

      cardList.appendChild(card);
    });
  }

  tabsEl.onclick = e => {
    const tab = e.target.closest(".tab");
    if (!tab) return;

    document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
    tab.classList.add("active");

    currentTab = tab.dataset.tab;

    if (currentTab === "同期・管理") {
      document.getElementById("listSection").classList.add("hidden");
      document.getElementById("settingsSection").classList.remove("hidden");
    } else {
      document.getElementById("listSection").classList.remove("hidden");
      document.getElementById("settingsSection").classList.add("hidden");
      render();
    }
  };

  searchInput.oninput = () => render();

  passSubmit.onclick = () => {
    if (adminPass.value === PASSWORD) {
      adminMode = true;
      passwordBlock.classList.add("hidden");
      formBlock.classList.remove("hidden");
    } else alert("パスワードが違います");
  };

  addSubmit.onclick = () => {
    const item = {
      title: newTitle.value,
      url: newURL.value,
      detail: newDetail.value,
      category: newCategory.value
    };

    if (editIndex !== null) {
      items[editIndex] = item;
    } else {
      items.push(item);
    }

    saveToFirebase();
    alert("保存しました");

    editIndex = null;
    newTitle.value = "";
    newURL.value = "";
    newDetail.value = "";
    newCategory.value = "京王";

    document.getElementById("listSection").classList.remove("hidden");
    document.getElementById("settingsSection").classList.add("hidden");
    render();
  };

  backToList.onclick = () => {
    editIndex = null;
    document.getElementById("listSection").classList.remove("hidden");
    document.getElementById("settingsSection").classList.add("hidden");
  };

  cloudLoad.onclick = () => { loadFromFirebase(); alert("読み込み完了"); };
  cloudSave.onclick = () => { saveToFirebase(); alert("保存完了"); };

  function startEdit(item) {
    editIndex = items.indexOf(item);
    newTitle.value = item.title;
    newURL.value = item.url;
    newDetail.value = item.detail;
    newCategory.value = item.category;
    addSubmit.textContent = "上書き保存";
    document.getElementById("listSection").classList.add("hidden");
    document.getElementById("settingsSection").classList.remove("hidden");
  }
</script>

</body>
</html>
