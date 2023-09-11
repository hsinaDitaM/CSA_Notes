---
toc: true
comments: true
layout: post
title: Snake Game Revamped
description:  Snake Game
type: hacks
---
<head>
    <title>Snake Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: #f2f2f2;
        }

        #game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        #game-board {
            position: relative;
            width: 400px;
            height: 400px;
            border: 2px solid #333;
            background-color: #ddd;
            display: grid;
            grid-template-columns: repeat(20, 1fr);
        }

        .snake, .food {
            position: absolute;
            width: 18px;
            height: 18px;
            background-color: #4CAF50;
            border-radius: 50%;
        }

        .food {
            background-color: #FF5722;
        }

        #score-container {
            margin-top: 20px;
            font-size: 20px;
            font-weight: bold;
        }

        #best-score-container {
            font-size: 16px;
            margin-top: 10px;
        }

        #score-table {
            margin-bottom: 20px;
        }

        #score-table td {
            padding: 5px 10px;
            text-align: center;
            font-weight: bold;
        }

        #restart-button {
            font-size: 16px;
            padding: 10px 20px;
            background-color: #4CAF50;
            color: #fff;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        body {
            margin: 0;
            padding: 0;
            background: gray
        }
        body {
            font-family: Arial, sans-serif;
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: #f2f2f2;
            margin: 0; /* Remove margin to fill the whole body */
        }

        #game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        #game-board {
            position: relative;
            width: 400px;
            height: 400px;
            border: 2px solid #333;
            display: grid;
            grid-template-columns: repeat(20, 1fr);
        }

    </style>
</head>
<body>
    <div id="game-container">
        <h1>"Classic" Snake Game</h1>
        <table id="score-table">
            <tr>
                <td>Score:</td>
                <td id="score">0</td>
            </tr>
            <tr>
                <td>Best Score:</td>
                <td id="best-score">0</td>
            </tr>
        </table>
        <div id="game-board"></div>
        <button id="restart-button">Restart</button>
    </div>

    <script>
        cd game variables
        var gridSize = 20;
        var gridWidth = 400 / gridSize;
        var gridHeight = 400 / gridSize;
        var snake = [{ x: 0, y: 0 }];
        var food = { x: 0, y: 0 };
        var direction = "right";
        var gameLoop;
        var score = 0;
        var bestScore = 0;

        // starts the game
        function init() {
            bestScore = getBestScoreFromLocalStorage();
            document.getElementById("best-score").innerText = bestScore;

            createFood();
            gameLoop = setInterval(update, 100);
            document.addEventListener("keydown", changeDirection);

            var restartButton = document.getElementById("restart-button");
            restartButton.addEventListener("click", restartGame);
        }

        // restarts the game
        function restartGame() {
            clearInterval(gameLoop);
            snake = [{ x: 0, y: 0 }];
            direction = "right";
            score = 0;
            document.getElementById("score").innerText = score;
            createFood();
            gameLoop = setInterval(update, 100);
        }

        // creates food 
        function createFood() {
            food.x = Math.floor(Math.random() * gridWidth);
            food.y = Math.floor(Math.random() * gridHeight);
        }

        function update() {
            var head = { x: snake[0].x, y: snake[0].y };

            // update the snake's head position 
            switch (direction) {
                case "up":
                    head.y--;
                    break;
                case "down":
                    head.y++;
                    break;
                case "left":
                    head.x--;
                    break;
                case "right":
                    head.x++;
                    break;
            }

            // checks if the snake has collided with the walls or itself
            if (
                head.x < 0 ||
                head.x >= gridWidth ||
                head.y < 0 ||
                head.y >= gridHeight ||
                checkCollision(head)
            ) {
                gameOver();
                return;
            }

            // checks if the snake has eaten the food
            if (head.x === food.x && head.y === food.y) {
                score++;
                document.getElementById("score").innerText = score;
                createFood();
            } else {
                snake.pop(); // removes the tail segment if the snake hasn't eaten the food
            }

            snake.unshift(head); // adds the new head segment to the snake

            draw(); // updates the game board
        }

        // checks if the snake has collided with itself
        function checkCollision(head) {
            for (var i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    return true;
                }
            }
            return false;
        }

        // ends the game
        function gameOver() {
            clearInterval(gameLoop);
            updateBestScore();
            alert("Game Over!");
        }

        // draws the game board
        function draw() {
            var gameBoard = document.getElementById("game-board");
            gameBoard.innerHTML = "";

            // draw the snake
            snake.forEach(function (segment) {
                var snakeSegment = document.createElement("div");
                snakeSegment.className = "snake";
                snakeSegment.style.left = segment.x * gridSize + "px";
                snakeSegment.style.top = segment.y * gridSize + "px";
                gameBoard.appendChild(snakeSegment);
            });

            // draws the food
            var foodElement = document.createElement("div");
            foodElement.className = "food";
            foodElement.style.left = food.x * gridSize + "px";
            foodElement.style.top = food.y * gridSize + "px";
            gameBoard.appendChild(foodElement);
        }

        // changes the direction of the snake based on keyboard input
        function changeDirection(event) {
            var keyPressed = event.key.toLowerCase();

            if (keyPressed === "a" && direction !== "right") {
                direction = "left";
            } else if (keyPressed === "w" && direction !== "down") {
                direction = "up";
            } else if (keyPressed === "d" && direction !== "left") {
                direction = "right";
            } else if (keyPressed === "s" && direction !== "up") {
                direction = "down";
            }
        }

        // updates the best score and store it in localStorage
        function updateBestScore() {
            if (score > bestScore) {
                bestScore = score;
                document.getElementById("best-score").innerText = bestScore;
                setBestScoreInLocalStorage(bestScore);
            }
        }

        // stores the best score in localStorage
        function setBestScoreInLocalStorage(score) {
            localStorage.setItem("bestScore", score);
        }

        // retrieves the best score from localStorage
        function getBestScoreFromLocalStorage() {
            return localStorage.getItem("bestScore") || 0;
        }

        // start the game
        init();
    </script>
</body>
</html>