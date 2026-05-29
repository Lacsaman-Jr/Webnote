<script setup lang="ts">
defineProps<{ username: string }>()
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">Terminal Home Hub</h1>
    <p class="view-tagline">Welcome back user node instance. System operating at baseline efficiency.</p>

    <div class="metrics-grid">
      <div class="panel-card metric-box">
        <div class="icon-ring">📡</div>
        <div>
          <h6>Session Latency</h6>
          <p class="m-val">12 ms</p>
        </div>
      </div>
      <div class="panel-card metric-box">
        <div class="icon-ring">🕸️</div>
        <div>
          <h6>Obsidian Backlinks</h6>
          <p class="m-val">4,198 Nodes</p>
        </div>
      </div>
      <div class="panel-card metric-box">
        <div class="icon-ring">👥</div>
        <div>
          <h6>Network Peers</h6>
          <p class="m-val">18 Online</p>
        </div>
      </div>
    </div>

    <div class="panel-card logs-console">
      <h5>Network Ledger Telemetry</h5>
      <div class="log-row">
        <span class="timestamp">[11:19:24]</span> <span class="message">Linked note index #241 to dynamic Social Profile.</span>
      </div>
      <div class="log-row">
        <span class="timestamp">[09:41:03]</span> <span class="message">Successfully re-cached structural vault diagrams.</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.metrics-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 16px; margin-bottom: 20px; }
.metric-box { display: flex; align-items: center; gap: 16px; margin-bottom: 0; }
.icon-ring { width: 44px; height: 44px; background: rgba(16, 185, 129, 0.08); border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 1.3rem; color: #10b981; }
.metric-box h6 { margin: 0; font-size: 0.75rem; color: #64748b; text-transform: uppercase; }
.m-val { margin: 2px 0 0 0; font-size: 1.3rem; font-weight: 700; color: #10b981; }
.logs-console h5 { margin: 0 0 14px 0; font-size: 0.9rem; color: #cbd5e1; }
.log-row { font-family: monospace; font-size: 0.8rem; padding: 6px 0; border-bottom: 1px solid rgba(16, 185, 129, 0.05); }
.timestamp { color: #10b981; padding-right: 8px; }
.message { color: #94a3b8; }
</style>