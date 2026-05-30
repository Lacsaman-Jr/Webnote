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
const isSidebarOpen = ref(true)

const navItems = [
  { id: 'home', label: 'Home', icon: '🏠' },
  { id: 'profile', label: 'Profile', icon: '👤' },
  { id: 'newsfeed', label: 'News feed', icon: '📰' },
  { id: 'messages', label: 'Messages', icon: '💬' },
  { id: 'lectures', label: 'Lectures', icon: '📚' },
  { id: 'history', label: 'History', icon: '🐙' },
  { id: 'settings', label: 'Settings', icon: '⚙️' },
  { id: 'about', label: 'About', icon: 'ℹ️' },
]

const handleSearchSubmit = () => {
  if (searchQuery.value.trim()) {
    alert(`Searching workspace index for: ${searchQuery.value}`)
  }
}
</script>

<template>
  <div class="dashboard-layout">
    
    <aside class="sidebar" :class="{ 'is-closed': !isSidebarOpen }">
      <div class="sidebar-header">
        <div class="logo-icon">💠</div>
        <h2 class="sidebar-title" v-show="isSidebarOpen">Knowledge Web</h2>
      </div>

      <nav class="sidebar-nav">
        <button 
          v-for="item in navItems" 
          :key="item.id"
          :class="['nav-btn', { active: currentTab === item.id }]"
          @click="currentTab = item.id"
          :title="!isSidebarOpen ? item.label : ''"
        >
          <span class="nav-icon">{{ item.icon }}</span>
          <span class="nav-label" v-show="isSidebarOpen">{{ item.label }}</span>
        </button>
      </nav>

      <div class="sidebar-footer">
        <button class="logout-btn" @click="emit('logout')" :title="!isSidebarOpen ? 'Disconnect' : ''">
          <span class="logout-icon">🚪</span>
          <span v-show="isSidebarOpen">Logout</span>
        </button>
      </div>
    </aside>

    <main class="workspace-main">
      
      <header class="workspace-header">
        <button class="toggle-sidebar-btn" @click="isSidebarOpen = !isSidebarOpen">
          <span class="toggle-icon">{{ isSidebarOpen ? '◀' : '▶' }}</span>
          <span class="toggle-text">{{ isSidebarOpen ? 'Hide Menu' : 'Show Menu' }}</span>
        </button>

        <div class="search-container">
          <input 
            type="text" 
            v-model="searchQuery" 
            @keyup.enter="handleSearchSubmit"
            placeholder="Search workspace index..." 
            class="global-search"
          />
          <span class="search-icon">🔍</span>
        </div>

        <div class="user-badge">
          <span class="user-name">{{ username }}</span>
          <div class="user-avatar">👤</div>
        </div>
      </header>

      <div class="workspace-viewport">
        <div class="workspace-scroll-container">
          <Home v-if="currentTab === 'home'" :username="username" />
          <Profile v-if="currentTab === 'profile'" :username="username" />
          <NewsFeed v-if="currentTab === 'newsfeed'" :username="username" />
          <Messages v-if="currentTab === 'messages'" :username="username" />
          <Lectures v-if="currentTab === 'lectures'" :username="username" />
          <History v-if="currentTab === 'history'" :username="username" />
          <Settings v-if="currentTab === 'settings'" :username="username" />
          <About v-if="currentTab === 'about'" :username="username" />
        </div>
      </div>
      
    </main>
  </div>
</template>

<style scoped>
.dashboard-layout {
  display: flex;
  height: 100vh;
  width: 100vw;
  background: #020205;
  color: #e2e8f0;
  overflow: hidden;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
}

.sidebar {
  width: 260px;
  background: rgba(4, 12, 20, 0.95);
  border-right: 1px solid rgba(16, 185, 129, 0.2);
  display: flex;
  flex-direction: column;
  transition: width 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  z-index: 30;
}
.sidebar.is-closed {
  width: 70px;
}

.sidebar-header {
  height: 70px;
  display: flex;
  align-items: center;
  padding: 0 20px;
  border-bottom: 1px solid rgba(16, 185, 129, 0.1);
  gap: 12px;
}
.logo-icon {
  font-size: 1.5rem;
  color: #10b981;
  text-shadow: 0 0 10px rgba(16, 185, 129, 0.5);
}
.sidebar-title {
  font-size: 1.2rem;
  font-weight: 700;
  color: #fff;
  margin: 0;
  white-space: nowrap;
}

.sidebar-nav {
  flex: 1;
  padding: 20px 10px;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.nav-btn {
  display: flex;
  align-items: center;
  gap: 12px;
  background: transparent;
  border: 1px solid transparent;
  color: #94a3b8;
  padding: 12px 14px;
  border-radius: 8px;
  cursor: pointer;
  text-align: left;
  transition: all 0.2s;
  white-space: nowrap;
}
.nav-btn:hover {
  background: rgba(255, 255, 255, 0.05);
  color: #e2e8f0;
}
.nav-btn.active {
  background: rgba(16, 185, 129, 0.1);
  border-color: rgba(16, 185, 129, 0.3);
  color: #10b981;
  font-weight: 600;
}
.sidebar.is-closed .nav-btn {
  justify-content: center;
  padding: 12px 0;
}
.nav-icon {
  font-size: 1.2rem;
}
.nav-label {
  font-size: 0.9rem;
}

.sidebar-footer {
  padding: 20px 10px;
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
  white-space: nowrap;
}
.sidebar.is-closed .logout-btn {
  justify-content: center;
  padding: 10px 0;
}
.logout-btn:hover {
  background: rgba(220, 38, 38, 0.12);
  border-color: rgba(220, 38, 38, 0.3);
}

.workspace-main {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-width: 0;
  position: relative;
  background: radial-gradient(circle at 80% 20%, rgba(2, 44, 34, 0.2) 0%, rgba(0, 0, 0, 0) 65%);
}

.workspace-header {
  height: 70px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 35px;
  background: rgba(2, 6, 12, 0.8);
  border-bottom: 1px solid rgba(16, 185, 129, 0.2);
  backdrop-filter: blur(10px);
  z-index: 20;
  gap: 20px;
}

.toggle-sidebar-btn {
  background: rgba(16, 185, 129, 0.05);
  border: 1px solid rgba(16, 185, 129, 0.3);
  color: #10b981;
  border-radius: 8px;
  padding: 8px 14px;
  font-size: 0.8rem;
  font-weight: 700;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: all 0.2s;
  white-space: nowrap;
}
.toggle-sidebar-btn:hover {
  background: rgba(16, 185, 129, 0.15);
  box-shadow: 0 0 10px rgba(16, 185, 129, 0.4);
}

.search-container {
  flex: 1;
  max-width: 500px;
  position: relative;
}
.global-search {
  width: 100%;
  background: rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(16, 185, 129, 0.3);
  border-radius: 8px;
  padding: 10px 16px 10px 38px;
  color: #e2e8f0;
  font-size: 0.9rem;
  outline: none;
  transition: all 0.2s;
}
.global-search:focus {
  border-color: #10b981;
  background: rgba(0, 0, 0, 0.6);
  box-shadow: 0 0 12px rgba(16, 185, 129, 0.1);
}
.search-icon {
  position: absolute;
  left: 14px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 0.9rem;
  opacity: 0.6;
}

.user-badge {
  display: flex;
  align-items: center;
  gap: 12px;
}
.user-name {
  font-size: 0.85rem;
  font-weight: 600;
  color: #cbd5e1;
}
.user-avatar {
  width: 36px;
  height: 36px;
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.4);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.1rem;
}

.workspace-viewport {
  flex: 1;
  position: relative;
  overflow: hidden; 
}

.workspace-scroll-container {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  overflow-x: hidden;
  padding: 35px;
  box-sizing: border-box;
}

:deep(.view-container) {
  max-width: 100%;
  width: 100%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 24px;
}
</style>
