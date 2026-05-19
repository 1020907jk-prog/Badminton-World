<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>羽球即時計分板 (可選賽制)</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }
        
        body {
            background-color: #111827;
            color: #ffffff;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
        }

        h1 {
            font-size: 1.6rem;
            margin-bottom: 0.5rem;
            color: #9ca3af;
            letter-spacing: 2px;
        }

        /* 賽制選擇區塊 */
        .game-mode-selector {
            margin-bottom: 1.5rem;
            display: flex;
            align-items: center;
            gap: 0.5rem;
            background: #1f2937;
            padding: 0.5rem 1rem;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.2);
        }
        .game-mode-selector label {
            color: #9ca3af;
            font-size: 0.9rem;
        }
        .game-mode-selector select {
            background: #374151;
            color: #ffc13b;
            border: none;
            padding: 0.4rem 0.8rem;
            font-size: 1rem;
            font-weight: bold;
            border-radius: 6px;
            cursor: pointer;
            outline: none;
        }

        /* 獲勝公告 */
        .winner-announcement {
            font-size: 1.5rem;
            font-weight: bold;
            color: #10b981;
            margin-bottom: 1rem;
            height: 2rem; /* 固定高度防止畫面跳動 */
            text-align: center;
        }

        /* 計分板外框 */
        .scoreboard {
            background-color: #1f2937;
            border-radius: 20px;
            padding: 2rem;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.5);
            display: flex;
            align-items: center;
            gap: 2rem;
            width: 100%;
            max-width: 700px;
        }

        /* 隊伍區塊 */
        .team-box {
            flex: 1;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            position: relative;
        }

        /* 發球權燈號 */
        .serve-indicator {
            width: 15px;
            height: 15px;
            background-color: transparent;
            border-radius: 50%;
            margin-bottom: 0.5rem;
            transition: 0.3s;
        }
        .team-box.serving .serve-indicator {
            background-color: #10b981;
            box-shadow: 0 0 12px #10b981;
        }

        .team-name {
            font-size: 1.4rem;
            color: #9ca3af;
            font-weight: 600;
            margin-bottom: 0.5rem;
        }

        /* 超大分數顯示 */
        .score-display {
            font-size: 7rem;
            font-weight: 800;
            color: #f59e0b;
            line-height: 1;
            margin: 0.5rem 0;
            cursor: pointer;
            user-select: none;
            width: 100%;
            padding: 1rem 0;
            border-radius: 12px;
            transition: background 0.2s;
        }
        .score-display:hover {
            background-color: #374151;
        }

        /* 當某隊贏了，分數變綠色並發光 */
        .score-display.winner-score {
            color: #10b981 !important;
            animation: pulse 1.5s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.6; }
            100% { opacity: 1; }
        }

        .vs {
            font-size: 1.5rem;
            color: #4b5563;
            font-weight: bold;
        }

        /* 按鈕 */
        .btn-group {
            display: flex;
            gap: 0.8rem;
            width: 100%;
        }
        
        .btn {
            flex: 1;
            border: none;
            padding: 0.75rem;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            transition: 0.2s;
        }

        .btn-plus { background-color: #10b981; color: white; }
        .btn-plus:hover { background-color: #059669; }

        .btn-minus { background-color: #4b5563; color: white; }
        .btn-minus:hover { background-color: #374151; }

        /* 功能控制列 */
        .controls {
            margin-top: 2rem;
            display: flex;
            gap: 1rem;
        }

        .btn-control {
            background-color: #ef4444;
            color: white;
            padding: 0.6rem 1.5rem;
            font-size: 1rem;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
        }
        .btn-control:hover { background-color: #dc2626; }
        
        .btn-swap { background-color: #3b82f6; }
        .btn-swap:hover { background-color: #2563eb; }

        @media (max-width: 600px) {
            .scoreboard {
                flex-direction: column;
                gap: 1rem;
                padding: 1.5rem;
            }
            .vs { margin: 0.5rem 0; }
            .score-display { font-size: 6rem; }
        }
    </style>
</head>
<body>

    <h1>🏸 BADMINTON SCOREBOARD</h1>

    <!-- 新增：賽制選擇器 -->
    <div class="game-mode-selector">
        <label for="maxScoreSelect">賽制點數：</label>
        <select id="maxScoreSelect" onchange="resetScore()">
            <option value="15">15 分制 (短局/臨打)</option>
            <option value="21" selected>21 分制 (正規賽)</option>
            <option value="31">31 分制 (單局決戰)</option>
        </select>
    </div>

    <!-- 新增：獲勝公告欄 -->
    <div class="winner-announcement" id="winnerText"></div>

    <div class="scoreboard">
        <!-- TEAM A -->
        <div class="team-box serving" id="boxA" onclick="setServer('A')">
            <div class="serve-indicator"></div>
            <div class="team-name">TEAM A</div>
            <div class="score-display" id="scoreA" onclick="changeScore('A', 1, event)">0</div>
            <div class="btn-group">
                <button class="btn btn-minus" onclick="changeScore('A', -1, event)">-1</button>
                <button class="btn btn-plus" onclick="changeScore('A', 1, event)">+1</button>
            </div>
        </div>

        <div class="vs">VS</div>

        <!-- TEAM B -->
        <div class="team-box" id="boxB" onclick="setServer('B')">
            <div class="serve-indicator"></div>
            <div class="team-name">TEAM B</div>
            <div class="score-display" id="scoreB" onclick="changeScore('B', 1, event)">0</div>
            <div class="btn-group">
                <button class="btn btn-minus" onclick="changeScore('B', -1, event)">-1</button>
                <button class="btn btn-plus" onclick="changeScore('B', 1, event)">+1</button>
            </div>
        </div>
    </div>

    <div class="controls">
        <button class="btn-control btn-swap" onclick="swapSides()">更換場地</button>
        <button class="btn-control" onclick="resetScore()">重設分數</button>
    </div>

    <script>
        let scoreA = 0;
        let scoreB = 0;
        let gameOver = false;

        function changeScore(team, value, event) {
            if (event) event.stopPropagation(); 
            if (gameOver && value > 0) return; // 如果比賽結束，無法再加分（但可以減分修正錯誤）

            let maxScore = parseInt(document.getElementById('maxScoreSelect').value);

            if (team === 'A') {
                scoreA = Math.max(0, scoreA + value);
                document.getElementById('scoreA').innerText = scoreA;
                if (value > 0) setServer('A');
            } else if (team === 'B') {
                scoreB = Math.max(0, scoreB + value);
                document.getElementById('scoreB').innerText = scoreB;
                if (value > 0) setServer('B');
            }

            checkWinner(maxScore);
        }

        // 檢查是否有人獲勝 (包含簡化版的 Deuce 規則：必須贏 2 分，或先到最高分)
        // 這裡採用最常見的臨打規則：先到設定分數即獲勝
        function checkWinner(maxScore) {
            let winText = document.getElementById('winnerText');
            let displayA = document.getElementById('scoreA');
            let displayB = document.getElementById('scoreB');

            // 重設樣式
            gameOver = false;
            winText.innerText = "";
            displayA.classList.remove('winner-score');
            displayB.classList.remove('winner-score');

            if (scoreA >= maxScore) {
                winText.innerText = "🎉 TEAM A 獲勝！";
                displayA.classList.add('winner-score');
                gameOver = true;
            } else if (scoreB >= maxScore) {
                winText.innerText = "🎉 TEAM B 獲勝！";
                displayB.classList.add('winner-score');
                gameOver = true;
            }
        }

        function setServer(team) {
            if (gameOver) return; // 結束後不切換發球
            document.getElementById('boxA').classList.remove('serving');
            document.getElementById('boxB').classList.remove('serving');
            
            if (team === 'A') {
                document.getElementById('boxA').classList.add('serving');
            } else {
                document.getElementById('boxB').classList.add('serving');
            }
        }

        function swapSides() {
            // 交換分數
            let tempScore = scoreA;
            scoreA = scoreB;
            scoreB = tempScore;
            document.getElementById('scoreA').innerText = scoreA;
            document.getElementById('scoreB').innerText = scoreB;

            // 交換發球燈
            let boxA = document.getElementById('boxA');
            let boxB = document.getElementById('boxB');
            let isAServing = boxA.classList.contains('serving');
            
            if (isAServing) {
                boxA.classList.remove('serving');
                boxB.classList.add('serving');
            } else if (boxB.classList.contains('serving')) {
                boxB.classList.remove('serving');
                boxA.classList.add('serving');
            }

            // 重新判定勝負狀態
            let maxScore = parseInt(document.getElementById('maxScoreSelect').value);
            checkWinner(maxScore);
        }

        function resetScore() {
            scoreA = 0;
            scoreB = 0;
            gameOver = false;
            document.getElementById('scoreA').innerText = 0;
            document.getElementById('scoreB').innerText = 0;
            document.getElementById('scoreA').classList.remove('winner-score');
            document.getElementById('scoreB').classList.remove('winner-score');
            document.getElementById('winnerText').innerText = "";
            setServer('A');
        }
    </script>
</body>
</html>
