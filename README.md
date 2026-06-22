<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>tetsudo-site6</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
body {
  font-family: sans-serif;
  background: #f5f5f5;
  margin: 0;
  padding: 0;
}

header {
  background: #ff9800;
  padding: 10px;
  color: white;
  font-size: 20px;
  text-align: center;
}

#tabs {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
  padding: 10px;
  background: #fff;
}

.tab {
  padding: 6px 12px;
  border-radius: 6px;
  background: #ddd;
  cursor: pointer;
  font-size: 14px;
}

.tab.active {
  background: #ff9800;
  color: white;
}

#searchArea {
  display: flex;
  gap: 8px;
  padding: 10px;
  background: #fff;
}

#searchInput {
  flex: 1;
  padding: 8px;
  font-size: 14px;
}

#sortSelect {
  padding: 8px;
  font-size: 14px;
}

#listArea {
  padding: 10px;
}

.card {
  background: white;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 8px;
  border-left: 6px solid #ff9800;
}

.card-title {
  font-size: 16px;
  font-weight: bold;
}

.card-detail {
  margin-top: 4px;
  color: #333;
  font-size: 14px;
}

#editArea {
  padding: 10px;
  background: #fff;
}

label {
  display: block;
  margin-top: 10px;
  font-size: 14px;
}

input, textarea, select {
  width: 100%;
  padding: 8px;
  margin-top: 4px;
  font-size: 14px;
}

button {
  padding: 10px 14px;
  margin-top: 10px;
  font-size: 14px;
  background: #ff9800;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}
</style>
</head>

<body>

<header>tetsudo-site6</header>

<div id="tabs">
  <div class="tab active" data-cat="すべて">すべて</div>
  <div class="tab" data-cat="京王">京王</div>
  <div class="tab" data-cat="JR">JR</div>
  <div class="tab" data-cat="大手私鉄">大手私鉄</div>
  <div class="tab" data-cat="その他">その他</div>
  <div class="tab" data-cat="資料">資料</div>
  <div class="tab" data-cat="画像">画像</div>
  <div class="tab" data-cat="同期・管理">同期・管理</div>
</div>

<div id="searchArea">
  <input id="searchInput" type="text" placeholder="タイトルで検索…">
  <select id="sortSelect">
    <option value="az">五十音順</option>
    <option value="za">逆五十音順</option>
    <option value="new">新しい順</option>
    <option value="old">古い順</option>
    <option value="type">種別順</option>
  </select>
</div>

<div id="editArea" class="hidden">
  <label>タイトル</label>
  <input id="newTitle">

  <label>種別（任意）</label>
  <input id="newType">

  <label>行き先（任意）</label>
  <input id="newDest">

  <label>URL</label>
  <input id="newURL">

  <label>詳細</label>
  <textarea id="newDetail"></textarea>

  <label>カテゴリ</label>
  <input id="newCategory">

  <button id="addSubmit">保存</button>
</div>

<div id="listArea"></div>

