<script lang="ts">
    // step 1: Lets create a TypeScript type for each app icon
    export type DockApp = {
        name: string;
        icon: string // Url or Emoji
    };

    // step 2: let us receive a list of apps from the parent
    export let apps: DockApp[] = [];
    export let openApps: Set<string> = new Set();
    export let activeApp: string | null = null;

    // functions we call when an app is clicked 
    export let onSelect: (app: DockApp) => void;
</script>

<!-- Dock Container-->
<div class="fixed bottom-4 left-1/2 transform -translate-x-1/2 bg-white/10 backdrop-blur-md rounded-2xl px-4 py-3 flex gap-3 shadow-2xl border border-white/20 z-50">
  {#each apps as app}
    <div class="relative">
      <button
        class="flex flex-col items-center cursor-pointer hover:scale-110 transition-all duration-200 {activeApp === app.name ? 'scale-105' : ''}"
        on:click={() => onSelect(app)}
        title={app.name}
      >
        <div class="mb-1 {activeApp === app.name ? 'drop-shadow-lg' : ''}">
          {#if app.icon.startsWith('http') || app.icon.startsWith('/') || app.icon.includes('.')}
            <img src={app.icon} alt={app.name} class="w-12 h-12 object-contain" />
          {:else}
            <span class="text-4xl">{app.icon}</span>
          {/if}
        </div>
        <span class="text-xs">{app.name}</span>
      </button>
      {#if openApps.has(app.name)}
        <div class="absolute -bottom-2 left-1/2 transform -translate-x-1/2 w-1 h-1 bg-white rounded-full"></div>
      {/if}
    </div>
  {/each}
</div>
