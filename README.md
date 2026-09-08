# MoodyOS — Mehmood Ahmad's Portfolio

A responsive, OS-inspired portfolio built with SvelteKit, TypeScript, and Tailwind CSS. MoodyOS brings a macOS-style desktop to larger screens and an iOS/iPadOS-inspired experience to phones and tablets, combining liquid-glass surfaces with retro pixel art.

## About

I wanted my portfolio to feel like somewhere you could explore, not just another page of project cards. MoodyOS started as a macOS-inspired desktop and grew into a device-aware portfolio with different ways to browse the same work.

On desktop, you can open Finder, move windows around, explore the Terminal, and arrange widgets. On iPhone and iPad, the interface switches to a touch-friendly home screen: Messages holds my projects, Contacts holds my experience, and apps open into focused screens.

The pixel-art wallpaper, portrait, and app icons tie everything together. The glass surfaces and familiar navigation keep it usable without losing that retro feel.

## Desktop, phone, and tablet

- **Desktop:** macOS-inspired menu bar, glass dock, draggable windows, traffic-light controls, and movable widgets.
- **iPhone:** iOS-inspired status bar, compact home widgets, a four-app bottom dock, and full-screen app views without desktop chrome.
- **iPad:** a roomier, touch-friendly layout with the same mobile app navigation.
- Device detection uses browser device information and touch/viewport fallbacks. These are web interfaces inspired by Apple platforms, not native apps or complete operating-system replicas.

## Apps and features

### Finder / Messages / Contacts

- Browse featured projects, work experience, and portfolio highlights.
- Desktop Finder offers a sidebar, folder-style items, icon/list views, and individual detail screens.
- Mobile Messages presents projects as a searchable list with conversation-style overviews, impact, technologies, and repository links.
- Mobile Contacts presents work experience as searchable entries with role details and contributions.
- A built-in portfolio guide explains how to get around.
- Résumé, GitHub, LinkedIn, and research links connect the portfolio to the underlying work.

### Terminal

A small portfolio command interface—not a system shell. Available commands:

- `help` — list commands
- `about` — background and education
- `skills` — technologies I work with
- `experience` — work history
- `projects` — selected projects
- `publication` — research information
- `contact` — email, GitHub, and LinkedIn
- `clear` — clear the output

The toolbar includes working clear and help controls. Unknown commands are handled without interrupting the app.

### Notes

- A private, single-note scratchpad with an editable filename.
- Automatically saves the draft in browser storage when available.
- Keeps drafts across closing and reopening the app; minimizing a desktop window also preserves app state.
- Search the current note, insert checklist items, or download it as a text file.
- Live word and character counts.
- Starting a new note asks before clearing existing content.

Notes stay in the current browser; there is no iCloud sync or account-based storage. Download anything you want to keep permanently.

### Flappy Bird

- Play with a tap, click, Space, or the Up arrow.
- Fixed-step physics keeps game speed consistent across screen refresh rates.
- A responsive canvas preserves the playfield proportions and pixel-art rendering.
- One-tap restart and a locally saved high score.
- Pauses when hidden or inactive; Escape pauses, and the next input resumes.
- Stops the animation loop while idle or paused and caps canvas resolution to limit rendering work.

### Desktop controls and widgets

- Open apps from the dock and bring windows to the front.
- Minimize, restore, maximize, and close desktop windows.
- Move the profile, stats, and research widgets, or reset their positions from the portfolio menu.
- Open portfolio notifications by clicking the clock.
- Adjust the website's visual brightness from Control Center.
- Copy contact details or open portfolio sections from the menu bar.

Brightness affects only the webpage, not the device display. Notifications are portfolio shortcuts, not a live system notification feed. Unsupported decorative toolbar controls have been removed.

## Run locally

```bash
npm install
npm run dev
```

Open the local URL printed by Vite, usually `http://localhost:5173`.

```bash
# Type and Svelte diagnostics
npm run check

# Build the static site
npm run build

# Preview the production build locally
npm run preview
```

The production output is written to `build/`. Sites hosting is configured in `.openai/hosting.json`; changing the README does not publish the site.

## Project structure

```text
src/
├── lib/
│   ├── assets/              # Pixel-art wallpaper, portrait, and app icons
│   └── components/
│       ├── Dock.svelte      # Desktop dock
│       ├── Window.svelte    # Window controls, focus, and dragging
│       ├── MobileHome.svelte
│       ├── Finder.svelte    # Finder and mobile project/experience views
│       ├── Terminal.svelte
│       ├── Notes.svelte
│       └── FlappyBird.svelte
└── routes/
    ├── +page.svelte         # App state, device layout, menus, and widgets
    ├── +layout.svelte      # Shared layout and metadata
    └── layout.css
static/                     # Downloadable résumé and other static files
```

## Validation

The release review covered desktop, iPhone, and iPad browser emulation: project and experience navigation, Notes persistence, window controls, notifications, brightness, game input, viewport changes, résumé loading, and blocked browser storage.

Emulation is not a substitute for testing on physical devices. Real-device Safari testing remains part of the launch checklist.

## Contact

- [GitHub](https://github.com/MehmoodAhmad21)
- [LinkedIn](https://www.linkedin.com/in/mehmood-ahmad-2bb43b244/)
- [Email](mailto:mehmood3@ualberta.ca)


