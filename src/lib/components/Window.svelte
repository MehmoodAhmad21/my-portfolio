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

  function close() {
    dispatch('close');
  }

  function minimize() {
    isMinimized = !isMinimized;
    dispatch('minimize', { minimized: isMinimized });
  }

  function maximize() {
    isMaximized = !isMaximized;
    dispatch('maximize', { maximized: isMaximized });
  }

  function handleMouseDown(event: MouseEvent) {
    if (isMaximized) return; // Don't drag when maximized
    
    const target = event.target as HTMLElement;
    if (target.closest('.window-controls')) return; // Don't drag when clicking controls
    
    isDragging = true;
    dragStartX = event.clientX - x;
    dragStartY = event.clientY - y;
    
    dispatch('focus');
  }

  function handleTouchStart(event: TouchEvent) {
    if (isMaximized) return; // Don't drag when maximized
    
    const target = event.target as HTMLElement;
    if (target.closest('.window-controls')) return; // Don't drag when clicking controls
    
    const touch = event.touches[0];
    isDragging = true;
    dragStartX = touch.clientX - x;
    dragStartY = touch.clientY - y;
    
    dispatch('focus');
  }

  function handleMouseMove(event: MouseEvent) {
    if (!isDragging || isMaximized) return;
    
    x = event.clientX - dragStartX;
    y = event.clientY - dragStartY;
    
    // Keep window within viewport bounds
    const windowRect = windowElement?.getBoundingClientRect();
    if (windowRect) {
      const maxX = window.innerWidth - windowRect.width;
      const maxY = window.innerHeight - windowRect.height - 100; // Account for dock
      
      x = Math.max(0, Math.min(x, maxX));
      y = Math.max(0, Math.min(y, maxY));
    }
  }

  function handleTouchMove(event: TouchEvent) {
    if (!isDragging || isMaximized) return;
    
    const touch = event.touches[0];
    x = touch.clientX - dragStartX;
    y = touch.clientY - dragStartY;
    
    // Keep window within viewport bounds
    const windowRect = windowElement?.getBoundingClientRect();
    if (windowRect) {
      const maxX = window.innerWidth - windowRect.width;
      const maxY = window.innerHeight - windowRect.height - 100; // Account for dock
      
      x = Math.max(0, Math.min(x, maxX));
      y = Math.max(0, Math.min(y, maxY));
    }
  }

  function handleMouseUp() {
    isDragging = false;
  }

  function handleTouchEnd() {
    isDragging = false;
  }

  onMount(() => {
    document.addEventListener('mousemove', handleMouseMove);
    document.addEventListener('mouseup', handleMouseUp);
    document.addEventListener('touchmove', handleTouchMove);
    document.addEventListener('touchend', handleTouchEnd);
    
    return () => {
      document.removeEventListener('mousemove', handleMouseMove);
      document.removeEventListener('mouseup', handleMouseUp);
      document.removeEventListener('touchmove', handleTouchMove);
      document.removeEventListener('touchend', handleTouchEnd);
    };
  });
</script>

<div 
  bind:this={windowElement}
  class="window-container {isMinimized ? 'minimized' : ''} {isDragging ? 'dragging' : ''}"
  style={isMaximized 
    ? 'position: fixed; top: 3.5rem; left: 0.25rem; right: 0.25rem; bottom: 4.5rem; width: auto; height: auto;' 
    : `position: fixed; left: ${x}px; top: ${y}px;`}
  on:click={() => dispatch('focus')}
  role="dialog"
  aria-label={title}
>
  <div class="bg-white/10 backdrop-blur-xl border rounded-xl shadow-2xl overflow-hidden flex flex-col h-full transition-all duration-200 {isActive ? 'border-white/30' : 'border-white/10 opacity-95'}">
    <!-- Title bar -->
    <div 
      class="px-3 md:px-4 py-2 flex items-center justify-between select-none transition-colors {!isMaximized ? 'cursor-grab active:cursor-grabbing' : ''} {isActive ? 'bg-white/10 border-b border-white/20' : 'bg-white/5 border-b border-white/10'}"
      on:mousedown={handleMouseDown}
      on:touchstart={handleTouchStart}
      role="button"
      tabindex="-1"
    >
      <div class="flex items-center gap-3">
        <!-- Window controls -->
        <div class="flex gap-1.5 md:gap-2 window-controls">
          <button
            on:click={close}
            class="w-3 h-3 md:w-3 md:h-3 rounded-full bg-red-500 hover:bg-red-600 active:bg-red-700 transition-all group relative flex items-center justify-center touch-manipulation"
            aria-label="Close"
          >
            <span class="text-red-900 text-[10px] font-bold opacity-0 group-hover:opacity-100 md:group-hover:opacity-100 transition-opacity leading-none">×</span>
          </button>
          <button
            on:click={minimize}
            class="w-3 h-3 md:w-3 md:h-3 rounded-full bg-yellow-500 hover:bg-yellow-600 active:bg-yellow-700 transition-all group relative flex items-center justify-center touch-manipulation hidden md:flex"
            aria-label="Minimize"
          >
            <span class="text-yellow-900 text-[10px] font-bold opacity-0 group-hover:opacity-100 transition-opacity leading-none pb-[2px]">−</span>
          </button>
          <button
            on:click={maximize}
            class="w-3 h-3 md:w-3 md:h-3 rounded-full bg-green-500 hover:bg-green-600 active:bg-green-700 transition-all group relative flex items-center justify-center touch-manipulation"
            aria-label="Maximize"
          >
            <svg class="w-2 h-2 text-green-900 opacity-0 group-hover:opacity-100 md:group-hover:opacity-100 transition-opacity" viewBox="0 0 12 12" fill="none" stroke="currentColor" stroke-width="1.5">
              <rect x="2" y="2" width="8" height="8" />
            </svg>
          </button>
        </div>
        
        <div class="flex items-center gap-1.5 md:gap-2 pointer-events-none">
          {#if icon.startsWith('http') || icon.startsWith('/') || icon.includes('.')}
            <img src={icon} alt={title} class="w-4 h-4 md:w-5 md:h-5 object-contain" />
          {:else}
            <span class="text-base md:text-xl">{icon}</span>
          {/if}
          <span class="text-xs md:text-sm font-medium truncate">{title}</span>
        </div>
      </div>
    </div>

    <!-- Content area -->
    {#if !isMinimized}
      <div class="flex-1 overflow-hidden">
        <slot />
      </div>
    {/if}
  </div>
</div>

<style>
  .window-container {
    animation: windowOpen 0.2s ease-out;
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .window-container.dragging {
    transition: none;
  }

  .window-container.minimized {
    animation: windowMinimize 0.3s ease-out forwards;
    pointer-events: none;
  }

  @keyframes windowOpen {
    from {
      opacity: 0;
      transform: scale(0.9);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  @keyframes windowMinimize {
    to {
      opacity: 0;
      transform: scale(0.1) translateY(500px);
    }
  }

  .select-none {
    user-select: none;
    -webkit-user-select: none;
  }
</style>


