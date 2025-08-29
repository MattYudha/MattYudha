<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GitHub Snake Animation</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #0d1117, #161b22);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        .container {
            text-align: center;
            background: rgba(22, 27, 34, 0.8);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(48, 54, 61, 0.3);
        }

        h1 {
            color: #58a6ff;
            margin-bottom: 20px;
            font-size: 2.5em;
            text-shadow: 0 0 20px rgba(88, 166, 255, 0.3);
        }

        .github-grid {
            display: grid;
            grid-template-columns: repeat(53, 12px);
            grid-template-rows: repeat(7, 12px);
            gap: 2px;
            margin: 20px auto;
            padding: 20px;
            background: rgba(13, 17, 23, 0.8);
            border-radius: 10px;
            border: 2px solid rgba(48, 54, 61, 0.5);
            position: relative;
            overflow: hidden;
        }

        .github-grid::before {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            background: linear-gradient(45deg, #58a6ff, #39d353, #f85149, #ffab70);
            z-index: -1;
            border-radius: 12px;
            opacity: 0.3;
            animation: borderGlow 3s ease-in-out infinite alternate;
        }

        @keyframes borderGlow {
            0% { opacity: 0.3; }
            100% { opacity: 0.6; }
        }

        .cell {
            width: 12px;
            height: 12px;
            border-radius: 2px;
            background: #161b22;
            border: 1px solid rgba(48, 54, 61, 0.3);
            transition: all 0.3s ease;
            position: relative;
        }

        .cell.level-1 {
            background: #0e4429;
            box-shadow: 0 0 8px rgba(57, 211, 83, 0.4);
            animation: pulse 2s ease-in-out infinite;
        }

        .cell.level-2 {
            background: #006d32;
            box-shadow: 0 0 12px rgba(57, 211, 83, 0.6);
            animation: pulse 2s ease-in-out infinite 0.2s;
        }

        .cell.level-3 {
            background: #26a641;
            box-shadow: 0 0 16px rgba(57, 211, 83, 0.8);
            animation: pulse 2s ease-in-out infinite 0.4s;
        }

        .cell.level-4 {
            background: #39d353;
            box-shadow: 0 0 20px rgba(57, 211, 83, 1);
            animation: pulse 2s ease-in-out infinite 0.6s;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.1); }
        }

        .snake-segment {
            background: linear-gradient(45deg, #58a6ff, #1f6feb) !important;
            border-radius: 50% !important;
            box-shadow: 0 0 15px rgba(88, 166, 255, 0.8) !important;
            transform: scale(1.2);
            animation: snakeGlow 0.8s ease-in-out infinite alternate;
            z-index: 10;
            border: 2px solid rgba(255, 255, 255, 0.3) !important;
        }

        .snake-head {
            background: linear-gradient(45deg, #f85149, #da3633) !important;
            box-shadow: 0 0 20px rgba(248, 81, 73, 1) !important;
            transform: scale(1.3) !important;
            animation: headGlow 0.6s ease-in-out infinite alternate;
        }

        .snake-head::after {
            content: '👁️';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 8px;
        }

        @keyframes snakeGlow {
            0% { box-shadow: 0 0 15px rgba(88, 166, 255, 0.8); }
            100% { box-shadow: 0 0 25px rgba(88, 166, 255, 1); }
        }

        @keyframes headGlow {
            0% { 
                box-shadow: 0 0 20px rgba(248, 81, 73, 1);
                transform: scale(1.3);
            }
            100% { 
                box-shadow: 0 0 30px rgba(248, 81, 73, 1.2);
                transform: scale(1.35);
            }
        }

        .eaten {
            animation: eatEffect 0.5s ease-out forwards;
        }

        @keyframes eatEffect {
            0% { 
                transform: scale(1.2);
                background: #39d353;
                box-shadow: 0 0 20px rgba(57, 211, 83, 1);
            }
            50% { 
                transform: scale(1.8);
                background: #ffab70;
                box-shadow: 0 0 30px rgba(255, 171, 112, 1);
            }
            100% { 
                transform: scale(0);
                background: transparent;
                box-shadow: none;
            }
        }

        .stats {
            display: flex;
            justify-content: space-around;
            margin: 20px 0;
            background: rgba(13, 17, 23, 0.6);
            padding: 15px;
            border-radius: 10px;
            border: 1px solid rgba(48, 54, 61, 0.3);
        }

        .stat {
            text-align: center;
            color: #f0f6fc;
        }

        .stat-value {
            font-size: 2em;
            font-weight: bold;
            color: #39d353;
            text-shadow: 0 0 10px rgba(57, 211, 83, 0.5);
        }

        .controls {
            margin-top: 20px;
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        .btn {
            padding: 10px 20px;
            background: linear-gradient(45deg, #238636, #2ea043);
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.3s ease;
            box-shadow: 0 2px 8px rgba(35, 134, 54, 0.3);
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(35, 134, 54, 0.5);
            background: linear-gradient(45deg, #2ea043, #238636);
        }

        .btn:active {
            transform: translateY(0);
        }

        .speed-slider {
            margin: 20px 0;
            text-align: center;
        }

        .speed-slider input[type="range"] {
            width: 200px;
            height: 6px;
            background: #21262d;
            outline: none;
            border-radius: 3px;
            -webkit-appearance: none;
        }

        .speed-slider input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            appearance: none;
            width: 20px;
            height: 20px;
            background: #39d353;
            cursor: pointer;
            border-radius: 50%;
            box-shadow: 0 0 10px rgba(57, 211, 83, 0.5);
        }

        .speed-label {
            color: #f0f6fc;
            margin-bottom: 10px;
            display: block;
        }

        .particles {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            overflow: hidden;
        }

        .particle {
            position: absolute;
            width: 4px;
            height: 4px;
            background: #39d353;
            border-radius: 50%;
            animation: float 3s ease-in-out infinite;
            opacity: 0;
        }

        @keyframes float {
            0% { 
                opacity: 1;
                transform: translateY(0px) scale(1);
            }
            100% { 
                opacity: 0;
                transform: translateY(-100px) scale(0.5);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐍 GitHub Snake Game</h1>
        
        <div class="stats">
            <div class="stat">
                <div class="stat-value" id="score">0</div>
                <div>Score</div>
            </div>
            <div class="stat">
                <div class="stat-value" id="length">3</div>
                <div>Length</div>
            </div>
            <div class="stat">
                <div class="stat-value" id="contributions">247</div>
                <div>Contributions</div>
            </div>
        </div>

        <div class="github-grid" id="grid">
            <div class="particles" id="particles"></div>
        </div>

        <div class="speed-slider">
            <label class="speed-label" for="speed">Snake Speed</label>
            <input type="range" id="speed" min="1" max="10" value="5">
        </div>

        <div class="controls">
            <button class="btn" onclick="startGame()">🎮 Start Game</button>
            <button class="btn" onclick="pauseGame()">⏸️ Pause</button>
            <button class="btn" onclick="resetGame()">🔄 Reset</button>
        </div>
    </div>

    <script>
        class GitHubSnake {
            constructor() {
                this.grid = document.getElementById('grid');
                this.particles = document.getElementById('particles');
                this.cols = 53;
                this.rows = 7;
                this.cells = [];
                this.snake = [];
                this.direction = { x: 1, y: 0 };
                this.food = [];
                this.score = 0;
                this.gameRunning = false;
                this.gameLoop = null;
                this.speed = 200;
                
                this.initGrid();
                this.generateGitHubPattern();
                this.initSnake();
                this.bindEvents();
                
                // Auto start for demo
                setTimeout(() => this.startGame(), 1000);
            }

            initGrid() {
                this.grid.innerHTML = '<div class="particles" id="particles"></div>';
                this.particles = document.getElementById('particles');
                
                for (let i = 0; i < this.rows * this.cols; i++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.dataset.index = i;
                    this.grid.appendChild(cell);
                    this.cells.push(cell);
                }
            }

            generateGitHubPattern() {
                // Simulate realistic GitHub contribution pattern
                this.food = [];
                
                for (let i = 0; i < this.rows * this.cols; i++) {
                    const randomValue = Math.random();
                    let level = 0;
                    
                    if (randomValue > 0.85) level = 4;
                    else if (randomValue > 0.7) level = 3;
                    else if (randomValue > 0.5) level = 2;
                    else if (randomValue > 0.3) level = 1;
                    
                    if (level > 0) {
                        this.cells[i].className = `cell level-${level}`;
                        this.food.push({ index: i, level });
                    }
                }
            }

            initSnake() {
                // Start snake in middle left
                const startRow = Math.floor(this.rows / 2);
                const startCol = 5;
                
                this.snake = [];
                for (let i = 0; i < 3; i++) {
                    const index = startRow * this.cols + (startCol - i);
                    this.snake.push({ index, x: startCol - i, y: startRow });
                }
                
                this.updateSnakeDisplay();
            }

            updateSnakeDisplay() {
                // Clear previous snake display
                this.cells.forEach(cell => {
                    cell.classList.remove('snake-segment', 'snake-head');
                });

                // Display snake
                this.snake.forEach((segment, i) => {
                    if (segment.index >= 0 && segment.index < this.cells.length) {
                        if (i === 0) {
                            this.cells[segment.index].classList.add('snake-head');
                        } else {
                            this.cells[segment.index].classList.add('snake-segment');
                        }
                    }
                });
            }

            moveSnake() {
                if (!this.gameRunning) return;

                const head = { ...this.snake[0] };
                head.x += this.direction.x;
                head.y += this.direction.y;

                // Wrap around edges
                if (head.x >= this.cols) head.x = 0;
                if (head.x < 0) head.x = this.cols - 1;
                if (head.y >= this.rows) head.y = 0;
                if (head.y < 0) head.y = this.rows - 1;

                head.index = head.y * this.cols + head.x;

                // Check collision with self
                if (this.snake.some(segment => segment.index === head.index)) {
                    this.gameOver();
                    return;
                }

                this.snake.unshift(head);

                // Check food collision
                const foodIndex = this.food.findIndex(f => f.index === head.index);
                if (foodIndex !== -1) {
                    const eatenFood = this.food[foodIndex];
                    this.score += eatenFood.level * 10;
                    this.food.splice(foodIndex, 1);
                    
                    // Create eat effect
                    this.cells[head.index].classList.add('eaten');
                    setTimeout(() => {
                        this.cells[head.index].classList.remove('eaten');
                        this.cells[head.index].className = 'cell';
                    }, 500);
                    
                    // Create particles
                    this.createParticles(head.index);
                    
                    // Update stats
                    document.getElementById('score').textContent = this.score;
                    document.getElementById('length').textContent = this.snake.length;
                    
                    // Generate new food if running low
                    if (this.food.length < 20) {
                        this.addRandomFood();
                    }
                } else {
                    // Remove tail if no food eaten
                    const tail = this.snake.pop();
                    if (tail && !this.food.some(f => f.index === tail.index)) {
                        this.cells[tail.index].classList.remove('snake-segment', 'snake-head');
                    }
                }

                this.updateSnakeDisplay();
                
                // Change direction occasionally for interesting movement
                if (Math.random() < 0.1) {
                    this.changeDirectionTowardsFood();
                }
            }

            changeDirectionTowardsFood() {
                if (this.food.length === 0) return;

                const head = this.snake[0];
                const nearestFood = this.food.reduce((nearest, current) => {
                    const currentDist = Math.abs(current.index % this.cols - head.x) + 
                                      Math.abs(Math.floor(current.index / this.cols) - head.y);
                    const nearestDist = Math.abs(nearest.index % this.cols - head.x) + 
                                       Math.abs(Math.floor(nearest.index / this.cols) - head.y);
                    return currentDist < nearestDist ? current : nearest;
                });

                const foodX = nearestFood.index % this.cols;
                const foodY = Math.floor(nearestFood.index / this.cols);

                const directions = [];
                if (foodX > head.x) directions.push({ x: 1, y: 0 });
                if (foodX < head.x) directions.push({ x: -1, y: 0 });
                if (foodY > head.y) directions.push({ x: 0, y: 1 });
                if (foodY < head.y) directions.push({ x: 0, y: -1 });

                if (directions.length > 0) {
                    this.direction = directions[Math.floor(Math.random() * directions.length)];
                }
            }

            addRandomFood() {
                for (let i = 0; i < 10; i++) {
                    const randomIndex = Math.floor(Math.random() * this.cells.length);
                    if (!this.snake.some(segment => segment.index === randomIndex) &&
                        !this.food.some(f => f.index === randomIndex)) {
                        const level = Math.floor(Math.random() * 4) + 1;
                        this.cells[randomIndex].className = `cell level-${level}`;
                        this.food.push({ index: randomIndex, level });
                    }
                }
            }

            createParticles(index) {
                const cell = this.cells[index];
                const rect = cell.getBoundingClientRect();
                const gridRect = this.grid.getBoundingClientRect();
                
                for (let i = 0; i < 5; i++) {
                    const particle = document.createElement('div');
                    particle.className = 'particle';
                    particle.style.left = (rect.left - gridRect.left + 6) + 'px';
                    particle.style.top = (rect.top - gridRect.top + 6) + 'px';
                    particle.style.animationDelay = (i * 0.1) + 's';
                    this.particles.appendChild(particle);
                    
                    setTimeout(() => {
                        if (particle.parentNode) {
                            particle.parentNode.removeChild(particle);
                        }
                    }, 3000);
                }
            }

            startGame() {
                if (this.gameRunning) return;
                
                this.gameRunning = true;
                this.gameLoop = setInterval(() => this.moveSnake(), this.speed);
            }

            pauseGame() {
                this.gameRunning = false;
                if (this.gameLoop) {
                    clearInterval(this.gameLoop);
                    this.gameLoop = null;
                }
            }

            resetGame() {
                this.pauseGame();
                this.score = 0;
                this.initGrid();
                this.generateGitHubPattern();
                this.initSnake();
                document.getElementById('score').textContent = '0';
                document.getElementById('length').textContent = '3';
            }

            gameOver() {
                this.pauseGame();
                setTimeout(() => {
                    alert(`Game Over! Final Score: ${this.score}\nSnake Length: ${this.snake.length}`);
                    this.resetGame();
                }, 100);
            }

            bindEvents() {
                document.getElementById('speed').addEventListener('input', (e) => {
                    this.speed = 300 - (parseInt(e.target.value) * 25);
                    if (this.gameRunning) {
                        this.pauseGame();
                        this.startGame();
                    }
                });

                // Keyboard controls
                document.addEventListener('keydown', (e) => {
                    if (!this.gameRunning) return;
                    
                    switch(e.key) {
                        case 'ArrowUp':
                            this.direction = { x: 0, y: -1 };
                            break;
                        case 'ArrowDown':
                            this.direction = { x: 0, y: 1 };
                            break;
                        case 'ArrowLeft':
                            this.direction = { x: -1, y: 0 };
                            break;
                        case 'ArrowRight':
                            this.direction = { x: 1, y: 0 };
                            break;
                        case ' ':
                            e.preventDefault();
                            this.gameRunning ? this.pauseGame() : this.startGame();
                            break;
                    }
                });
            }
        }

        let game;

        function startGame() {
            if (!game) {
                game = new GitHubSnake();
            } else {
                game.startGame();
            }
        }

        function pauseGame() {
            if (game) game.pauseGame();
        }

        function resetGame() {
            if (game) game.resetGame();
        }

        // Initialize game when page loads
        window.addEventListener('load', () => {
            game = new GitHubSnake();
        });
    </script>
</body>
</html>
