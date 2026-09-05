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
      } else if (responses[command]) {
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
  <div class="terminal-tabs"><span class="active"><b>◇</b> mehmood — zsh</span><span>+</span></div>
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
  .terminal { display: flex; height: 100%; flex-direction: column; overflow: hidden; color: #cbf7e6; background: linear-gradient(150deg, rgba(3,10,17,.93), rgba(5,21,27,.94)); font-family: ui-monospace, "SFMono-Regular", Menlo, Monaco, Consolas, monospace; }
  .terminal-tabs { display: flex; min-height: 2.5rem; align-items: center; justify-content: space-between; padding: 0 1rem; border-bottom: 1px solid rgba(120,242,205,.1); color: rgba(218,251,240,.4); background: rgba(255,255,255,.025); font-size: .69rem; }
  .terminal-tabs .active { color: rgba(231,255,247,.75); }
  .terminal-tabs b { color: #73f2c4; }
  .terminal-content { flex: 1; overflow-y: auto; padding: 1.25rem; font-size: clamp(.72rem, 1.4vw, .84rem); line-height: 1.7; }
  .line { min-height: 1.35em; white-space: pre-wrap; }
  .line.prompt { margin-top: .35rem; color: #8de8ff; }
  .input-row { display: flex; align-items: center; gap: .55rem; color: #c2e3ff; }
  .input-row b { color: #7af0c3; }
  input { min-width: 3rem; flex: 1; padding: 0; border: 0; outline: 0; color: #f2fff9; background: transparent; box-shadow: none; caret-color: #7af0c3; font: inherit; }
</style>
