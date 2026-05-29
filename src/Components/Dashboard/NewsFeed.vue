<script setup lang="ts">
import { ref } from 'vue'

interface Post {
  id: number
  author: string
  timestamp: string
  body: string
  likes: number
  liked: boolean
}

const feedPosts = ref<Post[]>([
  { id: 1, author: 'Architect_Vance', timestamp: '2 hours ago', body: 'Just initialized the Markdown directory parsing index routines. Dynamic rendering of document notes graph views scales optimally up to 500 nodes.', likes: 14, liked: false },
  { id: 2, author: 'Root_User', timestamp: '5 hours ago', body: 'Please review updated system parameters on port 8080 configuration tables to clear socket sync locks.', likes: 8, liked: false }
])

const inputContent = ref('')

const handlePublish = () => {
  if (!inputContent.value.trim()) return
  feedPosts.value.unshift({
    id: Date.now(),
    author: 'Active Terminal Instance',
    timestamp: 'Just now',
    body: inputContent.value,
    likes: 0,
    liked: false
  })
  inputContent.value = ''
}
</script>

<template>
  <div class="view-container">
    <h1 class="view-title">Network News Feed</h1>
    <p class="view-tagline">Real-time terminal updates and active network status transmissions.</p>

    <div class="panel-card post-box">
      <textarea v-model="inputContent" placeholder="Broadcast a status update or markdown block..."></textarea>
      <div class="controls-row">
        <span class="format-tip">Text packet wrapper format active</span>
        <button class="action-submit" @click="handlePublish">Sync Stream</button>
      </div>
    </div>

    <div v-for="post in feedPosts" :key="post.id" class="panel-card feed-card">
      <div class="card-header">
        <div class="user-avatar-placeholder">🤖</div>
        <div>
          <h4>{{ post.author }}</h4>
          <span>{{ post.timestamp }}</span>
        </div>
      </div>
      <p class="card-body">{{ post.body }}</p>
      <div class="card-footer">
        <button :class="['sync-btn', { active: post.liked }]" @click="post.liked = !post.liked; post.liked ? post.likes++ : post.likes--">
          💚 {{ post.likes }} Nodes Synchronized
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.post-box textarea { width: 100%; height: 75px; background: rgba(0, 0, 0, 0.5); border: 1px solid rgba(16, 185, 129, 0.2); border-radius: 8px; padding: 12px; color: #fff; outline: none; resize: none; font-size: 0.9rem; font-family: inherit; }
.post-box textarea:focus { border-color: #10b981; }
.controls-row { display: flex; justify-content: space-between; align-items: center; margin-top: 10px; }
.format-tip { font-size: 0.75rem; color: #64748b; font-family: monospace; }
.action-submit { background: #047857; border: 1px solid #10b981; border-radius: 6px; color: #fff; padding: 6px 14px; font-weight: 600; font-size: 0.85rem; cursor: pointer; }
.action-submit:hover { background: #059669; }

.feed-card { border-left: 2px solid #10b981; }
.card-header { display: flex; gap: 12px; align-items: center; margin-bottom: 12px; }
.user-avatar-placeholder { width: 34px; height: 34px; background: rgba(16, 185, 129, 0.1); border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 1rem; }
.card-header h4 { margin: 0; font-size: 0.9rem; color: #ffffff; }
.card-header span { font-size: 0.75rem; color: #64748b; }
.card-body { color: #cbd5e1; font-size: 0.9rem; line-height: 1.5; margin: 0 0 12px 0; }
.sync-btn { background: transparent; border: 1px solid rgba(16, 185, 129, 0.2); border-radius: 6px; color: #94a3b8; padding: 5px 10px; font-size: 0.8rem; cursor: pointer; transition: all 0.2s; }
.sync-btn.active { color: #10b981; background: rgba(16, 185, 129, 0.05); border-color: #10b981; }
</style>