<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>tetsudo-site6</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <style>
    * { box-sizing: border-box; }
    html, body {
      margin: 0;
      padding: 0;
      overflow-x: hidden;
    }
    body {
      font-family: system-ui, sans-serif;
      background: #f5f5f5;
    }

    .container {
      width: 100%;
      max-width: 100%;
      min-height: 100vh;
      background: #fff;
    }

    header {
      background: #1976d2;
      color: #fff;
      padding: 12px 16px;
    }

    header h1 {
      margin: 0 0 8px;
      font-size: 22px;
      text-align: center;
    }

    .tabs {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 6px;
    }

    .tab {
      padding: 8px 14px;
      border-radius: 18px;
      cursor: pointer;
      font-size: 14px;
      color: #fff;
      border: 1px solid #fff4;
      transition: 0.2s;
      white-space: nowrap;
    }

    .tab[data-tab="京王"] { background: #b4007f; }
    .tab[data-tab="JR"] { background: #4caf50; }
    .tab[data-tab="大手私鉄"] { background: #ffd54f; color:#333; }
    .tab[data-tab="その他"] { background: #9e9e9e; }
    .tab[data-tab="資料"] { background: #ba68c8; }
    .tab[data-tab="画像"] { background: #42a5f5; }

    .tab.active {
      background: #fff;
      color: #1976d2;
      font-weight: 600;
    }

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

    main {
      padding: 12px 16px 24px;
    }

    .section-title {
      font-size: 18px;
      margin: 10px 0 16px;
      text-align: center;
    }

    .card-list {
      display: flex;
      flex-direction: column;
      gap: 10px;
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

    .card:hover {
      background: #f7faff;
      transform: translateY(-2px);
    }

    .card-category {
      font-size: 13px;
      font-weight: 700;
      margin-bottom: 6px;
      opacity: 0.9;
    }

    .card-title {
      font-size: 22px;
      font-weight: 700;
      line-height: 1.4;
      margin-bottom: 8px;
    }

    .card-detail {
      font-size: 16px;
      line-height: 1.6;
      color: #444;
    }

    .cat-京王 { border-left-color: #b4007f; }
    .cat-JR { border-left-color: #4caf50; }
    .cat-大手私鉄 { border-left-color: #ffd54f; }
    .cat-その他 { border-left-color: #9e9e9e; }
    .cat-資料 { border-left-color: #ba68c8; }
    .cat-画像 { border-left-color: #42a5f5; }

    .edit-buttons {
      margin-top: 8px;
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
    }

    .edit-btn, .delete-btn {
      padding: 6px 10px;
      font-size: 13px;
      border-radius: 6px;
      border: none;
      cursor: pointer;
      color: #fff;
    }

    .edit-btn { background: #1976d2; }
    .delete-btn { background: #d32f2f; }

    .settings {
      max-width: 520px;
      margin: 0 auto;
    }

    .settings label {
      font-size: 14px;
      display: block;
      margin: 10px 0 4px;
    }

    .settings input,
    .settings select,
    .settings textarea {
      width: 100%;
      padding: 8px;
      font-size: 14px;
      border-radius: 6px;
      border: 1px solid #bbb;
    }

    .settings textarea {
      resize: vertical;
      min-height: 70px;
    }

    .settings button {
      margin-top: 10px;
      padding: 10px 14px;
      font-size: 15px;
      border-radius: 6px;
      border: none;
      cursor: pointer;
      font-weight: 600;
    }

    .hidden { display: none; }
  </style>
</head>
<body>
<div class="container">

<header>
  <h1>tetsudo-site6</h1>

  <div class="tabs" id="tabs">
    <div class="tab active" data-tab="すべて">すべて</div>
    <div class="tab" data-tab="京王">京王</div>
    <div class="tab" data-tab="JR">JR</div>
    <div class="tab" data-tab="大手私鉄">大手私鉄</div>
    <div class="tab" data-tab="その他">その他</div>
    <div class="tab" data-tab="資料">資料</div>
    <div class="tab" data-tab="画像">画像</div>
    <div class="tab" data-tab="同期・管理">同期・管理</div>
  </div>
</header>

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
      <input type="password" id="adminPass" placeholder="パスワードを入力">
      <button id="passSubmit" style="background:#555;color:#fff;">送信</button>
      <div class="hint">正しいパスワードを入力すると追加・編集・削除が有効になります。</div>
    </div>

    <div class="settings hidden" id="formBlock">
      <div class="back-row">
        <button class="back-btn" id="backToList">◀ 戻る</button>
        <span id="formModeLabel" style="font-size:14px;color:#333;"></span>
      </div>

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
        <option value="その他">その他</option>
        <option value="資料">資料</option>
        <option value="画像">画像</option>
      </select>

      <label>画像</label>
      <input type="file" id="newImage">

      <label>
        <input type="checkbox" id="deleteImage">
        画像を削除する
      </label>

      <button id="addSubmit" style="background:#1976d2;color:#fff;">追加</button>

      <hr style="margin:16px 0;">

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

  import { getStorage, ref as sRef, uploadBytes, getDownloadURL, deleteObject }
    from "https://www.gstatic.com/firebasejs/12.14.0/firebase-storage.js";

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
  const storage = getStorage();

  let items = [];
  let currentTab = "すべて";
  let searchText = "";
  let adminMode = false;
  let editIndex = null;
  let viewMode = "list";

  const tabsEl = document.getElementById("tabs");
  const searchInput = document.getElementById("searchInput");
  const searchBar = document.querySelector(".search-bar");
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
  const newImage = document.getElementById("newImage");
  const deleteImage = document.getElementById("deleteImage");
  const addSubmit = document.getElementById("addSubmit");
  const formTitle = document.getElementById("formTitle");
  const formModeLabel = document.getElementById("formModeLabel");
  const backToList = document.getElementById("backToList");

  const cloudLoad = document.getElementById("cloudLoad");
  const cloudSave = document.getElementById("cloudSave");

  // Firebase 認証（匿名）
  signInAnonymously(auth).then(() => {
    loadFromFirebase();
  });

  // Firebase からデータ読み込み
  function loadFromFirebase() {
    const dataRef = ref(db, "urlData");
    onValue(dataRef, (snapshot) => {
      const val = snapshot.val();
      items = val ? val : [];
      render();
    });
  }

  // Firebase に保存
  function saveToFirebase() {
    const dataRef = ref(db, "urlData");
    set(dataRef, items);
  }

  // 画面切り替え
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

  // フォーム初期化
  function resetForm() {
    editIndex = null;
    formTitle.textContent = "新規URL追加";
    formModeLabel.textContent = "";
    addSubmit.textContent = "追加";
    newTitle.value = "";
    newURL.value = "";
    newDetail.value = "";
    newCategory.value = "京王";
    newImage.value = "";
    deleteImage.checked = false;
    passwordBlock.classList.add("hidden");
    formBlock.classList.remove("hidden");
  }
  // カード描画処理
  function render() {
    sectionTitle.textContent = currentTab;

    const filtered = items
      .filter(item => {
        if (currentTab !== "すべて" && item.category !== currentTab) return false;
        if (!searchText) return true;
        return (item.title || "").toLowerCase().includes(searchText.toLowerCase());
      })
      .sort((a, b) => {
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
    filtered.forEach((item) => {
      const card = document.createElement("div");
      const catClass = "cat-" + (item.category || "");
      card.className = "card " + catClass;

      const wrapper = document.createElement("div");
      wrapper.style.display = "flex";
      wrapper.style.alignItems = "flex-start";
      wrapper.style.gap = "12px";

      if (item.imageUrl) {
        const img = document.createElement("img");
        img.src = item.imageUrl;
        img.style.width = "80px";
        img.style.height = "80px";
        img.style.objectFit = "cover";
        img.style.borderRadius = "8px";
        wrapper.appendChild(img);
      }

      const textBox = document.createElement("div");
      textBox.style.flex = "1";

      const cat = document.createElement("div");
      cat.className = "card-category";
      cat.textContent = item.category || "";

      const title = document.createElement("div");
      title.className = "card-title";
      title.textContent = item.title || "";

      const detail = document.createElement("div");
      detail.className = "card-detail";
      detail.textContent = item.detail || "";

      textBox.appendChild(cat);
      textBox.appendChild(title);
      textBox.appendChild(detail);

      wrapper.appendChild(textBox);
      card.appendChild(wrapper);

      card.onclick = () => {
        if (item.url) window.open(item.url, "_blank");
      };

      const realIndex = items.indexOf(item);

      if (adminMode) {
        const btns = document.createElement("div");
        btns.className = "edit-buttons";

        const editBtn = document.createElement("button");
        editBtn.className = "edit-btn";
        editBtn.textContent = "編集";
        editBtn.onclick = (e) => {
          e.stopPropagation();
          startEdit(item, realIndex);
        };

        const delBtn = document.createElement("button");
        delBtn.className = "delete-btn";
        delBtn.textContent = "削除";
        delBtn.onclick = (e) => {
          e.stopPropagation();
          deleteItem(realIndex);
        };

        btns.appendChild(editBtn);
        btns.appendChild(delBtn);
        card.appendChild(btns);
      }

      cardList.appendChild(card);
    });
  }

  // 編集開始
  function startEdit(item, index) {
    if (!adminMode) return;
    editIndex = index;

    formTitle.textContent = "URL編集";
    formModeLabel.textContent = "編集モード";
    addSubmit.textContent = "上書き保存";

    newTitle.value = item.title || "";
    newURL.value = item.url || "";
    newDetail.value = item.detail || "";
    newCategory.value = item.category || "京王";
    newImage.value = "";
    deleteImage.checked = false;

    viewMode = "settings";
    searchBar.classList.add("hidden");
    passwordBlock.classList.add("hidden");
    formBlock.classList.remove("hidden");
    updateView();
  }

  // 削除処理
  async function deleteItem(index) {
    if (!adminMode) return;
    if (!confirm("削除しますか？")) return;

    const oldImageUrl = items[index].imageUrl;

    if (oldImageUrl) {
      try {
        const oldRef = sRef(storage, oldImageUrl);
        await deleteObject(oldRef);
      } catch (e) {
        console.log("画像削除失敗:", e);
      }
    }

    items.splice(index, 1);
    saveToFirebase();
    render();
  }

  // タブ切り替え
  tabsEl.addEventListener("click", (e) => {
    const tab = e.target.closest(".tab");
    if (!tab) return;

    const tabName = tab.dataset.tab;

    document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
    tab.classList.add("active");

    if (tabName === "同期・管理") {
      viewMode = "settings";
      searchBar.classList.add("hidden");
      updateView();
      return;
    }

    currentTab = tabName;
    viewMode = "list";
    searchBar.classList.remove("hidden");
    updateView();
    render();
  });

  // 検索
  searchInput.addEventListener("input", () => {
    searchText = searchInput.value.trim();
    render();
  });

  // パスワード認証
  passSubmit.addEventListener("click", () => {
    if (adminPass.value === PASSWORD) {
      adminMode = true;
      adminPass.value = "";
      alert("編集モードが有効になりました");
      passwordBlock.classList.add("hidden");
      formBlock.classList.remove("hidden");
      resetForm();
      render();
    } else {
      alert("パスワードが違います");
    }
  });

  // 追加・編集保存
  
addSubmit.addEventListener("click", async () => {
  const title = newTitle.value.trim();
  const url = newURL.value.trim();
  const detail = newDetail.value.trim();
  const category = newCategory.value;

  if (!title || !url) {
    alert("タイトルとURLは必須です");
    return;
  }

  let imageUrl = "";

  // 編集モードなら既存画像を保持
  if (editIndex !== null) {
    imageUrl = items[editIndex].imageUrl || "";
  }

  // 🔥 古い画像削除処理を安全化（URL → パス変換）
  if (deleteImage.checked && imageUrl) {
    try {
      // URL からパス部分だけ抽出
      const path = imageUrl.split("/o/")[1].split("?")[0];
      const decodedPath = decodeURIComponent(path);

      const oldRef = sRef(storage, decodedPath);
      await deleteObject(oldRef);
      imageUrl = "";
    } catch (e) {
      console.log("画像削除失敗:", e);
    }
  }

  // 🔥 新しい画像を追加したときの処理（安全なファイル名）
  if (newImage.files.length > 0) {
    const file = newImage.files[0];

    // 日本語・記号を安全な文字に変換
    const safeName = file.name.replace(/[^a-zA-Z0-9._-]/g, "_");

    const storageRef = sRef(storage, "images/" + Date.now() + "_" + safeName);

    try {
      await uploadBytes(storageRef, file);
      imageUrl = await getDownloadURL(storageRef);
    } catch (e) {
      console.log("画像アップロード失敗:", e);
      alert("画像のアップロードに失敗しました");
      return;
    }
  }

  const newItem = { title, url, detail, category, imageUrl };

  // 編集モードなら上書き
  if (editIndex !== null) {
    items[editIndex] = newItem;
  } else {
    items.push(newItem);
  }

  saveToFirebase();
  alert("保存しました");

  resetForm();
  viewMode = "list";
  searchBar.classList.remove("hidden");
  updateView();
  render();
});


  // 戻る
  backToList.addEventListener("click", () => {
    resetForm();
    viewMode = "list";
    searchBar.classList.remove("hidden");
    updateView();
    render();
  });

  // クラウド受信
  cloudLoad.addEventListener("click", () => {
    loadFromFirebase();
    alert("クラウドから最新データを読み込みました");
  });

  // クラウド保存
  cloudSave.addEventListener("click", () => {
    saveToFirebase();
    alert("クラウドに保存しました");
  });

  updateView();
  render();
</script>

</body>
</html>
