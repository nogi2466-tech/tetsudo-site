<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>tetsudo-site6</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
  * { box-sizing: border-box; }
  body {
    margin: 0;
    font-family: system-ui, sans-serif;
    background: #f5f5f5;
  }

  .container { background: #fff; min-height: 100vh; }

  header {
    background: #1976d2;
    color: #fff;
    padding: 12px 16px;
    text-align: center;
  }

  .tabs {
    display: flex;
    flex-direction: row;
    overflow-x: auto;
    white-space: nowrap;
    gap: 6px;
    padding: 10px;
  }

  .tab {
    padding: 8px 14px;
    border-radius: 18px;
    cursor: pointer;
    font-size: 14px;
    color: #fff;
    border: 1px solid #fff4;
    white-space: nowrap;
    transition: 0.2s;
  }

  .tab.active {
    background: #fff;
    color: #1976d2;
    font-weight: 600;
  }

  .tab[data-tab="京王"] { background: #b4007f; }
  .tab[data-tab="JR"] { background: #4caf50; }
  .tab[data-tab="大手私鉄"] { background: #ffd54f; color:#333; }
  .tab[data-tab="地下鉄"] { background: #ff9800; }
  .tab[data-tab="その他"] { background: #9e9e9e; }
  .tab[data-tab="資料"] { background: #ba68c8; }
  .tab[data-tab="画像"] { background: #42a5f5; }
  .tab[data-tab="同期・管理"] { background: #000; }

  .search-bar {
    padding: 10px 16px;
    background: #e3f2fd;
  }
  .search-bar input {
    width: 100%;
    padding: 8px;
    border-radius: 6px;
    border: 1px solid #bbb;
    font-size: 15px;
  }

  main { padding: 12px 16px 24px; }

  .section-title {
    font-size: 18px;
    margin: 10px 0 16px;
    text-align: center;
  }

  .card-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
    max-height: 70vh;
    overflow-y: auto;
    padding-right: 4px;
  }

  .card {
    background: #fff;
    border-radius: 12px;
    padding: 16px;
    border-left: 6px solid #ccc;
    box-shadow: 0 2px 6px rgba(0,0,0,0.08);
    transition: 0.2s;
    cursor: pointer;
  }

  .card:hover { background: #f7faff; transform: translateY(-2px); }

  .cat-京王 { border-left-color: #b4007f; }
  .cat-JR { border-left-color: #4caf50; }
  .cat-大手私鉄 { border-left-color: #ffd54f; }
  .cat-地下鉄 { border-left-color: #ff9800; }
  .cat-その他 { border-left-color: #9e9e9e; }
  .cat-資料 { border-left-color: #ba68c8; }
  .cat-画像 { border-left-color: #42a5f5; }

  .card-category { font-size: 13px; font-weight: 700; margin-bottom: 6px; }
  .card-title { font-size: 22px; font-weight: 700; margin-bottom: 8px; }
  .card-detail { font-size: 16px; color: #444; white-space: pre-wrap; }

  button {
    background: #1976d2;
    color: #fff;
    border: none;
    padding: 12px 18px;
    font-size: 16px;
    border-radius: 8px;
    cursor: pointer;
    font-weight: 600;
    width: 100%;
    margin-top: 10px;
  }

  button:hover {
    background: #0f5bb5;
  }

  .settings {
    max-width: 520px;
    margin: 0 auto;
    text-align: center;
  }

  .settings label { display:block; margin:10px 0 4px; }

  .settings input, .settings textarea, .settings select {
    width:100%; padding:8px; border-radius:6px; border:1px solid #bbb;
  }

  .settings textarea {
    min-height: 120px;
    max-height: 120px;
    resize: none;
  }

  .hidden { display:none; }
</style>
</head>

<body>
<div class="container">

<header>
  <h1>tetsudo-site6</h1>
</header>

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
  <input type="text" id="searchInput" placeholder="タイトルで検索…">
</div>

<main>
  <div id="listSection">
    <div class="section-title" id="sectionTitle">すべて</div>
    <div class="card-list" id="cardList"></div>
  </div>

  <div id="settingsSection" class="hidden">
    <div class="section-title">同期・管理</div>

    <div class="settings" id="passwordBlock">
      <label>編集パスワード</label>
      <input type="password" id="adminPass">
      <button id="passSubmit">送信</button>
    </div>

    <div class="settings hidden" id="formBlock">
      <button id="backToList">◀ 戻る</button>
      <div class="section-title" id="formTitle">新規URL追加</div>

      <label>タイトル</label>
      <input type="text" id="newTitle">

      <label>URL</label>
      <input type="text" id="newURL">

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

      <button id="addSubmit">追加</button>

      <hr>
      <button id="cloudLoad">クラウド受信</button>
      <button id="cloudSave">クラウド保存</button>
    </div>
  </div>
</main>

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
  let searchText = "";
  let adminMode = false;
  let editIndex = null;
  let viewMode = "list";

  const tabsEl = document.getElementById("tabs");
  const searchInput = document.getElementById("searchInput");
  const cardList = document.getElementById("cardList");
  const sectionTitle = document.getElementById("sectionTitle");
  const listSection = document.getElementById("listSection");
  const settingsSection = document.getElementById("settingsSection");

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

  signInAnonymously(auth).then(() => loadFromFirebase());

  function loadFromFirebase() {
    const dataRef = ref(db, "urlData");
    onValue(dataRef, (snapshot) => {
      items = snapshot.val() || [];
      render();
    });
  }

  function saveToFirebase() {
    const dataRef = ref(db, "urlData");
    set(dataRef, items);
  }

  function updateView() {
    if (viewMode === "list") {
      listSection.classList.remove("hidden");
      settingsSection.classList.add("hidden");
      sectionTitle.classList.remove("hidden");
    } else {
      listSection.classList.add("hidden");
      settingsSection.classList.remove("hidden");
      sectionTitle.classList.add("hidden");
    }
  }

  function resetForm() {
    editIndex = null;
    newTitle.value = "";
    newURL.value = "";
    newDetail.value = "";
    newCategory.value = "京王";
    addSubmit.textContent = "追加";
  }

  function render() {
    sectionTitle.textContent = currentTab;

    const filtered = items.filter(item => {
      if (currentTab === "すべて") {
        if (item.category === "資料") return false;
        if (item.category === "画像") return false;
        return true;
      }
      if (item.category !== currentTab) return false;
      if (!searchText) return true;
      return (item.title || "").toLowerCase().includes(searchText.toLowerCase());
    });

    filtered.sort((a, b) => {
      const ta = a.title || "";
      const tb = b.title || "";
      const numA = ta.match(/^\d+$/);
      const numB = tb.match(/^\d+$/);
      if (numA && numB) return Number(ta) - Number(tb);
      if (numA) return -1;
      if (numB) return 1;
      return ta.localeCompare(tb, "ja");
    });

    cardList.innerHTML = "";

    filtered.forEach(item => {
      const card = document.createElement("div");
      card.className = "card cat-" + item.category;

      const cat = document.createElement("div");
      cat.className = "card-category";
      cat.textContent = item.category;

      const title = document.createElement("div");
      title.className = "card-title";
      title.textContent = item.title;

      const detail = document.createElement("div");
      detail.className = "card-detail";
      detail.textContent = item.detail;
      detail.style.whiteSpace = "pre-wrap";

      card.appendChild(cat);
      card.appendChild(title);
      card.appendChild(detail);

      card.onclick = () => {
        if (item.url) window.open(item.url, "_blank");
      };

      if (adminMode) {
        const btns = document.createElement("div");
        btns.className = "edit-buttons";

        const editBtn = document.createElement("button");
        editBtn.textContent = "編集";
        editBtn.onclick = (e) => {
          e.stopPropagation();
          startEdit(item);
        };

        const delBtn = document.createElement("button");
        delBtn.textContent = "削除";
        delBtn.onclick = (e) => {
          e.stopPropagation();
          deleteItem(item);
        };

        btns.appendChild(editBtn);
        btns.appendChild(delBtn);
        card.appendChild(btns);
      }

      cardList.appendChild(card);
    });
  }

  function startEdit(item) {
    adminMode = true;
    editIndex = items.indexOf(item);
    newTitle.value = item.title;
    newURL.value = item.url;
    newDetail.value = item.detail;
    newCategory.value = item.category;
    addSubmit.textContent = "上書き保存";
    viewMode = "settings";
    updateView();
  }

  function deleteItem(item) {
    if (!confirm("削除しますか？")) return;
    const index = items.indexOf(item);
    items.splice(index, 1);
    saveToFirebase();
    render();
  }

  tabsEl.addEventListener("click", (e) => {
    const tab = e.target.closest(".tab");
    if (!tab) return;
    document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
    tab.classList.add("active");
    currentTab = tab.dataset.tab;
    if (currentTab === "同期・管理") {
      viewMode = "settings";
      updateView();
      return;
    }
    viewMode = "list";
    updateView();
    render();
  });

  searchInput.addEventListener("input", () => {
    searchText = searchInput.value.trim();
    render();
  });

  passSubmit.addEventListener("click", () => {
    if (adminPass.value === PASSWORD) {
      adminMode = true;
      adminPass.value = "";
      passwordBlock.classList.add("hidden");
      formBlock.classList.remove("hidden");
    } else {
      alert("パスワードが違います");
    }
  });

  addSubmit.addEventListener("click", () => {
    const title = newTitle.value.trim();
    const url = newURL.value.trim();
    const detail = newDetail.value.trim();
    const category = newCategory.value;

    if (!title || !url) {
      alert("タイトルとURLは必須です");
      return;
    }

    const newItem = { title, url, detail, category };

    if (editIndex !== null) {
      items[editIndex] = newItem;
    } else {
      items.push(newItem);
    }

    saveToFirebase();
    alert("保存しました");

    resetForm();
    viewMode = "list";
    updateView();
    render();
  });

  backToList.addEventListener("click", () => {
    resetForm();
    viewMode = "list";
    updateView();
    render();
  });

  cloudLoad.addEventListener("click", () => {
    loadFromFirebase();
    alert("クラウドから読み込みました");
  });

  cloudSave.addEventListener("click", () => {
    saveToFirebase();
    alert("クラウドに保存しました");
  });

  updateView();
  render();
</script>

</body>
</html>
