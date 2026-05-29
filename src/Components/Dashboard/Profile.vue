<script setup lang="ts">
defineProps<{ username: string }>()
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">User Vector Card</h1>
    <p class="view-tagline">Manage details, bio nodes, and network identity metadata references.</p>

    <div class="panel-card profile-hero">
      <div class="cyber-banner"></div>
      <div class="user-profile-row">
        <div class="avatar-block">👤</div>
        <div class="meta-block">
          <h2>{{ username }}</h2>
          <p>Index Hash: <span class="hash-txt">@{{ username.toLowerCase().replace(/\s+/g, '_') }}</span></p>
        </div>
      </div>
    </div>

    <div class="panel-card info-panel">
      <h3>Vault Bio Node</h3>
      <p class="bio-text">
        Structuring social ecosystems down into modular Obsidian graphs. Currently establishing bidirectional linkages between data systems and student terminal streams.
      </p>
    </div>
  </div>
</template>

<style scoped>
.profile-hero { padding: 0; overflow: hidden; }
.cyber-banner { height: 130px; background: linear-gradient(90deg, #022c22 0%, #047857 100%); position: relative; }
.user-profile-row { display: flex; align-items: flex-end; gap: 16px; padding: 0 20px 20px 20px; margin-top: -30px; }
.avatar-block { width: 80px; height: 80px; background: #000; border: 2px solid #10b981; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 2.2rem; box-shadow: 0 4px 12px rgba(0,0,0,0.6); }
.meta-block h2 { margin: 0 0 2px 0; font-size: 1.3rem; }
.meta-block p { margin: 0; font-size: 0.85rem; color: #64748b; }
.hash-txt { color: #10b981; font-family: monospace; }
.info-panel h3 { margin: 0 0 10px 0; font-size: 1.1rem; color: #ffffff; }
.bio-text { margin: 0; color: #cbd5e1; font-size: 0.9rem; line-height: 1.6; }
</style>