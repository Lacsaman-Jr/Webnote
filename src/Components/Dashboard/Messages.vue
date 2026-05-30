<script setup lang="ts">
import { ref, computed } from 'vue'

interface Conversation {
  id: string
  name: string
  type: 'individual' | 'group'
  avatar: string
  lastMessage: string
  timestamp: string
  isPinned: boolean
  unread: number
}

interface Message {
  id: string
  senderId: string
  senderName: string
  text: string
  isMine: boolean
  timestamp: string
}

const searchQuery = ref('')
const activeFilter = ref<'all' | 'individual' | 'group'>('all')
const messageInput = ref('')
const activeChatId = ref<string>('c1')

const conversations = ref<Conversation[]>([
  { id: 'c1', name: 'Architect_Vance', type: 'individual', avatar: '👨‍💻', lastMessage: 'Ensure your dynamic vault linkages are parsed correctly.', timestamp: '10:42 AM', isPinned: true, unread: 2 },
  { id: 'c2', name: 'Global Vault Matrix', type: 'group', avatar: '🌐', lastMessage: 'System update rolling out tonight.', timestamp: '09:15 AM', isPinned: true, unread: 0 },
  { id: 'c3', name: 'System_Admin', type: 'individual', avatar: '🛡️', lastMessage: 'Session terminal instance linked.', timestamp: 'Yesterday', isPinned: false, unread: 0 },
  { id: 'c4', name: 'Dev_Team_Alpha', type: 'group', avatar: '⚡', lastMessage: 'Who broke the build?', timestamp: 'Yesterday', isPinned: false, unread: 5 },
  { id: 'c5', name: 'Nova_Prime', type: 'individual', avatar: '👩‍🚀', lastMessage: 'Got the files, thanks.', timestamp: 'Tuesday', isPinned: false, unread: 0 },
  { id: 'c6', name: 'UI/UX Node', type: 'group', avatar: '🎨', lastMessage: 'Let\'s review the new canvas shrouds.', timestamp: 'Monday', isPinned: false, unread: 0 },
])

const activeChatMessages = ref<Message[]>([
  { id: 'm1', senderId: 'vance', senderName: 'Architect_Vance', text: 'Hey, did you check the latest node deployments?', isMine: false, timestamp: '10:38 AM' },
  { id: 'm2', senderId: 'me', senderName: 'You', text: 'Yes, looking at the structural graphs now. Everything seems stable.', isMine: true, timestamp: '10:40 AM' },
  { id: 'm3', senderId: 'vance', senderName: 'Architect_Vance', text: 'Ensure your dynamic vault linkages are parsed correctly.', isMine: false, timestamp: '10:42 AM' }
])

const filteredConversations = computed(() => {
  let filtered = conversations.value

  if (searchQuery.value.trim()) {
    const query = searchQuery.value.toLowerCase()
    filtered = filtered.filter(c => 
      c.name.toLowerCase().includes(query) || 
      c.lastMessage.toLowerCase().includes(query)
    )
  }

  if (activeFilter.value !== 'all') {
    filtered = filtered.filter(c => c.type === activeFilter.value)
  }

  return filtered
})

const pinnedChats = computed(() => filteredConversations.value.filter(c => c.isPinned))
const unpinnedChats = computed(() => filteredConversations.value.filter(c => !c.isPinned))

const activeChatDetails = computed(() => {
  return conversations.value.find(c => c.id === activeChatId.value) || conversations.value[0]
})

const setFilter = (filter: 'all' | 'individual' | 'group') => {
  activeFilter.value = filter
}

const togglePin = (chatId: string) => {
  const chat = conversations.value.find(c => c.id === chatId)
  if (chat) {
    if (!chat.isPinned && pinnedChats.value.length >= 5) {
      alert("You can only pin up to 5 conversations.")
      return
    }
    chat.isPinned = !chat.isPinned
  }
}

const selectChat = (chatId: string) => {
  activeChatId.value = chatId
}

const handleSendMessage = () => {
  if (!messageInput.value.trim()) return
  
  activeChatMessages.value.push({
    id: `m${Date.now()}`,
    senderId: 'me',
    senderName: 'You',
    text: messageInput.value,
    isMine: true,
    timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
  })
  
  messageInput.value = ''
}

const triggerAttachment = (type: string) => {
  console.log(`Triggering upload for: ${type}`)
}
</script>

<template>
  <div class="messenger-fullscreen-container">
    
    <div class="inbox-sidebar">
      <div class="search-container">
        <input 
          type="text" 
          v-model="searchQuery" 
          placeholder="Search chats, people, messages..." 
          class="inbox-search"
        />
        <span class="search-icon">🔍</span>
      </div>

      <div class="filter-toggles">
        <button :class="{ active: activeFilter === 'all' }" @click="setFilter('all')">All</button>
        <button :class="{ active: activeFilter === 'individual' }" @click="setFilter('individual')">Individual</button>
        <button :class="{ active: activeFilter === 'group' }" @click="setFilter('group')">Groups</button>
      </div>

      <div class="chat-lists-scroll">
        
        <div v-if="pinnedChats.length > 0" class="chat-section">
          <div class="section-label">Pinned ({{ pinnedChats.length }}/5)</div>
          <div 
            v-for="chat in pinnedChats" 
            :key="chat.id"
            :class="['chat-row', { active: activeChatId === chat.id }]"
            @click="selectChat(chat.id)"
          >
            <div class="chat-avatar">{{ chat.avatar }}</div>
            <div class="chat-info">
              <div class="chat-header-row">
                <span class="chat-name">{{ chat.name }}</span>
                <span class="chat-time">{{ chat.timestamp }}</span>
              </div>
              <div class="chat-preview">{{ chat.lastMessage }}</div>
            </div>
            <button class="pin-btn pinned" @click.stop="togglePin(chat.id)" title="Unpin">📍</button>
          </div>
        </div>

        <div class="chat-section">
          <div class="section-label">Recent</div>
          <div 
            v-for="chat in unpinnedChats" 
            :key="chat.id"
            :class="['chat-row', { active: activeChatId === chat.id }]"
            @click="selectChat(chat.id)"
          >
            <div class="chat-avatar">{{ chat.avatar }}</div>
            <div class="chat-info">
              <div class="chat-header-row">
                <span class="chat-name" :class="{ unread: chat.unread > 0 }">{{ chat.name }}</span>
                <span class="chat-time">{{ chat.timestamp }}</span>
              </div>
              <div class="chat-preview" :class="{ unread: chat.unread > 0 }">
                {{ chat.lastMessage }}
              </div>
            </div>
            <button class="pin-btn" @click.stop="togglePin(chat.id)" title="Pin Chat">📍</button>
            <div v-if="chat.unread > 0" class="unread-badge">{{ chat.unread }}</div>
          </div>
        </div>

      </div>
    </div>

    <div class="active-chat-window">
      
      <div class="chat-header">
        <div class="chat-header-avatar">{{ activeChatDetails.avatar }}</div>
        <div class="chat-header-info">
          <h2>{{ activeChatDetails.name }}</h2>
          <span class="chat-status">
            <span class="status-dot"></span> {{ activeChatDetails.type === 'group' ? 'Group Matrix' : 'Online Pipeline' }}
          </span>
        </div>
      </div>

      <div class="messages-thread">
        <div 
          v-for="msg in activeChatMessages" 
          :key="msg.id"
          :class="['message-wrapper', msg.isMine ? 'outbound' : 'inbound']"
        >
          <div class="message-bubble">
            <div class="message-text">{{ msg.text }}</div>
            <div class="message-meta">{{ msg.timestamp }}</div>
          </div>
        </div>
      </div>

      <div class="chat-input-area">
        <div class="attachment-tools">
          <button class="tool-btn" @click="triggerAttachment('image')" title="Attach Image">📷</button>
          <button class="tool-btn" @click="triggerAttachment('video')" title="Attach Video">🎥</button>
          <button class="tool-btn" @click="triggerAttachment('file')" title="Attach File">📎</button>
          <button class="tool-btn" @click="triggerAttachment('voice')" title="Voice Message">🎤</button>
        </div>
        
        <div class="input-wrapper">
          <input 
            type="text" 
            v-model="messageInput" 
            @keyup.enter="handleSendMessage"
            placeholder="Transmit message across the grid..." 
            class="message-text-input"
          />
          <button class="send-btn" @click="handleSendMessage">📩 Send</button>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
.messenger-fullscreen-container {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  background: #020205;
  z-index: 10;
}

.inbox-sidebar {
  width: 320px;
  background: rgba(4, 12, 20, 0.9);
  border-right: 1px solid rgba(16, 185, 129, 0.2);
  display: flex;
  flex-direction: column;
}

.search-container {
  padding: 24px 16px 16px 16px;
  position: relative;
  border-bottom: 1px solid rgba(16, 185, 129, 0.1);
}

.inbox-search {
  width: 100%;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(16, 185, 129, 0.3);
  padding: 10px 14px 10px 34px;
  border-radius: 8px;
  color: #e2e8f0;
  font-size: 0.85rem;
  outline: none;
  transition: border-color 0.2s;
  box-sizing: border-box;
}
.inbox-search:focus {
  border-color: #10b981;
}

.search-icon {
  position: absolute;
  left: 26px;
  top: calc(50% + 4px);
  transform: translateY(-50%);
  font-size: 0.85rem;
  opacity: 0.5;
}

.filter-toggles {
  display: flex;
  padding: 12px 16px;
  gap: 8px;
  border-bottom: 1px solid rgba(16, 185, 129, 0.1);
}
.filter-toggles button {
  flex: 1;
  background: transparent;
  border: 1px solid rgba(16, 185, 129, 0.2);
  color: #94a3b8;
  padding: 8px 0;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s;
}
.filter-toggles button:hover {
  background: rgba(16, 185, 129, 0.05);
}
.filter-toggles button.active {
  background: rgba(16, 185, 129, 0.15);
  border-color: #10b981;
  color: #10b981;
}

.chat-lists-scroll {
  flex: 1;
  overflow-y: auto;
  padding-bottom: 16px;
}

.section-label {
  padding: 16px 16px 8px 16px;
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: #10b981;
  font-weight: bold;
}

.chat-row {
  display: flex;
  align-items: center;
  padding: 12px 16px;
  gap: 12px;
  cursor: pointer;
  transition: background 0.2s;
  position: relative;
}
.chat-row:hover {
  background: rgba(255, 255, 255, 0.03);
}
.chat-row.active {
  background: rgba(16, 185, 129, 0.08);
  border-left: 3px solid #10b981;
}

.chat-avatar {
  width: 40px;
  height: 40px;
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.3);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.2rem;
  flex-shrink: 0;
}

.chat-info {
  flex: 1;
  min-width: 0;
}

.chat-header-row {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 4px;
}

.chat-name {
  font-size: 0.9rem;
  font-weight: 600;
  color: #e2e8f0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.chat-name.unread {
  color: #fff;
  font-weight: 800;
}

.chat-time {
  font-size: 0.7rem;
  color: #64748b;
}

.chat-preview {
  font-size: 0.8rem;
  color: #94a3b8;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.chat-preview.unread {
  color: #e2e8f0;
  font-weight: 600;
}

.pin-btn {
  background: none;
  border: none;
  font-size: 0.9rem;
  cursor: pointer;
  opacity: 0;
  filter: grayscale(100%);
  transition: all 0.2s;
  padding: 4px;
}
.chat-row:hover .pin-btn {
  opacity: 0.5;
}
.pin-btn:hover {
  opacity: 1 !important;
  transform: scale(1.1);
}
.pin-btn.pinned {
  opacity: 1;
  filter: grayscale(0%);
}

.unread-badge {
  background: #10b981;
  color: #000;
  font-size: 0.65rem;
  font-weight: bold;
  padding: 2px 6px;
  border-radius: 10px;
}

.active-chat-window {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: transparent;
}

.chat-header {
  padding: 24px 32px;
  border-bottom: 1px solid rgba(16, 185, 129, 0.2);
  display: flex;
  align-items: center;
  gap: 16px;
  background: rgba(16, 185, 129, 0.02);
}

.chat-header-avatar {
  width: 48px;
  height: 48px;
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.4);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
}

.chat-header-info h2 {
  margin: 0 0 4px 0;
  font-size: 1.2rem;
  color: #fff;
}

.chat-status {
  font-size: 0.75rem;
  color: #10b981;
  display: flex;
  align-items: center;
  gap: 6px;
}

.status-dot {
  width: 8px;
  height: 8px;
  background: #10b981;
  border-radius: 50%;
  box-shadow: 0 0 8px rgba(16, 185, 129, 0.6);
}

.messages-thread {
  flex: 1;
  padding: 32px;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.message-wrapper {
  display: flex;
  flex-direction: column;
  max-width: 70%;
}

.message-wrapper.inbound {
  align-self: flex-start;
}

.message-wrapper.outbound {
  align-self: flex-end;
  align-items: flex-end;
}

.message-bubble {
  padding: 12px 16px;
  border-radius: 12px;
  font-size: 0.95rem;
  line-height: 1.5;
  position: relative;
}

.inbound .message-bubble {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #e2e8f0;
  border-bottom-left-radius: 2px;
}

.outbound .message-bubble {
  background: rgba(16, 185, 129, 0.15);
  border: 1px solid rgba(16, 185, 129, 0.3);
  color: #fff;
  border-bottom-right-radius: 2px;
}

.message-meta {
  font-size: 0.7rem;
  color: #64748b;
  margin-top: 6px;
  text-align: right;
}
.outbound .message-meta {
  color: rgba(16, 185, 129, 0.7);
}

.chat-input-area {
  padding: 24px 32px;
  background: rgba(2, 6, 12, 0.9);
  border-top: 1px solid rgba(16, 185, 129, 0.2);
}

.attachment-tools {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
}

.tool-btn {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s;
  font-size: 0.9rem;
}
.tool-btn:hover {
  background: rgba(16, 185, 129, 0.15);
  border-color: #10b981;
  transform: translateY(-2px);
}

.input-wrapper {
  display: flex;
  gap: 16px;
}

.message-text-input {
  flex: 1;
  background: rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(16, 185, 129, 0.3);
  border-radius: 8px;
  padding: 14px 16px;
  color: #e2e8f0;
  font-family: inherit;
  font-size: 0.95rem;
  outline: none;
  transition: border-color 0.2s;
  box-sizing: border-box;
}
.message-text-input:focus {
  border-color: #10b981;
  background: rgba(0, 0, 0, 0.6);
}

.send-btn {
  background: #10b981;
  color: #000;
  border: none;
  border-radius: 8px;
  padding: 0 24px;
  font-weight: bold;
  font-size: 0.95rem;
  cursor: pointer;
  transition: all 0.2s;
}
.send-btn:hover {
  background: #34d399;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
}
</style>
