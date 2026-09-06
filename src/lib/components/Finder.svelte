<script lang="ts">
  import { base } from '$app/paths';

  export let initialTab: 'highlights' | 'projects' | 'experience' = 'highlights';

  type Project = {
    name: string;
    eyebrow: string;
    description: string;
    impact: string;
    technologies: string[];
    link: string;
    accent: string;
  };

  type Role = {
    company: string;
    title: string;
    period: string;
    points: string[];
  };

  const projects: Project[] = [
    {
      name: 'Offspring.exe',
      eyebrow: 'Simulation / 2026',
      description: 'A browser-based genetics simulator that models Mendelian inheritance and polygenic traits, then draws the predicted outcome as a procedural SVG avatar.',
      impact: '20,000 seeded Monte Carlo outcomes per run',
      technologies: ['React', 'TypeScript', 'Vite', 'SVG'],
      link: 'https://github.com/MehmoodAhmad21/Offspring.exe',
      accent: '#c6a5ff'
    },
    {
      name: 'Trackme',
      eyebrow: 'Health intelligence',
      description: 'A full-stack mobile health and productivity tracker combining Apple HealthKit activity data, nutrition lookup, and personalized AI insights.',
      impact: 'Mobile + API + data intelligence in one system',
      technologies: ['React Native', 'FastAPI', 'SQLite', 'HealthKit'],
      link: 'https://github.com/MehmoodAhmad21/Trackme',
      accent: '#6ee7d2'
    },
    {
      name: 'SCAT6 Neuro Exam',
      eyebrow: 'Clinical workflow',
      description: 'A seven-step desktop assessment flow for symptoms, memory, balance, eye tracking, delayed recall, and structured JSON export.',
      impact: 'Turns a complex assessment into a guided workflow',
      technologies: ['Python', 'Tkinter', 'JSON', 'Accessibility'],
      link: 'https://github.com/MehmoodAhmad21/SCAT6NeuroExamAPP',
      accent: '#79c8ff'
    },
    {
      name: 'SafeHaven',
      eyebrow: 'IoT / NatHacks 2025',
      description: 'An emergency-escalation system for people living alone, connecting ESP32 sensors, HealthKit data, automated calls, and caregiver alerts.',
      impact: 'Environmental sensing through emergency escalation',
      technologies: ['React Native', 'ESP32', 'FastAPI', 'Twilio'],
      link: 'https://github.com/AbuDubu/NatHacks2025-SafeHouse',
      accent: '#ffad88'
    },
    {
      name: 'Event Lottery',
      eyebrow: 'Android platform',
      description: 'A fair event sign-up app with entrant, organizer, and admin roles, backed by real-time data, QR registration, and geolocation checks.',
      impact: 'Fair allocation with role-aware operations',
      technologies: ['Android', 'Firebase', 'Java', 'QR'],
      link: 'https://github.com/CMPUT301F24mohggg/MohgggDraw',
      accent: '#ffd16e'
    }
  ];

  const roles: Role[] = [
    {
      company: 'University of Alberta',
      title: 'Software Engineer Intern',
      period: 'May 2024 - Sep 2025',
      points: [
        'Led a YOLOv11 + vision-language safety monitor for live camera feeds, lifting hazard-identification F1 from 34.5% to 50.6% with about 2.5 ms added latency.',
        'Built a high-performance FFmpeg streaming pipeline and a ROS inspection robot controlled through a Unity/C# VR interface.',
        'Reduced deployment and startup to one command through Bash automation.'
      ]
    },
    {
      company: 'Aro Robotic Systems',
      title: 'Software Engineer Intern',
      period: 'May 2023 - Sep 2023',
      points: [
        'Cut average deployment time by about 70% with Docker and Jenkins pipelines across staging and production.',
        'Built end-to-end GitLab CI workflows with testing, linting, and security checks.',
        'Lowered cloud operating costs by about 15% through AWS and DigitalOcean right-sizing.'
      ]
    },
    {
      company: 'Aro Robotic Systems',
      title: 'Software Engineer Intern',
      period: 'May 2022 - Sep 2022',
      points: [
        'Designed and shipped the company website from scratch with a responsive, reusable component system.',
        'Built branded navigation, landing sections, cards, and contact flows with HTML, CSS, JavaScript, and WordPress.'
      ]
    }
  ];

  let activeTab: 'highlights' | 'projects' | 'experience' = initialTab;
  let selectedProject: Project | null = null;
  let viewMode: 'icons' | 'list' = 'icons';

  $: windowTitle = selectedProject?.name ?? (activeTab === 'highlights' ? 'Recents' : activeTab === 'projects' ? 'Projects' : 'Experience');

  function showTab(tab: typeof activeTab) {
    activeTab = tab;
    selectedProject = null;
  }
</script>

<div class="finder">
  <aside>
    <p class="section-label">Favorites</p>
    <button class:active={activeTab === 'highlights'} on:click={() => showTab('highlights')}><span>◴</span> Recents</button>
    <button class:active={activeTab === 'projects'} on:click={() => showTab('projects')}><span>▦</span> Projects</button>
    <button class:active={activeTab === 'experience'} on:click={() => showTab('experience')}><span>▤</span> Experience</button>

    <p class="section-label spaced">Locations</p>
    <a href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank"><span>⌄</span> Downloads</a>
    <a href="https://github.com/MehmoodAhmad21" target="_blank" rel="noreferrer"><span>⌘</span> GitHub</a>
    <div class="sidebar-user"><span>⌂</span><strong>mehmoodahmad</strong><i></i></div>
  </aside>

  <section class="browser">
    <header class="toolbar">
      <div class="history-controls">
        <button on:click={() => { selectedProject = null; }} disabled={!selectedProject} aria-label="Back">‹</button>
        <button disabled aria-label="Forward">›</button>
      </div>
      <strong class="folder-title">{windowTitle}</strong>
      <div class="toolbar-spacer"></div>
      <div class="view-controls" aria-label="View options">
        <button class:active={viewMode === 'icons'} on:click={() => viewMode = 'icons'} aria-label="Icon view">▦</button>
        <button class:active={viewMode === 'list'} on:click={() => viewMode = 'list'} aria-label="List view">☷</button>
        <button disabled aria-label="Column view">▥</button>
        <button disabled aria-label="Gallery view">▰</button>
      </div>
      <button class="tool-button" aria-label="Group items">⌗</button>
      <button class="tool-button" aria-label="Share">⇧</button>
      <button class="tool-button" aria-label="More options">•••</button>
      <a class="search-button" href={`${base}/mehmood-ahmad-resume.pdf`} target="_blank" aria-label="Open resume">⌕</a>
    </header>

    <div class="viewport">
      {#if selectedProject}
        <article class="project-detail" style={`--accent:${selectedProject.accent}`}>
          <span class="project-glyph">◆</span>
          <p class="pixel-label">{selectedProject.eyebrow}</p>
          <h2>{selectedProject.name}</h2>
          <p class="lead">{selectedProject.description}</p>
          <div class="impact"><span>Signal</span><strong>{selectedProject.impact}</strong></div>
          <div class="tech-row">
            {#each selectedProject.technologies as technology}<span>{technology}</span>{/each}
          </div>
          <a class="primary-link" href={selectedProject.link} target="_blank" rel="noreferrer">View repository <span>↗</span></a>
        </article>
      {:else if activeTab === 'highlights'}
        <div class="highlights-view">
          <div class="view-heading">
            <div><h2>Recents</h2><p>Everything a recruiter should open first.</p></div>
            <span class="count">4 items</span>
          </div>

          <div class:finder-icons={viewMode === 'icons'} class:finder-list={viewMode === 'list'} class="feature-grid">
            <button class="feature-card safety" on:click={() => showTab('experience')}>
              <span class="file-art blue"><b>50.6%</b><i>AI</i></span>
              <span class="file-copy"><strong>AI Safety.project</strong><p>YOLOv11 + VLM hazard identification</p></span>
            </button>
            <button class="feature-card genetics" on:click={() => { activeTab = 'projects'; selectedProject = projects[0]; }}>
              <span class="file-art purple"><b>20K</b><i>SIM</i></span>
              <span class="file-copy"><strong>Offspring.app</strong><p>Seeded genetics simulation</p></span>
            </button>
            <button class="feature-card systems" on:click={() => showTab('experience')}>
              <span class="file-art green"><b>70%</b><i>OPS</i></span>
              <span class="file-copy"><strong>Deployments.log</strong><p>Faster containerized delivery</p></span>
            </button>
            <a class="feature-card research" href="https://arxiv.org/abs/2604.05210" target="_blank" rel="noreferrer">
              <span class="file-art paper"><b>PDF</b><i>2026</i></span>
              <span class="file-copy"><strong>Construction Safety.pdf</strong><p>Object detection + small VLMs</p></span>
            </a>
          </div>

          <div class="publication">
            <span class="pub-icon">⌁</span>
            <div><p class="pixel-label">Quick Look · 2026</p><h3>Object Detection + Small VLMs for Construction Safety</h3><p>Co-author · arXiv:2604.05210</p></div>
            <a href="https://arxiv.org/abs/2604.05210" target="_blank" rel="noreferrer" aria-label="Read publication">↗</a>
          </div>
        </div>
      {:else if activeTab === 'projects'}
        <div class="projects-view">
          <div class="view-heading"><div><p class="pixel-label">Build log</p><h2>Featured projects</h2></div><span class="count">GitHub / public</span></div>
          <div class="project-list">
            {#each projects as project, index}
              <button on:click={() => selectedProject = project} style={`--accent:${project.accent}`}>
                <span class="project-number">0{index + 1}</span>
                <span class="project-marker">◆</span>
                <span class="project-copy"><small>{project.eyebrow}</small><strong>{project.name}</strong><p>{project.description}</p></span>
                <span class="arrow">›</span>
              </button>
            {/each}
          </div>
        </div>
      {:else}
        <div class="experience-view">
          <div class="view-heading"><div><p class="pixel-label">Work history</p><h2>Software engineering experience</h2></div><span class="count">Edmonton, AB</span></div>
          <div class="timeline">
            {#each roles as role}
              <article>
                <div class="timeline-dot"></div>
                <div class="role-head"><div><small>{role.period}</small><h3>{role.company}</h3><p>{role.title}</p></div></div>
                <ul>{#each role.points as point}<li>{point}</li>{/each}</ul>
              </article>
            {/each}
          </div>
        </div>
      {/if}
    </div>
  </section>
</div>

<style>
  .finder { display: grid; grid-template-columns: 13rem 1fr; height: 100%; color: #f7fbff; background: repeating-linear-gradient(135deg, rgba(255,255,255,.014) 0 4px, transparent 4px 8px); }
  aside { padding: 1.15rem .75rem; border-right: 2px solid rgba(155,225,246,.25); background: linear-gradient(180deg, rgba(13,41,67,.72), rgba(4,18,34,.58)); }
  .section-label { margin: 0 .65rem .45rem; color: rgba(235,244,255,.42); font-size: .67rem; font-weight: 750; letter-spacing: .1em; text-transform: uppercase; }
  .section-label.spaced { margin-top: 1.4rem; }
  aside button, aside a { display: flex; width: 100%; align-items: center; gap: .65rem; padding: .58rem .65rem; border: 0; border-radius: 2px; color: rgba(246,250,255,.72); background: transparent; font-family: ui-monospace, monospace; font-size: .76rem; font-weight: 700; text-align: left; text-decoration: none; cursor: pointer; }
  aside button:hover, aside a:hover, aside button.active { color: #071522; background: #9feaff; }
  aside button.active { box-shadow: inset 0 0 0 2px rgba(255,255,255,.45), 3px 3px 0 rgba(0,0,0,.32); }
  aside button.active span { color: #173554; }
  aside span { color: #86dfff; font-family: ui-monospace, monospace; }

  .browser { display: flex; min-width: 0; min-height: 0; flex-direction: column; }
  .toolbar { display: flex; min-height: 3.15rem; align-items: center; gap: .75rem; padding: 0 1rem; border-bottom: 2px solid rgba(137,217,244,.23); background: linear-gradient(90deg, rgba(101,207,239,.12), rgba(255,255,255,.025)); }
  .viewport { flex: 1; min-height: 0; overflow-y: auto; }
  .highlights-view, .projects-view, .experience-view { padding: clamp(1.25rem, 3vw, 2.3rem); }
  .view-heading { display: flex; align-items: flex-end; justify-content: space-between; gap: 1rem; margin-bottom: 1.35rem; }
  .view-heading h2 { margin: .3rem 0 0; font-size: clamp(1.5rem, 3vw, 2.35rem); letter-spacing: -.045em; text-shadow: 3px 3px 0 rgba(25,75,107,.42); }
  .view-heading p { margin: 0; color: #81e6ff; }
  .count { color: rgba(238,246,255,.42); font-family: ui-monospace, monospace; font-size: .68rem; text-transform: uppercase; }

  .feature-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: .85rem; }
  .feature-card { min-height: 13rem; padding: 1rem; border: 2px solid rgba(224,248,255,.62); border-radius: 3px; color: white; box-shadow: 0 0 0 2px rgba(8,24,42,.72), 4px 4px 0 rgba(0,0,0,.32); text-align: left; cursor: pointer; clip-path: polygon(6px 0, 100% 0, 100% calc(100% - 6px), calc(100% - 6px) calc(100% - 6px), calc(100% - 6px) 100%, 0 100%, 0 6px, 6px 6px); transition: transform 120ms steps(2), border-color 120ms ease; }
  .feature-card:hover { transform: translate(-2px,-2px); border-color: #fff7bd; }
  .feature-card.safety { background: radial-gradient(circle at 80% 15%, rgba(113,222,255,.22), transparent 40%), linear-gradient(145deg, rgba(31,80,130,.72), rgba(8,20,37,.82)); }
  .feature-card.genetics { background: radial-gradient(circle at 80% 15%, rgba(198,165,255,.25), transparent 40%), linear-gradient(145deg, rgba(79,49,118,.72), rgba(20,12,38,.84)); }
  .feature-card.systems { background: radial-gradient(circle at 80% 15%, rgba(101,239,190,.2), transparent 40%), linear-gradient(145deg, rgba(32,91,74,.7), rgba(7,28,27,.86)); }
  .feature-card strong { display: block; font-size: clamp(2.2rem, 5vw, 4rem); letter-spacing: -.07em; line-height: .9; }
  .feature-card p { margin: .8rem 0 1.15rem; color: rgba(244,249,255,.72); font-size: .8rem; line-height: 1.45; }
  .publication { display: grid; grid-template-columns: auto 1fr auto; align-items: center; gap: 1rem; margin-top: .9rem; padding: 1rem; border: 2px solid rgba(228,250,255,.62); border-radius: 2px; background: rgba(255,255,255,.06); box-shadow: 3px 3px 0 rgba(0,0,0,.3); }
  .pub-icon { display: grid; width: 2.8rem; height: 2.8rem; place-items: center; border: 2px solid #0e2a43; border-radius: 2px; color: #07111f; background: #ffdc70; box-shadow: 2px 2px 0 rgba(0,0,0,.3); font-size: 1.45rem; }
  .publication h3 { margin: .2rem 0; font-size: .96rem; }
  .publication p { margin: 0; color: rgba(238,246,255,.51); font-size: .72rem; }
  .publication .pixel-label { color: #81e6ff; font-size: .61rem; }
  .publication a { display: grid; width: 2.2rem; height: 2.2rem; place-items: center; border-radius: 50%; color: white; background: rgba(255,255,255,.09); text-decoration: none; }

  .project-list { display: grid; gap: .65rem; }
  .project-list button { display: grid; grid-template-columns: 2rem 1.7rem 1fr auto; align-items: center; gap: .85rem; padding: 1rem; border: 2px solid rgba(205,242,255,.34); border-radius: 2px; color: white; background: rgba(255,255,255,.05); box-shadow: 3px 3px 0 rgba(0,0,0,.24); text-align: left; cursor: pointer; transition: background 120ms steps(2), transform 120ms steps(2); }
  .project-list button:hover { transform: translateX(4px); color: #071522; background: #dff8f6; }
  .project-list button:hover .project-copy p, .project-list button:hover .project-number, .project-list button:hover .arrow { color: #29475e; }
  .project-number { color: rgba(255,255,255,.28); font-family: ui-monospace, monospace; font-size: .68rem; }
  .project-marker { color: var(--accent); text-shadow: 0 0 12px var(--accent); }
  .project-copy { min-width: 0; }
  .project-copy small { display: block; color: var(--accent); font-family: ui-monospace, monospace; font-size: .61rem; text-transform: uppercase; }
  .project-copy strong { display: block; margin: .16rem 0; font-size: 1rem; }
  .project-copy p { margin: 0; overflow: hidden; color: rgba(240,247,255,.55); font-size: .72rem; line-height: 1.35; text-overflow: ellipsis; white-space: nowrap; }
  .arrow { color: rgba(255,255,255,.5); font-size: 1.5rem; }

  .timeline { position: relative; padding-left: .45rem; }
  .timeline::before { position: absolute; top: .45rem; bottom: 0; left: .77rem; width: 1px; background: linear-gradient(#74dbff, rgba(116,219,255,.08)); content: ''; }
  .timeline article { position: relative; padding: 0 0 1.55rem 2rem; }
  .timeline-dot { position: absolute; top: .38rem; left: 0; width: .68rem; height: .68rem; border: 2px solid #0e263e; border-radius: 2px; background: #7fe2ff; box-shadow: 0 0 12px rgba(127,226,255,.7); transform: rotate(45deg); }
  .role-head small { color: #80e2ff; font-family: ui-monospace, monospace; font-size: .65rem; text-transform: uppercase; }
  .role-head h3 { margin: .25rem 0 .08rem; font-size: 1.15rem; }
  .role-head p { margin: 0; color: rgba(245,250,255,.57); font-size: .78rem; }
  .timeline ul { margin: .65rem 0 0; padding-left: 1rem; color: rgba(242,248,255,.72); font-size: .77rem; line-height: 1.55; }
  .timeline li + li { margin-top: .3rem; }

  .project-detail { min-height: 100%; padding: clamp(1.5rem, 5vw, 4rem); background: radial-gradient(circle at 82% 10%, color-mix(in srgb, var(--accent) 24%, transparent), transparent 32%); }
  .project-glyph { display: grid; width: 3.5rem; height: 3.5rem; margin-bottom: 2.2rem; place-items: center; border: 2px solid color-mix(in srgb, var(--accent) 55%, white); border-radius: 2px; color: var(--accent); background: color-mix(in srgb, var(--accent) 14%, transparent); font-size: 1.2rem; box-shadow: 3px 3px 0 rgba(0,0,0,.3), 0 0 28px color-mix(in srgb, var(--accent) 18%, transparent); }
  .project-detail .pixel-label { margin: 0; color: var(--accent); }
  .project-detail h2 { margin: .55rem 0 .8rem; font-size: clamp(2rem, 5vw, 4rem); letter-spacing: -.06em; line-height: .95; }
  .project-detail .lead { max-width: 44rem; color: rgba(244,249,255,.7); font-size: clamp(.95rem, 2vw, 1.15rem); line-height: 1.65; }
  .impact { display: flex; max-width: 43rem; align-items: center; gap: 1rem; margin: 1.5rem 0; padding: .9rem 1rem; border-left: 2px solid var(--accent); background: rgba(255,255,255,.045); }
  .impact span { color: var(--accent); font-family: ui-monospace, monospace; font-size: .66rem; text-transform: uppercase; }
  .impact strong { font-size: .82rem; }
  .tech-row { display: flex; flex-wrap: wrap; gap: .45rem; margin-bottom: 1.7rem; }
  .tech-row span { padding: .38rem .62rem; border: 2px solid rgba(255,255,255,.18); border-radius: 2px; color: rgba(255,255,255,.82); background: rgba(255,255,255,.06); font-family: ui-monospace, monospace; font-size: .64rem; font-weight: 800; }
  .primary-link { display: inline-flex; align-items: center; gap: .6rem; padding: .75rem 1rem; border: 2px solid rgba(255,255,255,.62); border-radius: 2px; color: #07111f; background: var(--accent); box-shadow: 3px 3px 0 rgba(0,0,0,.35); font-family: ui-monospace, monospace; font-size: .7rem; font-weight: 900; text-decoration: none; }

  /* Native Finder chrome, with pixel artwork reserved for the files themselves. */
  .finder { grid-template-columns: 13.4rem 1fr; color: #f4f4f5; background: rgba(39,39,41,.96); }
  aside { position: relative; padding: 1.2rem .7rem 4rem; border-right: 1px solid rgba(255,255,255,.09); background: rgba(28,28,30,.88); backdrop-filter: blur(34px) saturate(135%); }
  .section-label { margin: 0 .65rem .42rem; color: #8f8f94; font-size: .68rem; font-weight: 650; letter-spacing: .01em; text-transform: none; }
  .section-label.spaced { margin-top: 1.55rem; }
  aside button, aside a { gap: .65rem; padding: .48rem .65rem; border-radius: .5rem; color: #ededf0; font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", sans-serif; font-size: .84rem; font-weight: 520; }
  aside button:hover, aside a:hover { color: white; background: rgba(255,255,255,.07); }
  aside button.active { color: white; background: rgba(255,255,255,.115); box-shadow: none; }
  aside button.active span { color: #0a84ff; }
  aside span { display: grid; width: 1.2rem; place-items: center; color: #0a84ff; font-family: -apple-system, BlinkMacSystemFont, sans-serif; font-size: 1rem; }
  .sidebar-user { position: absolute; right: .7rem; bottom: .8rem; left: .7rem; display: grid; grid-template-columns: 1.2rem 1fr auto; align-items: center; gap: .65rem; padding: .5rem .65rem; color: #ededf0; font-size: .78rem; }
  .sidebar-user strong { overflow: hidden; font-weight: 520; text-overflow: ellipsis; }
  .sidebar-user i { width: .43rem; height: .43rem; border-radius: 50%; background: #31c757; box-shadow: 0 0 0 2px rgba(49,199,87,.14); }

  .browser { background: rgba(43,43,45,.97); }
  .toolbar { min-height: 3.65rem; gap: .7rem; padding: 0 .9rem; border-bottom: 1px solid rgba(255,255,255,.08); background: rgba(41,41,43,.96); }
  .history-controls, .view-controls { display: flex; align-items: center; overflow: hidden; border: 1px solid rgba(255,255,255,.1); border-radius: .72rem; background: rgba(0,0,0,.1); }
  .history-controls button, .view-controls button, .tool-button, .search-button { display: grid; width: 2.2rem; height: 2rem; padding: 0; place-items: center; border: 0; color: #dadadd; background: transparent; font-size: 1.13rem; text-decoration: none; cursor: pointer; }
  .history-controls button + button, .view-controls button + button { border-left: 1px solid rgba(255,255,255,.09); }
  .history-controls button:disabled, .view-controls button:disabled { color: #66666a; cursor: default; }
  .history-controls button:not(:disabled):hover, .view-controls button:not(:disabled):hover, .tool-button:hover, .search-button:hover { background: rgba(255,255,255,.08); }
  .view-controls button.active { color: white; background: #555559; box-shadow: inset 0 1px 0 rgba(255,255,255,.1); }
  .folder-title { margin-left: .25rem; color: #f5f5f6; font-size: .94rem; font-weight: 670; letter-spacing: -.01em; }
  .toolbar-spacer { flex: 1; }
  .tool-button, .search-button { border: 1px solid rgba(255,255,255,.1); border-radius: .72rem; background: rgba(0,0,0,.1); font-size: .94rem; }
  .search-button { font-size: 1.35rem; }

  .viewport { background: #29292b; scrollbar-color: #66666a transparent; }
  .highlights-view, .projects-view, .experience-view { padding: 1.35rem 1.6rem 2rem; }
  .view-heading { align-items: center; margin-bottom: 1.3rem; }
  .view-heading h2 { margin: 0; font-size: 1.2rem; font-weight: 680; letter-spacing: -.02em; text-shadow: none; }
  .view-heading p { margin: .18rem 0 0; color: #9a9aa0; font-size: .76rem; }
  .count { color: #88888e; font-family: -apple-system, BlinkMacSystemFont, sans-serif; font-size: .7rem; text-transform: none; }

  .feature-grid.finder-icons { display: grid; grid-template-columns: repeat(4, minmax(7rem, 1fr)); gap: 1.65rem 1.1rem; align-items: start; }
  .feature-card, .feature-card.research { display: flex; min-width: 0; min-height: 0; flex-direction: column; align-items: center; padding: .35rem; border: 0; border-radius: .55rem; color: #f4f4f5; background: transparent; box-shadow: none; text-align: center; text-decoration: none; clip-path: none; }
  .feature-card:hover { transform: none; border-color: transparent; background: rgba(255,255,255,.065); }
  .file-art { position: relative; display: grid; width: 6.5rem; height: 5.45rem; margin-bottom: .65rem; place-items: center; border: 1px solid rgba(255,255,255,.22); border-radius: .38rem; color: white; background: linear-gradient(145deg, #3c99ea, #1767c3); box-shadow: inset 0 1px 0 rgba(255,255,255,.38), 0 8px 18px rgba(0,0,0,.25); font-family: ui-monospace, monospace; image-rendering: pixelated; }
  .file-art::before { position: absolute; top: -.38rem; left: .28rem; width: 2.55rem; height: .65rem; border: 1px solid rgba(255,255,255,.2); border-bottom: 0; border-radius: .32rem .32rem 0 0; background: inherit; content: ''; }
  .file-art b { font-size: 1.35rem; letter-spacing: -.06em; text-shadow: 2px 2px 0 rgba(0,0,0,.28); }
  .file-art i { position: absolute; right: .38rem; bottom: .34rem; padding: .12rem .2rem; color: rgba(255,255,255,.82); background: rgba(0,0,0,.2); font-size: .54rem; font-style: normal; font-weight: 850; }
  .file-art.purple { background: linear-gradient(145deg, #a98aea, #6551bb); }
  .file-art.green { background: linear-gradient(145deg, #53c9a6, #237d70); }
  .file-art.paper { width: 5rem; background: linear-gradient(145deg, #f7f7f7, #cfcfd2); color: #d83442; }
  .file-art.paper::before { display: none; }
  .file-art.paper i { color: #4e4e52; background: transparent; }
  .file-copy { display: block; width: 100%; }
  .file-copy strong { display: block; overflow: hidden; font-size: .76rem; font-weight: 560; text-overflow: ellipsis; white-space: nowrap; }
  .file-copy p { margin: .2rem 0 0; overflow: hidden; color: #8f8f94; font-size: .65rem; line-height: 1.25; text-overflow: ellipsis; white-space: nowrap; }

  .feature-grid.finder-list { display: grid; gap: 0; overflow: hidden; border: 1px solid rgba(255,255,255,.08); border-radius: .55rem; }
  .finder-list .feature-card { display: grid; grid-template-columns: 3rem 1fr; align-items: center; gap: .75rem; width: 100%; flex-direction: initial; padding: .55rem .7rem; border-radius: 0; text-align: left; }
  .finder-list .feature-card + .feature-card { border-top: 1px solid rgba(255,255,255,.065); }
  .finder-list .file-art { width: 2.65rem; height: 2.15rem; margin: 0; }
  .finder-list .file-art::before, .finder-list .file-art i { display: none; }
  .finder-list .file-art b { font-size: .68rem; }

  .publication { margin-top: 1.6rem; padding: .85rem; border: 1px solid rgba(255,255,255,.09); border-radius: .65rem; background: rgba(255,255,255,.04); box-shadow: none; }
  .pub-icon { width: 2.7rem; height: 2.7rem; border: 0; border-radius: .48rem; color: white; background: #0a84ff; box-shadow: inset 0 1px 0 rgba(255,255,255,.28); }
  .publication h3 { font-size: .88rem; font-weight: 590; }
  .publication p { color: #909096; font-size: .68rem; }
  .publication .pixel-label { color: #0a84ff; font-family: -apple-system, BlinkMacSystemFont, sans-serif; font-size: .66rem; }
  .publication a { background: rgba(255,255,255,.08); }

  .project-list { gap: 0; overflow: hidden; border: 1px solid rgba(255,255,255,.08); border-radius: .6rem; }
  .project-list button { padding: .78rem .9rem; border: 0; border-radius: 0; background: transparent; box-shadow: none; }
  .project-list button + button { border-top: 1px solid rgba(255,255,255,.065); }
  .project-list button:hover { transform: none; color: white; background: rgba(10,132,255,.32); }
  .project-list button:hover .project-copy p, .project-list button:hover .project-number, .project-list button:hover .arrow { color: rgba(255,255,255,.68); }
  .project-marker { text-shadow: none; }
  .project-copy strong { font-weight: 580; }
  .project-copy p { font-size: .7rem; }

  .project-detail { background: radial-gradient(circle at 82% 10%, color-mix(in srgb, var(--accent) 14%, transparent), transparent 32%); }
  .project-glyph { border: 1px solid color-mix(in srgb, var(--accent) 55%, white); border-radius: .55rem; box-shadow: inset 0 1px 0 rgba(255,255,255,.14); }
  .tech-row span { border: 1px solid rgba(255,255,255,.13); border-radius: .45rem; font-family: -apple-system, BlinkMacSystemFont, sans-serif; font-weight: 550; }
  .primary-link { border: 0; border-radius: .5rem; box-shadow: none; font-family: -apple-system, BlinkMacSystemFont, sans-serif; }

  @media (max-width: 760px) {
    .finder { grid-template-columns: 1fr; }
    aside { display: flex; overflow-x: auto; gap: .3rem; padding: .5rem; border-right: 0; border-bottom: 1px solid rgba(255,255,255,.1); }
    aside .section-label, aside a { display: none; }
    aside button { width: auto; flex: 1 0 auto; justify-content: center; padding: .52rem; font-size: .72rem; }
    .toolbar { min-height: 2.75rem; padding: 0 .65rem; }
    .feature-grid.finder-icons { grid-template-columns: repeat(2, minmax(0,1fr)); gap: 1rem; }
    .tool-button { display: none; }
    .view-controls button:nth-child(n+3) { display: none; }
    .feature-card { min-height: 10rem; }
    .publication { grid-template-columns: auto 1fr; }
    .publication > a { display: none; }
    .view-heading { align-items: flex-start; }
    .count { display: none; }
    .project-list button { grid-template-columns: 1.4rem 1.3rem 1fr auto; gap: .55rem; padding: .8rem; }
    .project-copy p { white-space: normal; display: -webkit-box; line-clamp: 2; -webkit-line-clamp: 2; -webkit-box-orient: vertical; }
    .impact { align-items: flex-start; flex-direction: column; gap: .35rem; }
  }
</style>
