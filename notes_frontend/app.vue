<template>
  <div class="app-root">
    <HeaderBar class="header" />
    <div class="body-container">
      <Sidebar
        class="sidebar"
        :notes="notes"
        :selected-id="selectedNoteId"
        :search="search"
        @select="handleSelect"
        @create="handleCreate"
        @search="search = $event"
        @delete="handleDelete"
      />
      <main class="main-panel">
        <NoteEditor
          v-if="activeNote"
          :note="activeNote"
          @update="handleUpdate"
        />
        <div v-else class="no-note">
          <span>Select a note to view or edit</span>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import HeaderBar from '~/components/HeaderBar.vue'
import Sidebar from '~/components/Sidebar.vue'
import NoteEditor from '~/components/NoteEditor.vue'

interface Note {
  id: string;
  title: string;
  content: string;
  updated: number;
}

const STORAGE_KEY = 'notesapp-v1'

const notes = ref<Note[]>([])
const selectedNoteId = ref<string | null>(null)
const search = ref('')
const filterNotes = computed(() =>
  search.value.trim()
    ? notes.value.filter(n =>
        n.title.toLowerCase().includes(search.value.toLowerCase()) ||
        n.content.toLowerCase().includes(search.value.toLowerCase()))
    : notes.value
)
const activeNote = computed(
  () => notes.value.find(n => n.id === selectedNoteId.value) ?? null,
)

// Load notes from local storage on mounted
if (process.client) {
  const raw = localStorage.getItem(STORAGE_KEY)
  if (raw) {
    notes.value = JSON.parse(raw)
  }
}

watch(
  notes,
  (val) => {
    if (process.client) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(val))
    }
  },
  { deep: true }
)

// PUBLIC_INTERFACE
function handleCreate() {
  const id = 'note-' + Math.random().toString(36).slice(2)
  const newNote: Note = { id, title: 'Untitled', content: '', updated: Date.now() }
  // Use a new array to ensure Vue's reactivity properly propagates changes to child components
  notes.value = [newNote, ...notes.value]
  selectedNoteId.value = id
}

// PUBLIC_INTERFACE
function handleSelect(id: string) {
  selectedNoteId.value = id
}

// PUBLIC_INTERFACE
function handleDelete(id: string) {
  const idx = notes.value.findIndex(n => n.id === id)
  if (idx !== -1) {
    notes.value.splice(idx, 1)
    if (selectedNoteId.value === id) selectedNoteId.value = null
  }
}

// PUBLIC_INTERFACE
function handleUpdate(updatedNote: Note) {
  const idx = notes.value.findIndex(n => n.id === updatedNote.id)
  if (idx !== -1) {
    notes.value[idx] = { ...updatedNote, updated: Date.now() }
    notes.value = [...notes.value] // trigger reactivity
  }
}
</script>

<style scoped>
:root {
  --color-primary: #4F8A8B;
  --color-secondary: #FBD46D;
  --color-accent: #F67280;
  --color-bg: #f7f7fa;
  --color-text: #232323;
  --header-height: 56px;
  --sidebar-width: 270px;
}
.app-root {
  min-height: 100vh;
  background: var(--color-bg);
  color: var(--color-text);
  display: flex;
  flex-direction: column;
}
.header {
  height: var(--header-height);
  z-index: 40;
}
.body-container {
  display: flex;
  flex: 1;
  min-height: 0;
}
.sidebar {
  width: var(--sidebar-width);
  min-width: 180px;
  background: #fff;
  border-right: 1px solid #eee;
  padding: 0;
}
.main-panel {
  flex: 1;
  background: var(--color-bg);
  padding: 0;
  min-width: 0;
  overflow-y: auto;
  display: flex;
  justify-content: center;
  align-items: stretch;
}
.no-note {
  width: 100%;
  margin: auto;
  text-align: center;
  font-size: 1.2rem;
  color: #999;
  opacity: 0.7;
}
@media (max-width: 800px) {
  .body-container {
    flex-direction: column;
  }
  .sidebar {
    width: 100vw !important;
    min-width: 0;
    max-height: 35vh;
    border-right: none;
    border-bottom: 1px solid #eee;
  }
  .main-panel {
    min-height: 50vh;
    max-height: 65vh;
    padding: 0 8px;
  }
}
</style>
