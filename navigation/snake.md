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
        #menu, #gameover, #setting {
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
            <button id="setting_menu" class="menu-btn">Settings</button>
        </div>
        <!-- Game Over -->
        <div id="gameover">
            <p>Game Over! Try again?</p>
            <button id="retry_game" class="menu-btn">Play Again</button>
            <button id="setting_menu1" class="menu-btn">Settings</button>
        </div>
        <!-- Game Canvas -->
        <canvas id="snake" class="wrap" width="320" height="320" tabindex="1"></canvas>
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
            // Game Setup
            const canvas = document.getElementById("snake");
            const ctx = canvas.getContext("2d");
            const menu = document.getElementById("menu");
            const gameover = document.getElementById("gameover");
            const settings = document.getElementById("setting");
            const startGameButton = document.getElementById("start_game");
            const retryGameButton = document.getElementById("retry_game");
            const backMenuButton = document.getElementById("back_menu");
            const settingMenuButton = document.getElementById("setting_menu");
            const settingMenuButton1 = document.getElementById("setting_menu1");
            const scoreDisplay = document.getElementById("score_value");
            const speedSettings = document.getElementsByName("speed");
            const wallSettings = document.getElementsByName("wall");
            let snake = [];
            let food = {};
            let score = 0;
            let snakeSpeed = 120;
            let wall = 1;
            let direction = 1; // 0=Up, 1=Right, 2=Down, 3=Left
            let nextDirection = direction;
            let gameInterval;

            const BLOCK_SIZE = 10;
            const WIDTH = canvas.width / BLOCK_SIZE;
            const HEIGHT = canvas.height / BLOCK_SIZE;

            // Utility Functions
            const showScreen = (screen) => {
                menu.style.display = screen === "menu" ? "block" : "none";
                gameover.style.display = screen === "gameover" ? "block" : "none";
                settings.style.display = screen === "settings" ? "block" : "none";
                canvas.style.display = screen === "game" ? "block" : "none";
            };

            const setSnakeSpeed = (value) => {
                snakeSpeed = parseInt(value, 10);
            };

            const setWall = (value) => {
                wall = parseInt(value, 10);
            };

            const updateScore = () => {
                scoreDisplay.textContent = score;
            };

            // Game Initialization
            const initGame = () => {
                snake = [{ x: 5, y: 5 }];
                direction = 1;
                nextDirection = direction;
                score = 0;
                updateScore();
                spawnFood();
                clearInterval(gameInterval);
                gameInterval = setInterval(gameLoop, snakeSpeed);
            };

            const gameOver = () => {
                clearInterval(gameInterval);
                showScreen("gameover");
            };

            const spawnFood = () => {
                food.x = Math.floor(Math.random() * WIDTH);
                food.y = Math.floor(Math.random() * HEIGHT);
            };

            const gameLoop = () => {
                const head = { ...snake[0] };
                direction = nextDirection;

                // Move Snake
                if (direction === 0) head.y -= 1;
                if (direction === 1) head.x += 1;
                if (direction === 2) head.y += 1;
                if (direction === 3) head.x -= 1;

                // Wall Collision
                if (
                    (wall && (head.x < 0 || head.y < 0 || head.x >= WIDTH || head.y >= HEIGHT)) ||
                    snake.some((s) => s.x === head.x && s.y === head.y)
                ) {
                    return gameOver();
                }

                // Food Collision
                if (head.x === food.x && head.y === food.y) {
                    score += 1;
                    updateScore();
                    spawnFood();
                } else {
                    snake.pop(); // Remove tail if no food eaten
                }

                snake.unshift(head);

                // Draw Game
                ctx.fillStyle = "#333";
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                ctx.fillStyle = "#ffffff";
                ctx.fillRect(food.x * BLOCK_SIZE, food.y * BLOCK_SIZE, BLOCK_SIZE, BLOCK_SIZE);

                snake.forEach((segment, index) => {
                    const colors = ["#ff0000", "#ffffff", "#00ff00"];
                    ctx.fillStyle = colors[index % 3];
                    ctx.fillRect(segment.x * BLOCK_SIZE, segment.y * BLOCK_SIZE, BLOCK_SIZE, BLOCK_SIZE);
                });
            };

            // Event Listeners
            document.addEventListener("keydown", (e) => {
                if (e.key === "ArrowUp" && direction !== 2) nextDirection = 0;
                if (e.key === "ArrowRight" && direction !== 3) nextDirection = 1;
                if (e.key === "ArrowDown" && direction !== 0) nextDirection = 2;
                if (e.key === "ArrowLeft" && direction !== 1) nextDirection = 3;
            });

            startGameButton.addEventListener("click", () => {
                showScreen("game");
                initGame();
                canvas.focus();
            });

            retryGameButton.addEventListener("click", () => {
                showScreen("game");
                initGame();
                canvas.focus();
            });

            backMenuButton.addEventListener("click", () => showScreen("menu"));
            settingMenuButton.addEventListener("click", () => showScreen("settings"));
            settingMenuButton1.addEventListener("click", () => showScreen("settings"));

            // Setting Events
            speedSettings.forEach((radio) =>
                radio.addEventListener("change", () => setSnakeSpeed(radio.value))
            );

            wallSettings.forEach((radio) =>
                radio.addEventListener("change", () => setWall(radio.value))
            );
        })();
    </script>
</body>
</html>


