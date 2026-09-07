<script lang="ts">
  import { onMount } from 'svelte';
  import flappyBirdImg from '$lib/assets/flappy.png';
  export let isActive = true;
  const worldWidth = 360;
  const worldHeight = 540;
  const step = 1 / 120;
  let lastTime = 0;
  let accumulator = 0;
  let paused = false;
  let mounted = false;

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
    gravity: 900,
    jumpStrength: -300,
    size: 20
  };

  let pipes: Array<{ x: number; topHeight: number; gap: number; scored: boolean }> = [];
  const pipeWidth = 50;
  const pipeGap = 170;
  const pipeSpeed = 72;

  $: if (mounted && !isActive) pauseGame();

  function pauseGame() {
    if (gameStarted && !gameOver) paused = true;
    cancelAnimationFrame(gameLoop);
    lastTime = 0;
    accumulator = 0;
    draw();
  }

  function resizeCanvas() {
    const scale = Math.min(window.devicePixelRatio || 1, 2);
    canvas.width = Math.round(canvas.clientWidth * scale);
    canvas.height = Math.round(canvas.clientHeight * scale);
    ctx.setTransform(canvas.width / worldWidth, 0, 0, canvas.height / worldHeight, 0, 0);
    ctx.imageSmoothingEnabled = false;
    draw();
  }

  function init() {
    if (!canvas) return;
    ctx = canvas.getContext('2d')!;
    resizeCanvas();
    
    // Load bird image
    birdImage = new Image();
    birdImage.onload = () => {
      imageLoaded = true;
      draw();
    };
    birdImage.src = flappyBirdImg;
    
    // Load high score
    try {
      const saved = Number(localStorage.getItem('flappyBirdHighScore'));
      if (Number.isSafeInteger(saved) && saved >= 0) highScore = saved;
    } catch { /* The game remains playable when storage is unavailable. */ }
  }

  function resetGame() {
    bird.y = worldHeight / 2;
    bird.velocity = 0;
    pipes = [];
    score = 0;
    gameOver = false;
    gameStarted = false;
  }

  function jump() {
    if (!isActive || document.hidden) return;
    if (gameOver) resetGame();
    paused = false;
    if (!gameStarted) {
      gameStarted = true;
      gameOver = false;
    }
    if (!gameOver) {
      bird.velocity = bird.jumpStrength;
    }
    if (!lastTime) { cancelAnimationFrame(gameLoop); gameLoop = requestAnimationFrame(gameLoopFn); }
  }

  function update(dt: number) {
    if (!gameStarted || gameOver || paused) return;

    // Update bird
    bird.velocity += bird.gravity * dt;
    bird.y += bird.velocity * dt;

    // Check ceiling and floor collision
    if (bird.y < 0 || bird.y + bird.size > worldHeight) {
      endGame();
      return;
    }

    // Add new pipes
    if (pipes.length === 0 || pipes[pipes.length - 1].x < worldWidth - 200) {
      const topHeight = Math.random() * (worldHeight - pipeGap - 100) + 50;
      pipes.push({ x: worldWidth, topHeight, gap: pipeGap, scored: false });
    }

    // Update pipes
    for (let i = pipes.length - 1; i >= 0; i--) {
      pipes[i].x -= pipeSpeed * dt;

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
      try { localStorage.setItem('flappyBirdHighScore', highScore.toString()); } catch { /* Session score still works. */ }
    }
  }

  function draw() {
    if (!ctx) return;

    // Clear canvas
    ctx.fillStyle = '#87CEEB';
    ctx.fillRect(0, 0, worldWidth, worldHeight);

    // Draw pipes
    ctx.fillStyle = '#2ecc71';
    for (const pipe of pipes) {
      // Top pipe
      ctx.fillRect(pipe.x, 0, pipeWidth, pipe.topHeight);
      // Bottom pipe
      ctx.fillRect(pipe.x, pipe.topHeight + pipe.gap, pipeWidth, worldHeight);
      
      // Pipe borders
      ctx.strokeStyle = '#27ae60';
      ctx.lineWidth = 3;
      ctx.strokeRect(pipe.x, 0, pipeWidth, pipe.topHeight);
      ctx.strokeRect(pipe.x, pipe.topHeight + pipe.gap, pipeWidth, worldHeight);
    }

    // Draw bird
    if (imageLoaded) {
      // Calculate rotation angle based on velocity
      const angle = Math.min(Math.max(bird.velocity / 600, -0.5), 0.5);
      
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
    if (!gameStarted || gameOver || paused) {
      ctx.fillStyle = 'rgba(8, 25, 40, .86)';
      ctx.fillRect(25, worldHeight / 2 - 75, 310, 150);
      ctx.fillStyle = 'white';
      ctx.textAlign = 'center';
      ctx.font = 'bold 28px monospace';
      ctx.fillText(gameOver ? 'Game Over!' : paused ? 'Paused' : 'Flappy Bird', worldWidth / 2, worldHeight / 2 - 30);
      ctx.font = '16px monospace';
      ctx.fillText(gameOver ? `Score: ${score} · Best: ${highScore}` : 'Avoid the pipes!', worldWidth / 2, worldHeight / 2 + 4);
      ctx.font = '14px monospace';
      ctx.fillText('Tap / click / Space', worldWidth / 2, worldHeight / 2 + 34);
      ctx.fillText(gameOver ? 'to restart' : paused ? 'to resume' : 'to fly', worldWidth / 2, worldHeight / 2 + 55);
      ctx.textAlign = 'left';
    }
  }

  function gameLoopFn(time: number) {
    if (!isActive || document.hidden || paused) { pauseGame(); return; }
    // Fixed steps keep physics identical at 30/60/120/144 Hz. Cap catch-up
    // so a stalled frame never teleports the bird through a pipe.
    if (lastTime) accumulator += Math.min((time - lastTime) / 1000, .05);
    lastTime = time;
    while (accumulator >= step && !gameOver) {
      update(step);
      accumulator -= step;
    }
    draw();
    if (!gameOver && gameStarted) gameLoop = requestAnimationFrame(gameLoopFn);
    else { lastTime = 0; accumulator = 0; }
  }

  function handleKeyPress(e: KeyboardEvent) {
    if (!isActive || e.repeat || e.ctrlKey || e.metaKey || e.altKey) return;
    if ((e.target as HTMLElement)?.closest('input, textarea, button, [contenteditable="true"]')) return;
    if (e.code === 'Space' || e.code === 'ArrowUp') { e.preventDefault(); jump(); }
    else if (e.code === 'Escape') pauseGame();
  }

  function handlePointer(e: PointerEvent) {
    if (!e.isPrimary || e.button !== 0) return;
    e.preventDefault();
    canvas.focus({ preventScroll: true });
    jump();
  }

  onMount(() => {
    init();
    resetGame();
    draw();
    mounted = true;
    const observer = new ResizeObserver(resizeCanvas);
    observer.observe(canvas);
    const visibility = () => { if (document.hidden) pauseGame(); };
    window.addEventListener('keydown', handleKeyPress);
    window.addEventListener('blur', pauseGame);
    document.addEventListener('visibilitychange', visibility);
    return () => {
      mounted = false;
      cancelAnimationFrame(gameLoop);
      observer.disconnect();
      birdImage.onload = null;
      window.removeEventListener('keydown', handleKeyPress);
      window.removeEventListener('blur', pauseGame);
      document.removeEventListener('visibilitychange', visibility);
    };
  });
</script>

<div class="game-stage">
  <canvas bind:this={canvas} on:pointerdown={handlePointer} tabindex="0" aria-label="Flappy Bird. Tap, click, Space or Up arrow to fly. Escape pauses.">Flappy Bird requires canvas support.</canvas>
  <span class="sr-only" aria-live="polite">{gameOver ? `Game over. Score ${score}. Best ${highScore}. Tap or press Space to restart.` : paused ? 'Paused. Tap or press Space to resume.' : ''}</span>
</div>

<style>
  .game-stage { width:100%; height:100%; min-height:0; display:grid; place-items:center; overflow:hidden; background:#112c3b; container-type:size; }
  canvas { display:block; width:min(100cqw,66.6667cqh); height:min(100cqh,150cqw); touch-action:none; user-select:none; -webkit-user-select:none; cursor:pointer; }
  canvas:focus-visible { outline:2px solid #ffe77b; outline-offset:-3px; }
  .sr-only { position:absolute; width:1px; height:1px; overflow:hidden; clip-path:inset(50%); }
</style>
