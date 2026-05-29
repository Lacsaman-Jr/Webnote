<script setup lang="ts">
import { ref } from 'vue'
import Home from './Dashboard/Home.vue'
import Profile from './Dashboard/Profile.vue'
import NewsFeed from './Dashboard/NewsFeed.vue'
import Messages from './Dashboard/Messages.vue'
import Lectures from './Dashboard/Lectures.vue'
import History from './Dashboard/History.vue'
import Settings from './Dashboard/Settings.vue'
import About from './Dashboard/About.vue'

defineProps<{ username: string }>()
const emit = defineEmits(['logout'])

const currentTab = ref('home')
const searchQuery = ref('')

const navItems = [
  { id: 'home', label: 'Home Node', icon: '🏠' },
  { id: 'profile', label: 'Profile Dossier', icon: '👤' },
  { id: 'newsfeed', label: 'Social Feed Stream', icon: '📰' },
  { id: 'messages', label: 'Secure Messages', icon: '💬' },
  { id: 'lectures', label: 'Lectures Vault', icon: '📚' },
  { id: 'history', label: 'History Graph', icon: '🐙' },
  { id: 'settings', label: 'System Settings', icon: '⚙️' },
  { id: 'about', label: 'About System', icon: 'ℹ️' },
]

const handleSearchSubmit = () => {
  if (searchQuery.value.trim()) {
    alert(`Searching workspace index & user vectors for: "${searchQuery.value}"`)
    searchQuery.value = ''
  }
}
</script>

<template>
  <div class="dashboard-container">
    <aside class="sidebar">
      
      <div class="search-module">
        <form @submit.prevent="handleSearchSubmit" class="search-wrapper">
          <span class="search-icon">🔍</span>
          <input 
            type="text" 
            v-model="searchQuery" 
            placeholder="Search users or nodes..." 
            autocomplete="off"
          />
        </form>
      </div>

      <div class="sidebar-header">
        <div class="logo-hex"><span class="logo-icon">🕸️</span></div>
        <div class="user-meta">
          <h3>{{ username || 'Agent_Core' }}</h3>
          <span class="status-indicator">Online Vector</span>
        </div>
      </div>

      <nav class="sidebar-nav">
        <button 
          v-for="item in navItems" 
          :key="item.id"
          :class="['nav-link', { active: currentTab === item.id }]"
          @click="currentTab = item.id"
        >
          <span class="nav-icon">{{ item.icon }}</span>
          <span class="nav-label">{{ item.label }}</span>
        </button>
      </nav>

      <div class="sidebar-footer">
        <button class="logout-btn" @click="emit('logout')">
          <span class="nav-icon">🚪</span> Terminate Session
        </button>
      </div>
    </aside>

    <main class="workspace-viewport">
      <div class="workspace-scroll-container">
        <Home v-if="currentTab === 'home'" :username="username" />
        <Profile v-else-if="currentTab === 'profile'" :username="username" />
        <NewsFeed v-else-if="currentTab === 'newsfeed'" />
        <Messages v-else-if="currentTab === 'messages'" />
        <Lectures v-else-if="currentTab === 'lectures'" />
        <History v-else-if="currentTab === 'history'" />
        <Settings v-else-if="currentTab === 'settings'" />
        <About v-else-if="currentTab === 'about'" />
      </div>
    </main>
  </div>
</template>

<style scoped>
.dashboard-container {
  display: flex;
  width: 100vw;
  height: 100vh;
  background-color: #000000;
  color: #ffffff;
  overflow: hidden;
}

.sidebar {
  width: 25vw;
  min-width: 280px;
  max-width: 350px;
  height: 100%;
  background: linear-gradient(180deg, #050505 0%, #021410 100%);
  border-right: 1px solid rgba(16, 185, 129, 0.15);
  display: flex;
  flex-direction: column;
  padding: 20px 16px;
  z-index: 30;
}

.search-module {
  margin-bottom: 20px;
}

.search-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.search-icon {
  position: absolute;
  left: 12px;
  font-size: 0.9rem;
  opacity: 0.6;
}

.search-wrapper input {
  width: 100%;
  background-color: rgba(0, 0, 0, 0.6);
  border: 1px solid rgba(16, 185, 129, 0.25);
  border-radius: 8px;
  padding: 10px 12px 10px 36px;
  font-size: 0.9rem;
  color: #ffffff;
  outline: none;
  transition: all 0.25s ease;
}

.search-wrapper input:focus {
  border-color: #10b981;
  background-color: rgba(2, 44, 34, 0.2);
  box-shadow: 0 0 10px rgba(16, 185, 129, 0.2);
}

.sidebar-header {
  display: flex;
  align-items: center;
  gap: 12px;
  padding-bottom: 16px;
  border-bottom: 1px solid rgba(16, 185, 129, 0.1);
}

.logo-hex {
  width: 38px;
  height: 38px;
  background: #022c22;
  border: 1px solid #10b981;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 0 8px rgba(16, 185, 129, 0.2);
}

.logo-icon { font-size: 1.1rem; }

.user-meta h3 {
  margin: 0;
  font-size: 0.95rem;
  color: #ffffff;
  letter-spacing: -0.3px;
}

.status-indicator {
  font-size: 0.7rem;
  color: #10b981;
  display: flex;
  align-items: center;
  gap: 4px;
}

.status-indicator::before {
  content: '';
  display: inline-block;
  width: 6px;
  height: 6px;
  background-color: #10b981;
  border-radius: 50%;
  box-shadow: 0 0 8px #10b981;
}

.sidebar-nav {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-top: 16px;
  overflow-y: auto;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 11px 14px;
  background: transparent;
  border: 1px solid transparent;
  border-radius: 8px;
  color: #94a3b8;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  text-align: left;
  transition: all 0.2s ease;
}

.nav-link:hover {
  background: rgba(16, 185, 129, 0.04);
  color: #34d399;
}

.nav-link.active {
  background: rgba(4, 120, 87, 0.15);
  border-color: rgba(16, 185, 129, 0.25);
  color: #10b981;
}

.sidebar-footer {
  padding-top: 14px;
  border-top: 1px solid rgba(16, 185, 129, 0.1);
}

.logout-btn {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 14px;
  background: rgba(220, 38, 38, 0.05);
  border: 1px solid rgba(220, 38, 38, 0.15);
  border-radius: 8px;
  color: #fca5a5;
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.2s;
}

.logout-btn:hover {
  background: rgba(220, 38, 38, 0.12);
  border-color: rgba(220, 38, 38, 0.3);
}

.workspace-viewport {
  flex: 1;
  height: 100%;
  position: relative;
  background: radial-gradient(circle at 80% 20%, rgba(2, 44, 34, 0.2) 0%, rgba(0, 0, 0, 0) 65%);
}

.workspace-scroll-container {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  padding: 35px;
}

:deep(.view-container) {
  max-width: 950px;
  margin: 0 auto;
  animation: view-entry 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

:deep(.view-title) {
  font-size: 1.8rem;
  font-weight: 800;
  margin: 0 0 6px 0;
  background: linear-gradient(180deg, #ffffff 40%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

:deep(.view-tagline) {
  color: #64748b;
  margin: 0 0 28px 0;
  font-size: 0.9rem;
}

:deep(.panel-card) {
  background: rgba(8, 8, 8, 0.65);
  border: 1px solid rgba(16, 185, 129, 0.14);
  border-radius: 12px;
  padding: 22px;
  backdrop-filter: blur(12px);
  margin-bottom: 20px;
}

@keyframes view-entry {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>