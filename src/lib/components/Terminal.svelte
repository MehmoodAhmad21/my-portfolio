<script lang="ts">
  import { onMount } from 'svelte';

  let commandHistory: string[] = [];
  let currentInput = '';
  let inputElement: HTMLInputElement;

  const aboutMe = `
╔═══════════════════════════════════════════════════════════╗
║                      ABOUT ME                             ║
╚═══════════════════════════════════════════════════════════╝

Name:       Mehmood Ahmad
Role:       Software Engineer Intern
Location:   Edmonton, Alberta
Email:      mehmood3@ualberta.ca
Education:  University of Alberta (2021 - 2026)
            Bachelor of Science - Computer Science
            Minor in Biological Sciences

`;

  onMount(() => {
    commandHistory = [
      'Welcome to MoodyOS Terminal',
      '',
      'Available commands:',
      '━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━',
      '  help       - Show this help message',
      '  about      - About me',
      '  skills     - Technical skills',
      '  contact    - Contact information',
      '  experience - Work experience',
      '  clear      - Clear terminal',
      ''
    ];
    
    setTimeout(() => {
      inputElement?.focus();
    }, 100);
  });

  function handleCommand(event: KeyboardEvent) {
    if (event.key === 'Enter') {
      const command = currentInput.trim().toLowerCase();
      commandHistory.push(`$ ${currentInput}`);
      
      switch(command) {
        case 'help':
          commandHistory.push('Available commands:');
          commandHistory.push('  help       - Show this help message');
          commandHistory.push('  about      - About me');
          commandHistory.push('  skills     - Technical skills');
          commandHistory.push('  contact    - Contact information');
          commandHistory.push('  experience - Work experience');
          commandHistory.push('  clear      - Clear terminal');
          break;
        case 'about':
          commandHistory.push(aboutMe);
          break;
        case 'skills':
          commandHistory.push('Technical Skills:');
          commandHistory.push('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
          commandHistory.push('');
          commandHistory.push('Languages:');
          commandHistory.push('  Python, C, C++, C#, Java, JavaScript, TypeScript');
          commandHistory.push('  SQL, HTML/CSS, LaTeX, Shell');
          commandHistory.push('');
          commandHistory.push('Frameworks & Libraries:');
          commandHistory.push('  React, Svelte, SvelteKit, Node.js, Django, Bootstrap');
          commandHistory.push('  Unity, ROS, TailwindCSS');
          commandHistory.push('');
          commandHistory.push('Developer Tools:');
          commandHistory.push('  Git, VS Code, Visual Studio, PyCharm, WebStorm, REST APIs');
          commandHistory.push('');
          commandHistory.push('Data Science & ML Libraries:');
          commandHistory.push('  pandas, NumPy, Matplotlib, transformers, Peft, Tkinter');
          commandHistory.push('  BeautifulSoup, Scrapy, Torch');
          commandHistory.push('');
          commandHistory.push('Technologies:');
          commandHistory.push('  AI, Large Language Models (LLMs), Computer Vision');
          commandHistory.push('  Docker, Jenkins, GitLab CI, AWS, DigitalOcean');
          commandHistory.push('  SQLite3, MongoDB, FFmpeg, YOLOv8');
          break;
        case 'contact':
          commandHistory.push('Contact Information:');
          commandHistory.push('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
          commandHistory.push('Email:    mehmood3@ualberta.ca');
          commandHistory.push('GitHub:   https://github.com/MehmoodAhmad21');
          commandHistory.push('LinkedIn: https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/');
          break;
        case 'experience':
          commandHistory.push('Work Experience:');
          commandHistory.push('━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━');
          commandHistory.push('');
          commandHistory.push('University of Alberta | Software Engineer Intern');
          commandHistory.push('May 2024 - Present | Edmonton, AB');
          commandHistory.push('  • Real-time streaming and AI-powered applications');
          commandHistory.push('  • Safety Monitor System with YOLOv8 & LLM-based risk assessment');
          commandHistory.push('  • High-performance 360° camera streaming with FFmpeg');
          commandHistory.push('  • Remote Inspection Robot with VR headset control (ROS)');
          commandHistory.push('  • VR application development using Unity and C#');
          commandHistory.push('');
          commandHistory.push('Aro Robotic Systems | Software Engineer Intern');
          commandHistory.push('May 2023 - September 2023 | Edmonton, AB');
          commandHistory.push('  • Optimized deployment pipelines (Docker, Jenkins) - 70% faster');
          commandHistory.push('  • Built CI/CD pipelines in GitLab CI - 40% fewer vulnerabilities');
          commandHistory.push('  • AWS & DigitalOcean infrastructure - 15% cost reduction');
          commandHistory.push('  • Containerization - 43% uptime improvement');
          commandHistory.push('');
          commandHistory.push('Aro Robotic Systems | Software Engineer Intern');
          commandHistory.push('May 2022 - September 2022 | Edmonton, AB');
          commandHistory.push('  • Automated monitoring tools - 25% better deployment success');
          commandHistory.push('  • Designed RESTful APIs for system interoperability');
          commandHistory.push('  • Cloud-based logging system for diagnostics');
          commandHistory.push('  • Built scalable solutions with C, JavaScript, Python, Django');
          break;
        case 'clear':
          commandHistory = [];
          break;
        case '':
          break;
        default:
          commandHistory.push(`Command not found: ${command}`);
          commandHistory.push('Type "help" for available commands');
      }
      
      if (command !== 'clear') {
        commandHistory.push('');
      }
      currentInput = '';
      commandHistory = commandHistory;
      
      // Scroll to bottom
      setTimeout(() => {
        const terminal = document.querySelector('.terminal-content');
        if (terminal) {
          terminal.scrollTop = terminal.scrollHeight;
        }
      }, 0);
    }
  }
</script>

<div class="h-full flex flex-col bg-black/90 rounded-lg overflow-hidden font-mono text-xs md:text-sm">
  <div class="terminal-content flex-1 overflow-y-auto p-2 md:p-4 text-green-400">
    {#each commandHistory as line}
      <div class="whitespace-pre-wrap">{line}</div>
    {/each}
    <div class="flex items-center">
      <span class="text-green-400">$</span>
      <input
        bind:this={inputElement}
        bind:value={currentInput}
        on:keydown={handleCommand}
        class="flex-1 bg-transparent border-none outline-none ml-2 text-green-400 text-xs md:text-sm"
        type="text"
        spellcheck="false"
        autocomplete="off"
      />
    </div>
  </div>
</div>

<style>
  .terminal-content::-webkit-scrollbar {
    width: 8px;
  }
  
  .terminal-content::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0.3);
  }
  
  .terminal-content::-webkit-scrollbar-thumb {
    background: rgba(74, 222, 128, 0.3);
    border-radius: 4px;
  }
  
  .terminal-content::-webkit-scrollbar-thumb:hover {
    background: rgba(74, 222, 128, 0.5);
  }
</style>

