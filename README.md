<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>YouTube プレイヤー</title>
    <style>
        /* 全体のスタイル */
        body { font-family: sans-serif; text-align: center; margin: 0; padding: 0; background: #f4f7f6; }
        
        /* 上部のヘッダー色 */
        header { background: #007bff; color: white; padding: 15px; margin-bottom: 20px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        
        .container { padding: 20px; }

        /* 入力欄のデザイン（ボタン風） */
        input[type="text"], input[type="password"] {
            padding: 10px; border: 2px solid #ddd; border-radius: 5px;
            width: 250px; font-size: 16px; margin: 5px; outline: none;
        }

        /* 立体的なボタンのデザイン */
        .btn {
            padding: 10px 20px; font-size: 16px; font-weight: bold;
            color: white; border: none; border-radius: 5px;
            cursor: pointer; position: relative; transition: 0.1s;
            margin: 5px;
        }
        .btn-play { background: #28a745; box-shadow: 0 4px #1e7e34; }
        .btn-delete { background: #dc3545; box-shadow: 0 4px #a71d2a; }

        /* ボタンを押した時の動き */
        .btn:active { top: 3px; box-shadow: none; }

        #playerArea { margin-top: 20px; }
    </style>
</head>
<body>

<header>
    <h1>YouTube プレイヤー</h1>
</header>

<div class="container">
    <!-- URL入力 -->
    <div>
        <input type="text" id="videoUrl" placeholder="YouTubeのURLを貼り付け">
        <button class="btn btn-play" onclick="playVideo()">再生する</button>
    </div>

    <!-- 削除用（パスワード） -->
    <div style="margin-top: 20px; border-top: 1px solid #ccc; padding-top: 20px;">
        <input type="password" id="passInput" placeholder="パスワードを入力">
        <button class="btn btn-delete" onclick="deleteVideo()">動画を削除</button>
    </div>

    <!-- 動画表示エリア -->
    <div id="playerArea"></div>
</div>

<script>
    const SECRET_PASS = "1234"; // 削除用パスワード

    function playVideo() {
        const urlInput = document.getElementById('videoUrl').value;
        let videoId = "";

        if (urlInput.includes("v=")) {
            videoId = urlInput.split("v=")[1].split("&")[0];
        } else if (urlInput.includes("youtu.be/")) {
            videoId = urlInput.split("youtu.be/")[1].split("?")[0];
        }

        if (videoId) {
            document.getElementById('playerArea').innerHTML = `
                <iframe width="560" height="315" src="https://youtube.com{videoId}" 
                frameborder="0" allowfullscreen></iframe>`;
        } else {
            alert("正しいURLを入力してください");
        }
    }

    function deleteVideo() {
        const pass = document.getElementById('passInput').value;
        if (pass === SECRET_PASS) {
            document.getElementById('playerArea').innerHTML = ""; // 動画を消去
            document.getElementById('videoUrl').value = "";   // URL入力欄も空に
            document.getElementById('passInput').value = "";  // パスワード欄も空に
            alert("動画を削除しました");
        } else {
            alert("パスワードが違います");
        }
    }
</script>

</body>
</html>
