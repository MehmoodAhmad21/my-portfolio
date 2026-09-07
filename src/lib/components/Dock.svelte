<script module lang="ts">
  export type DockApp = {
    name: string;
    icon: string;
  };
</script>

<script lang="ts">
  export let apps: DockApp[] = [];
  export let openApps: Set<string> = new Set();
  export let activeApp: string | null = null;
  export let platform: 'macos' | 'ipados' | 'ios' = 'macos';
  export let onSelect: (app: DockApp) => void;
</script>

<nav class:ios={platform === 'ios'} class:ipados={platform === 'ipados'} class:app-open={Boolean(activeApp)} class="dock-shell" aria-label="Portfolio apps">
  <div class="dock-highlight"></div>
  {#each apps as app}
    {@const appLabel = app.name === 'Finder' && platform !== 'macos' ? 'Files' : app.name}
    <div class="dock-item">
      <button
        class:active={activeApp === app.name}
        class="dock-button"
        on:click={() => onSelect(app)}
        aria-label={`Open ${appLabel}`}
        aria-pressed={activeApp === app.name}
      >
        <span class="dock-tooltip">{appLabel}</span>
        <span class="icon-wrap">
          <img src={app.icon} alt="" draggable="false" />
        </span>
      </button>
      <span class:running={openApps.has(app.name)} class="running-dot"></span>
    </div>
  {/each}
</nav>

<style>
  .dock-shell {
    position: fixed;
    z-index: 1000;
    left: 50%;
    bottom: max(.55rem, env(safe-area-inset-bottom));
    display: flex;
    transform: translateX(-50%);
    align-items: flex-end;
    gap: .16rem;
    padding: .3rem .38rem .34rem;
    border: 1px solid rgba(255,255,255,.32);
    border-radius: .9rem;
    background: linear-gradient(180deg, rgba(255,255,255,.2), rgba(255,255,255,.06)), rgba(30,35,45,.5);
    box-shadow: 0 15px 35px rgba(0,0,0,.38), 0 2px 6px rgba(0,0,0,.28), inset 0 1px 0 rgba(255,255,255,.38), inset 0 -1px 0 rgba(0,0,0,.26);
    backdrop-filter: blur(30px) saturate(165%);
    -webkit-backdrop-filter: blur(30px) saturate(165%);
  }

  .dock-highlight {
    position: absolute;
    inset: 1px .7rem auto;
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,.48) 15%, rgba(255,255,255,.28) 85%, transparent);
    pointer-events: none;
  }

  .dock-item { position: relative; display: flex; flex-direction: column; align-items: center; }

  .dock-button {
    position: relative;
    display: grid;
    width: 3.35rem;
    height: 3.35rem;
    place-items: center;
    border: 0;
    border-radius: .62rem;
    background: transparent;
    cursor: pointer;
    transform-origin: 50% 100%;
    transition: transform 190ms cubic-bezier(.2,.82,.2,1), filter 170ms ease;
  }

  .dock-button:hover,
  .dock-button:focus-visible { z-index: 2; transform: translateY(-11px) scale(1.24); }
  .dock-item:has(.dock-button:hover) + .dock-item .dock-button,
  .dock-item:has(+ .dock-item .dock-button:hover) .dock-button { transform: translateY(-4px) scale(1.08); }
  .dock-button:active { transform: translateY(-4px) scale(.94); }
  .dock-button.active { filter: drop-shadow(0 7px 11px rgba(69,151,255,.3)); }
  .dock-button:focus-visible { outline: 2px solid #fff; outline-offset: 3px; }

  .icon-wrap {
    display: grid;
    width: 3rem;
    height: 3rem;
    place-items: center;
    overflow: hidden;
    border-radius: .68rem;
    filter: drop-shadow(0 4px 5px rgba(0,0,0,.32));
  }

  .icon-wrap img { width: 100%; height: 100%; object-fit: cover; image-rendering: pixelated; user-select: none; }

  .dock-tooltip {
    position: absolute;
    bottom: calc(100% + .82rem);
    left: 50%;
    padding: .35rem .55rem;
    transform: translate(-50%, 5px);
    border: 1px solid rgba(255,255,255,.2);
    border-radius: .55rem;
    background: rgba(28,33,42,.76);
    color: white;
    font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", sans-serif;
    font-size: .72rem;
    font-weight: 520;
    opacity: 0;
    pointer-events: none;
    white-space: nowrap;
    box-shadow: 0 5px 16px rgba(0,0,0,.34), inset 0 1px 0 rgba(255,255,255,.15);
    backdrop-filter: blur(22px) saturate(145%);
    transition: opacity 140ms ease, transform 140ms ease;
  }

  .dock-button:hover .dock-tooltip,
  .dock-button:focus-visible .dock-tooltip { opacity: 1; transform: translate(-50%, 0); }

  .running-dot { display: block; width: 4px; height: 4px; margin: .05rem auto 0; border-radius: 50%; background: transparent; }
  .running-dot.running { background: rgba(255,255,255,.78); box-shadow: 0 1px 2px rgba(0,0,0,.55); }

  @media (max-width: 640px) {
    .dock-shell { gap: .25rem; padding: .42rem .5rem .35rem; border-radius: .4rem; }
    .dock-button { width: 3.1rem; height: 3.1rem; }
    .icon-wrap { width: 2.85rem; height: 2.85rem; border-radius: .78rem; }
    .dock-tooltip { display: none; }
  }

  .dock-shell.ios, .dock-shell.ipados {
    bottom: max(.55rem, env(safe-area-inset-bottom));
    gap: .4rem;
    padding: .5rem .65rem;
    border: 1px solid rgba(255,255,255,.34);
    border-radius: 1.65rem;
    background: linear-gradient(145deg, rgba(255,255,255,.3), rgba(255,255,255,.1)), rgba(19,28,43,.33);
    box-shadow: inset 0 1px 0 rgba(255,255,255,.5), 0 12px 32px rgba(0,0,0,.28);
    clip-path: none;
    backdrop-filter: blur(35px) saturate(175%);
  }
  .dock-shell.ios .dock-highlight, .dock-shell.ipados .dock-highlight { display: none; }
  .dock-shell.ios .dock-button, .dock-shell.ipados .dock-button { width: 3.35rem; height: 3.35rem; }
  .dock-shell.ios .icon-wrap, .dock-shell.ipados .icon-wrap { width: 3.1rem; height: 3.1rem; border-radius: .9rem; filter: drop-shadow(0 5px 8px rgba(0,0,0,.32)); }
  .dock-shell.ios .running-dot, .dock-shell.ipados .running-dot { display: none; }
  .dock-shell.ios.app-open { opacity: 0; transform: translate(-50%, 150%); pointer-events: none; }
</style>
