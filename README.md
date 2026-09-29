<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Pong</title>
  <style>
    body {
      margin: 0;
      background: #111;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
    }

    h1 {
      margin: 20px 0 10px;
    }

    canvas {
      background: #000;
      border: 3px solid white;
      max-width: 95vw;
    }

    p {
      color: #bbb;
    }
  </style>
</head>
<body>

<h1>🏓 Pong</h1>
<canvas id="game" width="800" height="500"></canvas>
<p>Sen: W / S &nbsp; | &nbsp; Bilgisayar: AI</p>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const paddleWidth = 12;
const paddleHeight = 100;

let player = {
  x: 20,
  y: 200,
  width: paddleWidth,
  height: paddleHeight,
  speed: 7
};

let computer = {
  x: canvas.width - 32,
  y: 200,
  width: paddleWidth,
  height: paddleHeight,
  speed: 5
};

let ball = {
  x: canvas.width / 2,
  y: canvas.height / 2,
  size: 12,
  speedX: 5,
  speedY: 4
};

let playerScore = 0;
let computerScore = 0;

const keys = {};

document.addEventListener("keydown", e => {
  keys[e.key.toLowerCase()] = true;
});

document.addEventListener("keyup", e => {
  keys[e.key.toLowerCase()] = false;
});

function resetBall(direction) {
  ball.x = canvas.width / 2;
  ball.y = canvas.height / 2;

  ball.speedX = 5 * direction;
  ball.speedY = (Math.random() > 0.5 ? 1 : -1) * 4;
}

function update() {
  // Oyuncu
  if (keys["w"]) {
    player.y -= player.speed;
  }

  if (keys["s"]) {
    player.y += player.speed;
  }

  // Ekrandan çıkmasını engelle
  player.y = Math.max(0, Math.min(canvas.height - player.height, player.y));

  // Bilgisayar AI
  const computerCenter = computer.y + computer.height / 2;

  if (computerCenter < ball.y - 10) {
    computer.y += computer.speed;
  } else if (computerCenter > ball.y + 10) {
    computer.y -= computer.speed;
  }

  computer.y = Math.max(
    0,
    Math.min(canvas.height - computer.height, computer.y)
  );

  // Top
  ball.x += ball.speedX;
  ball.y += ball.speedY;

  // Üst / alt duvar
  if (
    ball.y - ball.size / 2 <= 0 ||
    ball.y + ball.size / 2 >= canvas.height
  ) {
    ball.speedY *= -1;
  }

  // Oyuncu raketiyle çarpışma
  if (
    ball.x - ball.size / 2 < player.x + player.width &&
    ball.x + ball.size / 2 > player.x &&
    ball.y > player.y &&
    ball.y < player.y + player.height &&
    ball.speedX < 0
  ) {
    ball.speedX *= -1.1;

    let hit =
      (ball.y - (player.y + player.height / 2)) /
      (player.height / 2);

    ball.speedY = hit * 6;
  }

  // Bilgisayar raketiyle çarpışma
  if (
    ball.x + ball.size / 2 > computer.x &&
    ball.x - ball.size / 2 < computer.x + computer.width &&
    ball.y > computer.y &&
    ball.y < computer.y + computer.height &&
    ball.speedX > 0
  ) {
    ball.speedX *= -1.1;

    let hit =
      (ball.y - (computer.y + computer.height / 2)) /
      (computer.height / 2);

    ball.speedY = hit * 6;
  }

  // Bilgisayar sayı aldı
  if (ball.x < 0) {
    computerScore++;
    resetBall(1);
  }

  // Oyuncu sayı aldı
  if (ball.x > canvas.width) {
    playerScore++;
    resetBall(-1);
  }
}

function draw() {
  // Arka plan
  ctx.fillStyle = "#000";
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  // Orta çizgi
  ctx.strokeStyle = "#555";
  ctx.setLineDash([10, 10]);
  ctx.beginPath();
  ctx.moveTo(canvas.width / 2, 0);
  ctx.lineTo(canvas.width / 2, canvas.height);
  ctx.stroke();
  ctx.setLineDash([]);

  // Raketler
  ctx.fillStyle = "#fff";
  ctx.fillRect(
    player.x,
    player.y,
    player.width,
    player.height
  );

  ctx.fillRect(
    computer.x,
    computer.y,
    computer.width,
    computer.height
  );

  // Top
  ctx.fillStyle = "#00ff88";
  ctx.beginPath();
  ctx.arc(
    ball.x,
    ball.y,
    ball.size / 2,
    0,
    Math.PI * 2
  );
  ctx.fill();

  // Skor
  ctx.font = "50px Arial";
  ctx.textAlign = "center";
  ctx.fillText(
    playerScore,
    canvas.width / 2 - 80,
    60
  );

  ctx.fillText(
    computerScore,
    canvas.width / 2 + 80,
    60
  );
}

function gameLoop() {
  update();
  draw();
  requestAnimationFrame(gameLoop);
}

gameLoop();
</script>

</body>
</html>
# Oyun
