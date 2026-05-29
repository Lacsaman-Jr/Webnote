<script setup lang="ts">
import { ref } from 'vue'

const files = ref([
  { title: 'Intro to Graph Knowledge Bases.md', folder: 'Vault/Core', size: '14 KB' },
  { title: 'Data Relations & System Backlinks.md', folder: 'Vault/Advanced', size: '28 KB' },
  { title: 'Local Storage Sync Pipelines.md', folder: 'Vault/Protocols', size: '9 KB' }
])

const activeFile = ref('Intro to Graph Knowledge Bases.md')
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">Lectures Vault Pane</h1>
    <p class="view-tagline">Obsidian markdown style explorer panel tracking document structures.</p>

    <div class="vault-grid-shell">
      <div class="panel-card file-tree-card">
        <h6>Vault Directory</h6>
        <div v-for="f in files" :key="f.title" :class="['file-item', { selected: activeFile === f.title }]" @click="activeFile = f.title">
          <span class="f-icon">📄</span>
          <div class="f-info">
            <p class="f-title">{{ f.title }}</p>
            <span class="f-meta">{{ f.folder }} • {{ f.size }}</span>
          </div>
        </div>
      </div>

      <div class="panel-card markdown-content-card">
        <div class="md-header">Markdown Node Preview: <span>{{ activeFile }}</span></div>
        <div class="md-body">
          <h3># Dynamic Document Mapping Parameters</h3>
          <p>This tutorial details setup criteria. All elements interact in bidirectional sync models. Ensure file targets correlate with active database parameters.</p>
          <blockquote>
            **System Context Note**: Verify structural connections using localized tracking nodes.
          </blockquote>
          <p>Backlinks tracking array mapped successfully via client environment buffers.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.vault-grid-shell { display: grid; grid-template-columns: 260px 1fr; gap: 16px; }
.file-tree-card { margin-bottom: 0; padding: 14px; display: flex; flex-direction: column; gap: 8px; }
.file-tree-card h6 { margin: 0 0 10px 0; font-size: 0.75rem; text-transform: uppercase; color: #64748b; letter-spacing: 0.5px; }
.file-item { display: flex; gap: 10px; align-items: flex-start; padding: 8px; border-radius: 6px; cursor: pointer; transition: background 0.2s; }
.file-item:hover { background: rgba(16, 185, 129, 0.04); }
.file-item.selected { background: rgba(4, 120, 87, 0.12); border: 1px solid rgba(16, 185, 129, 0.15); }
.f-icon { font-size: 1rem; }
.f-title { margin: 0; font-size: 0.8rem; font-weight: 600; color: #cbd5e1; word-break: break-all; }
.f-meta { font-size: 0.7rem; color: #64748b; font-family: monospace; }

.markdown-content-card { margin-bottom: 0; display: flex; flex-direction: column; }
.md-header { font-family: monospace; font-size: 0.75rem; color: #64748b; padding-bottom: 12px; border-bottom: 1px solid rgba(16, 185, 129, 0.1); margin-bottom: 16px; }
.md-header span { color: #10b981; }
.md-body h3 { margin: 0 0 12px 0; font-size: 1.2rem; color: #ffffff; }
.md-body p { font-size: 0.9rem; color: #cbd5e1; line-height: 1.6; margin: 0 0 14px 0; }
.md-body blockquote { margin: 0 0 14px 0; padding: 8px 12px; background: rgba(16, 185, 129, 0.04); border-left: 2px solid #10b981; font-style: italic; font-size: 0.85rem; color: #a7f3d0; }
</style>