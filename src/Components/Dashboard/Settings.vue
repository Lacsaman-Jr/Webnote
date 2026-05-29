<script setup lang="ts">
import { ref } from 'vue'

const enforceEncryption = ref(true)
const matrixGlow = ref(true)
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">System Settings</h1>
    <p class="view-tagline">Adjust terminal flags, display constraints, and user preference flags.</p>

    <div class="panel-card options-panel">
      <h3>Terminal Interface Directives</h3>
      
      <div class="toggle-control-row">
        <div>
          <p class="opt-label">Enforce Hardware Package Encryption</p>
          <span>Encrypt user text packets dispatched down to external socket streams.</span>
        </div>
        <input type="checkbox" v-model="enforceEncryption" />
      </div>

      <div class="toggle-control-row">
        <div>
          <p class="opt-label">Enable Ambient Matrix Glow Elements</p>
          <span>Render backing background vector designs and glowing canvas shaders.</span>
        </div>
        <input type="checkbox" v-model="matrixGlow" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.options-panel h3 { margin: 0 0 16px 0; font-size: 1.1rem; }
.toggle-control-row { display: flex; justify-content: space-between; align-items: center; padding: 14px 0; border-bottom: 1px solid rgba(16, 185, 129, 0.05); }
.toggle-control-row:last-of-type { border-bottom: none; }
.opt-label { margin: 0; font-size: 0.9rem; font-weight: 600; color: #ffffff; }
.toggle-control-row span { font-size: 0.75rem; color: #64748b; display: block; margin-top: 2px; }
input[type="checkbox"] { width: 16px; height: 16px; accent-color: #10b981; cursor: pointer; }
</style>