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
  import pixelAvatar from '$lib/assets/mehmood-pixel-avatar.png';
  import finderIcon from '$lib/assets/finder.png';
  import terminalIcon from '$lib/assets/terminal.png';
  import notesIcon from '$lib/assets/notes.png';
  import flappyIcon from '$lib/assets/flappy.png';

  type FinderTab = 'highlights' | 'projects' | 'experience';
  type MenuName = 'apple' | 'app' | 'file' | 'edit' | 'view' | 'go' | 'window' | 'help' | 'status' | 'control' | 'clock';
  type PortfolioNotification = { id: string; app: string; icon: string; title: string; body: string; time: string; accent: string };
  type WindowState = { isMinimized: boolean; isMaximized: boolean; x: number; y: number; zIndex: number };

  const apps: DockApp[] = [
    { name: 'Finder', icon: finderIcon },
    { name: 'Terminal', icon: terminalIcon },
    { name: 'Notes', icon: notesIcon },
    { name: 'Flappy Bird', icon: flappyIcon }
  ];

  const notifications: PortfolioNotification[] = [
    { id: 'research', app: 'Research', icon: 'R', title: 'New publication available', body: 'Object Detection + Small VLMs for Construction Safety · arXiv 2026', time: 'now', accent: '#0a84ff' },
    { id: 'projects', app: 'Finder', icon: 'F', title: '5 featured projects', body: 'Offspring.exe, Trackme, SCAT6, SafeHaven, and Event Lottery are ready to explore.', time: '2m', accent: '#67d5ef' },
    { id: 'contact', app: 'Messages', icon: 'M', title: 'Mehmood is available', body: 'Open to software engineering and applied AI opportunities.', time: '5m', accent: '#35c759' }
  ];

  let openApps: Set<string> = new Set();
  let activeApp: string | null = null;
  let maxZIndex = 100;
  let currentTime = '';
  let currentDate = '';
  let currentLongDate = '';
  let currentMonth = '';
  let currentDay = '';
  let finderView: FinderTab = 'highlights';
  let finderKey = 0;
  let openMenu: MenuName | null = null;
  let menuNotice = '';
  let brightness = 100;
  let dismissedNotifications: string[] = [];

  $: visibleNotifications = notifications.filter((notification) => !dismissedNotifications.includes(notification.id));

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
    currentLongDate = now.toLocaleDateString('en-US', { weekday: 'long', month: 'long', day: 'numeric' });
    currentMonth = now.toLocaleDateString('en-US', { month: 'short' }).toUpperCase();
    currentDay = now.toLocaleDateString('en-US', { day: '2-digit' });
  }

  onMount(() => {
    const savedBrightness = Number(localStorage.getItem('mehmood-os-brightness'));
    if (savedBrightness >= 50 && savedBrightness <= 120) brightness = savedBrightness;
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
        Finder: [940, 640], Terminal: [760, 510], Notes: [860, 540], 'Flappy Bird': [620, 500]
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

  function toggleMenu(menu: MenuName) {
    openMenu = openMenu === menu ? null : menu;
  }

  function runMenuAction(action: () => void) {
    action();
    openMenu = null;
  }

  async function copyContact(value: string, label: string) {
    try {
      await navigator.clipboard.writeText(value);
      menuNotice = `${label} copied`;
    } catch {
      menuNotice = `Copy ${value}`;
    }
    openMenu = null;
    setTimeout(() => menuNotice = '', 1800);
  }

  function changeBrightness(event: Event) {
    brightness = Number((event.currentTarget as HTMLInputElement).value);
    localStorage.setItem('mehmood-os-brightness', String(brightness));
  }

  function dismissNotification(id: string) {
    dismissedNotifications = [...dismissedNotifications, id];
  }

  function openNotification(id: string) {
    if (id === 'research') window.open('https://arxiv.org/abs/2604.05210', '_blank', 'noopener,noreferrer');
    if (id === 'projects') openFinder('projects');
    if (id === 'contact') window.location.href = 'mailto:mehmood3@ualberta.ca';
    openMenu = null;
  }

  function handleWindowClick(event: MouseEvent) {
    const target = event.target as HTMLElement;
    if (!target.closest('.menu-bar') || target.closest('.menu-popover a, .notification-center a')) openMenu = null;
  }

  function handleKeydown(event: KeyboardEvent) {
    if (event.key === 'Escape' && openMenu) {
      openMenu = null;
      return;
    }
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

<svelte:window on:keydown={handleKeydown} on:click={handleWindowClick} />

<div class="desktop" style={`--screen-brightness:${brightness}%`}>
  <div class="wallpaper" style={`background-image: url('${wallpaper}')`}></div>
  <div class="night-shade"></div>
  <div class="scanlines"></div>

  <header class="menu-bar">
    <div class="menu-left">
      <div class="menu-item">
        <button class:active={openMenu === 'apple'} class="pixel-apple" on:click={() => toggleMenu('apple')} aria-label="Portfolio menu" aria-expanded={openMenu === 'apple'}>M</button>
        {#if openMenu === 'apple'}
          <div class="menu-popover apple-popover" role="menu">
            <button on:click={() => runMenuAction(() => openFinder('highlights'))}><span>About This Portfolio</span></button>
            <div class="menu-separator"></div>
            <a href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank"><span>Résumé…</span></a>
            <button on:click={() => runMenuAction(() => openApp('Terminal'))}><span>System Information…</span></button>
            <div class="menu-separator"></div>
            <a href="mailto:mehmood3@ualberta.ca"><span>Contact Mehmood…</span></a>
          </div>
        {/if}
      </div>

      <div class="menu-item app-menu">
        <button class:active={openMenu === 'app'} class="menu-button app-button" on:click={() => toggleMenu('app')} aria-expanded={openMenu === 'app'}>{activeApp ?? 'Finder'}</button>
        {#if openMenu === 'app'}
          <div class="menu-popover" role="menu">
            <button on:click={() => runMenuAction(() => activeApp ? openApp(activeApp) : openFinder('highlights'))}><span>About {activeApp ?? 'Finder'}</span></button>
            <div class="menu-separator"></div>
            <button on:click={() => runMenuAction(() => openFinder('highlights'))}><span>Portfolio Home</span><kbd>⌘H</kbd></button>
            {#if activeApp}<button on:click={() => runMenuAction(() => closeApp(activeApp!))}><span>Quit {activeApp}</span><kbd>⌘Q</kbd></button>{/if}
          </div>
        {/if}
      </div>

      <div class="menu-item"><button class:active={openMenu === 'file'} class="menu-button" on:click={() => toggleMenu('file')}>File</button>{#if openMenu === 'file'}<div class="menu-popover" role="menu"><button on:click={() => runMenuAction(() => openFinder('highlights'))}><span>New Finder Window</span><kbd>⌘N</kbd></button><button on:click={() => runMenuAction(() => openFinder('projects'))}><span>Open Projects</span><kbd>⌘O</kbd></button><a href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank"><span>Open Résumé…</span></a><div class="menu-separator"></div><button disabled={!activeApp} on:click={() => activeApp && runMenuAction(() => closeApp(activeApp!))}><span>Close Window</span><kbd>⌘W</kbd></button></div>{/if}</div>
      <div class="menu-item"><button class:active={openMenu === 'edit'} class="menu-button" on:click={() => toggleMenu('edit')}>Edit</button>{#if openMenu === 'edit'}<div class="menu-popover" role="menu"><button on:click={() => copyContact('mehmood3@ualberta.ca', 'Email')}><span>Copy Email</span><kbd>⌘C</kbd></button><button on:click={() => copyContact('https://github.com/MehmoodAhmad21', 'GitHub link')}><span>Copy GitHub Link</span></button><button on:click={() => copyContact('https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/', 'LinkedIn link')}><span>Copy LinkedIn Link</span></button></div>{/if}</div>
      <div class="menu-item"><button class:active={openMenu === 'view'} class="menu-button" on:click={() => toggleMenu('view')}>View</button>{#if openMenu === 'view'}<div class="menu-popover" role="menu"><button on:click={() => runMenuAction(() => openFinder('highlights'))}><span>Show Recents</span></button><button on:click={() => runMenuAction(() => openFinder('projects'))}><span>Show Projects</span></button><button on:click={() => runMenuAction(() => openFinder('experience'))}><span>Show Experience</span></button><div class="menu-separator"></div><button disabled={!activeApp} on:click={() => activeApp && runMenuAction(() => windowStates[activeApp!].isMaximized = !windowStates[activeApp!].isMaximized)}><span>Enter Full Screen</span><kbd>⌃⌘F</kbd></button></div>{/if}</div>
      <div class="menu-item"><button class:active={openMenu === 'go'} class="menu-button" on:click={() => toggleMenu('go')}>Go</button>{#if openMenu === 'go'}<div class="menu-popover" role="menu"><a href="https://github.com/MehmoodAhmad21" target="_blank" rel="noreferrer"><span>GitHub</span><kbd>↗</kbd></a><a href="https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/" target="_blank" rel="noreferrer"><span>LinkedIn</span><kbd>↗</kbd></a><a href="https://arxiv.org/abs/2604.05210" target="_blank" rel="noreferrer"><span>Research Paper</span><kbd>↗</kbd></a></div>{/if}</div>
      <div class="menu-item"><button class:active={openMenu === 'window'} class="menu-button" on:click={() => toggleMenu('window')}>Window</button>{#if openMenu === 'window'}<div class="menu-popover" role="menu"><button disabled={!activeApp} on:click={() => activeApp && runMenuAction(() => { windowStates[activeApp!].isMinimized = true; activeApp = null; })}><span>Minimize</span><kbd>⌘M</kbd></button><div class="menu-separator"></div>{#each apps as app}<button on:click={() => runMenuAction(() => openApp(app.name))}><span>{openApps.has(app.name) ? '✓' : ''}&nbsp;&nbsp;{app.name}</span></button>{/each}</div>{/if}</div>
      <div class="menu-item"><button class:active={openMenu === 'help'} class="menu-button" on:click={() => toggleMenu('help')}>Help</button>{#if openMenu === 'help'}<div class="menu-popover align-right" role="menu"><button on:click={() => runMenuAction(() => openFinder('highlights'))}><span>Portfolio Guide</span></button><button on:click={() => runMenuAction(() => openApp('Terminal'))}><span>Terminal Commands</span></button><div class="menu-separator"></div><a href="mailto:mehmood3@ualberta.ca"><span>Ask Mehmood a Question…</span></a></div>{/if}</div>
    </div>
    <div class="menu-right">
      <div class="menu-item right-menu"><button class:active={openMenu === 'status'} class="status-button" on:click={() => toggleMenu('status')}><span class="status-dot"><i></i> available</span></button>{#if openMenu === 'status'}<div class="menu-popover status-panel" role="menu"><div class="status-card"><i></i><div><strong>Available for opportunities</strong><span>Software engineering · Applied AI</span></div></div><p>Edmonton, Alberta · Open to relocation and remote work.</p><a href="https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/" target="_blank" rel="noreferrer"><span>Start a conversation</span><kbd>↗</kbd></a></div>{/if}</div>
      <div class="menu-item right-menu"><button class:active={openMenu === 'control'} class="system-button" on:click={() => toggleMenu('control')} aria-label="Control Center"><span class="wifi">▴</span><span class="battery"><i></i></span></button>{#if openMenu === 'control'}<div class="control-center"><button on:click={() => copyContact('mehmood3@ualberta.ca', 'Email')}><span class="control-icon blue">⌁</span><span><strong>Wi-Fi</strong><small>Portfolio Network</small></span></button><button on:click={() => runMenuAction(() => openApp('Terminal'))}><span class="control-icon purple">⌘</span><span><strong>Focus</strong><small>Recruiter mode</small></span></button><label class="control-slider"><span>☀</span><input type="range" min="50" max="120" step="1" value={brightness} style={`--brightness:${brightness}`} on:input={changeBrightness} aria-label="Display brightness" /><output>{brightness}%</output></label><div class="battery-row"><span class="battery large"><i></i></span><strong>Battery</strong><small>78%</small></div></div>{/if}</div>
      <div class="menu-item right-menu">
        <button class:active={openMenu === 'clock'} class="clock-button" on:click={() => toggleMenu('clock')} aria-label="Open Notification Center" aria-expanded={openMenu === 'clock'}><time>{currentDate}&nbsp;&nbsp;{currentTime}</time></button>
        {#if openMenu === 'clock'}
          <aside class="notification-center" aria-label="Notification Center">
            <header class="notification-header">
              <div><strong>{currentLongDate}</strong><span>{currentTime}</span></div>
              <div class="calendar-tile"><span>{currentMonth}</span><b>{currentDay}</b></div>
            </header>

            <div class="notification-title"><strong>Notifications</strong>{#if visibleNotifications.length}<button on:click={() => dismissedNotifications = notifications.map((notification) => notification.id)}>Clear</button>{/if}</div>
            <section class="notification-list" aria-live="polite">
              {#if visibleNotifications.length}
                {#each visibleNotifications as notification}
                  <article class="notification-card">
                    <button class="dismiss-notification" on:click={() => dismissNotification(notification.id)} aria-label={`Dismiss ${notification.title}`}>×</button>
                    <button class="notification-content" on:click={() => openNotification(notification.id)}>
                      <span class="notification-icon" style={`--notification-accent:${notification.accent}`}>{notification.icon}</span>
                      <span><small>{notification.app}<i>{notification.time}</i></small><strong>{notification.title}</strong><p>{notification.body}</p></span>
                    </button>
                  </article>
                {/each}
              {:else}
                <div class="notifications-empty"><span>✓</span><strong>All caught up</strong><p>No new notifications.</p></div>
              {/if}
            </section>

            <div class="notification-widgets">
              <button on:click={() => runMenuAction(() => openFinder('experience'))}><span>50.6%</span><small>VISION F1</small></button>
              <a href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank"><span>PDF</span><small>RÉSUMÉ</small></a>
            </div>
          </aside>
        {/if}
      </div>
    </div>
  </header>

  {#if menuNotice}<div class="menu-toast">{menuNotice}</div>{/if}

  <main class:apps-open={openApps.size > 0} class="desktop-area">
    <div class="route-chip"><span>ROUTE 21</span><b>MEHMOOD.OS</b></div>
    <section class="desktop-icons" aria-label="Desktop files">
      <button class="desktop-icon" on:click={() => openFinder('highlights')}>
        <span class="icon-frame portrait-icon"><img src={pixelAvatar} alt="" /></span>
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
        <div class="pixel-notches"></div>
        <header class="trainer-header"><span>ENGINEER CARD</span><b>NO. 021</b></header>
        <div class="profile-top">
          <div class="sprite-well"><img src={pixelAvatar} alt="Pixel portrait of Mehmood Ahmad" /><i></i></div>
          <div><span class="pixel-kicker">PLAYER ONE</span><h1>Mehmood<br />Ahmad</h1><span class="level">LV. 04 · ENGINEER</span></div>
        </div>
        <p>Software engineer building applied AI, real-time systems, robotics, and thoughtful product experiences.</p>
        <div class="type-chips"><span>AI</span><span>SYSTEMS</span><span>PRODUCT</span></div>
        <div class="profile-links">
          <a href="mailto:mehmood3@ualberta.ca">Email</a>
          <a href="https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/" target="_blank" rel="noreferrer">LinkedIn ↗</a>
        </div>
      </article>

      <article class="widget signal-widget">
        <div class="pixel-notches"></div>
        <header><span class="pixel-kicker">SKILL STATS</span><i class="live-light"></i></header>
        <div class="signal-row"><strong>50.6%</strong><span>VISION F1<br /><small>YOLOv11 + VLM</small></span></div>
        <div class="meter-label"><span>CV</span><b>HP</b></div>
        <div class="pixel-meter"><i style="width: 76%"></i></div>
        <div class="signal-pair">
          <div><strong>~2.5ms</strong><span>SPD / latency</span></div>
          <div><strong>+70%</strong><span>DEV / deploys</span></div>
        </div>
      </article>

      <button class="widget publication-widget dialogue-box" on:click={() => openFinder('highlights')}>
        <span class="paper-pixel">!</span>
        <span><small>NEW RESEARCH UNLOCKED</small><strong>Object Detection + Small VLMs</strong><em>arXiv:2604.05210 · OPEN QUEST LOG</em></span>
        <b class="dialogue-arrow">▼</b>
      </button>
    </section>

    <div class="desktop-hint"><span>▶</span> Choose a file or open an app from the dock</div>

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
  .desktop { position: relative; width: 100%; min-height: 100svh; overflow: hidden; color: #f8fbff; background: #07111f; filter: brightness(var(--screen-brightness, 100%)); transition: filter 120ms ease; }
  .wallpaper { position: absolute; inset: 0; background-position: center; background-size: cover; transform: scale(1.01); }
  .night-shade { position: absolute; inset: 0; background: radial-gradient(circle at 48% 35%, transparent 0 25%, rgba(1,8,19,.1) 57%, rgba(1,7,17,.38)), linear-gradient(110deg, rgba(2,10,22,.16), transparent 54%, rgba(2,8,18,.22)); }
  .scanlines { position: absolute; inset: 0; opacity: .1; background: repeating-linear-gradient(180deg, rgba(255,255,255,.04) 0 1px, transparent 1px 4px); pointer-events: none; }

  .menu-bar { position: fixed; z-index: 2000; inset: 0 0 auto; display: flex; min-height: 2.35rem; align-items: center; justify-content: space-between; padding: .32rem .85rem; border-bottom: 1px solid rgba(255,255,255,.28); color: rgba(255,255,255,.86); background: linear-gradient(180deg, rgba(255,255,255,.21), rgba(255,255,255,.07)), rgba(4,14,28,.38); box-shadow: inset 0 1px 0 rgba(255,255,255,.38); backdrop-filter: blur(28px) saturate(155%); -webkit-backdrop-filter: blur(28px) saturate(155%); font-size: .75rem; }
  .menu-left, .menu-right { display: flex; align-items: center; gap: .08rem; }
  .menu-item { position: relative; }
  .menu-button, .status-button, .system-button, .clock-button { min-height: 1.72rem; padding: 0 .52rem; border: 0; border-radius: .34rem; color: rgba(255,255,255,.82); background: transparent; font-size: .75rem; cursor: pointer; }
  .menu-button:hover, .menu-button.active, .status-button:hover, .status-button.active, .system-button:hover, .system-button.active, .clock-button:hover, .clock-button.active { color: white; background: rgba(255,255,255,.18); }
  .app-button { color: white; font-weight: 680; }
  .pixel-apple { display: grid; width: 1.45rem; height: 1.45rem; padding: 0; place-items: center; border: 1px solid #d9f7ff; border-radius: 3px; color: #07121f; background: #9beaff; box-shadow: 2px 2px 0 rgba(0,0,0,.45); font-family: ui-monospace, monospace; font-size: .68rem; font-weight: 950; cursor: pointer; }
  .pixel-apple.active { outline: 2px solid rgba(255,255,255,.45); background: #c7f5ff; }
  .menu-right { gap: .05rem; }
  .system-button { display: flex; align-items: center; gap: .48rem; }
  .status-dot { display: flex; align-items: center; gap: .35rem; font-family: ui-monospace, monospace; font-size: .62rem; text-transform: uppercase; }
  .status-dot i, .live-light { width: .42rem; height: .42rem; border-radius: 1px; background: #69f1b9; box-shadow: 0 0 8px #69f1b9; }
  .wifi { transform: rotate(180deg); }
  .battery { display: block; width: 1.25rem; height: .62rem; padding: 2px; border: 1px solid currentColor; border-radius: 2px; }
  .battery i { display: block; width: 78%; height: 100%; background: #82efc4; }

  .menu-popover, .control-center, .notification-center { position: absolute; top: calc(100% + .42rem); left: 0; min-width: 15.5rem; padding: .38rem; border: 1px solid rgba(255,255,255,.24); border-radius: .72rem; color: #f6f6f7; background: linear-gradient(145deg, rgba(65,70,78,.88), rgba(28,32,39,.9)); box-shadow: 0 18px 45px rgba(0,0,0,.45), inset 0 1px 0 rgba(255,255,255,.15); backdrop-filter: blur(34px) saturate(150%); -webkit-backdrop-filter: blur(34px) saturate(150%); }
  .menu-popover::before, .control-center::before, .notification-center::before { position: absolute; inset: 0; border-radius: inherit; background: repeating-linear-gradient(180deg, rgba(255,255,255,.016) 0 1px, transparent 1px 4px); content: ''; pointer-events: none; }
  .menu-popover button, .menu-popover a { position: relative; z-index: 1; display: flex; width: 100%; min-height: 1.8rem; align-items: center; justify-content: space-between; gap: 1rem; padding: .28rem .58rem; border: 0; border-radius: .34rem; color: #f4f4f5; background: transparent; font-size: .76rem; text-decoration: none; cursor: pointer; }
  .menu-popover button:hover:not(:disabled), .menu-popover a:hover { color: white; background: #0a84ff; }
  .menu-popover button:disabled { color: rgba(255,255,255,.32); cursor: default; }
  .menu-popover kbd { color: rgba(255,255,255,.52); font-family: -apple-system, BlinkMacSystemFont, sans-serif; font-size: .68rem; }
  .menu-popover button:hover kbd, .menu-popover a:hover kbd { color: white; }
  .menu-separator { position: relative; z-index: 1; height: 1px; margin: .32rem .45rem; background: rgba(255,255,255,.13); }
  .align-right { right: 0; left: auto; }
  .right-menu > .menu-popover, .right-menu > .control-center, .right-menu > .notification-center { right: 0; left: auto; }
  .status-panel { width: 18rem; padding: .65rem; }
  .status-card { position: relative; z-index: 1; display: grid; grid-template-columns: auto 1fr; align-items: center; gap: .65rem; padding: .42rem; }
  .status-card > i { width: .72rem; height: .72rem; border-radius: 50%; background: #43d88f; box-shadow: 0 0 14px rgba(67,216,143,.78); }
  .status-card strong, .status-card span { display: block; }
  .status-card strong { font-size: .8rem; }
  .status-card span, .status-panel p { color: rgba(245,249,255,.62); font-size: .67rem; }
  .status-panel p { position: relative; z-index: 1; margin: .35rem .42rem .55rem; line-height: 1.45; }
  .control-center { display: grid; width: 18.5rem; grid-template-columns: 1fr 1fr; gap: .5rem; padding: .6rem; }
  .control-center > button { position: relative; z-index: 1; display: grid; grid-template-columns: auto 1fr; align-items: center; gap: .55rem; padding: .6rem; border: 0; border-radius: .72rem; color: white; background: rgba(255,255,255,.09); text-align: left; cursor: pointer; }
  .control-center > button:hover { background: rgba(255,255,255,.14); }
  .control-center button strong, .control-center button small { display: block; }
  .control-center button strong { font-size: .72rem; }
  .control-center button small { margin-top: .08rem; color: rgba(255,255,255,.55); font-size: .58rem; }
  .control-icon { display: grid; width: 2rem; height: 2rem; place-items: center; border-radius: 50%; background: #0a84ff; }
  .control-icon.purple { background: #7658d6; }
  .control-slider, .battery-row { position: relative; z-index: 1; grid-column: 1 / -1; display: flex; align-items: center; gap: .58rem; padding: .55rem .65rem; border-radius: .72rem; background: rgba(255,255,255,.09); }
  .control-slider input { min-width: 0; height: .42rem; flex: 1; appearance: none; border-radius: 1rem; outline: none; background: linear-gradient(90deg, white 0%, white calc((var(--brightness, 100) - 50) * 1.428%), rgba(255,255,255,.2) calc((var(--brightness, 100) - 50) * 1.428%)); cursor: pointer; accent-color: white; }
  .control-slider input::-webkit-slider-thumb { width: 1rem; height: 1rem; appearance: none; border: 1px solid rgba(0,0,0,.15); border-radius: 50%; background: white; box-shadow: 0 1px 4px rgba(0,0,0,.35); }
  .control-slider input::-moz-range-thumb { width: 1rem; height: 1rem; border: 1px solid rgba(0,0,0,.15); border-radius: 50%; background: white; box-shadow: 0 1px 4px rgba(0,0,0,.35); }
  .control-slider output { min-width: 2.35rem; color: rgba(255,255,255,.68); font-size: .62rem; text-align: right; }
  .battery-row strong { flex: 1; font-size: .7rem; }
  .battery-row small { color: rgba(255,255,255,.58); font-size: .65rem; }
  .battery.large { width: 1.55rem; height: .75rem; }
  .notification-center { position: fixed; top: 2.8rem; right: .65rem; left: auto; width: min(23rem, calc(100vw - 1rem)); max-height: calc(100svh - 3.5rem); overflow-y: auto; padding: .7rem; border-radius: 1rem; animation: notification-in 220ms cubic-bezier(.2,.8,.2,1); }
  .notification-header { position: relative; z-index: 1; display: flex; align-items: center; justify-content: space-between; padding: .35rem .25rem .8rem; }
  .notification-header > div:first-child strong, .notification-header > div:first-child span { display: block; }
  .notification-header > div:first-child strong { font-size: 1.22rem; letter-spacing: -.025em; }
  .notification-header > div:first-child span { margin-top: .14rem; color: rgba(255,255,255,.55); font-size: .75rem; }
  .calendar-tile { display: grid; width: 3.15rem; overflow: hidden; border-radius: .55rem; background: white; box-shadow: 0 5px 14px rgba(0,0,0,.28); text-align: center; }
  .calendar-tile span { padding: .15rem; color: white; background: #f14e5d; font-size: .55rem; font-weight: 800; }
  .calendar-tile b { padding: .15rem 0 .25rem; color: #1c1c1e; font-size: 1.3rem; line-height: 1; }
  .notification-title { position: relative; z-index: 1; display: flex; align-items: center; justify-content: space-between; padding: .35rem .25rem .45rem; }
  .notification-title strong { font-size: .78rem; }
  .notification-title button { padding: .2rem .42rem; border: 0; border-radius: .35rem; color: #80bfff; background: rgba(255,255,255,.08); font-size: .66rem; cursor: pointer; }
  .notification-list { position: relative; z-index: 1; display: grid; gap: .48rem; }
  .notification-card { position: relative; overflow: hidden; border: 1px solid rgba(255,255,255,.18); border-radius: .88rem; background: linear-gradient(145deg, rgba(255,255,255,.2), rgba(255,255,255,.08)); box-shadow: 0 8px 20px rgba(0,0,0,.22), inset 0 1px 0 rgba(255,255,255,.14); }
  .notification-content { display: grid; width: 100%; grid-template-columns: auto 1fr; align-items: start; gap: .65rem; padding: .72rem 2rem .72rem .72rem; border: 0; color: white; background: transparent; text-align: left; cursor: pointer; }
  .notification-content:hover { background: rgba(255,255,255,.04); }
  .notification-icon { display: grid; width: 2.15rem; height: 2.15rem; place-items: center; border-radius: .52rem; color: white; background: var(--notification-accent); box-shadow: inset 0 1px 0 rgba(255,255,255,.3), 0 3px 8px rgba(0,0,0,.25); font-family: ui-monospace, monospace; font-weight: 900; }
  .notification-content > span:last-child { min-width: 0; }
  .notification-content small { display: flex; justify-content: space-between; color: rgba(255,255,255,.6); font-size: .61rem; text-transform: uppercase; }
  .notification-content small i { font-style: normal; text-transform: none; }
  .notification-content strong { display: block; margin-top: .22rem; font-size: .76rem; }
  .notification-content p { margin: .18rem 0 0; color: rgba(255,255,255,.7); font-size: .68rem; line-height: 1.38; }
  .dismiss-notification { position: absolute; z-index: 2; top: .45rem; right: .45rem; display: grid; width: 1.2rem; height: 1.2rem; padding: 0; place-items: center; border: 0; border-radius: 50%; color: rgba(255,255,255,.65); background: rgba(0,0,0,.22); cursor: pointer; }
  .dismiss-notification:hover { color: white; background: rgba(0,0,0,.38); }
  .notifications-empty { padding: 1.6rem; border: 1px solid rgba(255,255,255,.12); border-radius: .85rem; color: rgba(255,255,255,.7); background: rgba(255,255,255,.06); text-align: center; }
  .notifications-empty span, .notifications-empty strong, .notifications-empty p { display: block; }
  .notifications-empty span { color: #67e0a3; font-size: 1.25rem; }
  .notifications-empty strong { margin-top: .4rem; font-size: .78rem; }
  .notifications-empty p { margin: .18rem 0 0; font-size: .67rem; }
  .notification-widgets { position: relative; z-index: 1; display: grid; grid-template-columns: 1fr 1fr; gap: .48rem; margin-top: .6rem; }
  .notification-widgets button, .notification-widgets a { display: flex; min-height: 4.1rem; flex-direction: column; align-items: flex-start; justify-content: flex-end; padding: .65rem; border: 1px solid rgba(255,255,255,.17); border-radius: .82rem; color: white; background: linear-gradient(145deg, rgba(42,151,216,.44), rgba(38,72,123,.32)); text-decoration: none; cursor: pointer; }
  .notification-widgets a { background: linear-gradient(145deg, rgba(212,75,92,.48), rgba(112,42,57,.36)); }
  .notification-widgets span { font-size: 1.15rem; font-weight: 780; }
  .notification-widgets small { margin-top: .15rem; color: rgba(255,255,255,.64); font-size: .58rem; letter-spacing: .08em; }
  @keyframes notification-in { from { opacity: 0; transform: translateX(1.1rem) scale(.98); } to { opacity: 1; transform: translateX(0) scale(1); } }
  .menu-toast { position: fixed; z-index: 2200; top: 3rem; left: 50%; transform: translateX(-50%); padding: .48rem .72rem; border: 1px solid rgba(255,255,255,.25); border-radius: .55rem; color: white; background: rgba(20,28,38,.82); box-shadow: 0 10px 25px rgba(0,0,0,.28); backdrop-filter: blur(20px); font-size: .7rem; }

  .desktop-area { position: relative; z-index: 2; display: grid; min-height: 100svh; grid-template-columns: minmax(22rem, 1fr) minmax(18rem, 23rem); padding: 5.35rem 2.3rem 6.5rem; transition: filter 180ms ease; }
  .desktop-area.apps-open > .desktop-icons, .desktop-area.apps-open > .widgets, .desktop-area.apps-open > .desktop-hint { filter: saturate(.8) brightness(.72); }

  .route-chip { position: absolute; top: 3.2rem; left: 2.3rem; display: flex; align-items: center; gap: .55rem; padding: .3rem .48rem; border: 2px solid rgba(5,19,32,.88); outline: 2px solid rgba(210,246,255,.68); color: #061524; background: #9ceaff; box-shadow: 3px 3px 0 rgba(1,8,18,.55); font-family: ui-monospace, monospace; font-size: .6rem; letter-spacing: .08em; }
  .route-chip span { padding: .15rem .3rem; color: #e9fbff; background: #183353; }
  .route-chip b { font-size: .62rem; }

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
  .widget { position: relative; overflow: hidden; border: 2px solid rgba(223,249,255,.72); border-radius: .3rem; color: white; background: linear-gradient(145deg, rgba(255,255,255,.2), rgba(255,255,255,.065) 48%, rgba(91,154,232,.08)), rgba(4,14,28,.58); box-shadow: 0 0 0 2px rgba(8,24,42,.8), 5px 5px 0 rgba(1,8,18,.52), 0 20px 40px rgba(0,5,15,.22), inset 0 1px 0 rgba(255,255,255,.42); backdrop-filter: blur(25px) saturate(145%); -webkit-backdrop-filter: blur(25px) saturate(145%); clip-path: polygon(6px 0, calc(100% - 6px) 0, calc(100% - 6px) 2px, 100% 2px, 100% calc(100% - 6px), calc(100% - 2px) calc(100% - 6px), calc(100% - 2px) 100%, 6px 100%, 6px calc(100% - 2px), 0 calc(100% - 2px), 0 6px, 2px 6px, 2px 2px, 6px 2px); }
  .widget-glint { position: absolute; inset: 0; background: radial-gradient(circle at 10% 0%, rgba(255,255,255,.23), transparent 34%); pointer-events: none; }
  .pixel-notches { position: absolute; inset: 5px; border: 1px dashed rgba(151,229,255,.18); pointer-events: none; }
  .profile-widget { padding: .72rem 1rem 1rem; }
  .trainer-header { position: relative; display: flex; align-items: center; justify-content: space-between; margin: -.72rem -1rem .8rem; padding: .42rem .65rem; color: #071523; background: linear-gradient(90deg, #9feeff 0 65%, #ffdf7a 65%); box-shadow: inset 0 -2px 0 rgba(7,22,36,.35); font-family: ui-monospace, monospace; font-size: .6rem; font-weight: 900; letter-spacing: .08em; }
  .trainer-header b { color: #192039; }
  .profile-top { position: relative; display: grid; grid-template-columns: 5.25rem 1fr; align-items: center; gap: .8rem; }
  .sprite-well { position: relative; display: grid; width: 5.25rem; height: 5.25rem; place-items: center; border: 2px solid #c9f3ff; background: linear-gradient(135deg, rgba(115,221,255,.3), rgba(80,104,177,.22)), repeating-linear-gradient(0deg, rgba(255,255,255,.08) 0 3px, transparent 3px 6px); box-shadow: inset 0 0 0 2px rgba(6,19,34,.66), 3px 3px 0 rgba(1,8,18,.55); clip-path: polygon(6px 0, 100% 0, 100% calc(100% - 6px), calc(100% - 6px) calc(100% - 6px), calc(100% - 6px) 100%, 0 100%, 0 6px, 6px 6px); }
  .profile-top img { position: relative; z-index: 2; width: 4.65rem; height: 4.65rem; object-fit: cover; image-rendering: pixelated; }
  .sprite-well i { position: absolute; right: .25rem; bottom: .25rem; width: .7rem; height: .7rem; border: 2px solid rgba(6,21,35,.65); background: #ffdc6a; box-shadow: -2px -2px 0 rgba(255,255,255,.4); transform: rotate(45deg); }
  .pixel-kicker { color: #89e6ff; font-family: ui-monospace, monospace; font-size: .62rem; font-weight: 850; letter-spacing: .11em; }
  .profile-top h1 { margin: .15rem 0 0; font-size: 1.65rem; letter-spacing: -.045em; line-height: .88; }
  .level { display: inline-block; margin-top: .45rem; padding: .16rem .28rem; color: #092039; background: #a6f0ff; font-family: ui-monospace, monospace; font-size: .55rem; font-weight: 900; }
  .profile-widget > p { position: relative; margin: .8rem 0 .65rem; color: rgba(240,248,255,.78); font-size: .8rem; line-height: 1.5; }
  .type-chips { position: relative; display: flex; gap: .35rem; margin-bottom: .75rem; }
  .type-chips span { padding: .2rem .38rem; border: 1px solid rgba(255,255,255,.3); color: #092036; background: #9deaff; box-shadow: 2px 2px 0 rgba(1,8,18,.42); font-family: ui-monospace, monospace; font-size: .54rem; font-weight: 950; }
  .type-chips span:nth-child(2) { background: #bda8ff; }
  .type-chips span:nth-child(3) { background: #ffdb79; }
  .profile-links { position: relative; display: flex; gap: .45rem; }
  .profile-links a { padding: .42rem .6rem; border: 1px solid rgba(255,255,255,.17); border-radius: .35rem; color: white; background: rgba(255,255,255,.075); font-family: ui-monospace, monospace; font-size: .65rem; font-weight: 700; text-decoration: none; }
  .profile-links a:hover { background: rgba(128,225,255,.18); }

  .signal-widget { padding: .9rem 1rem 1rem; }
  .signal-widget header { display: flex; align-items: center; justify-content: space-between; }
  .signal-row { display: flex; align-items: center; gap: .75rem; margin-top: .75rem; }
  .signal-row > strong { font-size: 2.15rem; letter-spacing: -.06em; }
  .signal-row > span { color: rgba(244,249,255,.7); font-size: .72rem; font-weight: 720; line-height: 1.2; }
  .signal-row small { color: rgba(235,245,255,.42); font-size: .6rem; font-weight: 500; }
  .meter-label { display: flex; justify-content: space-between; margin-top: .55rem; color: rgba(230,248,255,.62); font-family: ui-monospace, monospace; font-size: .55rem; font-weight: 900; }
  .meter-label b { color: #ffde73; }
  .pixel-meter { height: .7rem; margin: .18rem 0 .85rem; padding: 2px; border: 2px solid rgba(6,20,34,.92); outline: 1px solid rgba(187,238,255,.58); background: rgba(1,9,20,.5); }
  .pixel-meter i { display: block; height: 100%; background: repeating-linear-gradient(90deg, #6eedb5 0 7px, #8bf6ca 7px 9px, transparent 9px 11px); box-shadow: 0 0 9px rgba(91,239,179,.35); }
  .signal-pair { display: grid; grid-template-columns: 1fr 1fr; border-top: 1px solid rgba(255,255,255,.1); }
  .signal-pair div { padding-top: .7rem; }
  .signal-pair div + div { padding-left: .9rem; border-left: 1px solid rgba(255,255,255,.1); }
  .signal-pair strong, .signal-pair span { display: block; }
  .signal-pair strong { font-size: 1rem; }
  .signal-pair span { margin-top: .08rem; color: rgba(240,247,255,.48); font-size: .62rem; }

  .publication-widget { display: grid; grid-template-columns: auto 1fr auto; align-items: center; gap: .75rem; width: 100%; padding: .75rem; text-align: left; cursor: pointer; }
  .dialogue-box { border-color: #eefcff; background: linear-gradient(145deg, rgba(251,250,231,.94), rgba(201,236,241,.9)); color: #0a1b2a; box-shadow: 0 0 0 2px #162b43, 5px 5px 0 rgba(1,8,18,.58), inset 0 0 0 3px rgba(104,164,185,.32); backdrop-filter: blur(18px); }
  .paper-pixel { display: grid; width: 2.7rem; height: 2.7rem; place-items: center; border: 2px solid #17314a; border-radius: 50%; color: #11233b; background: #ffdc6b; box-shadow: 3px 3px 0 rgba(8,28,46,.42), inset -3px -3px 0 rgba(195,135,35,.24); font-family: ui-monospace, monospace; font-size: 1.25rem; font-weight: 950; }
  .publication-widget > span:nth-child(2) { min-width: 0; }
  .publication-widget small, .publication-widget strong, .publication-widget em { display: block; }
  .publication-widget small { color: #315273; font-family: ui-monospace, monospace; font-size: .58rem; font-weight: 900; }
  .publication-widget strong { margin: .16rem 0; overflow: hidden; font-size: .75rem; text-overflow: ellipsis; white-space: nowrap; }
  .publication-widget em { color: #557084; font-family: ui-monospace, monospace; font-size: .58rem; font-style: normal; }
  .publication-widget > b { color: #17314a; font-size: .75rem; }
  .dialogue-arrow { animation: dialogue-bounce 700ms steps(2) infinite; }

  .desktop-hint { position: fixed; left: 50%; bottom: 6.3rem; display: flex; transform: translateX(-50%); align-items: center; gap: .45rem; padding: .38rem .62rem; border: 1px solid rgba(255,255,255,.16); border-radius: .4rem; color: rgba(255,255,255,.58); background: rgba(2,10,22,.35); font-family: ui-monospace, monospace; font-size: .62rem; backdrop-filter: blur(12px); }
  .desktop-hint span { color: #87e8ff; }

  @keyframes dialogue-bounce { 50% { transform: translateY(3px); } }

  .finder-window { width: min(940px, calc(100vw - 1rem)); height: min(640px, calc(100svh - 7.9rem)); }
  .terminal-window { width: min(760px, calc(100vw - 1rem)); height: min(510px, calc(100svh - 7.9rem)); }
  .notes-window { width: min(860px, calc(100vw - 1rem)); height: min(540px, calc(100svh - 7.9rem)); }
  .game-window { width: min(620px, calc(100vw - 1rem)); height: min(500px, calc(100svh - 7.9rem)); }

  @media (max-width: 800px) {
    .menu-left > .menu-item:not(:first-child):not(.app-menu), .status-button { display: none; }
    .menu-popover { position: fixed; top: 2.65rem; right: .5rem; left: .5rem; width: auto; min-width: 0; }
    .app-menu .menu-popover { right: auto; left: .5rem; width: min(18rem, calc(100vw - 1rem)); }
    .control-center, .notification-center { position: fixed; top: 2.65rem; right: .5rem; left: auto; max-width: calc(100vw - 1rem); }
    .desktop-area { display: block; height: 100svh; overflow-y: auto; padding: 4.3rem .75rem 6.8rem; }
    .route-chip { top: 2.9rem; left: .85rem; }
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
    .system-button .wifi { display: none; }
    .profile-widget { display: block; }
    .profile-links { justify-content: flex-start; }
  }
</style>
