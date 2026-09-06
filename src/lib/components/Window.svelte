<script lang="ts">
  import { createEventDispatcher, onMount } from 'svelte';

  export let title: string;
  export let icon: string;
  export let isMinimized = false;
  export let isMaximized = false;
  export let x = 0;
  export let y = 0;
  export let isActive = true;

  const dispatch = createEventDispatcher();
  let isDragging = false;
  let dragStartX = 0;
  let dragStartY = 0;
  let windowElement: HTMLDivElement;

  function close() { dispatch('close'); }
  function minimize() {
    isMinimized = !isMinimized;
    dispatch('minimize', { minimized: isMinimized });
  }
  function maximize() {
    isMaximized = !isMaximized;
    dispatch('maximize', { maximized: isMaximized });
  }

  function startDrag(clientX: number, clientY: number, target: EventTarget | null) {
    if (isMaximized || (target as HTMLElement)?.closest('.window-controls')) return;
    isDragging = true;
    dragStartX = clientX - x;
    dragStartY = clientY - y;
    dispatch('focus');
  }

  function handleMouseDown(event: MouseEvent) { startDrag(event.clientX, event.clientY, event.target); }
  function handleTouchStart(event: TouchEvent) {
    const touch = event.touches[0];
    if (touch) startDrag(touch.clientX, touch.clientY, event.target);
  }

  function move(clientX: number, clientY: number) {
    if (!isDragging || isMaximized || !windowElement) return;
    const rect = windowElement.getBoundingClientRect();
    const nextX = clientX - dragStartX;
    const nextY = clientY - dragStartY;
    x = Math.max(8, Math.min(nextX, window.innerWidth - rect.width - 8));
    y = Math.max(42, Math.min(nextY, window.innerHeight - rect.height - 82));
  }

  function handleMouseMove(event: MouseEvent) { move(event.clientX, event.clientY); }
  function handleTouchMove(event: TouchEvent) {
    const touch = event.touches[0];
    if (touch) move(touch.clientX, touch.clientY);
  }
  function stopDrag() { isDragging = false; }

  onMount(() => {
    document.addEventListener('mousemove', handleMouseMove);
    document.addEventListener('mouseup', stopDrag);
    document.addEventListener('touchmove', handleTouchMove, { passive: true });
    document.addEventListener('touchend', stopDrag);
    return () => {
      document.removeEventListener('mousemove', handleMouseMove);
      document.removeEventListener('mouseup', stopDrag);
      document.removeEventListener('touchmove', handleTouchMove);
      document.removeEventListener('touchend', stopDrag);
    };
  });
</script>

<div
  bind:this={windowElement}
  class:active={isActive}
  class:dragging={isDragging}
  class:minimized={isMinimized}
  class="window-container"
  style={isMaximized
    ? 'position: fixed; top: 2.65rem; left: .45rem; right: .45rem; bottom: 5.25rem; width: auto; height: auto;'
    : `position: fixed; left: ${x}px; top: ${y}px;`}
  on:click={() => dispatch('focus')}
  on:keydown={(event) => { if (event.key === 'Enter' || event.key === ' ') dispatch('focus'); }}
  role="dialog"
  tabindex="-1"
  aria-label={title}
>
  <div class:maximized={isMaximized} class="glass-window">
    <div class="glass-reflection"></div>
    <div
      class:grab={!isMaximized}
      class="titlebar"
      on:mousedown={handleMouseDown}
      on:touchstart={handleTouchStart}
      on:dblclick={maximize}
      role="button"
      tabindex="-1"
    >
      <div class="window-controls" aria-label="Window controls">
        <button on:click|stopPropagation={close} class="traffic red" aria-label={`Close ${title}`}><span>×</span></button>
        <button on:click|stopPropagation={minimize} class="traffic yellow" aria-label={`Minimize ${title}`}><span>−</span></button>
        <button on:click|stopPropagation={maximize} class="traffic green" aria-label={`${isMaximized ? 'Restore' : 'Maximize'} ${title}`}><span>↗</span></button>
      </div>
      <div class="title">
        <img src={icon} alt="" />
        <span>{title}</span>
      </div>
      <div class="title-spacer"></div>
    </div>

    {#if !isMinimized}
      <div class="content"><slot /></div>
    {/if}
  </div>
</div>

<style>
  .window-container {
    filter: drop-shadow(6px 7px 0 rgba(1, 7, 17, .55)) drop-shadow(0 30px 45px rgba(2, 8, 20, .35));
    animation: window-in 280ms steps(5, end);
    transition: opacity 180ms ease, transform 220ms ease;
  }
  .window-container.dragging { transition: none; }
  .window-container.minimized { opacity: 0; transform: translateY(55vh) scale(.12); pointer-events: none; }

  .glass-window {
    position: relative;
    display: flex;
    height: 100%;
    min-height: 0;
    flex-direction: column;
    overflow: hidden;
    border: 2px solid rgba(226,249,255,.72);
    border-radius: .35rem;
    background: linear-gradient(145deg, rgba(255,255,255,.17), rgba(255,255,255,.055) 44%, rgba(74,128,203,.08)), rgba(7,16,30,.66);
    box-shadow: 0 0 0 2px rgba(5,19,34,.82), inset 0 1px 0 rgba(255,255,255,.42), inset 0 -1px 0 rgba(255,255,255,.08);
    backdrop-filter: blur(32px) saturate(145%);
    -webkit-backdrop-filter: blur(32px) saturate(145%);
    clip-path: polygon(7px 0, calc(100% - 7px) 0, calc(100% - 7px) 2px, 100% 2px, 100% calc(100% - 7px), calc(100% - 2px) calc(100% - 7px), calc(100% - 2px) 100%, 7px 100%, 7px calc(100% - 2px), 0 calc(100% - 2px), 0 7px, 2px 7px, 2px 2px, 7px 2px);
  }
  .glass-window.maximized { border-radius: .2rem; }
  .glass-reflection { position: absolute; inset: 0; background: radial-gradient(circle at 17% 0%, rgba(255,255,255,.16), transparent 28%); pointer-events: none; }

  .titlebar {
    position: relative;
    z-index: 2;
    display: grid;
    grid-template-columns: 1fr auto 1fr;
    align-items: center;
    min-height: 3rem;
    padding: .6rem .85rem;
    border-bottom: 2px solid rgba(143,221,245,.24);
    background: linear-gradient(180deg, rgba(126,217,245,.18), rgba(255,255,255,.035));
    user-select: none;
  }
  .titlebar.grab { cursor: grab; }
  .titlebar.grab:active { cursor: grabbing; }

  .window-controls { display: flex; gap: .5rem; width: max-content; }
  .traffic { display: grid; width: .78rem; height: .78rem; padding: 0; place-items: center; border: 1px solid rgba(5,20,33,.62); border-radius: 2px; cursor: pointer; box-shadow: 2px 2px 0 rgba(0,0,0,.28), inset 1px 1px 0 rgba(255,255,255,.35); }
  .traffic.red { background: #ff5d57; }
  .traffic.yellow { background: #febc2e; }
  .traffic.green { background: #28c840; }
  .traffic span { color: rgba(0,0,0,.57); font-size: .61rem; font-weight: 900; line-height: 1; opacity: 0; }
  .window-controls:hover .traffic span, .traffic:focus-visible span { opacity: 1; }
  .traffic:focus-visible { outline: 2px solid white; outline-offset: 2px; }

  .title { display: flex; align-items: center; gap: .5rem; color: rgba(255,255,255,.9); font-family: ui-monospace, monospace; font-size: .76rem; font-weight: 850; letter-spacing: .03em; }
  .title img { width: 1.3rem; height: 1.3rem; border-radius: .2rem; object-fit: cover; image-rendering: pixelated; }
  .title-spacer { min-width: 4rem; }
  .content { position: relative; z-index: 1; min-height: 0; flex: 1; overflow: hidden; }

  @keyframes window-in {
    from { opacity: 0; transform: translateY(14px) scale(.93); }
    to { opacity: 1; transform: translateY(0) scale(1); }
  }

  @media (max-width: 767px) {
    .window-container { inset: 2.55rem .35rem 4.65rem !important; width: auto !important; height: auto !important; }
    .glass-window { border-radius: .25rem; }
    .titlebar { min-height: 2.7rem; padding: .5rem .72rem; }
    .traffic { width: .82rem; height: .82rem; }
    .traffic.yellow { display: none; }
    .title { font-size: .77rem; }
  }
</style>
