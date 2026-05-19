<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>羽球橫向即時計分板</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Arial, sans-serif;
        }
        
        body {
            background-color: #111827;
            color: #ffffff;
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow: hidden; /* 防止滾動條影響橫向視覺 */
            padding: 0.5rem;
        }

        /* 頂部控制列（橫向時盡量縮減高度，留給分數） */
        .top-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0.3rem 1.5rem;
            background: #1f2937;
            border-radius: 10px;
            margin-bottom: 0.5rem;
            height: 10vh;
        }

        h1 {
            font-size: 1.2rem;
            color: #9ca3af;
            letter-spacing: 1px;
        }

        .game-mode-selector {
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }
        .game-mode-selector select {
            background: #374151;
            color: #ffc13b;
            border: none;
            padding: 0.3rem 0.6rem;
            font-size: 0.9rem;
            font-weight: bold;
            border-radius: 5px;
            cursor: pointer;
        }

        .winner-announcement {
            font-size: 1.3rem;
            font-weight: bold;
            color: #10b981;
        }

        .btn-control {
            background-color: #ef4444;
            color: white;
            padding: 0.4rem 1rem;
            font-size: 0.9rem;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-weight: bold;
            margin-left: 0.5rem;
        }
        .btn-control:hover { background-color: #dc2626; }
        .btn-swap { background-color: #3b82f6; }
        .btn-swap:hover { background-color: #2563eb; }

        /* 橫向滿版計分板主體 */
        .scoreboard-container {
            display: flex;
            flex: 1;
            gap: 0.5rem;
            height: 88vh;
        }

        /* 左右隊伍大區塊 */
        .team-box {
            flex: 1;
            background-color: #1f2937;
            border-radius: 15px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center;
            padding: 1.5rem;
            position: relative;
            transition: background 0.3s;
        }

        /* 發球權燈號：改在隊伍名稱旁邊 */
        .team-header {
            display: flex;
            align-items: center;
            gap: 0.8rem;
        }

        .serve-indicator {
            width: 14px;
            height: 14px;
            background-color: transparent;
            border-radius: 50%;
            transition: 0.3s;
        }
        .team-box.serving .serve-indicator {
            background-color: #10b981;
            box-shadow: 0 0 12px #10b981;
        }

        .team-name {
            font-size: 1.5rem;
            color: #9ca3af;
            font-weight: 600;
        }

        /* 橫向超大滿版分數（佔據主要空間） */
        .score-display {
            font-size: 35vh; /* 依據螢幕高度縮放，保證字體極大 */
            font-weight: 900;
            color: #f59e0b;
            line-height: 1;
            cursor: pointer;
            user-select: none;
            width: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            flex: 1;
            transition: color 0.2s;
        }
        .score-display:hover {
            color: #ffffff;
        }

        .score-display.winner-score {
            color: #10b981 !important;
            animation: pulse 1.5s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.6; }
            100% { opacity: 1; }
        }

        /* 減分按鈕（放到底部，大大的加分只要點數字就好） */
        .btn-minus {
            background-color: #4b5563;
            color: white;
            border: none;
            padding: 0.5rem 2rem;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            width: 50%;
            max-width: 150px;
            transition: 0.2s;
        }
        .btn-minus:hover { background-color: #374151; }

        /* 中央分隔線 */
        .vs-line {
            width: 2px;
            background-color: #374151;
            align-self: shrink;
        }
    </style>
</head>
<body>

    <!-- 頂部控制列 -->
    <div class="top-bar">
        <h1>🏸 BADMINTON</h1>
        
        <!-- 獲勝公告 -->
        <div class="winner-announcement" id="winnerText"></div>

        <div class="game-mode-selector">
            <select id="maxScoreSelect" onchange="resetScore()">
                <option value="15">15 分</option>
                <option value="21" selected>21 分</option>
                <option value="31">31 分</option>
            </select>
            <button class="btn-control btn-swap" onclick="swapSides()">換場</button>
            <button class="btn-control" onclick="resetScore()">重設</button>
        </div>
    </div>

    <!-- 橫向計分板主體 -->
    <div class="scoreboard-container">
        <!-- LEFT TEAM -->
        <div class="team-box serving" id="boxA" onclick="setServer('A')">
            <div class="team-header">
                <div class="serve-indicator"></div>
                <div class="team-name">TEAM A</div>
            </div>
            <!-- 點擊超大分數直接 +1 -->
            <div class="score-display" id="scoreA" onclick="changeScore('A', 1, event)">0</div>
            <button class="btn-minus" onclick="changeScore('A', -1, event)">-1</button>
        </div>

        <div class="vs-line"></div>

        <!-- RIGHT TEAM -->
        <div class="team-box" id="boxB" onclick="setServer('B')">
            <div class="team-header">
                <div class="serve-indicator"></div>
                <div class="team-name">TEAM B</div>
            </div>
            <!-- 點擊超大分數直接 +1 -->
            <div class="score-display" id="scoreB" onclick="changeScore('B', 1, event)">0</div>
            <button class="btn-minus" onclick="changeScore('B', -1, event)">-1</button>
        </div>
    </div>

    <script>
        let scoreA = 0;
        let scoreB = 0;
        let gameOver = false;

        function changeScore(team, value, event) {
            if (event) event.stopPropagation(); 
            if (gameOver && value > 0) return; 

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

        function checkWinner(maxScore) {
            let winText = document.getElementById('winnerText');
            let displayA = document.getElementById('scoreA');
            let displayB = document.getElementById('scoreB');

            gameOver = false;
            winText.innerText = "";
            displayA.classList.remove('winner-score');
            displayB.classList.remove('winner-score');

            if (scoreA >= maxScore) {
                winText.innerText = "🎉 TEAM A WIN!";
                displayA.classList.add('winner-score');
                gameOver = true;
            } else if (scoreB >= maxScore) {
                winText.innerText = "🎉 TEAM B WIN!";
                displayB.classList.add('winner-score');
                gameOver = true;
            }
        }

        function setServer(team) {
            if (gameOver) return;
            document.getElementById('boxA').classList.remove('serving');
            document.getElementById('boxB').classList.remove('serving');
            
            if (team === 'A') {
                document.getElementById('boxA').classList.add('serving');
            } else {
                document.getElementById('boxB').classList.add('serving');
            }
        }

        function swapSides() {
            let tempScore = scoreA;
            scoreA = scoreB;
            scoreB = tempScore;
            document.getElementById('scoreA').innerText = scoreA;
            document.getElementById('scoreB').innerText = scoreB;

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
