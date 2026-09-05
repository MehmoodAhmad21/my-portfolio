<script lang="ts">
  import { onMount } from 'svelte';
  import { base } from '$app/paths';
  import Dock, { type DockApp } from '$lib/components/Dock.svelte';
  import Window from '$lib/components/Window.svelte';
  import Terminal from '$lib/components/Terminal.svelte';
  import Finder from '$lib/components/Finder.svelte';
  import Notes from '$lib/components/Notes.svelte';
  import FlappyBird from '$lib/components/FlappyBird.svelte';
  import wallpaper from '$lib/assets/wallpaper.gif';
  import portrait from '$lib/assets/favicon.jpg';
  import folderIcon from '$lib/assets/finder.png';
  import terminalIcon from '$lib/assets/terminal.png';
  import notesIcon from '$lib/assets/notes.png';
  import flappyIcon from '$lib/assets/flappy.png';

  type WindowState = {
    isMinimized: boolean;
    isMaximized: boolean;
    x: number;
    y: number;
    zIndex: number;
  };

  const apps: DockApp[] = [
    { name: 'Work', icon: folderIcon },
    { name: 'Terminal', icon: terminalIcon },
    { name: 'Notes', icon: notesIcon },
    { name: 'Flappy Bird', icon: flappyIcon }
  ];

  let openApps: Set<string> = new Set();
  let activeApp: string | null = null;
  let maxZIndex = 100;
  let currentTime = '';
  let timer: ReturnType<typeof setInterval>;

  let windowStates: Record<string, WindowState> = {
    Work: { isMinimized: false, isMaximized: false, x: 120, y: 68, zIndex: 101 },
    Terminal: { isMinimized: false, isMaximized: false, x: 175, y: 92, zIndex: 102 },
    Notes: { isMinimized: false, isMaximized: false, x: 220, y: 108, zIndex: 103 },
    'Flappy Bird': { isMinimized: false, isMaximized: false, x: 270, y: 126, zIndex: 104 }
  };

  function formatTime() {
    return new Date().toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' });
  }

  onMount(() => {
    currentTime = formatTime();
    timer = setInterval(() => currentTime = formatTime(), 60000);
    return () => clearInterval(timer);
  });

  function handleAppSelect(app: DockApp) {
    const mobile = window.innerWidth < 768;
    if (openApps.has(app.name)) {
      windowStates[app.name].isMinimized = false;
      bringToFront(app.name);
      return;
    }

    openApps.add(app.name);
    openApps = new Set(openApps);
    if (mobile) {
      windowStates[app.name].isMaximized = true;
    } else {
      const sizes: Record<string, [number, number]> = {
        Work: [900, 630], Terminal: [730, 500], Notes: [700, 500], 'Flappy Bird': [620, 500]
      };
      const [width, height] = sizes[app.name];
      windowStates[app.name].x = Math.max(24, (window.innerWidth - width) / 2);
      windowStates[app.name].y = Math.max(52, (window.innerHeight - height) / 2 - 12);
    }
    bringToFront(app.name);
  }

  function bringToFront(name: string) {
    maxZIndex += 1;
    windowStates[name].zIndex = maxZIndex;
    activeApp = name;
  }

  function closeApp(name: string) {
    openApps.delete(name);
    openApps = new Set(openApps);
    windowStates[name].isMinimized = false;
    windowStates[name].isMaximized = false;
    if (activeApp === name) activeApp = null;
  }

  function handleMinimize(name: string, event: CustomEvent<{ minimized: boolean }>) {
    windowStates[name].isMinimized = event.detail.minimized;
    if (event.detail.minimized && activeApp === name) activeApp = null;
  }

  function handleKeydown(event: KeyboardEvent) {
    if ((event.metaKey || event.ctrlKey) && event.key.toLowerCase() === 'w' && activeApp) {
      event.preventDefault();
      closeApp(activeApp);
    }
    if ((event.metaKey || event.ctrlKey) && event.key.toLowerCase() === 'm' && activeApp) {
      event.preventDefault();
      windowStates[activeApp].isMinimized = true;
      activeApp = null;
    }
    if (event.key === 'Escape' && activeApp) windowStates[activeApp].isMaximized = false;
  }
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="desktop">
  <div class="wallpaper" style={`background-image: url('${wallpaper}')`}></div>
  <div class="color-wash"></div>
  <div class="pixel-grid"></div>

  <header class="menu-bar">
    <div class="menu-left">
      <button class="pixel-mark" aria-label="Mehmood home">M</button>
      <strong>Mehmood Ahmad</strong>
      <span class="menu-link">Portfolio</span>
      {#if activeApp}<span class="active-app">{activeApp}</span>{/if}
    </div>
    <div class="menu-right">
      <span class="signal" aria-label="Online"><i></i><i></i><i></i></span>
      <span class="battery"><i></i></span>
      <time>{currentTime}</time>
    </div>
  </header>

  <main class="desktop-content">
    <section class:softened={openApps.size > 0} class="welcome" aria-labelledby="intro-title">
      <div class="identity glass-panel">
        <div class="panel-shine"></div>
        <div class="portrait-shell">
          <div class="pixel-corners"></div>
          <img src={portrait} alt="Mehmood Ahmad" />
          <span class="availability"><i></i> Edmonton, AB</span>
        </div>

        <div class="intro-copy">
          <p class="pixel-label eyebrow"><span></span> Software engineer · Applied AI + systems</p>
          <h1 id="intro-title">I build intelligent systems that <em>see, move, and ship.</em></h1>
          <p class="summary">From real-time computer vision and VR robotics to mobile health products and deployment infrastructure. I care about measurable outcomes, crisp interfaces, and the machinery underneath.</p>

          <div class="actions">
            <button class="primary-action" on:click={() => handleAppSelect(apps[0])}>Explore selected work <span>↗</span></button>
            <a class="secondary-action" href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank">View résumé <span>↓</span></a>
          </div>

          <div class="contact-line">
            <a href="mailto:mehmood3@ualberta.ca">Email</a><i></i>
            <a href="https://github.com/MehmoodAhmad21" target="_blank" rel="noreferrer">GitHub</a><i></i>
            <a href="https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/" target="_blank" rel="noreferrer">LinkedIn</a>
          </div>
        </div>
      </div>

      <div class="signal-strip glass-panel" aria-label="Career highlights">
        <div><span class="metric">50.6%</span><p>Hazard F1</p><small>YOLOv11 + VLM</small></div>
        <div><span class="metric">~2.5ms</span><p>Added latency</p><small>Real-time inference</small></div>
        <div><span class="metric">70%</span><p>Faster deploys</p><small>Docker + Jenkins</small></div>
        <div class="mini-now"><span class="pixel-label">Now</span><p>BSc Computer Science</p><small>University of Alberta · Jan 2027</small></div>
      </div>
    </section>

    {#if openApps.has('Work')}
      <div style:z-index={windowStates.Work.zIndex}>
        <Window title="Selected Work" icon={folderIcon} isActive={activeApp === 'Work'} bind:x={windowStates.Work.x} bind:y={windowStates.Work.y} bind:isMinimized={windowStates.Work.isMinimized} bind:isMaximized={windowStates.Work.isMaximized} on:close={() => closeApp('Work')} on:minimize={(e) => handleMinimize('Work', e)} on:focus={() => bringToFront('Work')}>
          <div class="work-window"><Finder /></div>
        </Window>
      </div>
    {/if}

    {#if openApps.has('Terminal')}
      <div style:z-index={windowStates.Terminal.zIndex}>
        <Window title="Terminal" icon={terminalIcon} isActive={activeApp === 'Terminal'} bind:x={windowStates.Terminal.x} bind:y={windowStates.Terminal.y} bind:isMinimized={windowStates.Terminal.isMinimized} bind:isMaximized={windowStates.Terminal.isMaximized} on:close={() => closeApp('Terminal')} on:minimize={(e) => handleMinimize('Terminal', e)} on:focus={() => bringToFront('Terminal')}>
          <div class="terminal-window"><Terminal /></div>
        </Window>
      </div>
    {/if}

    {#if openApps.has('Notes')}
      <div style:z-index={windowStates.Notes.zIndex}>
        <Window title="Notes" icon={notesIcon} isActive={activeApp === 'Notes'} bind:x={windowStates.Notes.x} bind:y={windowStates.Notes.y} bind:isMinimized={windowStates.Notes.isMinimized} bind:isMaximized={windowStates.Notes.isMaximized} on:close={() => closeApp('Notes')} on:minimize={(e) => handleMinimize('Notes', e)} on:focus={() => bringToFront('Notes')}>
          <div class="notes-window"><Notes /></div>
        </Window>
      </div>
    {/if}

    {#if openApps.has('Flappy Bird')}
      <div style:z-index={windowStates['Flappy Bird'].zIndex}>
        <Window title="Flappy Bird" icon={flappyIcon} isActive={activeApp === 'Flappy Bird'} bind:x={windowStates['Flappy Bird'].x} bind:y={windowStates['Flappy Bird'].y} bind:isMinimized={windowStates['Flappy Bird'].isMinimized} bind:isMaximized={windowStates['Flappy Bird'].isMaximized} on:close={() => closeApp('Flappy Bird')} on:minimize={(e) => handleMinimize('Flappy Bird', e)} on:focus={() => bringToFront('Flappy Bird')}>
          <div class="game-window"><FlappyBird /></div>
        </Window>
      </div>
    {/if}
  </main>

  <Dock {apps} {openApps} {activeApp} onSelect={handleAppSelect} />
</div>

<style>
  .desktop { position: relative; width: 100%; min-height: 100svh; overflow: hidden; color: white; background: #07111f; }
  .wallpaper { position: absolute; inset: 0; background-position: center; background-size: cover; transform: scale(1.02); }
  .color-wash { position: absolute; inset: 0; background: radial-gradient(circle at 72% 34%, rgba(69,170,255,.18), transparent 30%), linear-gradient(120deg, rgba(2,12,26,.26), rgba(2,8,17,.14) 45%, rgba(1,8,18,.46)); }
  .pixel-grid { position: absolute; inset: 0; opacity: .14; background-image: linear-gradient(rgba(255,255,255,.045) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,.035) 1px, transparent 1px); background-size: 4px 4px; mask-image: linear-gradient(to bottom, rgba(0,0,0,.6), transparent 52%); pointer-events: none; }

  .menu-bar { position: fixed; z-index: 2000; inset: 0 0 auto; display: flex; min-height: 2.35rem; align-items: center; justify-content: space-between; padding: .35rem 1rem; border-bottom: 1px solid rgba(255,255,255,.19); background: linear-gradient(180deg, rgba(255,255,255,.18), rgba(255,255,255,.06)), rgba(5,15,29,.38); box-shadow: inset 0 1px 0 rgba(255,255,255,.32); backdrop-filter: blur(28px) saturate(150%); -webkit-backdrop-filter: blur(28px) saturate(150%); font-size: .76rem; }
  .menu-left, .menu-right { display: flex; align-items: center; gap: 1rem; }
  .menu-left strong { font-weight: 730; }
  .pixel-mark { display: grid; width: 1.45rem; height: 1.45rem; padding: 0; place-items: center; border: 1px solid rgba(255,255,255,.32); border-radius: .32rem; color: #03101e; background: linear-gradient(135deg, #9cf0ff, #8daaff); box-shadow: 2px 2px 0 rgba(0,0,0,.35), inset 0 0 0 1px rgba(255,255,255,.4); font-family: ui-monospace, monospace; font-size: .68rem; font-weight: 900; }
  .menu-link { color: rgba(255,255,255,.7); }
  .active-app { padding-left: 1rem; border-left: 1px solid rgba(255,255,255,.16); color: #95e9ff; }
  .menu-right { gap: .8rem; color: rgba(255,255,255,.76); }
  .signal { display: flex; align-items: flex-end; gap: 2px; height: .75rem; }
  .signal i { display: block; width: 2px; background: currentColor; }
  .signal i:nth-child(1) { height: 35%; } .signal i:nth-child(2) { height: 65%; } .signal i:nth-child(3) { height: 100%; }
  .battery { display: block; width: 1.25rem; height: .62rem; padding: 2px; border: 1px solid currentColor; border-radius: 2px; }
  .battery::after { position: absolute; width: 2px; height: 4px; margin: 0 0 0 3px; border-radius: 0 1px 1px 0; background: currentColor; content: ''; }
  .battery i { display: block; width: 78%; height: 100%; border-radius: 1px; background: #83f0c5; }

  .desktop-content { position: relative; z-index: 2; min-height: 100svh; padding: clamp(4.5rem, 9vh, 7rem) clamp(1rem, 5vw, 5rem) 7rem; }
  .welcome { width: min(1080px, 100%); margin: 0 auto; transition: filter 200ms ease, opacity 200ms ease, transform 200ms ease; }
  .welcome.softened { filter: blur(4px); opacity: .42; transform: scale(.985); }
  .glass-panel { position: relative; overflow: hidden; border: 1px solid rgba(255,255,255,.27); background: linear-gradient(145deg, rgba(255,255,255,.19), rgba(255,255,255,.065) 45%, rgba(104,166,255,.07)), rgba(6,16,31,.46); box-shadow: 0 28px 80px rgba(0,5,15,.36), inset 0 1px 0 rgba(255,255,255,.43); backdrop-filter: blur(28px) saturate(145%); -webkit-backdrop-filter: blur(28px) saturate(145%); }
  .panel-shine { position: absolute; inset: -1px -1px auto; height: 45%; background: radial-gradient(ellipse at 30% 0%, rgba(255,255,255,.22), transparent 60%); pointer-events: none; }
  .identity { display: grid; grid-template-columns: minmax(14rem, .76fr) minmax(0, 1.7fr); gap: clamp(1.8rem, 5vw, 4rem); align-items: center; padding: clamp(1.15rem, 3.3vw, 2.45rem); border-radius: 1.8rem; }

  .portrait-shell { position: relative; max-width: 18rem; aspect-ratio: 1; padding: .48rem; border: 1px solid rgba(255,255,255,.26); border-radius: 1.35rem; background: rgba(255,255,255,.08); box-shadow: inset 0 1px 0 rgba(255,255,255,.3), 0 18px 38px rgba(0,8,20,.35); }
  .portrait-shell img { width: 100%; height: 100%; border-radius: 1rem; object-fit: cover; object-position: 50% 34%; filter: saturate(.88) contrast(1.02); }
  .portrait-shell::after { position: absolute; inset: .48rem; border-radius: 1rem; background: linear-gradient(180deg, transparent 62%, rgba(2,10,22,.45)); content: ''; pointer-events: none; }
  .pixel-corners { position: absolute; z-index: 2; inset: -.15rem; border: 2px solid transparent; pointer-events: none; }
  .pixel-corners::before, .pixel-corners::after { position: absolute; width: 1rem; height: 1rem; border-color: #8be9ff; content: ''; }
  .pixel-corners::before { top: 0; left: 0; border-top: 2px solid #8be9ff; border-left: 2px solid #8be9ff; }
  .pixel-corners::after { right: 0; bottom: 0; border-right: 2px solid #8be9ff; border-bottom: 2px solid #8be9ff; }
  .availability { position: absolute; z-index: 3; right: .85rem; bottom: .85rem; display: flex; align-items: center; gap: .45rem; padding: .38rem .55rem; border: 1px solid rgba(255,255,255,.2); border-radius: .5rem; color: rgba(255,255,255,.88); background: rgba(4,13,25,.66); font-family: ui-monospace, monospace; font-size: .62rem; backdrop-filter: blur(10px); }
  .availability i { width: .42rem; height: .42rem; border-radius: 50%; background: #67f3ba; box-shadow: 0 0 9px #67f3ba; }

  .intro-copy { position: relative; z-index: 2; }
  .eyebrow { display: flex; align-items: center; gap: .55rem; margin: 0 0 .85rem; color: #9aeaff; }
  .eyebrow span { width: .55rem; height: .55rem; background: #9aeaff; box-shadow: 0 0 12px rgba(154,234,255,.8); clip-path: polygon(50% 0,100% 50%,50% 100%,0 50%); }
  h1 { max-width: 46rem; margin: 0; font-size: clamp(2.35rem, 5.2vw, 4.85rem); letter-spacing: -.065em; line-height: .97; text-wrap: balance; }
  h1 em { color: transparent; background: linear-gradient(90deg, #90e9ff, #a9b9ff 55%, #d0afff); background-clip: text; -webkit-background-clip: text; font-style: normal; }
  .summary { max-width: 42rem; margin: 1.1rem 0 1.4rem; color: rgba(239,247,255,.68); font-size: clamp(.93rem, 1.7vw, 1.08rem); line-height: 1.6; }
  .actions { display: flex; flex-wrap: wrap; gap: .7rem; }
  .actions button, .actions a { display: inline-flex; align-items: center; justify-content: center; gap: .7rem; min-height: 2.8rem; padding: .7rem 1rem; border-radius: .7rem; font-size: .78rem; font-weight: 760; text-decoration: none; cursor: pointer; transition: transform 160ms ease, background 160ms ease; }
  .actions button:hover, .actions a:hover { transform: translateY(-2px); }
  .primary-action { border: 1px solid rgba(255,255,255,.75); color: #061321; background: linear-gradient(135deg, #c7f7ff, #9ac3ff); box-shadow: 0 8px 25px rgba(110,204,255,.22), inset 0 1px 0 white; }
  .secondary-action { border: 1px solid rgba(255,255,255,.18); color: white; background: rgba(255,255,255,.08); }
  .contact-line { display: flex; align-items: center; gap: .75rem; margin-top: 1.15rem; }
  .contact-line a { color: rgba(239,247,255,.6); font-size: .72rem; text-decoration: none; }
  .contact-line a:hover { color: white; }
  .contact-line i { width: 3px; height: 3px; background: rgba(255,255,255,.25); transform: rotate(45deg); }

  .signal-strip { display: grid; grid-template-columns: repeat(3, .72fr) 1.2fr; margin-top: .75rem; border-radius: 1.2rem; }
  .signal-strip > div { padding: 1rem 1.15rem; border-right: 1px solid rgba(255,255,255,.1); }
  .signal-strip > div:last-child { border-right: 0; }
  .metric { display: block; font-size: clamp(1.25rem, 2.5vw, 1.8rem); font-weight: 790; letter-spacing: -.045em; }
  .signal-strip p { margin: .15rem 0; color: rgba(247,251,255,.78); font-size: .72rem; font-weight: 700; }
  .signal-strip small { color: rgba(237,246,255,.42); font-size: .62rem; }
  .mini-now { display: flex; flex-direction: column; justify-content: center; }
  .mini-now .pixel-label { color: #8de9ff; font-size: .61rem; }

  .work-window { width: min(900px, calc(100vw - 1rem)); height: min(630px, calc(100svh - 7.9rem)); }
  .terminal-window { width: min(730px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }
  .notes-window { width: min(700px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }
  .game-window { width: min(620px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }

  @media (max-width: 760px) {
    .menu-link, .menu-left strong, .signal { display: none; }
    .menu-left, .menu-right { gap: .65rem; }
    .desktop-content { display: flex; align-items: center; padding: 3.5rem .65rem 5.8rem; }
    .identity { grid-template-columns: 1fr; gap: 1rem; max-height: calc(100svh - 10rem); overflow-y: auto; border-radius: 1.25rem; }
    .portrait-shell { width: 7.25rem; border-radius: 1rem; }
    .portrait-shell img, .portrait-shell::after { border-radius: .72rem; }
    .availability { right: -.3rem; bottom: .4rem; }
    h1 { font-size: clamp(2rem, 10.5vw, 3.1rem); }
    .summary { margin: .8rem 0 1rem; font-size: .84rem; line-height: 1.5; }
    .contact-line { margin-top: .85rem; }
    .signal-strip { display: none; }
    .actions button, .actions a { flex: 1 1 10rem; }
    .work-window, .terminal-window, .notes-window, .game-window { width: 100%; height: 100%; }
  }

  @media (max-height: 720px) and (min-width: 761px) {
    .desktop-content { padding-top: 4.2rem; }
    .identity { grid-template-columns: 12rem 1fr; padding: 1.25rem; }
    .portrait-shell { max-width: 12rem; }
    h1 { font-size: clamp(2.15rem, 4.5vw, 3.65rem); }
    .summary { margin: .8rem 0 1rem; }
    .signal-strip > div { padding: .72rem 1rem; }
  }
</style>
