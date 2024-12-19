---
layout: base
title: Snake
permalink: /snake/
---


<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        /* General Body Styling */
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background: linear-gradient(120deg, #ff0000, rgb(255, 255, 255), #008000);
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }

        /* Center Wrapper */
        .container {
            text-align: center;
            width: 100%;
            max-width: 400px;
            padding: 20px;
            background-color: rgba(0, 0, 0, 0.7);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }

        /* Game Title */
        h2 {
            font-size: 2.5rem;
            margin-bottom: 20px;
            color: #ffffff;
            text-shadow: 2px 2px 10px rgba(255, 255, 255, 0.7);
        }

        /* Header Score Section */
        header {
            margin-bottom: 30px;
        }

        p.fs-4 {
            font-size: 1.2rem;
            margin: 0;
        }

        #score_value {
            font-weight: bold;
            font-size: 1.5rem;
            color: #ffffff;
        }

        /* Main Menu Styling */
        #menu, #gameover, #setting, #leaderboard {
            display: none;
        }

        #menu {
            display: block; /* Show menu by default */
        }

        .menu-btn {
            font-size: 1.2rem;
            background: #ffffff;
            color: #000;
            padding: 15px 20px;
            margin: 10px auto;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            display: block;
            width: 100%;
            max-width: 300px;
            text-transform: uppercase;
            font-weight: bold;
            transition: all 0.3s ease;
            box-shadow: 0 5px 10px rgba(0, 0, 0, 0.3);
        }

        .menu-btn:hover {
            background: #ffcccc;
            transform: scale(1.05);
            box-shadow: 0 7px 15px rgba(0, 0, 0, 0.5);
        }

        /* Canvas Styling */
        canvas {
            margin: 20px auto;
            border: 5px solid #fff;
            border-radius: 10px;
            background-color: #333;
            display: none; /* Hidden initially */
        }

        /* Setting Screen Styling */
        label {
            margin: 10px;
            padding: 5px 10px;
            border-radius: 5px;
            cursor: pointer;
            background-color: rgba(255, 255, 255, 0.1);
            transition: background-color 0.2s ease;
        }

        input[type="radio"]:checked + label {
            background-color: #ffffff;
            color: #000;
        }

        /* Game Over Screen */
        #gameover p {
            font-size: 1.3rem;
            color: #ffffff;
        }

        /* Leaderboard Styling */
        #leaderboard table {
            width: 100%;
            border-collapse: collapse;
        }

        #leaderboard th, #leaderboard td {
            padding: 10px;
            text-align: center;
            border: 1px solid #fff;
        }

        /* Responsive Design */
        @media screen and (max-width: 600px) {
            h2 {
                font-size: 2rem;
            }

            .menu-btn {
                font-size: 1rem;
                padding: 10px 15px;
            }

            canvas {
                width: 280px;
                height: 280px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Snake Game</h2>
        <!-- Score Section -->
        <header class="pb-3 mb-4">
            <p class="fs-4">Score: <span id="score_value">0</span></p>
        </header>
        <!-- Main Menu -->
        <div id="menu">
            <p>Welcome to Snake!</p>
            <button id="start_game" class="menu-btn">Start Game</button>
            <button id="leaderboard_menu" class="menu-btn">Leaderboard</button>
            <button id="setting_menu" class="menu-btn">Settings</button>
        </div>
        <!-- Game Over -->
        <div id="gameover">
            <p>Game Over! Your Score: <span id="final_score">0</span></p>
            <p>High Score: <span id="high_score">0</span></p>
            <button id="retry_game" class="menu-btn">Play Again</button>
            <button id="setting_menu1" class="menu-btn">Settings</button>
            <button id="leaderboard_menu1" class="menu-btn">Leaderboard</button>
        </div>
        <!-- Game Canvas -->
        <canvas id="snake" class="wrap" width="320" height="320" tabindex="1"></canvas>
        <!-- Leaderboard -->
        <div id="leaderboard">
            <h3>Leaderboard</h3>
            <table>
                <thead>
                    <tr>
                        <th>Rank</th>
                        <th>Score</th>
                    </tr>
                </thead>
                <tbody id="leaderboard_body">
                </tbody>
            </table>
            <button id="back_menu_leaderboard" class="menu-btn">Back to Menu</button>
        </div>
        <!-- Settings -->
        <div id="setting">
            <p>Settings:</p>
            <p>Speed:
                <input id="speed1" type="radio" name="speed" value="120" checked />
                <label for="speed1">Slow</label>
                <input id="speed2" type="radio" name="speed" value="75" />
                <label for="speed2">Normal</label>
                <input id="speed3" type="radio" name="speed" value="35" />
                <label for="speed3">Fast</label>
            </p>
            <p>Wall:
                <input id="wallon" type="radio" name="wall" value="1" checked />
                <label for="wallon">On</label>
                <input id="walloff" type="radio" name="wall" value="0" />
                <label for="walloff">Off</label>
            </p>
            <button id="back_menu" class="menu-btn">Back to Menu</button>
        </div>
    </div>
    <script>
        (function () {
            const canvas = document.getElementById("snake");
            const ctx = canvas.getContext("2d");
            const menu = document.getElementById("menu");
            const gameover = document.getElementById("gameover");
            const settings = document.getElementById("setting");
            const leaderboard = document.getElementById("leaderboard");
            const startGameButton = document.getElementById("start_game");
            const retryGameButton = document.getElementById("retry_game");
            const backMenuButton = document.getElementById("back_menu");
            const leaderboardMenuButton = document.getElementById("leaderboard_menu");
            const leaderboardMenuButton1 = document.getElementById("leaderboard_menu1");
            const backLeaderboardButton = document.getElementById("back_menu_leaderboard");
            const settingMenuButton = document.getElementById("setting_menu");
            const settingMenuButton1 = document.getElementById("setting_menu1");
            const scoreDisplay = document.getElementById("score_value");
            const finalScoreDisplay = document.getElementById("final_score");
            const highScoreDisplay = document.getElementById("high_score");
            const leaderboardBody = document.getElementById("leaderboard_body");
            const speedSettings = document.getElementsByName("speed");
            const wallSettings = document.getElementsByName("wall");

            // Game variables
            let snake = [{ x: 160, y: 160 }];
            let food = { x: 0, y: 0 };
            let direction = { x: 0, y: 0 };
            let nextDirection = { x: 0, y: 0 };
            let score = 0;
            let highScore = 0;
            let speed = 120;
            let wallEnabled = true;
            let gameInterval;
            let isPaused = false;

            // Helper functions
            function randomPosition() {
                return Math.floor(Math.random() * (canvas.width / 20)) * 20;
            }

            function spawnFood() {
                food.x = randomPosition();
                food.y = randomPosition();
                // Ensure food does not spawn on the snake
                if (snake.some(segment => segment.x === food.x && segment.y === food.y)) {
                    spawnFood();
                }
            }

            function drawSnake() {
                ctx.fillStyle = "#00FF00";
                snake.forEach(segment => {
                    ctx.fillRect(segment.x, segment.y, 20, 20);
                });
            }

            function drawFood() {
                ctx.fillStyle = "#" + Math.floor(Math.random() * 16777215).toString(16);
                ctx.fillRect(food.x, food.y, 20, 20);
            }

            function updateSnake() {
                const head = { x: snake[0].x + direction.x * 20, y: snake[0].y + direction.y * 20 };

                // Check for wall collision
                if (wallEnabled && (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height)) {
                    endGame();
                    return;
                }

                // Check for self-collision
                if (snake.some(segment => segment.x === head.x && segment.y === head.y)) {
                    endGame();
                    return;
                }

                // Add new head to the snake
                snake.unshift(head);

                // Check for food collision
                if (head.x === food.x && head.y === food.y) {
                    score++;
                    updateScore();
                    spawnFood();
                } else {
                    // Remove the last segment if no food was eaten
                    snake.pop();
                }

                // Handle wrapping if wall is disabled
                if (!wallEnabled) {
                    if (head.x < 0) head.x = canvas.width - 20;
                    if (head.x >= canvas.width) head.x = 0;
                    if (head.y < 0) head.y = canvas.height - 20;
                    if (head.y >= canvas.height) head.y = 0;
                }
            }

            function updateScore() {
                scoreDisplay.textContent = score;
                if (score > highScore) {
                    highScore = score;
                    highScoreDisplay.textContent = highScore;
                }
            }

            function endGame() {
                clearInterval(gameInterval);
                finalScoreDisplay.textContent = score;
                highScoreDisplay.textContent = highScore;
                saveHighScore();
                showScreen("gameover");
            }

            function saveHighScore() {
                const leaderboardData = JSON.parse(localStorage.getItem("leaderboard")) || [];
                leaderboardData.push(score);
                leaderboardData.sort((a, b) => b - a); // Sort in descending order
                localStorage.setItem("leaderboard", JSON.stringify(leaderboardData.slice(0, 5))); // Keep top 5 scores
            }

            function loadLeaderboard() {
                const leaderboardData = JSON.parse(localStorage.getItem("leaderboard")) || [];
                leaderboardBody.innerHTML = leaderboardData
                    .map((score, index) => `<tr><td>${index + 1}</td><td>${score}</td></tr>`)
                    .join("");
            }

            function draw() {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                drawFood();
                drawSnake();
            }

            function gameLoop() {
                if (!isPaused) {
                    updateSnake();
                    draw();
                }
            }

            function setSpeed() {
                speed = Array.from(speedSettings).find(radio => radio.checked).value;
            }

            function setWall() {
                wallEnabled = Array.from(wallSettings).find(radio => radio.checked).value === "1";
            }

            function showScreen(screen) {
                menu.style.display = "none";
                gameover.style.display = "none";
                settings.style.display = "none";
                leaderboard.style.display = "none";
                canvas.style.display = "none";

                if (screen === "menu") menu.style.display = "block";
                else if (screen === "gameover") gameover.style.display = "block";
                else if (screen === "settings") settings.style.display = "block";
                else if (screen === "leaderboard") leaderboard.style.display = "block";
                else if (screen === "game") canvas.style.display = "block";
            }

            function startGame() {
                snake = [{ x: 160, y: 160 }];
                direction = { x: 0, y: 0 };
                nextDirection = { x: 0, y: 0 };
                score = 0;
                updateScore();
                spawnFood();
                setSpeed();
                setWall();
                showScreen("game");
                gameInterval = setInterval(gameLoop, speed);
            }

            function togglePause() {
                isPaused = !isPaused;
            }

            // Event Listeners
            document.addEventListener("keydown", e => {
                if (e.key === "ArrowUp" && direction.y === 0) nextDirection = { x: 0, y: -1 };
                else if (e.key === "ArrowDown" && direction.y === 0) nextDirection = { x: 0, y: 1 };
                else if (e.key === "ArrowLeft" && direction.x === 0) nextDirection = { x: -1, y: 0 };
                else if (e.key === "ArrowRight" && direction.x === 0) nextDirection = { x: 1, y: 0 };
                else if (e.key === " ") togglePause();
            });

            startGameButton.addEventListener("click", startGame);
            retryGameButton.addEventListener("click", startGame);
            backMenuButton.addEventListener("click", () => showScreen("menu"));
            settingMenuButton.addEventListener("click", () => showScreen("settings"));
            settingMenuButton1.addEventListener("click", () => showScreen("settings"));
            leaderboardMenuButton.addEventListener("click", () => {
                loadLeaderboard();
                showScreen("leaderboard");
            });
            leaderboardMenuButton1.addEventListener("click", () => {
                loadLeaderboard();
                showScreen("leaderboard");
            });
            backLeaderboardButton.addEventListener("click", () => showScreen("menu"));

            // Start the game in the menu screen
            showScreen("menu");
        })();
    </script>
</body>
</html>

