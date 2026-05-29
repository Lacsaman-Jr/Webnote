<script setup lang="ts">
import { ref } from 'vue'

const channels = ref(['Global Vault Matrix', 'Network_Support', 'Architect_Vance'])
const currentChannel = ref('Global Vault Matrix')
const textInput = ref('')
const streams = ref([
  { from: 'Architect_Vance', message: 'Ensure your dynamic vault linkages are parsed correctly.' },
  { from: 'System', message: 'Session terminal instance linked correctly via socket pipeline.' }
])

const dispatchPacket = () => {
  if (!textInput.value.trim()) return
  streams.value.push({ from: 'You', message: textInput.value })
  textInput.value = ''
}
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">Communications Hub</h1>
    <p class="view-tagline">Secure encrypted messaging pipelines connecting server instances.</p>

    <div class="comms-panel-shell">
      <div class="channels-column">
        <h6>Active Pipelines</h6>
        <button 
          v-for="ch in channels" 
          :key="ch" 
          :class="['ch-button', { active: currentChannel === ch }]"
          @click="currentChannel = ch"
        >
          ⚡ {{ ch }}
        </button>
      </div>

      <div class="chat-viewport-pane">
        <div class="pane-banner">Target: #{{ currentChannel }}</div>
        <div class="messages-scroll-well">
          <div v-for="(msg, index) in streams" :key="index" :class="['bubble-wrap', { outbound: msg.from === 'You' }]">
            <span class="user-label">{{ msg.from }}</span>
            <p class="txt-content">{{ msg.message }}</p>
          </div>
        </div>
        <div class="pane-input-tray">
          <input v-model="textInput" placeholder="Transmit messaging sequence packet..." @keyup.enter="dispatchPacket" />
          <button @click="dispatchPacket">Send</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.comms-panel-shell { display: grid; grid-template-columns: 200px 1fr; gap: 16px; height: 450px; background: rgba(5, 5, 5, 0.5); border: 1px solid rgba(16, 185, 129, 0.15); border-radius: 12px; overflow: hidden; }
.channels-column { background: rgba(0, 0, 0, 0.4); border-right: 1px solid rgba(16, 185, 129, 0.1); padding: 14px; display: flex; flex-direction: column; gap: 6px; }
.channels-column h6 { margin: 0 0 10px 0; font-size: 0.75rem; text-transform: uppercase; color: #64748b; letter-spacing: 0.5px; }
.ch-button { width: 100%; text-align: left; background: transparent; border: none; padding: 8px 10px; border-radius: 6px; color: #cbd5e1; font-size: 0.85rem; font-weight: 600; cursor: pointer; white-space: nowrap; text-overflow: ellipsis; overflow: hidden; }
.ch-button:hover { background: rgba(16, 185, 129, 0.04); color: #10b981; }
.ch-button.active { background: rgba(4, 120, 87, 0.15); border: 1px solid rgba(16, 185, 129, 0.2); color: #10b981; }

.chat-viewport-pane { display: flex; flex-direction: column; height: 100%; }
.pane-banner { padding: 10px 16px; background: rgba(16, 185, 129, 0.03); border-bottom: 1px solid rgba(16, 185, 129, 0.1); font-family: monospace; font-size: 0.8rem; color: #10b981; font-weight: bold; }
.messages-scroll-well { flex: 1; padding: 16px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; background: rgba(0, 0, 0, 0.1); }
.bubble-wrap { align-self: flex-start; max-width: 80%; background: rgba(255, 255, 255, 0.04); border: 1px solid rgba(255, 255, 255, 0.03); padding: 8px 12px; border-radius: 8px; }
.bubble-wrap.outbound { align-self: flex-end; background: rgba(4, 120, 87, 0.12); border-color: rgba(16, 185, 129, 0.2); }
.user-label { display: block; font-size: 0.7rem; font-family: monospace; color: #10b981; margin-bottom: 2px; }
.bubble-wrap.outbound .user-label { color: #34d399; text-align: right; }
.txt-content { margin: 0; font-size: 0.85rem; color: #e2e8f0; line-height: 1.4; }
.pane-input-tray { display: flex; border-top: 1px solid rgba(16, 185, 129, 0.1); padding: 10px; background: #000; }
.pane-input-tray input { flex: 1; background: transparent; border: none; padding: 6px 10px; color: #fff; font-size: 0.9rem; outline: none; }
.pane-input-tray button { background: #047857; border: none; color: #fff; padding: 0 14px; border-radius: 6px; font-weight: 600; font-size: 0.8rem; cursor: pointer; }
</style>