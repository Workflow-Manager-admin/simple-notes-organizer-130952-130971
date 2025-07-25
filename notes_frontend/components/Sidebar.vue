<template>
  <aside class="sidebar-comp">
    <div class="sidebar-top">
      <input
        v-model="searchQuery"
        class="search-bar"
        type="search"
        placeholder="Search notes..."
        @input="emitSearch"
        aria-label="Search notes"
      />
      <button class="create-btn" @click="$emit('create')">
        <span class="create-icon">＋</span> New
      </button>
    </div>
    <ul class="notes-list">
      <li
        v-for="note in filteredNotes"
        :key="note.id"
        :class="['note-list-item', { active: note.id === selectedId }]"
        @click="select(note.id)"
      >
        <div class="note-info">
          <span class="note-title">{{ note.title || 'Untitled' }}</span>
          <span class="note-date" v-if="note.updated">
            {{ formattedDate(note.updated) }}
          </span>
        </div>
        <button
          class="delete-btn"
          @click.stop="deleteNote(note.id)"
          aria-label="Delete note"
        >
          🗑
        </button>
      </li>
    </ul>
  </aside>
</template>

<script setup lang="ts">
import { ref, watch, computed } from 'vue'
const props = defineProps<{
  notes: any[]
  selectedId: string | null
  search: string
}>()
const emit = defineEmits(['select', 'create', 'search', 'delete'])

const searchQuery = ref(props.search)

watch(
  () => props.search,
  v => {
    if (searchQuery.value !== v) searchQuery.value = v
  }
)
function emitSearch() {
  emit('search', searchQuery.value)
}
const filteredNotes = computed(() => props.notes)

function select(id: string) {
  emit('select', id)
}
function deleteNote(id: string) {
  emit('delete', id)
}
function formattedDate(ts: number) {
  const d = new Date(ts)
  return (
    d.toLocaleDateString(undefined, { month: 'short', day: 'numeric' }) +
    ' ' +
    d.toLocaleTimeString(undefined, { hour: '2-digit', minute: '2-digit' })
  )
}
</script>

<style scoped>
.sidebar-comp {
  width: 100%;
  height: 100%;
  background: #fff;
  padding: 0.8rem 0.7rem 0.3rem 0.7rem;
  display: flex;
  flex-direction: column;
  min-height: 0;
  border-right: 1px solid #ededed;
}
.sidebar-top {
  display: flex;
  gap: 0.5em;
  margin-bottom: 0.6rem;
}
.search-bar {
  flex: 1;
  font-size: 1rem;
  border: 1px solid #e3e3e8;
  padding: 0.45em 0.8em;
  border-radius: 5px;
  background: #f5f5f8;
  color: #32323d;
  transition: border 0.25s;
}
.search-bar:focus {
  border-color: var(--color-primary);
  outline: none;
}
.create-btn {
  padding: 0.4em 1.1em;
  background: var(--color-primary);
  color: #fff;
  border: none;
  border-radius: 5px;
  font-weight: 500;
  cursor: pointer;
  box-shadow: 0 0 3px #4f8a8b22;
  white-space: nowrap;
}
.create-icon {
  font-size: 1.1em;
  margin-right: 0.2em;
}
.notes-list {
  flex: 1;
  list-style: none;
  margin: 0;
  padding: 0 0.1em 0 0;
  overflow-y: auto;
}
.note-list-item {
  padding: 0.69em 0.5em 0.6em 0.8em;
  margin-bottom: 2px;
  border-radius: 5px;
  cursor: pointer;
  display: flex;
  align-items: center;
  background: none;
  border: none;
  transition: background 0.15s;
  position: relative;
}
.note-list-item.active,
.note-list-item:hover {
  background: #f7fafd;
}
.note-list-item.active {
  border-left: 4px solid var(--color-accent);
  background: #fcf8fa;
}
.note-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 2px;
}
.note-title {
  font-weight: 600;
  color: #2b2c2e;
  font-size: 1.08em;
  line-height: 1.18;
  word-break: break-word;
}
.note-date {
  color: #9da2ab;
  font-size: 0.92em;
}
.delete-btn {
  background: none;
  border: none;
  color: #cfcfd3;
  cursor: pointer;
  font-size: 1.05em;
  margin-left: 0.35em;
  transition: color 0.16s;
}
.delete-btn:hover {
  color: var(--color-accent);
}
</style>
