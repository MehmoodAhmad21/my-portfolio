<script lang="ts">
  import { onMount } from 'svelte';

  let commandHistory: string[] = [];
  let currentInput = '';
  let inputElement: HTMLInputElement;

  const responses: Record<string, string[]> = {
    about: [
      'Mehmood Ahmad — software engineer in Edmonton, Alberta.',
      'BSc Computer Science + Biological Sciences minor, University of Alberta (Jan 2027).',
      'I build at the edge of applied AI, real-time systems, mobile products, and developer infrastructure.'
    ],
    skills: [
      'AI / Vision    YOLOv11 · VLMs · PyTorch · transformers · PEFT',
      'Systems        Python · C/C++ · C# · Java · FFmpeg · ROS',
      'Product        TypeScript · React Native · SvelteKit · FastAPI',
      'Infrastructure Docker · Jenkins · GitLab CI · AWS · DigitalOcean'
    ],
    experience: [
      '2024—2025  University of Alberta  / Software Engineer Intern',
      '           YOLOv11 + VLM safety monitoring · FFmpeg · ROS · Unity VR',
      '2023       Aro Robotic Systems   / Software Engineer Intern',
      '           70% faster deployments · 15% lower cloud cost',
      '2022       Aro Robotic Systems   / Software Engineer Intern',
      '           Responsive web platform and reusable UI components'
    ],
    projects: [
      'Offspring.exe   seeded Monte Carlo genetics simulation + procedural SVG',
      'Trackme         HealthKit-connected health tracker + AI insight engine',
      'SCAT6           seven-step neurological assessment desktop workflow',
      'SafeHaven       IoT sensing + automated emergency escalation'
    ],
    publication: [
      'Integration of Object Detection and Small VLMs for Construction Safety Hazard Identification',
      'M. Adil, M. Ahmad, et al. · arXiv:2604.05210 · 2026'
    ],
    contact: [
      'email     mehmood3@ualberta.ca',
      'github    github.com/MehmoodAhmad21',
      'linkedin  linkedin.com/in/mehmood-ahmad-2bb43b244/'
    ]
  };

  const help = [
    'Available: about · skills · experience · projects · publication · contact · clear'
  ];

  onMount(() => {
    commandHistory = [
      'MoodyOS 2.0  ·  recruiter mode enabled',
      'Type “help” to explore, or try “projects”.',
      ''
    ];
    inputElement?.focus();
  });

  function handleCommand(event: KeyboardEvent) {
    if (event.key !== 'Enter') return;
    const command = currentInput.trim().toLowerCase();
    if (command === 'clear') {
      commandHistory = [];
    } else {
      commandHistory = [...commandHistory, `mehmood@portfolio ~ % ${currentInput}`];
      if (command === '') {
        commandHistory = [...commandHistory, ''];
      } else if (command === 'help') {
        commandHistory = [...commandHistory, ...help, ''];
      } else if (Object.hasOwn(responses, command)) {
        commandHistory = [...commandHistory, ...responses[command], ''];
      } else {
        commandHistory = [...commandHistory, `command not found: ${command}`, ...help, ''];
      }
    }
    currentInput = '';
    setTimeout(() => document.querySelector('.terminal-content')?.scrollTo({ top: 99999, behavior: 'smooth' }));
  }
</script>

<div class="terminal" on:click={() => inputElement?.focus()} role="presentation">
  <div class="terminal-toolbar"><div class="tab"><span>⌘</span><strong>mehmood — portfolio shell</strong></div><span class="toolbar-space"></span><button aria-label="Clear terminal" on:click={() => commandHistory = []}>⌫</button><button aria-label="Show terminal commands" on:click={() => commandHistory = [...commandHistory, ...help]}>?</button></div>
  <div class="terminal-content">
    {#each commandHistory as line}
      <div class:prompt={line.startsWith('mehmood@')} class="line">{line}</div>
    {/each}
    <label class="input-row">
      <span><b>mehmood</b>@portfolio ~ %</span>
      <input bind:this={inputElement} bind:value={currentInput} on:keydown={handleCommand} aria-label="Terminal command" spellcheck="false" autocomplete="off" />
    </label>
  </div>
</div>

<style>
  .terminal { display: flex; height: 100%; flex-direction: column; overflow: hidden; color: #e8e8e8; background: rgba(18,18,19,.97); font-family: ui-monospace, "SFMono-Regular", Menlo, Monaco, Consolas, monospace; }
  .terminal-toolbar { display: flex; min-height: 2.45rem; align-items: center; gap: .35rem; padding: .25rem .55rem 0; border-bottom: 1px solid rgba(255,255,255,.08); color: #a7a7ab; background: #2b2b2d; font-size: .67rem; }
  .terminal-toolbar button { display: grid; width: 1.7rem; height: 1.7rem; padding: 0; place-items: center; border: 0; border-radius: .38rem; color: #aaaab0; background: transparent; cursor: pointer; }
  .terminal-toolbar button:hover { background: rgba(255,255,255,.08); }
  .tab { display: grid; min-width: 12rem; height: 2.15rem; grid-template-columns: auto 1fr auto; align-items: center; gap: .5rem; padding: 0 .45rem .1rem .65rem; border: 1px solid rgba(255,255,255,.08); border-bottom-color: #1a1a1b; border-radius: .48rem .48rem 0 0; color: #e6e6e8; background: #1a1a1b; }
  .tab strong { font-weight: 530; text-align: center; }
  .tab > span { color: #78d6ff; }
  .toolbar-space { flex: 1; }
  .terminal-content { flex: 1; overflow-y: auto; padding: 1.15rem 1.25rem; font-size: clamp(.72rem, 1.4vw, .83rem); line-height: 1.68; }
  .line { min-height: 1.35em; white-space: pre-wrap; }
  .line.prompt { margin-top: .35rem; color: #70c9ff; }
  .input-row { display: flex; align-items: center; gap: .55rem; color: #d4d4d6; }
  .input-row b { color: #7ee2a8; }
  input { min-width: 3rem; flex: 1; padding: 0; border: 0; outline: 0; color: #f5f5f5; background: transparent; box-shadow: none; caret-color: #f5f5f5; font: inherit; }
</style>
