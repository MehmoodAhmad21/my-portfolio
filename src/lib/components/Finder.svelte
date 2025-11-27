<script lang="ts">
  type FileItem = {
    id: string;
    name: string;
    type: 'folder' | 'file';
    description: string;
    details?: string[];
    technologies?: string[];
    link?: string;
    timeperiod?: string;
    location?: string;
  };

  type FolderStructure = {
    [key: string]: FileItem[];
  };

  // Folder structure
  const folders: FolderStructure = {
    root: [
      { id: 'work', name: 'Work Experience', type: 'folder', description: 'My professional work experience' },
      { id: 'projects', name: 'Projects', type: 'folder', description: 'My personal and academic projects' }
    ],
    work: [
      {
        id: 'work1',
        name: 'University of Alberta',
        type: 'folder',
        description: 'Software Engineer Intern',
        timeperiod: 'May 2024 - Present',
        location: 'Edmonton, AB',
        details: [
          'Collaborated with a research group to develop advanced real-time streaming and AI-powered applications.',
          'Led the development of a Safety Monitor System using YOLOv8 for real-time hazard detection and LLM-based risk assessment.',
          'Designed a high-performance pipeline utilizing FFmpeg for seamless 360-degree camera streaming.',
          'Developed a Remote Inspection Robot controlled via a VR headset, integrating ROS for real-time robotic navigation and control.',
          'Built a VR application using Unity and C#, enabling immersive user interaction with remote environments.',
          'Automated deployment and system startup with Bash scripting, simplifying workflow execution with a single command.'
        ],
        technologies: ['YOLOv8', 'LLM', 'FFmpeg', 'ROS', 'Unity', 'C#', 'VR', 'Python', 'Bash']
      },
      {
        id: 'work2',
        name: 'Aro Robotic Systems',
        type: 'folder',
        description: 'Software Engineer Intern',
        timeperiod: 'May 2023 - September 2023',
        location: 'Edmonton, AB',
        details: [
          'Optimized deployment pipelines with Docker and Jenkins, reducing deployment time by 70% and improving release stability across environments.',
          'Built CI/CD pipelines in GitLab CI, integrating security checks that reduced vulnerabilities by 40% and streamlined the code deployment process.',
          'Deployed cloud infrastructure solutions using AWS and DigitalOcean, supporting scalable application deployment and reducing operational costs by 15% through resource optimization.',
          'Introduced containerization methods, improving system reliability and increasing uptime by 43%, ensuring application availability during high-demand periods.',
          'Leveraged data-driven insights to optimize deployment pipelines, improving efficiency by 40% and stability across cloud-based environments.'
        ],
        technologies: ['Docker', 'Jenkins', 'GitLab CI', 'AWS', 'DigitalOcean', 'CI/CD', 'DevOps']
      },
      {
        id: 'work3',
        name: 'Aro Robotic Systems',
        type: 'folder',
        description: 'Software Engineer Intern',
        timeperiod: 'May 2022 - September 2022',
        location: 'Edmonton, AB',
        details: [
          'Set up automated monitoring tools, improving deployment success rates by 25% and reducing post-deployment failures through proactive alerts and system tracking.',
          'Designed and developed RESTful APIs to enhance system interoperability and efficiency.',
          'Developed a cloud-based logging system, improving system diagnostics and debugging efficiency.',
          'Utilized C, JavaScript, Python, and Django to build scalable software solutions.'
        ],
        technologies: ['C', 'JavaScript', 'Python', 'Django', 'REST APIs', 'Cloud', 'Monitoring']
      }
    ],
    projects: [
      {
        id: 'proj1',
        name: 'Event Lottery System',
        type: 'folder',
        description: 'Mobile app for fair event registration',
        details: [
          'Developed a mobile app that enables fair event sign-ups via a lottery system, ensuring accessibility for users with scheduling constraints.',
          'Implemented Firebase for event storage, real-time updates, and QR code-based event registration.',
          'Designed a scalable system supporting multi-user roles: entrants, organizers, and administrators, each with distinct privileges.',
          'Integrated a geolocation-based verification system for event registrations, ensuring location authenticity.',
          'Designed and implemented a QR code scanning feature for quick access to event details and registration.'
        ],
        technologies: ['Android', 'Firebase', 'Java', 'Jetpack Compose', 'QR Code', 'Geolocation'],
        link: 'https://github.com/CMPUT301F24mohggg/MohgggDraw'
      },
      {
        id: 'proj2',
        name: 'macOS Portfolio Website',
        type: 'folder',
        description: 'Interactive portfolio with macOS interface',
        details: [
          'A stunning macOS-inspired portfolio website featuring fully functional desktop applications including Terminal, Finder, and Notes.',
          'Built with SvelteKit and TypeScript, featuring draggable windows, minimize/maximize functionality.',
          'Implemented beautiful glassmorphism design with smooth animations and transitions.',
          'Created interactive Terminal with command-line interface for exploring portfolio information.',
          'Developed functional Notes app that saves real text files to user\'s computer.',
          'You\'re looking at it right now!'
        ],
        technologies: ['SvelteKit', 'TypeScript', 'TailwindCSS', 'UI/UX', 'Web Design'],
        link: 'https://github.com/MehmoodAhmad21/myprotfolio'
      }
    ]
  };

  let currentPath: string[] = ['root'];
  let selectedItem: FileItem | null = null;

  function getCurrentFolder(): FileItem[] {
    const currentFolderKey = currentPath[currentPath.length - 1];
    return folders[currentFolderKey] || [];
  }

  function handleItemClick(item: FileItem) {
    if (item.type === 'folder' && currentPath.length === 1) {
      // Navigating into a main folder
      currentPath = [...currentPath, item.id];
      selectedItem = null;
    } else {
      // Viewing a file or subfolder
      selectedItem = item;
    }
  }

  function goBack() {
    if (currentPath.length > 1) {
      currentPath = currentPath.slice(0, -1);
      selectedItem = null;
    }
  }

  function getBreadcrumb(): string {
    return currentPath.map(p => {
      if (p === 'root') return 'Home';
      const item = Object.values(folders).flat().find(i => i.id === p);
      return item?.name || p;
    }).join(' > ');
  }
</script>

<div class="h-full flex flex-col bg-white/5 rounded-lg overflow-hidden">
  <!-- Toolbar -->
  <div class="bg-white/5 border-b border-white/10 px-4 py-2 flex items-center gap-2">
    <button
      on:click={goBack}
      disabled={currentPath.length === 1}
      class="px-2 py-1 rounded hover:bg-white/10 transition-colors disabled:opacity-30 disabled:cursor-not-allowed"
      title="Go back"
    >
      ←
    </button>
    <div class="flex-1 text-sm text-gray-300">
      {getBreadcrumb()}
    </div>
  </div>

  <div class="flex-1 flex overflow-hidden">
    <!-- Sidebar -->
    <div class="w-48 bg-white/5 border-r border-white/10 p-3 overflow-y-auto">
      <div class="text-xs font-semibold text-gray-400 mb-2 px-2">FAVORITES</div>
      <button
        on:click={() => { currentPath = ['root']; selectedItem = null; }}
        class="w-full text-left px-2 py-1.5 rounded flex items-center gap-2 hover:bg-white/10 transition-colors {currentPath.length === 1 ? 'bg-blue-500/30' : ''}"
      >
        <span class="text-lg">🏠</span>
        <span class="text-sm">Home</span>
      </button>
      
      <div class="mt-4 text-xs font-semibold text-gray-400 mb-2 px-2">FOLDERS</div>
      {#each folders.root as folder}
        <button
          on:click={() => { currentPath = ['root', folder.id]; selectedItem = null; }}
          class="w-full text-left px-2 py-1.5 rounded flex items-center gap-2 hover:bg-white/10 transition-colors {currentPath.includes(folder.id) ? 'bg-blue-500/30' : ''}"
        >
          <span class="text-lg">📁</span>
          <span class="text-sm truncate">{folder.name}</span>
        </button>
      {/each}
    </div>

    <!-- Main content area -->
    <div class="flex-1 overflow-y-auto">
      {#if !selectedItem}
        <!-- List view -->
        <div class="p-6">
          <div class="grid grid-cols-2 gap-4">
            {#each getCurrentFolder() as item}
              <button
                on:click={() => handleItemClick(item)}
                class="text-left p-4 rounded-lg hover:bg-white/10 transition-colors flex items-start gap-3 border border-white/10"
              >
                <span class="text-3xl">{item.type === 'folder' ? '📁' : '📄'}</span>
                <div class="flex-1 min-w-0">
                  <div class="font-medium truncate">{item.name}</div>
                  <div class="text-xs text-gray-400 mt-1 truncate">{item.description}</div>
                  {#if item.timeperiod}
                    <div class="text-xs text-gray-500 mt-1">{item.timeperiod}</div>
                  {/if}
                </div>
              </button>
            {/each}
          </div>
        </div>
      {:else}
        <!-- Detail view -->
        <div class="p-6 max-w-3xl">
          <div class="flex items-center gap-3 mb-6">
            <span class="text-5xl">📁</span>
            <div>
              <h2 class="text-2xl font-semibold">{selectedItem.name}</h2>
              <p class="text-gray-400 text-sm">{selectedItem.description}</p>
            </div>
          </div>

          {#if selectedItem.timeperiod}
            <div class="mb-4 text-sm">
              <span class="text-blue-400">📅 Time Period:</span>
              <span class="text-gray-300 ml-2">{selectedItem.timeperiod}</span>
            </div>
          {/if}

          {#if selectedItem.location}
            <div class="mb-4 text-sm">
              <span class="text-blue-400">📍 Location:</span>
              <span class="text-gray-300 ml-2">{selectedItem.location}</span>
            </div>
          {/if}
          
          <div class="bg-white/5 border border-white/10 rounded-lg p-4 mb-4">
            <div class="flex items-center gap-2 mb-3">
              <span class="text-2xl">📄</span>
              <span class="font-medium">README.txt</span>
            </div>
            <div class="font-mono text-sm bg-black/30 rounded p-4 leading-relaxed">
              <div class="text-green-400 mb-4">
                ═══════════════════════════════════════════════════════
                {selectedItem.name}
                ═══════════════════════════════════════════════════════
              </div>
              
              {#if selectedItem.details}
                <div class="mb-4">
                  <div class="text-blue-400 mb-2">DETAILS:</div>
                  <div class="text-gray-300 space-y-2">
                    {#each selectedItem.details as detail}
                      <div class="pl-4">• {detail}</div>
                    {/each}
                  </div>
                </div>
              {/if}
              
              {#if selectedItem.technologies}
                <div class="mb-4">
                  <div class="text-blue-400 mb-2">TECHNOLOGIES:</div>
                  <div class="text-gray-300 pl-4">
                    {selectedItem.technologies.join(' • ')}
                  </div>
                </div>
              {/if}
              
              {#if selectedItem.link}
                <div>
                  <div class="text-blue-400 mb-2">REPOSITORY:</div>
                  <div class="text-purple-400 pl-4">
                    {selectedItem.link}
                  </div>
                </div>
              {/if}
            </div>
          </div>

          <div class="flex gap-2">
            {#if selectedItem.link}
              <a
                href={selectedItem.link}
                target="_blank"
                rel="noopener noreferrer"
                class="px-4 py-2 bg-blue-500 hover:bg-blue-600 rounded-lg text-sm font-medium transition-colors"
              >
                View on GitHub
              </a>
            {/if}
            <button
              on:click={() => selectedItem = null}
              class="px-4 py-2 bg-white/10 hover:bg-white/20 rounded-lg text-sm font-medium transition-colors"
            >
              Back to List
            </button>
          </div>
        </div>
      {/if}
    </div>
  </div>
</div>

<style>
  ::-webkit-scrollbar {
    width: 8px;
  }
  
  ::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.05);
  }
  
  ::-webkit-scrollbar-thumb {
    background: rgba(255, 255, 255, 0.2);
    border-radius: 4px;
  }
  
  ::-webkit-scrollbar-thumb:hover {
    background: rgba(255, 255, 255, 0.3);
  }
</style>
