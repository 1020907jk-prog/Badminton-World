<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>熱血羽球世界 Badminton World</title>
    <style>
        /* 全域樣式 */
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        body {
            background-color: #f4f7f6;
            color: #333;
            line-height: 1.6;
        }
        a {
            text-decoration: none;
            color: inherit;
        }

        /* 導覽列 */
        header {
            background-color: #1e3d59;
            color: white;
            padding: 1rem 2rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .logo {
            font-size: 1.5rem;
            font-weight: bold;
            letter-spacing: 1px;
        }
        nav ul {
            display: flex;
            list-style: none;
        }
        nav ul li {
            margin-left: 2rem;
        }
        nav ul li a:hover {
            color: #ffc13b;
            transition: 0.3s;
        }

        /* 主視覺 Banner */
        .hero {
            background: linear-gradient(rgba(30, 61, 89, 0.8), rgba(30, 61, 89, 0.8)), 
                        url('https://images.unsplash.com/photo-1626224583764-f87db24ac4ea?ixlib=rb-4.0.3&auto=format&fit=crop&w=1200&q=80') center/cover;
            height: 60vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            color: white;
            text-align: center;
            padding: 0 1rem;
        }
        .hero h1 {
            font-size: 3rem;
            margin-bottom: 1rem;
        }
        .hero p {
            font-size: 1.2rem;
            margin-bottom: 2rem;
            max-width: 600px;
        }
        .btn {
            background-color: #ffc13b;
            color: #1e3d59;
            padding: 0.75rem 2rem;
            font-weight: bold;
            border-radius: 5px;
            transition: 0.3s;
        }
        .btn:hover {
            background-color: #f5f0e1;
            transform: translateY(-2px);
        }

        /* 特色區塊 */
        .services {
            padding: 4rem 2rem;
            max-width: 1200px;
            margin: 0 auto;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
        }
        .card {
            background: white;
            padding: 2rem;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            text-align: center;
        }
        .card h3 {
            color: #1e3d59;
            margin-bottom: 1rem;
        }

        /* 互動小工具：羽球記分板 */
        .scoreboard-section {
            background-color: #1e3d59;
            color: white;
            padding: 4rem 2rem;
            text-align: center;
        }
        .scoreboard {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 3rem;
            margin-top: 2rem;
        }
        .team {
            background: rgba(255,255,255,0.1);
            padding: 2rem;
            border-radius: 10px;
            min-width: 150px;
        }
        .score {
            font-size: 4rem;
            font-weight: bold;
            color: #ffc13b;
            margin: 1rem 0;
        }
        .score-btn {
            background: #ffc13b;
            border: none;
            color: #1e3d59;
            padding: 0.5rem 1rem;
            font-size: 1rem;
            font-weight: bold;
            border-radius: 5px;
            cursor: pointer;
            margin: 0 0.2rem;
        }
        .score-btn.reset {
            background: #ff6b6b;
            color: white;
            margin-top: 2rem;
        }

        /* 頁尾 */
        footer {
            background-color: #173044;
            color: #85a7cc;
            text-align: center;
            padding: 1.5rem;
            font-size: 0.9rem;
        }

        /* RWD 手機版優化 */
        @media (max-width: 768px) {
            header { flex-direction: column; gap: 1rem; }
            nav ul { flex-direction: column; align-items: center; gap: 0.5rem; }
            nav ul li { margin-left: 0; }
            .hero h1 { font-size: 2rem; }
            .scoreboard { flex-direction: column; gap: 1.5rem; }
        }
    </style>
</head>
<body>

    <!-- 導覽列 -->
    <header>
        <div class="logo">🏸 密斯特羽球</div>
        <nav>
            <ul>
                <li><a href="#">首頁</a></li>
                <li><a href="#features">球隊特色</a></li>
                <li><a href="#scoreboard">即時記分板</a></li>
            </ul>
        </nav>
    </header>

    <!-- 主視覺 Banner -->
    <section class="hero">
        <h1>揮灑汗水，享受擊球的瞬間！</h1>
        <p>不論你是剛接觸羽球的新手，還是尋找對手的神隊友，這裡都有屬於你的舞台。加入我們，一起燃燒羽球魂！</p>
        <a href="#scoreboard" class="btn">試用記分板</a>
    </section>

    <!-- 特色區塊 -->
    <section class="services" id="features">
        <div class="card">
            <h3>技術教學 💡</h3>
            <p>從基礎的握拍、正反手發球，到進階的殺球與步法移位，完整的精進指南。</p>
        </div>
        <div class="card">
            <h3>裝備評測 👟</h3>
            <p>不知道怎麼挑球拍與羽球鞋？我們提供最客觀的各品牌平衡點、中桿硬度實測報告。</p>
        </div>
        <div class="card">
            <h3>球友揪團 🤝</h3>
            <p>每週固定臨打團、季打團資訊更新，讓你隨時隨地都有球可以打。</p>
        </div>
    </section>

    <!-- 記分板小工具 -->
    <section class="scoreboard-section" id="scoreboard">
        <h2>🏸 線上羽球臨打記分板</h2>
        <p>打球時手機放旁邊，點擊即可輕鬆計分！</p>
        
        <div class="scoreboard">
            <div class="team">
                <h3>TEAM A</h3>
                <div class="score" id="scoreA">0</div>
                <button class="score-btn" onclick="changeScore('A', 1)">+1</button>
                <button class="score-btn" onclick="changeScore('A', -1)">-1</button>
            </div>
            
            <div class="team">
                <h3>VS</h3>
            </div>

            <div class="team">
                <h3>TEAM B</h3>
                <div class="score" id="scoreB">0</div>
                <button class="score-btn" onclick="changeScore('B', 1)">+1</button>
                <button class="score-btn" onclick="changeScore('B', -1)">-1</button>
            </div>
        </div>
        <button class="score-btn reset" onclick="resetScore()">重設分數</button>
    </section>

    <!-- 頁尾 -->
    <footer>
        <p>&copy; 2026 密斯特羽球網站. Built with GitHub Pages.</p>
    </footer>

    <!-- JavaScript 互動功能 -->
    <script>
        let scoreA = 0;
        let scoreB = 0;

        function changeScore(team, value) {
            if (team === 'A') {
                scoreA = Math.max(0, scoreA + value); // 確保分數不會低於 0
                document.getElementById('scoreA').innerText = scoreA;
            } else if (team === 'B') {
                scoreB = Math.max(0, scoreB + value);
                document.getElementById('scoreB').innerText = scoreB;
            }
        }

        function resetScore() {
            scoreA = 0;
            scoreB = 0;
            document.getElementById('scoreA').innerText = 0;
            document.getElementById('scoreB').innerText = 0;
        }
    </script>
</body>
</html>
