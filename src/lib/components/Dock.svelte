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
    border: 1px solid rgba(255, 255, 255, 0.38);
    border-radius: 1.55rem;
    background: linear-gradient(155deg, rgba(255,255,255,.31), rgba(255,255,255,.08) 45%, rgba(111,169,255,.12)), rgba(8, 16, 32, 0.38);
    box-shadow: 0 24px 60px rgba(1, 7, 18, 0.45), inset 0 1px 0 rgba(255,255,255,.55), inset 0 -1px 0 rgba(255,255,255,.1);
    backdrop-filter: blur(30px) saturate(155%);
    -webkit-backdrop-filter: blur(30px) saturate(155%);
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
    border-radius: 1rem;
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
    border-radius: .95rem;
    filter: drop-shadow(0 7px 8px rgba(0,0,0,.35));
  }

  .icon-wrap img { width: 100%; height: 100%; object-fit: cover; image-rendering: auto; user-select: none; }

  .dock-tooltip {
    position: absolute;
    bottom: calc(100% + .72rem);
    left: 50%;
    padding: .35rem .55rem;
    transform: translate(-50%, 5px);
    border: 1px solid rgba(255,255,255,.24);
    border-radius: .55rem;
    background: rgba(8,14,28,.8);
    color: white;
    font-size: .72rem;
    font-weight: 650;
    opacity: 0;
    pointer-events: none;
    white-space: nowrap;
    backdrop-filter: blur(12px);
    transition: opacity 140ms ease, transform 140ms ease;
  }

  .dock-button:hover .dock-tooltip,
  .dock-button:focus-visible .dock-tooltip { opacity: 1; transform: translate(-50%, 0); }

  .running-dot { display: block; width: 4px; height: 4px; margin: .2rem auto 0; border-radius: 99px; background: transparent; }
  .running-dot.running { background: rgba(255,255,255,.92); box-shadow: 0 0 7px white; }

  @media (max-width: 640px) {
    .dock-shell { gap: .25rem; padding: .42rem .5rem .35rem; border-radius: 1.25rem; }
    .dock-button { width: 3.1rem; height: 3.1rem; }
    .icon-wrap { width: 2.85rem; height: 2.85rem; border-radius: .78rem; }
    .dock-tooltip { display: none; }
  }
</style>
