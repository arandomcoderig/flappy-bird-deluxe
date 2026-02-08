<!DOCTYPE html><html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Flappy Bird Deluxe</title>
<style>
  body {
    margin: 0;
    background: linear-gradient(#70c5ce, #c0f0ff);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    font-family: Arial, sans-serif;
  }
  canvas {
    background: transparent;
    border-radius: 12px;
    box-shadow: 0 0 20px rgba(0,0,0,0.3);
  }
</style>
</head>
<body>
<canvas id="gameCanvas" width="400" height="600"></canvas><script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

let bird = {
  x: 90,
  y: 250,
  width: 34,
  height: 26,
  gravity: 0.35, // Slower fall
  lift: -7.5,    // Softer jump
  velocity: 0,
  rotation: 0
};

let pipes = [];
let pipeWidth = 70;
let pipeGap = 165;
let frame = 0;
let score = 0;
let gameOver = false;

function drawBird() {
  ctx.save();
  ctx.translate(bird.x + bird.width/2, bird.y + bird.height/2);
  ctx.rotate(bird.rotation);
  
  ctx.fillStyle = "#FFD93D";
  ctx.fillRect(-bird.width/2, -bird.height/2, bird.width, bird.height);
  
  ctx.restore();
}

function drawPipes() {
  pipes.forEach(pipe => {
    ctx.fillStyle = "#2ecc71";
    ctx.fillRect(pipe.x, 0, pipeWidth, pipe.top);
    ctx.fillRect(pipe.x, canvas.height - pipe.bottom, pipeWidth, pipe.bottom);

    // Pipe caps
    ctx.fillStyle = "#27ae60";
    ctx.fillRect(pipe.x - 5, pipe.top - 20, pipeWidth + 10, 20);
    ctx.fillRect(pipe.x - 5, canvas.height - pipe.bottom, pipeWidth + 10, 20);
  });
}

function updatePipes() {
  if (frame % 110 === 0) {
    let top = Math.random() * (canvas.height / 2);
    let bottom = canvas.height - top - pipeGap;
    pipes.push({ x: canvas.width, top, bottom, passed: false });
  }

  pipes.forEach(pipe => {
    pipe.x -= 2.2;

    if (!pipe.passed && pipe.x + pipeWidth < bird.x) {
      score++;
      pipe.passed = true;
    }

    if (
      bird.x < pipe.x + pipeWidth &&
      bird.x + bird.width > pipe.x &&
      (bird.y < pipe.top || bird.y + bird.height > canvas.height - pipe.bottom)
    ) {
      gameOver = true;
    }
  });

  pipes = pipes.filter(pipe => pipe.x + pipeWidth > 0);
}

function drawScore() {
  ctx.fillStyle = "#222";
  ctx.font = "bold 28px Arial";
  ctx.fillText(score, canvas.width / 2 - 8, 50);
}

function updateBird() {
  bird.velocity += bird.gravity;
  bird.velocity *= 0.98; // Smooth movement
  bird.y += bird.velocity;

  // Rotation animation
  bird.rotation = Math.min(Math.max(bird.velocity * 0.05, -0.5), 0.7);

  if (bird.y + bird.height >= canvas.height || bird.y <= 0) {
    gameOver = true;
  }
}

function drawBackground() {
  ctx.fillStyle = "#aee1f9";
  ctx.fillRect(0, 0, canvas.width, canvas.height);

  // Ground
  ctx.fillStyle = "#d4a373";
  ctx.fillRect(0, canvas.height - 40, canvas.width, 40);
}

function resetGame() {
  bird.y = 250;
  bird.velocity = 0;
  pipes = [];
  score = 0;
  frame = 0;
  gameOver = false;
}

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawBackground();

  if (!gameOver) {
    updateBird();
    updatePipes();
    drawPipes();
    drawBird();
    drawScore();
    frame++;
    requestAnimationFrame(gameLoop);
  } else {
    ctx.fillStyle = "#000";
    ctx.font = "40px Arial";
    ctx.fillText("Game Over", 110, 260);

    ctx.font = "18px Arial";
    ctx.fillText("Tap or Press Space", 125, 300);
  }
}

function flap() {
  if (gameOver) {
    resetGame();
    gameLoop();
  } else {
    bird.velocity = bird.lift;
  }
}

// Controls
canvas.addEventListener("click", flap);
document.addEventListener("keydown", e => {
  if (e.code === "Space") flap();
});

// Start game
resetGame();
gameLoop();
</script></body>
</html>
