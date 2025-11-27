# Mehmood Ahmad's Portfolio

A  macOS-inspired portfolio website built with SvelteKit and TailwindCSS. Features fully functional desktop applications including Terminal, Finder, and Notes.


## 👋 About This Portfolio

Hey there! Welcome to my little corner of the internet. I wanted to create something different from the typical portfolio website - something that would actually be fun to explore. As a macOS user and someone who spends way too much time in the terminal, I thought, "Why not bring that experience to the web?"

This portfolio is more than just a list of my projects and skills. It's an interactive experience where you can:
- Open a Terminal and type commands to learn about me (try typing `help`!)
- Browse through my work experience and projects in a Finder-style interface with actual folders
- Even take notes that save to your computer if you want to jot something down

I built this using SvelteKit and TypeScript because I wanted to challenge myself with modern web technologies. Every window is draggable, you can minimize and maximize them, and multiple apps can be open at once - just like a real operating system. The attention to detail matters to me, from the glassmorphism effects to the smooth animations.

Whether you're a recruiter checking out my work, a fellow developer looking for inspiration, or someone who just stumbled upon this - I hope you enjoy exploring! And hey, if you like what you see, the code is all here for you to learn from or build upon.

## ✨ Features

### 💻 Terminal App
- Interactive command-line interface with that classic green-on-black aesthetic
- Type `help` when you open it to see all available commands
- Commands include: `about`, `skills`, `contact`, `experience`, and `clear`
- Beautiful ASCII art formatting because who doesn't love some good terminal art?
- Actually focuses on the input automatically so you can start typing right away

### 📁 Finder App
- Browse through my work experience and projects organized in folders (just like the real Finder!)
- Two main folders: "Work Experience" and "Projects"
- Click into any folder to see detailed README files about each role or project
- Work experience includes time periods and locations
- Projects link directly to GitHub repositories
- Navigation breadcrumbs and a sidebar for easy browsing

### 📝 Notes App
- A fully functional note-taking app that **actually saves real .txt files to your computer!**
- Live character, word, and line counters (because sometimes you need to know)
- Edit the filename before saving
- Clean, yellow notepad aesthetic inspired by macOS Notes
- Create new notes with the "New" button

### 🎨 Window Management
- Drag windows around the screen (try it, it's satisfying!)
- Click any window to bring it to the front
- Real macOS-style traffic light buttons (🔴🟡🟢) with hover effects
- Minimize windows and restore them by clicking the dock icon
- Maximize for full-screen view, or keep them windowed
- Multiple apps can be open simultaneously
- Dock shows indicators for running apps
- Smooth animations everywhere because smooth is good

## 🚀 Quick Start

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

Visit `http://localhost:5173` to see your portfolio!

## 🏗️ Project Structure

```
my-portfolio/
├── src/
│   ├── lib/
│   │   ├── components/
│   │   │   ├── Dock.svelte          # macOS-style dock
│   │   │   ├── Window.svelte        # Window wrapper with controls
│   │   │   ├── Terminal.svelte      # Terminal application
│   │   │   ├── Finder.svelte        # Project browser
│   │   │   └── Notes.svelte         # Notes application
│   │   └── assets/
│   └── routes/
│       ├── +page.svelte            # Main page
│       ├── +layout.svelte          # Layout wrapper
│       └── layout.css              # Global styles
├── static/
├── package.json
└── README.md
```

## 🎯 How to Explore

### Terminal Commands
When you open the Terminal, you'll see available commands right away. Try these:
- `help` - Show all available commands (in case you forget)
- `about` - Learn about me, my education, and experience
- `skills` - See my full tech stack (spoiler: it's pretty extensive)
- `experience` - Detailed breakdown of my work history
- `contact` - Get my email, GitHub, and LinkedIn
- `clear` - Clean slate, fresh start

### Finder Navigation
1. Open Finder from the dock
2. You'll see two main folders: "Work Experience" and "Projects"
3. Click on either folder to dive in
4. Each subfolder represents a job or project
5. Click on any item to see the full README with details
6. Use the back button (←) or sidebar to navigate
7. For projects, click "View on GitHub" to see the code

### Notes App
1. Open Notes and start typing whatever you want
2. The app tracks your character, word, and line count in real-time
3. Change the filename at the top (default is "untitled.txt")
4. Click "💾 Save" to download the note to your computer
5. Click "New" to start fresh (it'll ask if you want to save unsaved changes)



