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
    const oldImageUrl = items[index].imageUrl;

    // Firebase Storage の画像削除
    if (oldImageUrl) {
      try {
        const oldRef = sRef(storage, oldImageUrl);
        await deleteObject(oldRef);
      } catch (e) {
        console.log("画像削除失敗:", e);
      }
    }

    // データ削除
    items.splice(index, 1);
    saveToFirebase();
    render();
  }

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

  searchInput.addEventListener("input", () => {
    searchText = searchInput.value.trim();
    render();
  });

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
    const deleteImage = document.getElementById("deleteImage");

    // 編集モードなら既存画像を保持
    if (editIndex !== null) {
      imageUrl = items[editIndex].imageUrl || "";
    }

    // 画像削除チェック
    if (deleteImage.checked && imageUrl) {
      try {
        const oldRef = sRef(storage, imageUrl);
        await deleteObject(oldRef);
      } catch (e) {
        console.log("画像削除失敗:", e);
      }
      imageUrl = "";
    }

    // 新しい画像が選択されている場合
    if (newImage.files.length > 0) {
      const file = newImage.files[0];
      const storageRef = sRef(storage, "images/" + Date.now() + "_" + file.name);
      await uploadBytes(storageRef, file);
      imageUrl = await getDownloadURL(storageRef);
    }

    const newItem = { title, url, detail, category, imageUrl };

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

  backToList.addEventListener("click", () => {
    resetForm();
    viewMode = "list";
    searchBar.classList.remove("hidden");
    updateView();
    render();
  });

  cloudLoad.addEventListener("click", () => {
    loadFromFirebase();
    alert("クラウドから最新データを読み込みました");
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
