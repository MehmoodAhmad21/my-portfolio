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
  import finderIcon from '$lib/assets/finder.png';
  import terminalIcon from '$lib/assets/terminal.png';
  import notesIcon from '$lib/assets/notes.png';
  import flappyIcon from '$lib/assets/flappy.png';

  type FinderTab = 'highlights' | 'projects' | 'experience';
  type WindowState = { isMinimized: boolean; isMaximized: boolean; x: number; y: number; zIndex: number };

  const apps: DockApp[] = [
    { name: 'Finder', icon: finderIcon },
    { name: 'Terminal', icon: terminalIcon },
    { name: 'Notes', icon: notesIcon },
    { name: 'Flappy Bird', icon: flappyIcon }
  ];

  let openApps: Set<string> = new Set();
  let activeApp: string | null = null;
  let maxZIndex = 100;
  let currentTime = '';
  let currentDate = '';
  let finderView: FinderTab = 'highlights';
  let finderKey = 0;

  let windowStates: Record<string, WindowState> = {
    Finder: { isMinimized: false, isMaximized: false, x: 110, y: 62, zIndex: 101 },
    Terminal: { isMinimized: false, isMaximized: false, x: 175, y: 92, zIndex: 102 },
    Notes: { isMinimized: false, isMaximized: false, x: 220, y: 108, zIndex: 103 },
    'Flappy Bird': { isMinimized: false, isMaximized: false, x: 270, y: 126, zIndex: 104 }
  };

  function updateClock() {
    const now = new Date();
    currentTime = now.toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit' });
    currentDate = now.toLocaleDateString('en-US', { weekday: 'short', month: 'short', day: 'numeric' });
  }

  onMount(() => {
    updateClock();
    const timer = setInterval(updateClock, 60000);
    return () => clearInterval(timer);
  });

  function openApp(name: string) {
    const app = apps.find((item) => item.name === name);
    if (app) handleAppSelect(app);
  }

  function openFinder(tab: FinderTab) {
    finderView = tab;
    finderKey += 1;
    openApp('Finder');
  }

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
        Finder: [900, 630], Terminal: [730, 500], Notes: [700, 500], 'Flappy Bird': [620, 500]
      };
      const [width, height] = sizes[app.name];
      windowStates[app.name].x = Math.max(24, (window.innerWidth - width) / 2);
      windowStates[app.name].y = Math.max(48, (window.innerHeight - height) / 2 - 8);
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
  <div class="night-shade"></div>
  <div class="scanlines"></div>

  <header class="menu-bar">
    <div class="menu-left">
      <button class="pixel-apple" on:click={() => openFinder('highlights')} aria-label="Open profile">M</button>
      <strong>{activeApp ?? 'Finder'}</strong>
      <span>File</span><span>Edit</span><span>View</span><span>Go</span><span>Window</span><span>Help</span>
    </div>
    <div class="menu-right">
      <span class="status-dot"><i></i> available</span>
      <span class="wifi" aria-label="Wi-Fi">▴</span>
      <span class="battery" aria-label="Battery"><i></i></span>
      <time>{currentDate}&nbsp;&nbsp;{currentTime}</time>
    </div>
  </header>

  <main class:apps-open={openApps.size > 0} class="desktop-area">
    <section class="desktop-icons" aria-label="Desktop files">
      <button class="desktop-icon" on:click={() => openFinder('highlights')}>
        <span class="icon-frame portrait-icon"><img src={portrait} alt="" /></span>
        <span>About_Me.app</span>
      </button>
      <button class="desktop-icon" on:click={() => openFinder('projects')}>
        <span class="icon-frame"><img src={finderIcon} alt="" /></span>
        <span>Projects</span>
      </button>
      <button class="desktop-icon" on:click={() => openFinder('experience')}>
        <span class="icon-frame pixel-file work-file"><b>WORK</b><i></i></span>
        <span>Experience.log</span>
      </button>
      <a class="desktop-icon" href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank">
        <span class="icon-frame pixel-file pdf-file"><b>PDF</b><i></i></span>
        <span>Resume_2026.pdf</span>
      </a>
      <a class="desktop-icon" href="https://github.com/MehmoodAhmad21" target="_blank" rel="noreferrer">
        <span class="icon-frame pixel-tile github-tile"><b>GH</b><i>↗</i></span>
        <span>GitHub.link</span>
      </a>
      <button class="desktop-icon" on:click={() => openApp('Terminal')}>
        <span class="icon-frame"><img src={terminalIcon} alt="" /></span>
        <span>Terminal</span>
      </button>
    </section>

    <section class="widgets" aria-label="Desktop widgets">
      <article class="widget profile-widget">
        <div class="widget-glint"></div>
        <div class="profile-top">
          <img src={portrait} alt="Mehmood Ahmad" />
          <div><span class="pixel-kicker">USER / 01</span><h1>Mehmood<br />Ahmad</h1></div>
        </div>
        <p>Software engineer building applied AI, real-time systems, robotics, and thoughtful product experiences.</p>
        <div class="profile-links">
          <a href="mailto:mehmood3@ualberta.ca">Email</a>
          <a href="https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
        </div>
      </article>

      <article class="widget signal-widget">
        <header><span class="pixel-kicker">SYSTEM SIGNALS</span><i class="live-light"></i></header>
        <div class="signal-row"><strong>50.6%</strong><span>Hazard F1<br /><small>YOLOv11 + VLM</small></span></div>
        <div class="pixel-meter"><i style="width: 50.6%"></i></div>
        <div class="signal-pair">
          <div><strong>~2.5ms</strong><span>latency</span></div>
          <div><strong>70%</strong><span>faster deploys</span></div>
        </div>
      </article>

      <button class="widget publication-widget" on:click={() => openFinder('highlights')}>
        <span class="paper-pixel">⌁</span>
        <span><small>NEW FILE · 2026</small><strong>Object Detection + Small VLMs</strong><em>arXiv:2604.05210</em></span>
        <b>›</b>
      </button>
    </section>

    <div class="desktop-hint"><span>◇</span> Select a file or open an app from the dock</div>

    {#if openApps.has('Finder')}
      <div style:z-index={windowStates.Finder.zIndex}>
        <Window title="Mehmood HD" icon={finderIcon} isActive={activeApp === 'Finder'} bind:x={windowStates.Finder.x} bind:y={windowStates.Finder.y} bind:isMinimized={windowStates.Finder.isMinimized} bind:isMaximized={windowStates.Finder.isMaximized} on:close={() => closeApp('Finder')} on:minimize={(e) => handleMinimize('Finder', e)} on:focus={() => bringToFront('Finder')}>
          <div class="finder-window">{#key finderKey}<Finder initialTab={finderView} />{/key}</div>
        </Window>
      </div>
    {/if}

    {#if openApps.has('Terminal')}
      <div style:z-index={windowStates.Terminal.zIndex}>
        <Window title="Terminal — zsh" icon={terminalIcon} isActive={activeApp === 'Terminal'} bind:x={windowStates.Terminal.x} bind:y={windowStates.Terminal.y} bind:isMinimized={windowStates.Terminal.isMinimized} bind:isMaximized={windowStates.Terminal.isMaximized} on:close={() => closeApp('Terminal')} on:minimize={(e) => handleMinimize('Terminal', e)} on:focus={() => bringToFront('Terminal')}>
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
        <Window title="Flappy Bird.app" icon={flappyIcon} isActive={activeApp === 'Flappy Bird'} bind:x={windowStates['Flappy Bird'].x} bind:y={windowStates['Flappy Bird'].y} bind:isMinimized={windowStates['Flappy Bird'].isMinimized} bind:isMaximized={windowStates['Flappy Bird'].isMaximized} on:close={() => closeApp('Flappy Bird')} on:minimize={(e) => handleMinimize('Flappy Bird', e)} on:focus={() => bringToFront('Flappy Bird')}>
          <div class="game-window"><FlappyBird /></div>
        </Window>
      </div>
    {/if}
  </main>

  <Dock {apps} {openApps} {activeApp} onSelect={handleAppSelect} />
</div>

<style>
  .desktop { position: relative; width: 100%; min-height: 100svh; overflow: hidden; color: #f8fbff; background: #07111f; }
  .wallpaper { position: absolute; inset: 0; background-position: center; background-size: cover; transform: scale(1.01); }
  .night-shade { position: absolute; inset: 0; background: radial-gradient(circle at 48% 35%, transparent 0 25%, rgba(1,8,19,.1) 57%, rgba(1,7,17,.38)), linear-gradient(110deg, rgba(2,10,22,.16), transparent 54%, rgba(2,8,18,.22)); }
  .scanlines { position: absolute; inset: 0; opacity: .1; background: repeating-linear-gradient(180deg, rgba(255,255,255,.04) 0 1px, transparent 1px 4px); pointer-events: none; }

  .menu-bar { position: fixed; z-index: 2000; inset: 0 0 auto; display: flex; min-height: 2.35rem; align-items: center; justify-content: space-between; padding: .32rem .85rem; border-bottom: 1px solid rgba(255,255,255,.28); color: rgba(255,255,255,.86); background: linear-gradient(180deg, rgba(255,255,255,.21), rgba(255,255,255,.07)), rgba(4,14,28,.38); box-shadow: inset 0 1px 0 rgba(255,255,255,.38); backdrop-filter: blur(28px) saturate(155%); -webkit-backdrop-filter: blur(28px) saturate(155%); font-size: .75rem; }
  .menu-left, .menu-right { display: flex; align-items: center; gap: .95rem; }
  .menu-left strong { color: white; }
  .menu-left span { color: rgba(255,255,255,.72); }
  .pixel-apple { display: grid; width: 1.45rem; height: 1.45rem; padding: 0; place-items: center; border: 1px solid #d9f7ff; border-radius: 3px; color: #07121f; background: #9beaff; box-shadow: 2px 2px 0 rgba(0,0,0,.45); font-family: ui-monospace, monospace; font-size: .68rem; font-weight: 950; cursor: pointer; }
  .menu-right { gap: .72rem; }
  .status-dot { display: flex; align-items: center; gap: .35rem; font-family: ui-monospace, monospace; font-size: .62rem; text-transform: uppercase; }
  .status-dot i, .live-light { width: .42rem; height: .42rem; border-radius: 1px; background: #69f1b9; box-shadow: 0 0 8px #69f1b9; }
  .wifi { transform: rotate(180deg); }
  .battery { display: block; width: 1.25rem; height: .62rem; padding: 2px; border: 1px solid currentColor; border-radius: 2px; }
  .battery i { display: block; width: 78%; height: 100%; background: #82efc4; }

  .desktop-area { position: relative; z-index: 2; display: grid; min-height: 100svh; grid-template-columns: minmax(22rem, 1fr) minmax(18rem, 23rem); padding: 4.15rem 2.3rem 6.5rem; transition: filter 180ms ease; }
  .desktop-area.apps-open > .desktop-icons, .desktop-area.apps-open > .widgets, .desktop-area.apps-open > .desktop-hint { filter: saturate(.8) brightness(.72); }

  .desktop-icons { display: grid; width: max-content; align-content: start; grid-template-columns: repeat(2, 7.8rem); gap: 1.2rem .8rem; }
  .desktop-icon { display: flex; width: 7.8rem; flex-direction: column; align-items: center; gap: .48rem; padding: .35rem; border: 0; border-radius: .55rem; color: white; background: transparent; text-align: center; text-decoration: none; cursor: default; }
  .desktop-icon:hover, .desktop-icon:focus-visible { background: rgba(81,151,255,.25); box-shadow: inset 0 0 0 1px rgba(197,230,255,.34); outline: none; }
  .desktop-icon > span:last-child { max-width: 7.25rem; padding: .14rem .28rem; border-radius: 2px; color: white; font-family: ui-monospace, "SFMono-Regular", monospace; font-size: .7rem; font-weight: 700; line-height: 1.25; text-shadow: 0 1px 3px #000, 1px 1px 0 #000; }
  .icon-frame { display: grid; position: relative; width: 4.8rem; height: 4.8rem; place-items: center; filter: drop-shadow(4px 5px 0 rgba(2,8,17,.45)) drop-shadow(0 10px 16px rgba(0,0,0,.28)); transition: transform 120ms steps(2); }
  .desktop-icon:hover .icon-frame { transform: translateY(-3px); }
  .icon-frame img { width: 100%; height: 100%; border-radius: .85rem; object-fit: cover; image-rendering: pixelated; }
  .portrait-icon { overflow: hidden; padding: 4px; border: 2px solid #bdefff; border-radius: 9px; background: linear-gradient(135deg, #6fdcf7, #738ff6); clip-path: polygon(7px 0, calc(100% - 7px) 0, calc(100% - 7px) 3px, 100% 3px, 100% calc(100% - 7px), calc(100% - 3px) calc(100% - 7px), calc(100% - 3px) 100%, 7px 100%, 7px calc(100% - 3px), 0 calc(100% - 3px), 0 7px, 3px 7px, 3px 3px, 7px 3px); }
  .portrait-icon img { border-radius: 5px; }
  .pixel-file { align-content: end; overflow: hidden; border: 2px solid rgba(255,255,255,.72); border-radius: 4px; background: linear-gradient(135deg, #eef9ff, #addcff 72%); box-shadow: inset -6px -6px 0 rgba(45,107,165,.15); }
  .pixel-file::before { position: absolute; top: -2px; right: -2px; width: 1.25rem; height: 1.25rem; border-left: 2px solid rgba(43,86,130,.38); border-bottom: 2px solid rgba(43,86,130,.38); background: rgba(255,255,255,.56); content: ''; }
  .pixel-file b { margin-bottom: .55rem; padding: .18rem .3rem; color: #061b31; background: #61d8ff; font-family: ui-monospace, monospace; font-size: .64rem; letter-spacing: .08em; }
  .pixel-file i { position: absolute; right: .45rem; bottom: .35rem; width: .65rem; height: .65rem; background: repeating-linear-gradient(90deg, #34658e 0 2px, transparent 2px 4px); }
  .pdf-file { background: linear-gradient(135deg, #fff2f4, #ffb9c2 72%); }
  .pdf-file b { color: white; background: #f14e65; }
  .work-file b { color: #052131; background: #73e6d1; }
  .pixel-tile { border: 2px solid #bcecff; border-radius: 7px; color: white; background: linear-gradient(145deg, #182a45, #080d18); clip-path: polygon(5px 0, calc(100% - 5px) 0, calc(100% - 5px) 2px, 100% 2px, 100% calc(100% - 5px), calc(100% - 2px) calc(100% - 5px), calc(100% - 2px) 100%, 5px 100%, 5px calc(100% - 2px), 0 calc(100% - 2px), 0 5px, 2px 5px, 2px 2px, 5px 2px); }
  .pixel-tile b { font-family: ui-monospace, monospace; font-size: 1.35rem; text-shadow: 2px 2px 0 #316892; }
  .pixel-tile i { position: absolute; right: .4rem; top: .25rem; color: #83e6ff; font-style: normal; }

  .widgets { display: grid; align-content: start; gap: .75rem; justify-self: end; width: min(23rem, 100%); }
  .widget { position: relative; overflow: hidden; border: 1px solid rgba(255,255,255,.3); border-radius: .85rem; color: white; background: linear-gradient(145deg, rgba(255,255,255,.19), rgba(255,255,255,.065) 48%, rgba(91,154,232,.08)), rgba(4,14,28,.52); box-shadow: 5px 5px 0 rgba(1,8,18,.3), 0 20px 40px rgba(0,5,15,.22), inset 0 1px 0 rgba(255,255,255,.42); backdrop-filter: blur(25px) saturate(145%); -webkit-backdrop-filter: blur(25px) saturate(145%); }
  .widget-glint { position: absolute; inset: 0; background: radial-gradient(circle at 10% 0%, rgba(255,255,255,.23), transparent 34%); pointer-events: none; }
  .profile-widget { padding: 1rem; }
  .profile-top { position: relative; display: grid; grid-template-columns: 4.1rem 1fr; align-items: center; gap: .85rem; }
  .profile-top img { width: 4.1rem; height: 4.1rem; border: 2px solid rgba(185,238,255,.72); border-radius: .55rem; object-fit: cover; image-rendering: pixelated; box-shadow: 3px 3px 0 rgba(5,15,29,.65); }
  .pixel-kicker { color: #89e6ff; font-family: ui-monospace, monospace; font-size: .62rem; font-weight: 850; letter-spacing: .11em; }
  .profile-top h1 { margin: .15rem 0 0; font-size: 1.65rem; letter-spacing: -.045em; line-height: .88; }
  .profile-widget > p { position: relative; margin: .85rem 0; color: rgba(240,248,255,.72); font-size: .82rem; line-height: 1.5; }
  .profile-links { position: relative; display: flex; gap: .45rem; }
  .profile-links a { padding: .42rem .6rem; border: 1px solid rgba(255,255,255,.17); border-radius: .35rem; color: white; background: rgba(255,255,255,.075); font-family: ui-monospace, monospace; font-size: .65rem; font-weight: 700; text-decoration: none; }
  .profile-links a:hover { background: rgba(128,225,255,.18); }

  .signal-widget { padding: .9rem 1rem 1rem; }
  .signal-widget header { display: flex; align-items: center; justify-content: space-between; }
  .signal-row { display: flex; align-items: center; gap: .75rem; margin-top: .75rem; }
  .signal-row > strong { font-size: 2.15rem; letter-spacing: -.06em; }
  .signal-row > span { color: rgba(244,249,255,.7); font-size: .72rem; font-weight: 720; line-height: 1.2; }
  .signal-row small { color: rgba(235,245,255,.42); font-size: .6rem; font-weight: 500; }
  .pixel-meter { height: .55rem; margin: .55rem 0 .85rem; padding: 2px; border: 1px solid rgba(174,230,255,.38); background: rgba(1,9,20,.4); }
  .pixel-meter i { display: block; height: 100%; background: repeating-linear-gradient(90deg, #73ddff 0 5px, transparent 5px 7px); box-shadow: 0 0 9px rgba(115,221,255,.35); }
  .signal-pair { display: grid; grid-template-columns: 1fr 1fr; border-top: 1px solid rgba(255,255,255,.1); }
  .signal-pair div { padding-top: .7rem; }
  .signal-pair div + div { padding-left: .9rem; border-left: 1px solid rgba(255,255,255,.1); }
  .signal-pair strong, .signal-pair span { display: block; }
  .signal-pair strong { font-size: 1rem; }
  .signal-pair span { margin-top: .08rem; color: rgba(240,247,255,.48); font-size: .62rem; }

  .publication-widget { display: grid; grid-template-columns: auto 1fr auto; align-items: center; gap: .75rem; width: 100%; padding: .75rem; text-align: left; cursor: pointer; }
  .paper-pixel { display: grid; width: 2.7rem; height: 2.7rem; place-items: center; border: 2px solid #b7ecff; border-radius: 3px; color: #07131f; background: #81dcf7; box-shadow: 3px 3px 0 rgba(0,0,0,.35); font-size: 1.3rem; }
  .publication-widget > span:nth-child(2) { min-width: 0; }
  .publication-widget small, .publication-widget strong, .publication-widget em { display: block; }
  .publication-widget small { color: #91e8ff; font-family: ui-monospace, monospace; font-size: .58rem; }
  .publication-widget strong { margin: .16rem 0; overflow: hidden; font-size: .75rem; text-overflow: ellipsis; white-space: nowrap; }
  .publication-widget em { color: rgba(240,248,255,.46); font-family: ui-monospace, monospace; font-size: .58rem; font-style: normal; }
  .publication-widget > b { color: rgba(255,255,255,.5); font-size: 1.35rem; }

  .desktop-hint { position: fixed; left: 50%; bottom: 6.3rem; display: flex; transform: translateX(-50%); align-items: center; gap: .45rem; padding: .38rem .62rem; border: 1px solid rgba(255,255,255,.16); border-radius: .4rem; color: rgba(255,255,255,.58); background: rgba(2,10,22,.35); font-family: ui-monospace, monospace; font-size: .62rem; backdrop-filter: blur(12px); }
  .desktop-hint span { color: #87e8ff; }

  .finder-window { width: min(900px, calc(100vw - 1rem)); height: min(630px, calc(100svh - 7.9rem)); }
  .terminal-window { width: min(730px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }
  .notes-window { width: min(700px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }
  .game-window { width: min(620px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }

  @media (max-width: 800px) {
    .menu-left span, .status-dot { display: none; }
    .desktop-area { display: block; height: 100svh; overflow-y: auto; padding: 3.3rem .75rem 6.8rem; }
    .desktop-icons { width: 100%; grid-template-columns: repeat(3, minmax(0, 1fr)); gap: .6rem .15rem; }
    .desktop-icon { width: 100%; padding: .28rem .05rem; }
    .desktop-icon > span:last-child { font-size: .58rem; }
    .icon-frame { width: 3.8rem; height: 3.8rem; }
    .widgets { width: 100%; margin-top: 1rem; }
    .profile-widget { display: grid; grid-template-columns: auto 1fr; gap: 0 1rem; }
    .profile-top { grid-column: 1 / -1; }
    .profile-links { justify-content: flex-end; align-items: center; }
    .desktop-hint { display: none; }
    .finder-window, .terminal-window, .notes-window, .game-window { width: 100%; height: 100%; }
  }

  @media (max-width: 430px) {
    .menu-right .wifi, .menu-right .battery { display: none; }
    .profile-widget { display: block; }
    .profile-links { justify-content: flex-start; }
  }
</style>
