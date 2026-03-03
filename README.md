<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Ping Pong Simples</title>
  <style>
    body {
      margin: 0;
      background: #000;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      color: #fff;
      font-family: sans-serif;
    }

    canvas {
      background: #111;
      border: 2px solid #0f0;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="500"></canvas>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    const paddleWidth = 10;
    const paddleHeight = 100;
    let playerY = canvas.height / 2 - paddleHeight / 2;
    let aiY = canvas.height / 2 - paddleHeight / 2;
    const paddleSpeed = 6;

    const ballSize = 10;
    let ballX = canvas.width / 2;
    let ballY = canvas.height / 2;
    let ballSpeedX = 5;
    let ballSpeedY = 4;

    let upPressed = false;
    let downPressed = false;

    function drawRect(x, y, w, h, color) {
      ctx.fillStyle = color;
      ctx.fillRect(x, y, w, h);
    }

    function drawCircle(x, y, r, color) {
      ctx.fillStyle = color;
      ctx.beginPath();
      ctx.arc(x, y, r, 0, Math.PI * 2);
      ctx.fill();
    }

    function drawNet() {
      for (let i = 0; i < canvas.height; i += 20) {
        drawRect(canvas.width / 2 - 1, i, 2, 10, "#0f0");
      }
    }

    function draw() {
      // Limpa tela
      drawRect(0, 0, canvas.width, canvas.height, "#000");

      // Rede
      drawNet();

      // Raquetes
      drawRect(0, playerY, paddleWidth, paddleHeight, "#0f0");
      drawRect(canvas.width - paddleWidth, aiY, paddleWidth, paddleHeight, "#f00");

      // Bola
      drawCircle(ballX, ballY, ballSize, "#fff");
    }

    function move() {
      // Jogador
      if (upPressed && playerY > 0) {
        playerY -= paddleSpeed;
      } else if (downPressed && playerY + paddleHeight < canvas.height) {
        playerY += paddleSpeed;
      }

      // Computador (IA simples)
      let aiCenter = aiY + paddleHeight / 2;
      if (ballY < aiCenter - 35) {
        aiY -= paddleSpeed * 0.6;
      } else if (ballY > aiCenter + 35) {
        aiY += paddleSpeed * 0.6;
      }

      // Bola
      ballX += ballSpeedX;
      ballY += ballSpeedY;

      // Colisão com topo/baixo
      if (ballY < 0 || ballY > canvas.height) {
        ballSpeedY *= -1;
      }

      // Colisão com raquete do jogador
      if (ballX < paddleWidth && ballY > playerY && ballY < playerY + paddleHeight) {
        ballSpeedX *= -1;
        let deltaY = ballY - (playerY + paddleHeight / 2);
        ballSpeedY = deltaY * 0.3;
      }

      // Colisão com raquete da IA
      if (ballX > canvas.width - paddleWidth &&
          ballY > aiY && ballY < aiY + paddleHeight) {
        ballSpeedX *= -1;
        let deltaY = ballY - (aiY + paddleHeight / 2);
        ballSpeedY = deltaY * 0.3;
      }

      // Reiniciar se sair
      if (ballX < 0 || ballX > canvas.width) {
        ballX = canvas.width / 2;
        ballY = canvas.height / 2;
        ballSpeedX *= -1;
        ballSpeedY = 4;
      }
    }

    function gameLoop() {
      move();
      draw();
      requestAnimationFrame(gameLoop);
    }

    document.addEventListener("keydown", (e) => {
      if (e.key === "w" || e.key === "W") upPressed = true;
      if (e.key === "s" || e.key === "S") downPressed = true;
    });

    document.addEventListener("keyup", (e) => {
      if (e.key === "w" || e.key === "W") upPressed = false;
      if (e.key === "s" || e.key === "S") downPressed = false;
    });

    gameLoop();
  </script>
</body>
</html>
