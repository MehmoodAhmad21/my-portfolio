<script lang="ts">
  let noteContent = '';
  let fileName = 'ideas.txt';
  let saveStatus = '';

  function downloadNote() {
    if (!noteContent.trim()) {
      saveStatus = 'Write something first';
      setTimeout(() => saveStatus = '', 1800);
      return;
    }
    const url = URL.createObjectURL(new Blob([noteContent], { type: 'text/plain' }));
    const link = document.createElement('a');
    link.href = url;
    link.download = fileName || 'note.txt';
    link.click();
    URL.revokeObjectURL(url);
    saveStatus = 'Saved';
    setTimeout(() => saveStatus = '', 1800);
  }

  function newNote() {
    if (noteContent.trim() && !confirm('Clear this note?')) return;
    noteContent = '';
    fileName = 'ideas.txt';
  }
</script>

<div class="notes">
  <aside class="folder-sidebar">
    <label class="search"><span>⌕</span><input aria-label="Search notes" placeholder="Search" /></label>
    <p>iCloud</p>
    <button class="folder active"><span>▤</span><strong>All iCloud</strong><b>1</b></button>
    <button class="folder"><span>▰</span><strong>Notes</strong><b>1</b></button>
    <div class="folder-bottom"><button aria-label="New folder">＋</button><span>New Folder</span></div>
  </aside>

  <section class="note-list">
    <header><strong>Notes</strong><button aria-label="More note options">•••</button></header>
    <button class="note-row active">
      <strong>{fileName.replace(/\.txt$/i, '') || 'New Note'}</strong>
      <span>Today</span>
      <p>{noteContent.trim().slice(0, 64) || 'Quick thought…'}</p>
    </button>
  </section>

  <section class="editor">
    <header class="editor-toolbar">
      <div class="format-controls"><button aria-label="Table">▦</button><button aria-label="Checklist">✓</button><button aria-label="Text format">Aa</button></div>
      <span class="status">{saveStatus}</span>
      <button on:click={newNote} aria-label="New note">▱＋</button>
      <button class="save" on:click={downloadNote} aria-label="Save note">⇧</button>
    </header>
    <div class="note-date">September 6, 2026 at 2:00 PM</div>
    <input class="note-title" bind:value={fileName} aria-label="Note filename" />
    <textarea bind:value={noteContent} aria-label="Note" placeholder={'Quick thought…\n\nWrite a note here. The share button saves it as a real text file.'} spellcheck="true"></textarea>
    <footer><span>{noteContent.trim() ? noteContent.trim().split(/\s+/).length : 0} words</span><span>{noteContent.length} characters</span></footer>
  </section>
</div>

<style>
  .notes { display: grid; height: 100%; grid-template-columns: 11.2rem 13.2rem minmax(0,1fr); overflow: hidden; color: #f4f4f5; background: #1f1f20; }
  button { border: 0; color: inherit; background: transparent; font: inherit; cursor: pointer; }
  .folder-sidebar { position: relative; padding: .8rem .55rem 3rem; border-right: 1px solid rgba(255,255,255,.08); background: rgba(45,45,47,.9); }
  .search { display: flex; align-items: center; gap: .3rem; margin-bottom: 1rem; padding: .28rem .48rem; border-radius: .42rem; color: #88888d; background: rgba(0,0,0,.2); box-shadow: inset 0 0 0 1px rgba(255,255,255,.06); }
  .search input { min-width: 0; width: 100%; padding: 0; border: 0; outline: 0; color: white; background: transparent; font-size: .72rem; }
  .search input::placeholder { color: #88888d; }
  .folder-sidebar > p { margin: 0 .5rem .35rem; color: #97979c; font-size: .68rem; font-weight: 650; }
  .folder { display: grid; width: 100%; grid-template-columns: 1.1rem 1fr auto; align-items: center; gap: .42rem; padding: .42rem .5rem; border-radius: .42rem; text-align: left; }
  .folder:hover, .folder.active { background: rgba(255,255,255,.1); }
  .folder > span { color: #ffd44c; }
  .folder strong, .folder b { font-size: .76rem; font-weight: 550; }
  .folder b { color: #96969a; }
  .folder-bottom { position: absolute; right: .7rem; bottom: .65rem; left: .7rem; display: flex; align-items: center; gap: .35rem; color: #a0a0a5; font-size: .68rem; }
  .folder-bottom button { color: #ffd44c; font-size: 1rem; }

  .note-list { overflow: hidden; border-right: 1px solid rgba(255,255,255,.08); background: #28282a; }
  .note-list header { display: flex; height: 2.75rem; align-items: center; justify-content: space-between; padding: 0 .8rem; border-bottom: 1px solid rgba(255,255,255,.07); }
  .note-list header strong { font-size: .8rem; }
  .note-list header button { color: #9c9ca1; }
  .note-row { display: block; width: calc(100% - .75rem); margin: .38rem; padding: .62rem .68rem; border-radius: .52rem; text-align: left; }
  .note-row.active { color: #191919; background: #ffd655; }
  .note-row strong { display: block; overflow: hidden; font-size: .76rem; text-overflow: ellipsis; white-space: nowrap; }
  .note-row span { display: block; margin-top: .15rem; font-size: .66rem; font-weight: 520; }
  .note-row p { margin: .12rem 0 0; overflow: hidden; color: rgba(0,0,0,.55); font-size: .66rem; text-overflow: ellipsis; white-space: nowrap; }

  .editor { display: flex; min-width: 0; flex-direction: column; background: #1f1f20; }
  .editor-toolbar { display: flex; min-height: 2.75rem; align-items: center; gap: .45rem; padding: 0 .7rem; border-bottom: 1px solid rgba(255,255,255,.07); }
  .format-controls { display: flex; overflow: hidden; border: 1px solid rgba(255,255,255,.1); border-radius: .48rem; }
  .format-controls button { width: 2rem; height: 1.75rem; color: #c7c7ca; }
  .format-controls button + button { border-left: 1px solid rgba(255,255,255,.08); }
  .status { min-width: 3rem; flex: 1; color: #79d99e; font-size: .67rem; text-align: right; }
  .editor-toolbar > button { width: 2rem; height: 1.75rem; border: 1px solid rgba(255,255,255,.1); border-radius: .48rem; color: #d0d0d3; }
  .editor-toolbar > button.save { color: #ffd44c; }
  .note-date { padding: 1rem 1.35rem .15rem; color: #77777b; font-size: .66rem; text-align: center; }
  .note-title { margin: .15rem 1.35rem 0; padding: 0; border: 0; outline: 0; color: #f3f3f4; background: transparent; font-size: 1.18rem; font-weight: 680; }
  textarea { min-height: 0; flex: 1; resize: none; padding: .75rem 1.35rem; border: 0; outline: 0; color: #ebebed; background: transparent; font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", sans-serif; font-size: .86rem; line-height: 1.58; }
  textarea::placeholder { color: #6e6e72; }
  footer { display: flex; justify-content: space-between; padding: .45rem .8rem; border-top: 1px solid rgba(255,255,255,.06); color: #77777b; font-size: .62rem; }

  @media (max-width: 760px) {
    .notes { grid-template-columns: 8rem 1fr; }
    .folder-sidebar { display: none; }
    .note-list { grid-column: 1; }
    .editor { grid-column: 2; }
  }

  @media (max-width: 520px) {
    .notes { display: block; }
    .note-list { display: none; }
    .editor { height: 100%; }
    .editor-toolbar { padding: 0 .9rem; background: rgba(47,47,50,.88); backdrop-filter: blur(24px); }
    .note-date { padding-top: 1.2rem; }
    .note-title { font-size: 1.35rem; }
    textarea { font-size: 1rem; }
  }
</style>
