<!DOCTYPE html>

<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ウイルス感染警告！</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            overflow: auto;
        }
        .animated-warning {
            animation: pulse 2s infinite, move 1s alternate infinite;
        }
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        @keyframes move {
            0% { transform: translateX(0); }
            100% { transform: translateX(10px); }
        }
        #close-button {
            cursor: pointer;
            background-color: #dc2626;
            color: #ffffff;
            font-weight: 600;
            padding: 0.75rem 1.5rem;
            border-radius: 0.375rem;
            transition: background-color 0.3s ease;
        }
        #close-button:hover {
            background-color: #b91c1c;
        }
        .full-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            z-index: 10000;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .warning-window {
            background-color: white;
            border: 2px solid #ccc;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            margin: 10px;
            width: 80%;
            max-width: 400px;
            animation: fadeIn 0.5s ease-in-out, moveAround 2s infinite;
            position: absolute;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        @keyframes moveAround {
            0% { transform: translate(0, 0); }
            25% { transform: translate(50px, 50px); }
            50% { transform: translate(-50px, 100px); }
            75% { transform: translate(-100px, -50px); }
            100% { transform: translate(0, 0); }
        }
        #repairing-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.9);
            z-index: 10001;
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 24px;
        }
        #repairing-overlay p {
            margin-top: 1rem;
            font-size: 1.5rem;
            font-weight: bold;
        }
    </style>
    <script src="https://unpkg.com/tone"></script>
    <script>
        // 警告音を再生する関数
        function playAlarm() {
            try {
                const synth = new Tone.Synth().toDestination();
                // マスターボリュームを最大に設定
                Tone.Master.volume.value = 0;
                // より強い警告音（サイレン）に変更
                synth.triggerAttackRelease("C4", "16n"); // 音符の長さを短くして、早くする
                setTimeout(() => synth.triggerAttackRelease("E4", "16n"), 100); // タイミングも短くする
                setTimeout(() => synth.triggerAttackRelease("G4", "16n"), 200);
                setTimeout(() => synth.triggerAttackRelease("C5", "16n"), 300);
            } catch (error) {
                console.error("警告音再生エラー:", error);
            }
        }

```
    // 詐欺音声を再生する関数
    function playScamAudio() {
        try {
            const audio = new Audio('https://www.your-site.com/scam_audio.mp3'); // 絶対URLを使用してください
            audio.play().catch(error => {
                console.error('音声再生エラー:', error);
                // エラー処理：代替の警告音を再生するなど
                if (typeof Tone !== 'undefined') {
                    const synth = new Tone.Synth().toDestination();
                    synth.triggerAttackRelease("C2", "1n"); // 代替音
                }
            });
        } catch (error) {
            console.error('音声オブジェクト作成エラー:', error);
            if (typeof Tone !== 'undefined') {
                const synth = new Tone.Synth().toDestination();
                synth.triggerAttackRelease("C2", "1n");
            }
        }
    }

    let closeButton;
    let body;
    let repairingOverlay;
    let alarmTimeout;
    let audioContext;
    let alarmInterval; // 警告音を繰り返すためのインターバルID
    let repairingProgress = 0;
    let progressInterval;
    let progressElement; // 進捗表示要素への参照を保持

    function initialize() {
        closeButton = document.getElementById('close-button');
        body = document.querySelector('body');
        repairingOverlay = document.getElementById('repairing-overlay');

        if (closeButton && body && repairingOverlay) {
            closeButton.addEventListener('click', handleCloseButtonClick); // ボタンクリックでイベント発火
            body.addEventListener('click', handleBodyClick);
        } else {
            console.error('必要な要素が見つかりません。HTML構造を確認してください。');
            return;
        }

        // AudioContext 初期化
        try {
            audioContext = new (window.AudioContext || window.webkitAudioContext)();
            if (typeof Tone !== 'undefined') {
                Tone.setContext(audioContext);
            }
        } catch (e) {
            console.error("AudioContextの初期化に失敗しました:", e);
            // AudioContextが利用できない場合の処理
            alert('このブラウザでは音声機能が利用できません。');
        }
    }

    function handleBodyClick(event) {
        event.preventDefault();
        // 警告音を再生
        if (audioContext && typeof Tone !== 'undefined') {
            playAlarm();
            alarmInterval = setInterval(playAlarm, 500); // 0.5秒ごとに再生
        }
        alarmTimeout = setTimeout(playScamAudio, 5000);
    }

    function handleCloseButtonClick(event) {
        event.preventDefault();
        showRepairingOverlay();
        // 警告音を停止
        clearInterval(alarmInterval);
        clearTimeout(alarmTimeout);
         if (audioContext && typeof Tone !== 'undefined') {
              try {
                const audio = new Audio('https://www.your-site.com/scam_audio.mp3');
                audio.pause();
                audio.currentTime = 0;
              } catch(e) {
                  console.log(e);
              }
        }
        startRepairingProgress();
    }

    function showRepairingOverlay() {
        if (repairingOverlay) {
            repairingOverlay.style.display = 'flex';
        }
    }

    function startRepairingProgress() {
        repairingProgress = 0;
        // 進捗表示要素が存在しない場合は作成
        if (!progressElement) {
            progressElement = document.createElement('p');
            repairingOverlay.appendChild(progressElement);
        }

        // 進行状況を更新する関数を定義
        function updateProgress() {
            repairingProgress += 1;
            progressElement.textContent = `${repairingProgress}%`; // テキスト内容を更新

            if (repairingProgress < 100) {
                // 100%未満の場合は、次の更新をスケジュール
                const randomDelay = Math.random() * 200 + 50; // 50ms～250msのランダムな遅延
                setTimeout(updateProgress, randomDelay); // 再帰的に呼び出す
            } else {
                // 100%に達したら、インターバルを停止し、完了メッセージを表示
                setTimeout(() => {
                    progressElement.textContent = "修復が完了しました。";
                    setTimeout(() => {
                        progressElement.textContent = "最終処理中...";
                        setTimeout(() => {
                            progressElement.textContent = "お使いのPCは安全な状態に戻りました。";
                            setTimeout(() => {
                                repairingOverlay.style.display = 'none';
                                repairingOverlay.removeChild(progressElement);
                                progressElement = null; // 参照をnullに設定
                            }, 3000); // 3秒後に非表示
                        }, 10000); // 10秒後に表示
                    }, 3000); // 3秒後に表示
                }, 500);
            }
        }

        // 最初の更新を開始
        updateProgress();
    }

    document.addEventListener('DOMContentLoaded', initialize);
</script>
```

</head>
<body class="bg-gray-100 flex flex-col items-center justify-center min-h-screen py-2">
    <div class="bg-white border border-gray-200 rounded-xl shadow-lg p-8 w-full max-w-2xl animated-warning">
        <div class="flex items-center mb-6">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/44/Microsoft_logo.svg/2048px-Microsoft_logo.svg.png" alt="Microsoft ロゴ" class="h-12 mr-4">
            <h1 class="text-2xl font-semibold text-red-600">緊急警告！ウイルス感染</h1>
        </div>
        <div class="mb-4">
            <p class="text-gray-700 font-semibold">お使いのデバイスは、深刻なウイルスに感染しています。システムが完全に破壊される可能性があります。</p>
            <p class="text-gray-700">直ちにマイクロソフトのサポートに連絡し、指示に従ってください。</p>
        </div>
        <div class="mb-6">
            <ul class="list-disc list-inside space-y-2">
                <li class="text-red-600 font-semibold">すべての個人情報（クレジットカード、パスワード、写真など）が盗まれる危険性があります。</li>
                <li class="text-red-600 font-semibold">ハードディスクが暗号化され、データが失われる可能性があります。</li>
                <li class="text-red-600 font-semibold">この警告を無視すると、法的な責任を問われる場合があります。</li>
            </ul>
        </div>
        <div class="mb-8">
            <p class="text-lg font-semibold text-blue-600">今すぐお電話ください: 03-1234-5678</p>
            <p class="text-gray-500 text-sm">通話料無料。24時間365日対応。マイクロソフト認定技術者が対応します。</p>
            <p class="text-red-500 text-sm mt-2 font-bold">警告：絶対にデバイスをシャットダウンしないでください。データが完全に失われます。</p>
        </div>
        <div class="flex justify-end space-x-4">
            <button id="close-button" class="bg-red-500 text-white font-semibold py-2 px-4 rounded-md focus:outline-none focus:shadow-outline">
                今すぐ修復
            </button>
        </div>
    </div>

```
<div id="repairing-overlay">
    <div class="flex items-center">
        <svg class="animate-spin h-10 w-10 mr-3 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
        </svg>
        <span class="text-white text-xl font-semibold">修復中...</span>
    </div>
</div>
```

</body>
</html>
