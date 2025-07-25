<template>
  <nav class="header-bar">
    <span class="brand">
      <span class="icon" />
      Notes<span class="highlight">App</span>
    </span>
  </nav>
</template>

<script setup lang="ts">
// HeaderBar is purely visual for branding.
</script>

<style scoped>
.header-bar {
  display: flex;
  align-items: center;
  height: var(--header-height, 56px);
  padding: 0 1.5rem;
  background: var(--color-primary);
  color: #fff;
  box-shadow: 0 2px 4px 0 #0001;
  font-weight: 600;
  letter-spacing: 0.5px;
}
.brand {
  font-weight: bold;
  font-size: 1.32rem;
  letter-spacing: 0.5px;
  display: flex;
  align-items: center;
}
.icon {
  width: 1.3em;
  height: 1.3em;
  margin-right: 0.3em;
  border-radius: 4px;
  background: var(--color-secondary);
  display: inline-block;
  box-shadow: 0 0 6px 0 #fdc82b33;
}
.highlight {
  color: var(--color-secondary);
  margin-left: 2px;
  font-weight: 700;
}
</style>
