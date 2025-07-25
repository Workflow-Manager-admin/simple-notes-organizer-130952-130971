<template>
  <section class="note-editor">
    <input
      class="note-title"
      v-model="draft.title"
      placeholder="Title"
      @input="onInput"
      aria-label="Note Title"
      maxlength="100"
    />
    <textarea
      class="note-content"
      v-model="draft.content"
      placeholder="Start writing your note..."
      @input="onInput"
      rows="16"
      aria-label="Note Content"
    ></textarea>
    <div class="save-indicator" v-if="saving">
      Saving...
    </div>
    <div class="updated-at">
      {{ formattedUpdated }}
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, watch, computed } from 'vue'
const props = defineProps<{
  note: {
    id: string,
    title: string,
    content: string,
    updated: number
  }
}>()
const emit = defineEmits(['update'])
const draft = ref({ ...props.note })
const saving = ref(false)

watch(
  () => props.note,
  (n) => (draft.value = { ...n })
)

let saveTimeout: ReturnType<typeof setTimeout> | null = null
function onInput() {
  if (saveTimeout) clearTimeout(saveTimeout)
  saving.value = true
  saveTimeout = setTimeout(() => {
    emit('update', {
      ...draft.value,
      updated: Date.now()
    })
    saving.value = false
  }, 400)
}

const formattedUpdated = computed(() => {
  return draft.value.updated
    ? 'Last edited ' + new Date(draft.value.updated).toLocaleString()
    : ''
})
</script>

<style scoped>
.note-editor {
  margin: 36px auto 0 auto;
  width: 100%;
  max-width: 720px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 0 7px 0 #5f8b8b0e;
  display: flex;
  flex-direction: column;
  padding: 2rem 2.5rem 2rem 2.3rem;
  min-height: 450px;
  position: relative;
}
.note-title {
  border: none;
  outline: none;
  font-size: 2.1rem;
  font-weight: 700;
  color: var(--color-primary);
  background: none;
  margin-bottom: 1.1rem;
  transition: color 0.2s;
  padding: 0;
  width: 100%;
  word-break: break-word;
  caret-color: var(--color-accent);
}
.note-title:focus {
  color: var(--color-accent);
}
.note-content {
  border: none;
  outline: none;
  width: 100%;
  font-size: 1.17rem;
  min-height: 330px;
  resize: vertical;
  background: none;
  color: var(--color-text);
  line-height: 1.54;
  margin-bottom: 0.8rem;
  padding: 0;
  caret-color: var(--color-primary);
}
.save-indicator {
  color: var(--color-accent);
  font-size: 1em;
  position: absolute;
  top: 1.1em;
  right: 1.5em;
  opacity: 0.85;
}
.updated-at {
  color: #adb5be;
  font-size: 0.98em;
  margin-top: 0.8rem;
  text-align: right;
  letter-spacing: 0.05em;
}
@media (max-width: 800px) {
  .note-editor {
    max-width: 98vw;
    padding: 1.4rem 0.7rem;
    min-height: 310px;
  }
  .note-title {
    font-size: 1.35rem;
  }
  .note-content {
    font-size: 1.02rem;
    min-height: 180px;
  }
}
</style>
