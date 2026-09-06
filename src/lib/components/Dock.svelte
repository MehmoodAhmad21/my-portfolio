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
  export let onSelect: (app: DockApp) => void;
</script>

<nav class="dock-shell" aria-label="Portfolio apps">
  <div class="dock-highlight"></div>
  {#each apps as app}
    <div class="dock-item">
      <button
        class:active={activeApp === app.name}
        class="dock-button"
        on:click={() => onSelect(app)}
        aria-label={`Open ${app.name}`}
        aria-pressed={activeApp === app.name}
      >
        <span class="dock-tooltip">{app.name}</span>
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
    bottom: max(0.9rem, env(safe-area-inset-bottom));
    display: flex;
    transform: translateX(-50%);
    align-items: flex-end;
    gap: 0.55rem;
    padding: 0.55rem 0.7rem 0.45rem;
    border: 2px solid rgba(223, 249, 255, 0.76);
    border-radius: .45rem;
    background: linear-gradient(155deg, rgba(255,255,255,.31), rgba(255,255,255,.08) 45%, rgba(111,169,255,.12)), rgba(8, 16, 32, 0.38);
    box-shadow: 0 0 0 2px rgba(6,20,36,.76), 5px 5px 0 rgba(1,7,18,.5), 0 24px 60px rgba(1, 7, 18, 0.4), inset 0 1px 0 rgba(255,255,255,.55);
    backdrop-filter: blur(30px) saturate(155%);
    -webkit-backdrop-filter: blur(30px) saturate(155%);
    clip-path: polygon(7px 0, calc(100% - 7px) 0, calc(100% - 7px) 2px, 100% 2px, 100% calc(100% - 7px), calc(100% - 2px) calc(100% - 7px), calc(100% - 2px) 100%, 7px 100%, 7px calc(100% - 2px), 0 calc(100% - 2px), 0 7px, 2px 7px, 2px 2px, 7px 2px);
  }

  .dock-highlight {
    position: absolute;
    inset: 3px 12% auto;
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,.85), transparent);
    pointer-events: none;
  }

  .dock-item { position: relative; }

  .dock-button {
    position: relative;
    display: grid;
    width: 3.65rem;
    height: 3.65rem;
    place-items: center;
    border: 0;
    border-radius: .35rem;
    background: transparent;
    cursor: pointer;
    transition: transform 170ms cubic-bezier(.2,.8,.2,1), filter 170ms ease;
  }

  .dock-button:hover,
  .dock-button:focus-visible { transform: translateY(-7px) scale(1.09); }
  .dock-button:active { transform: translateY(-2px) scale(.96); }
  .dock-button.active { filter: drop-shadow(0 8px 14px rgba(86, 168, 255, .42)); }
  .dock-button:focus-visible { outline: 2px solid #fff; outline-offset: 3px; }

  .icon-wrap {
    display: grid;
    width: 3.35rem;
    height: 3.35rem;
    place-items: center;
    overflow: hidden;
    border-radius: .5rem;
    filter: drop-shadow(3px 4px 0 rgba(0,0,0,.42));
  }

  .icon-wrap img { width: 100%; height: 100%; object-fit: cover; image-rendering: pixelated; user-select: none; }

  .dock-tooltip {
    position: absolute;
    bottom: calc(100% + .72rem);
    left: 50%;
    padding: .35rem .55rem;
    transform: translate(-50%, 5px);
    border: 2px solid rgba(228,250,255,.82);
    border-radius: .2rem;
    background: rgba(8,20,36,.92);
    color: white;
    font-family: ui-monospace, monospace;
    font-size: .66rem;
    font-weight: 650;
    opacity: 0;
    pointer-events: none;
    white-space: nowrap;
    box-shadow: 0 0 0 2px rgba(6,17,30,.76), 3px 3px 0 rgba(0,0,0,.42);
    backdrop-filter: blur(12px);
    transition: opacity 140ms ease, transform 140ms ease;
  }

  .dock-button:hover .dock-tooltip,
  .dock-button:focus-visible .dock-tooltip { opacity: 1; transform: translate(-50%, 0); }

  .running-dot { display: block; width: 5px; height: 5px; margin: .2rem auto 0; background: transparent; }
  .running-dot.running { background: #84eaff; box-shadow: 2px 2px 0 rgba(3,16,29,.8), 0 0 7px #84eaff; }

  @media (max-width: 640px) {
    .dock-shell { gap: .25rem; padding: .42rem .5rem .35rem; border-radius: .4rem; }
    .dock-button { width: 3.1rem; height: 3.1rem; }
    .icon-wrap { width: 2.85rem; height: 2.85rem; border-radius: .78rem; }
    .dock-tooltip { display: none; }
  }
</style>
