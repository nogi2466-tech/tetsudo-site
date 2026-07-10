<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>tetsudo-site6</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
*{box-sizing:border-box;}

body{
  margin:0;
  font-family:system-ui;
  font-size:16px;
  background:#f5f5f5;
}

.container{background:#fff;min-height:100vh;}

header{
  background:#1976d2;
  color:#fff;
  padding:12px;
  text-align:center;
}

.tabs{
  display:flex;
  flex-wrap:wrap;
  gap:6px;
  justify-content:center;
  padding:10px;
}

.tab{
  padding:8px 14px;
  border-radius:18px;
  cursor:pointer;
  color:#fff;
  font-size:15px;
}

.tab.active{
  border:3px solid #fff;
  background:inherit;
  color:#fff;
  font-weight:bold;
}

.tab[data-tab="京王"]{background:#b4007f;}
.tab[data-tab="JR"]{background:#4caf50;}
.tab[data-tab="大手私鉄"]{background:#ffd54f;color:#333;}
.tab[data-tab="その他"]{background:#9e9e9e;}
.tab[data-tab="資料"]{background:#ba68c8;}
.tab[data-tab="画像"]{background:#42a5f5;}
.tab[data-tab="同期・管理"]{background:#555;}

.search-bar{
  background:#e3f2fd;
  padding:10px;
}

.search-bar input{
  width:100%;
  padding:8px;
  border-radius:6px;
  border:1px solid #bbb;
}

main{padding:16px;}

.section-title{
  text-align:center;
  font-size:18px;
  margin-bottom:12px;
}

.card-list{
  display:flex;
  flex-direction:column;
  gap:10px;
}

/* ⭐ カード横並び（画像左・文字右） */
.card{
  background:#fff;
  padding:12px;
  border-radius:12px;
  border-left:6px solid #ccc;
  cursor:pointer;
  display:flex;
  gap:12px;
  align-items:flex-start;
}

.card-left{
  width:35%;
}

.card-right{
  width:65%;
  display:flex;
  flex-direction:column;
  gap:4px;
}

.card-image{
  width:100%;
  border-radius:10px;
  object-fit:cover;
}

.card-title{
  font-size:17px;
  font-weight:bold;
}

.card-detail{
  font-size:15px;
  line-height:1.4;
}

.cat-京王{border-left-color:#b4007f;}
.cat-JR{border-left-color:#4caf50;}
.cat-大手私鉄{border-left-color:#ffd54f;}
.cat-その他{border-left-color:#9e9e9e;}
.cat-資料{border-left-color:#ba68c8;}
.cat-画像{border-left-color:#42a5f5;}

.edit-buttons{
  margin-top:10px;
  display:flex;
  gap:8px;
}

.edit-btn{
  background:#1976d2;
  color:#fff;
  padding:6px 10px;
  border:none;
  border-radius:6px;
}

.delete-btn{
  background:#d32f2f;
  color:#fff;
  padding:6px 10px;
  border:none;
  border-radius:6px;
}

.settings{
  max-width:500px;
  margin:auto;
}

.settings input,
.settings textarea,
.settings select{
  width:100%;
  padding:8px;
  border-radius:6px;
  border:1px solid #bbb;
}

/* ⭐ 詳細欄の枠固定（resize禁止） */
.settings textarea{
  resize:none;
  font-size:15px;
  height:120px;
}

.hidden{display:none;}
</style>
</head>

<body>
<div class="container">

<header>
  <h1>tetsudo-site6</h1>

  <div class="tabs" id="tabsEl">
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

<div class="search-bar" id="searchBar">
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
      <button id="passSubmit" style="background:#555;color:#fff;">送信</button>
    </div>

    <div class="settings hidden" id="formBlock">
      <button id="backToList">◀ 戻る</button>
      <div class="section-title" id="formTitle">新規URL追加</div>

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
        <option value="その他">その他</option>
        <option value="資料">資料</option>
        <option value="画像">画像</option>
      </select>

      <label>画像ファイル</label>
      <input type="file" id="newImageFile" accept="image/*">

      <button id="addSubmit" style="background:#1976d2;color:#fff;">追加</button>

      <hr>

      <button id="cloudLoad">クラウド受信</button>
      <button id="cloudSave">クラウド保存</button>
    </div>
  </div>
</main>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-app.js";
import { getDatabase, ref, set, onValue } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-database.js";
import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-auth.js";
import { getStorage, ref as sRef, uploadBytes, getDownloadURL }
from "https://www.gstatic.com/firebasejs/12.14.0/firebase-storage.js";

const firebaseConfig = {
  apiKey: "AIzaSyD55Pawag1UichGwM-Uxddivb8lFr7QOU8",
  authDomain: "tetsudo-site6.firebaseapp.com",
  databaseURL: "https://tetsudo-site6-default-rtdb.firebaseio.com",
  projectId: "tetsudo-site6",
  storageBucket: "tetsudo-site6.firebasestorage.app",
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

signInAnonymously(auth).then(() => loadFromFirebase());

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
  const filtered = items.filter(item => {
    if (currentTab === "すべて") {
      if (item.category === "資料" || item.category === "画像") return false;
    } else if (item.category !== currentTab) return false;
    if (!searchText) return true;
    return (item.title || "").toLowerCase().includes(searchText.toLowerCase());
  });

  /* ⭐ 並び順（数字昇順＋50音順） */
  filtered.sort((a, b) => {
    const A = a.title;
    const B = b.title;

    if (!isNaN(A) && !isNaN(B)) {
      return Number(A) - Number(B);
    }

    return A.localeCompare(B, "ja");
  });

  cardList.innerHTML = "";

  filtered.forEach(item => {
    const card = document.createElement("div");
    card.className = "card cat-" + item.category;

    const detailText = (item.detail || "").replace(/\n/g, "<br>");

    card.innerHTML =
      `<div class="card-left">${item.image ? `<img src="${item.image}" class="card-image">` : ""}</div>
       <div class="card-right">
         <div class="card-category">${item.category}</div>
         <div class="card-title">${item.title}</div>
         <div class="card-detail">${detailText}</div>
       </div>`;

    card.onclick = () => {
      if (item.url) window.open(item.url, "_blank");
    };

    const index = items.indexOf(item);

    if (adminMode) {
      const btns = document.createElement("div");
      btns.className = "edit-buttons";
      btns.innerHTML =
      `<button class="edit-btn">編集</button>
       <button class="delete-btn">削除</button>`;

      btns.children[0].onclick = e => {
        e.stopPropagation();
        startEdit(item, index);
      };

      btns.children[1].onclick = e => {
        e.stopPropagation();
        deleteItem(index);
      };

      card.appendChild(btns);
    }

    cardList.appendChild(card);
  });
}

async function uploadImage(file) {
  const filePath = "images/" + Date.now() + "_" + file.name;
  const storageRef = sRef(storage, filePath);

  await uploadBytes(storageRef, file);
  return await getDownloadURL(storageRef);
}

addSubmit.addEventListener("click", async () => {
  const title = newTitle.value.trim();
  const url = newURL.value.trim();
  const detail = newDetail.value.trim();
  const category = newCategory.value;
  const imageFile = document.getElementById("newImageFile").files[0];

  if (!title || !url) {
    alert("タイトルとURLは必須です");
    return;
  }

  let imageURL = "";
  if (imageFile) {
    imageURL = await uploadImage(imageFile);
  }

  if (editIndex !== null) {
    items[editIndex] = {
      ...items[editIndex],
      title,
      url,
      detail,
      category,
      image: imageURL || items[editIndex].image
    };
  } else {
    items.push({
      title,
      url,
      detail,
      category,
      image: imageURL,
      createdAt: Date.now()
    });
  }

  saveToFirebase();
  alert("保存しました");

  resetForm();
  viewMode = "list";
  searchBar.classList.remove("hidden");
  updateView();
  render();
});

function startEdit(item, index) {
  adminMode && (
    editIndex = index,
    newTitle.value = item.title,
    newURL.value = item.url,
    newDetail.value = item.detail,
    newCategory.value = item.category,
    viewMode = "settings",
    searchBar.classList.add("hidden"),
    passwordBlock.classList.add("hidden"),
    formBlock.classList.remove("hidden"),
    updateView()
  );
}

function deleteItem(index) {
  if (!adminMode) return;
  if (!confirm("削除しますか？")) return;
  items.splice(index, 1);
  saveToFirebase();
  render();
}

tabsEl.addEventListener("click", e => {
  const tab = e.target.closest(".tab");
  if (!tab) return;

  document.querySelectorAll(".tab").forEach(t => t.classList.remove("active"));
  tab.classList.add("active");

  const name = tab.dataset.tab;

  if (name === "同期・管理") {
    viewMode = "settings";
    searchBar.classList.add("hidden");
    updateView();
    return;
  }

  currentTab = name;
  viewMode = "list";
  searchBar.classList.remove("hidden");
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
    resetForm();
    render();
  } else alert("パスワードが違います");
});

backToList.addEventListener("click", () => {
  resetForm();
  viewMode = "list";
  searchBar.classList.remove("hidden");
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
  document.getElementById("newImageFile").value = "";
  passwordBlock.classList.add("hidden");
  formBlock.classList.remove("hidden");
}

updateView();
render();
</script>

</body>
</html>
