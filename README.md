<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Breakout Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: url('background.jpeg') no-repeat center center fixed;
            background-size: cover;
        }
        canvas {
            display: block;
            margin: auto;
        }
    </style>
</head>
<body>
    <!-- The canvas for the game -->
    <canvas id="breakout"></canvas>

    <!-- Sound effect for the paddle bounce -->
    <audio id="bounceSound" src="sound.m4a"></audio>

    <!-- Background music that loops endlessly -->
    <audio id="backgroundMusic" src="background_music.m4a" loop autoplay></audio>

    <script>
        const canvas = document.getElementById('breakout');
        const ctx = canvas.getContext('2d');

        // Canvas dimensions
        canvas.width = window.innerWidth * 0.8;
        canvas.height = window.innerHeight * 0.8;

        // Game variables
        const paddle = {
            width: 120,
            height: 20,
            x: (canvas.width - 120) / 2,
            y: canvas.height - 40,
            dx: 0,
            speed: 7,
        };

        const ball = {
            x: canvas.width / 2,
            y: canvas.height / 2,
            radius: 10,
            speed: 4,
            dx: 4,
            dy: -4,
        };

        const bricks = [];
        const brickRows = 5;
        const brickCols = 10;
        const brickWidth = 75;
        const brickHeight = 20;
        const brickPadding = 10;
        const brickOffsetTop = 50;
        const brickOffsetLeft = 35;

        for (let r = 0; r < brickRows; r++) {
            bricks[r] = [];
            for (let c = 0; c < brickCols; c++) {
                bricks[r][c] = { x: 0, y: 0, visible: true };
            }
        }

        // Load sounds
        const bounceSound = document.getElementById('bounceSound');
        const backgroundMusic = document.getElementById('backgroundMusic');
        backgroundMusic.volume = 0.5; // Adjust volume for background music

        // Draw paddle
        function drawPaddle() {
            ctx.fillStyle = '#0095dd';
            ctx.fillRect(paddle.x, paddle.y, paddle.width, paddle.height);
        }

        // Draw ball
        function drawBall() {
            ctx.beginPath();
            ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
            ctx.fillStyle = '#0095dd';
            ctx.fill();
            ctx.closePath();
        }

        // Draw bricks
        function drawBricks() {
            bricks.forEach((row, r) => {
                row.forEach((brick, c) => {
                    if (brick.visible) {
                        const brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
                        const brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
                        brick.x = brickX;
                        brick.y = brickY;
                        ctx.fillStyle = '#0095dd';
                        ctx.fillRect(brickX, brickY, brickWidth, brickHeight);
                    }
                });
            });
        }

        // Move paddle
        function movePaddle() {
            paddle.x += paddle.dx;
            if (paddle.x < 0) paddle.x = 0;
            if (paddle.x + paddle.width > canvas.width) paddle.x = canvas.width - paddle.width;
        }

        // Move ball
        function moveBall() {
            ball.x += ball.dx;
            ball.y += ball.dy;

            // Wall collision
            if (ball.x - ball.radius < 0 || ball.x + ball.radius > canvas.width) ball.dx *= -1;
            if (ball.y - ball.radius < 0) ball.dy *= -1;

            // Paddle collision
            if (
                ball.x > paddle.x &&
                ball.x < paddle.x + paddle.width &&
                ball.y + ball.radius > paddle.y
            ) {
                ball.dy *= -1;
                bounceSound.play(); // Play paddle bounce sound
            }

            // Brick collision
            bricks.forEach(row => {
                row.forEach(brick => {
                    if (brick.visible) {
                        if (
                            ball.x > brick.x &&
                            ball.x < brick.x + brickWidth &&
                            ball.y - ball.radius > brick.y &&
                            ball.y - ball.radius < brick.y + brickHeight
                        ) {
                            ball.dy *= -1;
                            brick.visible = false;
                        }
                    }
                });
            });

            // Lose condition
            if (ball.y + ball.radius > canvas.height) {
                alert('Game Over');
                document.location.reload();
            }
        }

        // Draw everything
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawPaddle();
            drawBall();
            drawBricks();
        }

        // Update game frame
        function update() {
            movePaddle();
            moveBall();
            draw();
            requestAnimationFrame(update);
        }

        // Keyboard event listeners
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowRight' || e.key === 'd') paddle.dx = paddle.speed;
            if (e.key === 'ArrowLeft' || e.key === 'a') paddle.dx = -paddle.speed;
        });
        document.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowRight' || e.key === 'd') paddle.dx = 0;
            if (e.key === 'ArrowLeft' || e.key === 'a') paddle.dx = 0;
        });

        update();
    </script>
</body>
</html>
