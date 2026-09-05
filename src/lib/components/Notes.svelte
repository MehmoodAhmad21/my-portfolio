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
  <div class="toolbar">
    <input bind:value={fileName} aria-label="Note filename" />
    <span class="status">{saveStatus}</span>
    <button on:click={newNote}>New</button>
    <button class="save" on:click={downloadNote}>Save note</button>
  </div>
  <textarea bind:value={noteContent} aria-label="Note" placeholder={'Quick thought...\n\nThis little app works, too. Write a note and save it as a real text file.'} spellcheck="true"></textarea>
  <footer><span>{noteContent.trim() ? noteContent.trim().split(/\s+/).length : 0} words</span><span>{noteContent.length} characters</span></footer>
</div>

<style>
  .notes { display: flex; height: 100%; flex-direction: column; color: #16202b; background: linear-gradient(145deg, rgba(255,252,233,.98), rgba(245,237,195,.96)); }
  .toolbar { display: flex; align-items: center; gap: .5rem; padding: .75rem; border-bottom: 1px solid rgba(77,61,15,.13); background: rgba(255,255,255,.34); }
  input { min-width: 0; flex: 1; padding: .48rem .65rem; border: 1px solid rgba(68,54,14,.15); border-radius: .5rem; outline: 0; background: rgba(255,255,255,.4); font-size: .76rem; }
  input:focus { border-color: rgba(137,101,0,.45); box-shadow: 0 0 0 3px rgba(255,205,68,.2); }
  .status { min-width: 3rem; color: #5c7f54; font-size: .68rem; text-align: right; }
  button { padding: .48rem .68rem; border: 1px solid rgba(68,54,14,.14); border-radius: .48rem; color: #32270e; background: rgba(255,255,255,.46); font-size: .7rem; font-weight: 700; cursor: pointer; }
  button.save { border-color: #e1b637; background: #f7ce55; }
  textarea { flex: 1; resize: none; border: 0; outline: 0; padding: clamp(1.25rem, 4vw, 2.2rem); color: #2c2c28; background: repeating-linear-gradient(180deg, transparent 0, transparent 30px, rgba(119,94,22,.09) 31px); font-family: ui-serif, Georgia, serif; font-size: 1rem; line-height: 31px; box-shadow: none; }
  textarea::placeholder { color: rgba(52,47,32,.38); }
  footer { display: flex; justify-content: space-between; padding: .45rem .9rem; border-top: 1px solid rgba(77,61,15,.1); color: rgba(55,49,30,.52); background: rgba(255,255,255,.28); font-family: ui-monospace, monospace; font-size: .62rem; }
</style>
