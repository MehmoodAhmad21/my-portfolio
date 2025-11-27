<script lang="ts">
  let noteContent = '';
  let fileName = 'untitled.txt';
  let saveStatus = '';

  function downloadNote() {
    if (!noteContent.trim()) {
      saveStatus = 'Cannot save empty note';
      setTimeout(() => saveStatus = '', 2000);
      return;
    }

    const blob = new Blob([noteContent], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = fileName;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(url);
    
    saveStatus = `Saved as ${fileName}`;
    setTimeout(() => saveStatus = '', 3000);
  }

  function newNote() {
    if (noteContent.trim() && !confirm('Clear current note? Unsaved changes will be lost.')) {
      return;
    }
    noteContent = '';
    fileName = 'untitled.txt';
    saveStatus = '';
  }
</script>

<div class="h-full flex flex-col bg-gradient-to-br from-yellow-50 to-yellow-100 text-gray-900 rounded-lg overflow-hidden">
  <!-- Toolbar -->
  <div class="bg-yellow-200/50 border-b border-yellow-300 px-4 py-2 flex items-center justify-between">
    <div class="flex items-center gap-2">
      <input
        bind:value={fileName}
        class="bg-white/50 border border-yellow-300 rounded px-2 py-1 text-sm focus:outline-none focus:ring-2 focus:ring-yellow-400"
        placeholder="filename.txt"
      />
    </div>
    
    <div class="flex items-center gap-2">
      {#if saveStatus}
        <span class="text-sm text-green-700">{saveStatus}</span>
      {/if}
      <button
        on:click={newNote}
        class="px-3 py-1 bg-white/50 hover:bg-white/80 border border-yellow-300 rounded text-sm font-medium transition-colors"
      >
        New
      </button>
      <button
        on:click={downloadNote}
        class="px-3 py-1 bg-yellow-400 hover:bg-yellow-500 rounded text-sm font-medium transition-colors"
      >
        💾 Save
      </button>
    </div>
  </div>

  <!-- Text Editor -->
  <div class="flex-1 p-4 overflow-y-auto">
    <textarea
      bind:value={noteContent}
      class="w-full h-full bg-transparent resize-none outline-none font-serif text-base leading-relaxed"
      placeholder="Start typing your note here..."
      spellcheck="true"
    ></textarea>
  </div>

  <!-- Footer with stats -->
  <div class="bg-yellow-200/30 border-t border-yellow-300 px-4 py-1 text-xs text-gray-600 flex justify-between">
    <span>Characters: {noteContent.length}</span>
    <span>Words: {noteContent.trim() ? noteContent.trim().split(/\s+/).length : 0}</span>
    <span>Lines: {noteContent ? noteContent.split('\n').length : 1}</span>
  </div>
</div>

<style>
  textarea {
    font-family: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  }
  
  textarea::placeholder {
    color: rgba(0, 0, 0, 0.3);
  }
</style>

