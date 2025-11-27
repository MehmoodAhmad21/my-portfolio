<script lang="ts">
  import Dock, { type DockApp } from "$lib/components/Dock.svelte";
  import Window from "$lib/components/Window.svelte";
  import Terminal from "$lib/components/Terminal.svelte";
  import Finder from "$lib/components/Finder.svelte";
  import Notes from "$lib/components/Notes.svelte";
  import wallpaper from "$lib/assets/wallpaper.gif";
  import favicon from "$lib/assets/favicon.jpg";
  import folderIcon from "$lib/assets/finder.png";
  import terminalIcon from "$lib/assets/terminal.png";
  import notesIcon from "$lib/assets/notes.png";

  type WindowState = {
    isMinimized: boolean;
    isMaximized: boolean;
    x: number;
    y: number;
    zIndex: number;
  };

  const apps: DockApp[] = [
    { name: "Finder", icon: folderIcon },
    { name: "Terminal", icon: terminalIcon },
    { name: "Notes", icon: notesIcon }
  ];

  let openApps: Set<string> = new Set();
  let activeApp: string | null = null;
  let maxZIndex = 100;

  // Window states for each app
  let windowStates: Record<string, WindowState> = {
    Terminal: { isMinimized: false, isMaximized: false, x: 100, y: 100, zIndex: 100 },
    Finder: { isMinimized: false, isMaximized: false, x: 150, y: 80, zIndex: 101 },
    Notes: { isMinimized: false, isMaximized: false, x: 200, y: 120, zIndex: 102 }
  };

  function handleAppSelect(app: DockApp) {
    if (openApps.has(app.name)) {
      // If already open
      if (windowStates[app.name].isMinimized) {
        // Un-minimize and bring to front
        windowStates[app.name].isMinimized = false;
      }
      // Bring to front
      bringToFront(app.name);
    } else {
      // Open the app
      openApps.add(app.name);
      openApps = openApps;
      
      // Center the window
      const centerX = (window.innerWidth - 700) / 2;
      const centerY = (window.innerHeight - 500) / 2 - 40;
      windowStates[app.name].x = Math.max(50, centerX);
      windowStates[app.name].y = Math.max(50, centerY);
      
      bringToFront(app.name);
    }
  }

  function bringToFront(appName: string) {
    maxZIndex++;
    windowStates[appName].zIndex = maxZIndex;
    activeApp = appName;
  }

  function closeApp(appName: string) {
    openApps.delete(appName);
    openApps = openApps;
    if (activeApp === appName) {
      activeApp = null;
    }
    // Reset window state
    windowStates[appName].isMinimized = false;
    windowStates[appName].isMaximized = false;
  }

  function handleMinimize(appName: string, event: CustomEvent) {
    windowStates[appName].isMinimized = event.detail.minimized;
    if (event.detail.minimized && activeApp === appName) {
      // Find next active window
      const otherApps = Array.from(openApps).filter(name => 
        name !== appName && !windowStates[name].isMinimized
      );
      if (otherApps.length > 0) {
        // Find app with highest z-index
        activeApp = otherApps.reduce((highest, current) => 
          windowStates[current].zIndex > windowStates[highest].zIndex ? current : highest
        );
      } else {
        activeApp = null;
      }
    }
  }

  function handleMaximize(appName: string, event: CustomEvent) {
    windowStates[appName].isMaximized = event.detail.maximized;
  }

  function getCurrentTime() {
    const now = new Date();
    return now.toLocaleTimeString('en-US', { 
      hour: 'numeric', 
      minute: '2-digit',
      hour12: true 
    });
  }

  let currentTime = getCurrentTime();
  
  // Update time every minute
  setInterval(() => {
    currentTime = getCurrentTime();
  }, 60000);

  // Keyboard shortcuts
  function handleKeydown(event: KeyboardEvent) {
    // Cmd/Ctrl + W to close active window
    if ((event.metaKey || event.ctrlKey) && event.key === 'w' && activeApp) {
      event.preventDefault();
      closeApp(activeApp);
    }
    // Cmd/Ctrl + M to minimize active window
    if ((event.metaKey || event.ctrlKey) && event.key === 'm' && activeApp) {
      event.preventDefault();
      windowStates[activeApp].isMinimized = true;
      handleMinimize(activeApp, { detail: { minimized: true } } as CustomEvent);
    }
    // Escape to un-maximize if maximized
    if (event.key === 'Escape' && activeApp && windowStates[activeApp].isMaximized) {
      windowStates[activeApp].isMaximized = false;
    }
  }
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="min-h-screen w-full text-white overflow-hidden relative">
  <!-- Desktop wallpaper -->
  <div 
    class="absolute inset-0 bg-cover bg-center bg-no-repeat"
    style="background-image: url('{wallpaper}');"
  ></div>
  <!-- Overlay for better contrast -->
  <div class="absolute inset-0 bg-black/20"></div>
  <!-- Menu Bar -->
  <header class="fixed top-0 left-0 right-0 bg-black/30 backdrop-blur-md border-b border-white/10 px-6 py-2 flex justify-between items-center text-sm z-50">
    <div class="flex items-center gap-6">
      <img src={favicon} alt="Logo" class="w-5 h-5" />
      <span class="font-medium">Mehmood Ahmad</span>
      {#if activeApp}
        <span class="text-gray-300">{activeApp}</span>
      {/if}
    </div>
    <div class="flex items-center gap-4 text-gray-300">
      <span>{currentTime}</span>
      <span>🔋</span>
      <span>📶</span>
    </div>
  </header>

  <!-- Desktop Area -->
  <main class="pt-14 pb-24 min-h-screen p-4 relative">
    {#if openApps.size === 0}
      <div class="absolute inset-0 flex items-center justify-center text-center">
        <div>
          <div class="text-6xl mb-4 animate-bounce text-black font-bold">Hi!</div>
          <p class="text-xl font-semibold text-black">Welcome to Mehmood Ahmad's Portfolio</p>
          <p class="text-sm mt-2 text-black">Software Engineer</p>
          <p class="text-sm mt-4 text-black">Click an app in the dock to get started</p>
        </div>
      </div>
    {/if}

    <!-- Show all open windows with proper layering -->
    {#if openApps.has("Terminal")}
      <div style:z-index={windowStates.Terminal.zIndex}>
        <Window 
          title="Terminal" 
          icon={terminalIcon}
          isActive={activeApp === "Terminal"}
          bind:x={windowStates.Terminal.x}
          bind:y={windowStates.Terminal.y}
          bind:isMinimized={windowStates.Terminal.isMinimized}
          bind:isMaximized={windowStates.Terminal.isMaximized}
          on:close={() => closeApp("Terminal")}
          on:minimize={(e) => handleMinimize("Terminal", e)}
          on:maximize={(e) => handleMaximize("Terminal", e)}
          on:focus={() => bringToFront("Terminal")}
        >
          <div class="h-[500px] w-[700px]">
            <Terminal />
          </div>
        </Window>
      </div>
    {/if}

    {#if openApps.has("Finder")}
      <div style:z-index={windowStates.Finder.zIndex}>
        <Window 
          title="Finder" 
          icon={folderIcon}
          isActive={activeApp === "Finder"}
          bind:x={windowStates.Finder.x}
          bind:y={windowStates.Finder.y}
          bind:isMinimized={windowStates.Finder.isMinimized}
          bind:isMaximized={windowStates.Finder.isMaximized}
          on:close={() => closeApp("Finder")}
          on:minimize={(e) => handleMinimize("Finder", e)}
          on:maximize={(e) => handleMaximize("Finder", e)}
          on:focus={() => bringToFront("Finder")}
        >
          <div class="h-[600px] w-[800px]">
            <Finder />
          </div>
        </Window>
      </div>
    {/if}

    {#if openApps.has("Notes")}
      <div style:z-index={windowStates.Notes.zIndex}>
        <Window 
          title="Notes" 
          icon={notesIcon}
          isActive={activeApp === "Notes"}
          bind:x={windowStates.Notes.x}
          bind:y={windowStates.Notes.y}
          bind:isMinimized={windowStates.Notes.isMinimized}
          bind:isMaximized={windowStates.Notes.isMaximized}
          on:close={() => closeApp("Notes")}
          on:minimize={(e) => handleMinimize("Notes", e)}
          on:maximize={(e) => handleMaximize("Notes", e)}
          on:focus={() => bringToFront("Notes")}
        >
          <div class="h-[500px] w-[700px]">
            <Notes />
          </div>
        </Window>
      </div>
    {/if}
  </main>

  <!-- Dock -->
  <Dock {apps} {openApps} {activeApp} onSelect={handleAppSelect} />
</div>


