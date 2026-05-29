<script setup lang="ts">
// Generate a grid layout arrays block to emulate Github contribution blocks tracking Obsidian notes history activity logs
const gridBlocks = Array.from({ length: 96 }, (_, index) => {
  const intensities = [0, 1, 2, 3]
  const randomIntensity = intensities[Math.floor(Math.random() * intensities.length)]
  return { id: index, level: randomIntensity }
})
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">Vault History Graph</h1>
    <p class="view-tagline">GitHub-style submission tracking matrix monitoring active Obsidian file changes and sync points.</p>

    <div class="panel-card history-matrix-card">
      <h5>Markdown Commit Matrix Activity Logs</h5>
      
      <div class="commit-grid">
        <div 
          v-for="block in gridBlocks" 
          :key="block.id" 
          :class="['grid-cell', `intensity-${block.level}`]"
          :title="`Intensity level: ${block.level} index alterations`
        "></div>
      </div>

      <div class="grid-legend">
        <span>Fewer adjustments</span>
        <div class="grid-cell intensity-0"></div>
        <div class="grid-cell intensity-1"></div>
        <div class="grid-cell intensity-2"></div>
        <div class="grid-cell intensity-3"></div>
        <span>More adjustments</span>
      </div>
    </div>

    <div class="panel-card sync-details-list">
      <h3>Recent Matrix Ledger Sync Blocks</h3>
      <div class="ledger-row">
        <p class="l-title">vault://commit-e9812bf7</p>
        <span class="l-badge">14 additions</span>
      </div>
      <div class="ledger-row">
        <p class="l-title">vault://commit-a4192d1c</p>
        <span class="l-badge">3 additions</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.history-matrix-card h5 { margin: 0 0 16px 0; font-size: 0.9rem; color: #cbd5e1; }
.commit-grid { display: grid; grid-template-columns: repeat(16, 1fr); gap: 5px; max-width: 420px; margin-bottom: 14px; }
.grid-cell { aspect-ratio: 1; border-radius: 2px; background-color: rgba(255, 255, 255, 0.04); }

/* Intensity matrices matching cyber layout accents */
.grid-cell.intensity-0 { background-color: rgba(16, 185, 129, 0.05); }
.grid-cell.intensity-1 { background-color: rgba(4, 120, 87, 0.3); }
.grid-cell.intensity-2 { background-color: rgba(5, 150, 105, 0.6); }
.grid-cell.intensity-3 { background-color: #10b981; box-shadow: 0 0 8px rgba(16, 185, 129, 0.4); }

.grid-legend { display: flex; align-items: center; gap: 6px; font-size: 0.75rem; color: #64748b; }
.grid-legend .grid-cell { width: 10px; height: 10px; }

.sync-details-list h3 { margin: 0 0 12px 0; font-size: 1.1rem; }
.ledger-row { display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid rgba(16, 185, 129, 0.05); font-family: monospace; font-size: 0.8rem; }
.l-title { margin: 0; color: #cbd5e1; }
.l-badge { color: #10b981; background: rgba(16, 185, 129, 0.08); padding: 2px 6px; border-radius: 4px; }
</style>