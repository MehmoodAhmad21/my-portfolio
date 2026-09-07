<script lang="ts">
  import avatar from '$lib/assets/mehmood-pixel-avatar.png';
  import messages from '$lib/assets/messages-pixel.png';
  import contacts from '$lib/assets/contacts-pixel.png';
  import notes from '$lib/assets/notes.png';
  import game from '$lib/assets/flappy.png';
  export let openFinder: (tab: 'projects' | 'experience' | 'guide') => void;
  export let openApp: (name: string) => void;
</script>

<section class="phone-home" aria-label="MoodyOS home screen">
  <div class="route-label">ROUTE 21 <strong>MoodyOS</strong></div>
  <article class="identity glass">
    <img src={avatar} alt="Pixel portrait of Mehmood Ahmad" />
    <div><small>PLAYER ONE · ENGINEER</small><h1>Mehmood Ahmad</h1><p>Applied AI. Real-time systems.<br />Thoughtful products.</p></div>
    <a href="https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/" target="_blank" rel="noreferrer">Available for opportunities <span>↗</span></a>
  </article>
  <div class="mini-widgets">
    <button class="glass" on:click={() => openFinder('experience')}><small>VISION F1</small><strong>50.6<span>%</span></strong><span>YOLOv11 + VLM ↗</span></button>
    <button class="glass research" on:click={() => openFinder('guide')}><small>START HERE</small><strong>Hey,<br />explorer.</strong><span>Your portfolio guide ↗</span></button>
  </div>
  <nav class="apps glass" aria-label="App dock">
    <button on:click={() => openFinder('projects')} aria-label="Messages — Projects"><img src={messages} alt="" /><strong>Messages</strong><small>Projects</small></button>
    <button on:click={() => openFinder('experience')} aria-label="Contacts — Experience"><img src={contacts} alt="" /><strong>Contacts</strong><small>Experience</small></button>
    <button on:click={() => openApp('Notes')}><img src={notes} alt="" /><strong>Notes</strong><small>Scratchpad</small></button>
    <button on:click={() => openApp('Flappy Bird')}><img src={game} alt="" /><strong>Flappy Bird</strong><small>Play</small></button>
  </nav>
</section>

<style>
  .phone-home { position:relative; display:flex; flex-direction:column; gap:18px; width:min(100%,620px); min-height:100svh; box-sizing:border-box; margin:auto; padding:max(65px,calc(env(safe-area-inset-top) + 48px)) 20px max(24px,env(safe-area-inset-bottom)); color:white; font-family:-apple-system,BlinkMacSystemFont,sans-serif; }
  .route-label { font:11px monospace; letter-spacing:1.5px; text-shadow:0 2px 5px #123; }
  .route-label strong { margin-left:12px; }
  .glass { border:1px solid #ffffff50; border-radius:26px; background:linear-gradient(135deg,#ffffff28,#ffffff08),#142a3d99; box-shadow:inset 0 1px #ffffff38,0 10px 28px #081c3020; backdrop-filter:blur(22px) saturate(140%); -webkit-backdrop-filter:blur(22px) saturate(140%); }
  .identity { display:grid; grid-template-columns:70px minmax(0,1fr); gap:16px; align-items:center; padding:20px; }
  .identity img { width:70px; height:78px; object-fit:cover; image-rendering:pixelated; border:2px solid #99d9ed; }
  small { font:10px monospace; letter-spacing:.8px; color:#b8e9f5; }
  h1 { font-size:clamp(21px,5.4vw,28px); line-height:1.1; margin:7px 0; letter-spacing:-.7px; }
  .identity p { margin:0; font-size:12px; color:#d4dfe9; line-height:1.6; }
  .identity a { grid-column:1/-1; display:flex; justify-content:space-between; align-items:center; padding-top:14px; min-height:30px; border-top:1px solid #ffffff28; color:#b3f8db; text-decoration:none; font-size:12px; }
  .mini-widgets { display:grid; grid-template-columns:1fr 1fr; gap:14px; }
  .mini-widgets button { display:flex; flex-direction:column; gap:10px; padding:18px; text-align:left; color:white; cursor:pointer; }
  .mini-widgets strong { font-size:36px; line-height:1.05; letter-spacing:-1px; }
  .mini-widgets strong span { font-size:22px; }
  .mini-widgets button > span { font-size:10px; color:#d7e4f0; }
  .mini-widgets .research strong { font-size:23px; }
  .apps { display:grid; grid-template-columns:repeat(4,minmax(0,1fr)); gap:8px; margin-top:auto; padding:14px 8px; flex-shrink:0; }
  .apps button { display:flex; flex-direction:column; align-items:center; min-width:0; border:0; padding:0; background:transparent; color:white; cursor:pointer; text-shadow:0 1px 5px #132b40; }
  .apps img { width:66px; height:66px; object-fit:contain; image-rendering:pixelated; filter:drop-shadow(0 5px 5px #10243955); margin-bottom:7px; }
  .apps strong { font-size:11px; font-weight:600; white-space:nowrap; }
  .apps small { font-size:9px; margin-top:4px; letter-spacing:0; color:#e6f3fa; }
  .route-label, small, .apps small, .mini-widgets button > span { font-size:12px; }
  .identity p, .identity a, .apps strong { font-size:14px; }
  .apps strong { white-space:normal; }
  .apps img { max-width:100%; }
  @media(min-width:700px) { .phone-home { width:min(100%,780px); gap:24px; padding-top:90px; } .identity { padding:28px; grid-template-columns:100px 1fr; } .identity img { width:100px; height:110px; } .identity p { font-size:16px; } .apps img { width:88px; height:88px; } .apps strong { font-size:14px; } .apps small { font-size:11px; } }
  @media(max-height:700px) { .phone-home { gap:12px; } .identity { padding:14px; } .mini-widgets button { padding:14px; gap:6px; } }
</style>
