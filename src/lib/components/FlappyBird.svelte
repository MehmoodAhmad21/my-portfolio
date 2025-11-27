<script lang="ts">
  import { onMount } from 'svelte';
  import flappyBirdImg from '$lib/assets/flappy.png';

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  let gameLoop: number;
  let gameStarted = false;
  let gameOver = false;
  let score = 0;
  let highScore = 0;
  let birdImage: HTMLImageElement;
  let imageLoaded = false;

  // Game state
  const bird = {
    x: 50,
    y: 150,
    velocity: 0,
    gravity: 0.25,
    jumpStrength: -5,
    size: 20
  };

  let pipes: Array<{ x: number; topHeight: number; gap: number; scored: boolean }> = [];
  const pipeWidth = 50;
  const pipeGap = 170;
  const pipeSpeed = 1.2;

  function init() {
    if (!canvas) return;
    ctx = canvas.getContext('2d')!;
    canvas.width = canvas.offsetWidth;
    canvas.height = canvas.offsetHeight;
    
    // Load bird image
    birdImage = new Image();
    birdImage.src = flappyBirdImg;
    birdImage.onload = () => {
      imageLoaded = true;
    };
    
    // Load high score
    const saved = localStorage.getItem('flappyBirdHighScore');
    if (saved) highScore = parseInt(saved);
  }

  function resetGame() {
    bird.y = 150;
    bird.velocity = 0;
    pipes = [];
    score = 0;
    gameOver = false;
    gameStarted = false;
  }

  function jump() {
    if (!gameStarted) {
      gameStarted = true;
      gameOver = false;
    }
    if (!gameOver) {
      bird.velocity = bird.jumpStrength;
    } else {
      resetGame();
    }
  }

  function update() {
    if (!gameStarted || gameOver) return;

    // Update bird
    bird.velocity += bird.gravity;
    bird.y += bird.velocity;

    // Check ceiling and floor collision
    if (bird.y < 0 || bird.y + bird.size > canvas.height) {
      endGame();
      return;
    }

    // Add new pipes
    if (pipes.length === 0 || pipes[pipes.length - 1].x < canvas.width - 200) {
      const topHeight = Math.random() * (canvas.height - pipeGap - 100) + 50;
      pipes.push({ x: canvas.width, topHeight, gap: pipeGap, scored: false });
    }

    // Update pipes
    for (let i = pipes.length - 1; i >= 0; i--) {
      pipes[i].x -= pipeSpeed;

      // Check if bird scored
      if (!pipes[i].scored && pipes[i].x + pipeWidth < bird.x) {
        pipes[i].scored = true;
        score++;
      }

      // Check collision
      if (
        bird.x + bird.size > pipes[i].x &&
        bird.x < pipes[i].x + pipeWidth &&
        (bird.y < pipes[i].topHeight || bird.y + bird.size > pipes[i].topHeight + pipes[i].gap)
      ) {
        endGame();
        return;
      }

      // Remove off-screen pipes
      if (pipes[i].x + pipeWidth < 0) {
        pipes.splice(i, 1);
      }
    }
  }

  function endGame() {
    gameOver = true;
    if (score > highScore) {
      highScore = score;
      localStorage.setItem('flappyBirdHighScore', highScore.toString());
    }
  }

  function draw() {
    if (!ctx) return;

    // Clear canvas
    ctx.fillStyle = '#87CEEB';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Draw pipes
    ctx.fillStyle = '#2ecc71';
    for (const pipe of pipes) {
      // Top pipe
      ctx.fillRect(pipe.x, 0, pipeWidth, pipe.topHeight);
      // Bottom pipe
      ctx.fillRect(pipe.x, pipe.topHeight + pipe.gap, pipeWidth, canvas.height);
      
      // Pipe borders
      ctx.strokeStyle = '#27ae60';
      ctx.lineWidth = 3;
      ctx.strokeRect(pipe.x, 0, pipeWidth, pipe.topHeight);
      ctx.strokeRect(pipe.x, pipe.topHeight + pipe.gap, pipeWidth, canvas.height);
    }

    // Draw bird
    if (imageLoaded) {
      // Calculate rotation angle based on velocity
      const angle = Math.min(Math.max(bird.velocity * 0.1, -0.5), 0.5);
      
      ctx.save();
      ctx.translate(bird.x + bird.size / 2, bird.y + bird.size / 2);
      ctx.rotate(angle);
      ctx.drawImage(birdImage, -bird.size / 2, -bird.size / 2, bird.size, bird.size);
      ctx.restore();
    } else {
      // Fallback to circle if image not loaded yet
      ctx.fillStyle = '#f39c12';
      ctx.beginPath();
      ctx.arc(bird.x + bird.size / 2, bird.y + bird.size / 2, bird.size / 2, 0, Math.PI * 2);
      ctx.fill();
    }

    // Draw score
    ctx.fillStyle = 'white';
    ctx.font = 'bold 24px Arial';
    ctx.strokeStyle = 'black';
    ctx.lineWidth = 3;
    ctx.strokeText(`Score: ${score}`, 10, 30);
    ctx.fillText(`Score: ${score}`, 10, 30);
    
    ctx.font = '16px Arial';
    ctx.strokeText(`High Score: ${highScore}`, 10, 55);
    ctx.fillText(`High Score: ${highScore}`, 10, 55);

    // Draw instructions
    if (!gameStarted) {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
      ctx.fillRect(canvas.width / 2 - 150, canvas.height / 2 - 60, 300, 120);
      
      ctx.fillStyle = 'white';
      ctx.font = 'bold 24px Arial';
      ctx.textAlign = 'center';
      ctx.fillText('Flappy Bird', canvas.width / 2, canvas.height / 2 - 20);
      ctx.font = '16px Arial';
      ctx.fillText('Click, Space, or Touch to Fly', canvas.width / 2, canvas.height / 2 + 10);
      ctx.fillText('Avoid the pipes!', canvas.width / 2, canvas.height / 2 + 35);
      ctx.textAlign = 'left';
    }

    // Draw game over
    if (gameOver) {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
      ctx.fillRect(canvas.width / 2 - 120, canvas.height / 2 - 80, 240, 160);
      
      ctx.fillStyle = 'white';
      ctx.font = 'bold 32px Arial';
      ctx.textAlign = 'center';
      ctx.fillText('Game Over!', canvas.width / 2, canvas.height / 2 - 30);
      ctx.font = '20px Arial';
      ctx.fillText(`Score: ${score}`, canvas.width / 2, canvas.height / 2 + 5);
      ctx.fillText(`High Score: ${highScore}`, canvas.width / 2, canvas.height / 2 + 35);
      ctx.font = '16px Arial';
      ctx.fillText('Click to Restart', canvas.width / 2, canvas.height / 2 + 65);
      ctx.textAlign = 'left';
    }
  }

  function gameLoopFn() {
    update();
    draw();
    gameLoop = requestAnimationFrame(gameLoopFn);
  }

  function handleKeyPress(e: KeyboardEvent) {
    if (e.code === 'Space') {
      e.preventDefault();
      jump();
    }
  }

  onMount(() => {
    init();
    gameLoop = requestAnimationFrame(gameLoopFn);

    window.addEventListener('keydown', handleKeyPress);

    return () => {
      cancelAnimationFrame(gameLoop);
      window.removeEventListener('keydown', handleKeyPress);
    };
  });
</script>

<div class="h-full flex flex-col bg-gradient-to-b from-blue-400 to-blue-200 rounded-lg overflow-hidden">
  <canvas
    bind:this={canvas}
    on:click={jump}
    on:touchstart|preventDefault={jump}
    class="w-full h-full cursor-pointer touch-none"
  ></canvas>
</div>

<style>
  canvas {
    display: block;
  }
</style>

