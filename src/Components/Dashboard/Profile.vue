<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const props = defineProps<{ username: string }>()

const isCreatingPost = ref(false)

const globalPrivacyMode = ref('public') 

const bio = ref('A Born Prodigy ')
const followersCount = ref(1337)
const followingCount = ref(42)
const birthdate = ref('2002-10-24')
const address = ref('Sector 7, Quantum Grid Core Node, PH')

const socialLinks = ref([
  { id: 1, platform: 'GitHub', url: 'https://github.com/Lacsaman-Jr?tab=overview&from=2026-05-01&to=2026-05-30', icon: '💻' },
  { id: 2, platform: 'LinkedIn', url: 'https://ph.linkedin.com/in/abdullah-abuat-lacsaman-jr-5555a0336/tl', icon: '👔' },
  { id: 3, platform: 'Instagram', url: 'https://www.instagram.com/lacsaman_jr?igsh=MWE1eWF3MGt1M2Rjbg%3D%3D', icon: '👤' },
  { id: 4, platform: 'Facebook', url: 'https://www.facebook.com/abdullahabuatlacsamanjr', icon: '🌐' }
])

const showAcademicMenu = ref(false)
const isEditingAcademic = ref(false)

const showCertificatesMenu = ref(false)
const isEditingCertificates = ref(false)

const toggleAcademicEdit = () => {
  isEditingAcademic.value = !isEditingAcademic.value
  showAcademicMenu.value = false
}

const toggleCertificatesEdit = () => {
  isEditingCertificates.value = !isEditingCertificates.value
  showCertificatesMenu.value = false
}

const academicList = ref([
  { id: 1, level: 'Elementary School', institution: 'Grid Primary Academy', timeframe: '2008 - 2014' },
  { id: 2, level: 'High School', institution: 'Science & Technology Matrix High', timeframe: '2014 - 2020' },
  { id: 3, level: 'College (BS Computer Science)', institution: 'State Cybernetics University', timeframe: '2020 - 2024 (Current)' }
])

const newAcademicLevel = ref('College (BS)')
const newInstitution = ref('')
const newTimeframe = ref('')

const addAcademicRecord = () => {
  if (!newInstitution.value.trim() || !newTimeframe.value.trim()) return
  academicList.value.push({
    id: Date.now(),
    level: newAcademicLevel.value,
    institution: newInstitution.value.trim(),
    timeframe: newTimeframe.value.trim()
  })
  newInstitution.value = ''
  newTimeframe.value = ''
}
const removeAcademicRecord = (id: number) => {
  academicList.value = academicList.value.filter(item => item.id !== id)
}

const certificateList = ref([
  { id: 1, title: 'Advanced Neural Network Topologies Seminar', issuer: 'IEEE Sync Corp', year: '2025' },
  { id: 2, title: 'Full-Stack Quantum System Architecture Workshop', issuer: 'OpenAI Lab Suite', year: '2026' }
])

const newCertTitle = ref('')
const newCertIssuer = ref('')
const newCertYear = ref('')

const addCertificateRecord = () => {
  if (!newCertTitle.value.trim() || !newCertIssuer.value.trim()) return
  certificateList.value.push({
    id: Date.now(),
    title: newCertTitle.value.trim(),
    issuer: newCertIssuer.value.trim(),
    year: newCertYear.value.trim() || '2026'
  })
  newCertTitle.value = ''
  newCertIssuer.value = ''
  newCertYear.value = ''
}
const removeCertificateRecord = (id: number) => {
  certificateList.value = certificateList.value.filter(item => item.id !== id)
}

interface RepositoryFile { name: string; isFolder: boolean; commitMessage: string; sizeOrAge: string }
interface UserPost {
  id: number
  type: 'Code' | 'Research Paper' | 'Academic Paper'
  title: string
  date: string
  excerpt: string
  privacy: string
  attachedFiles: RepositoryFile[]
  isFolderExpanded: boolean
  showDotsMenu: boolean
  isInlineEditing: boolean
}

const postsList = ref<UserPost[]>([
  {
    id: 1,
    type: 'Code',
    title: 'Automated Force-Directed Scaling Matrix Layout Engine',
    date: 'May 28, 2026',
    excerpt: 'A pure TypeScript implementation of reactive system resizing algorithms for large interactive network maps. Click this card block layout framework directly below to browse the repository file array index nodes like in GitHub.',
    privacy: 'public',
    isFolderExpanded: true,
    showDotsMenu: false,
    isInlineEditing: false,
    attachedFiles: [
      { name: 'src/', isFolder: true, commitMessage: 'refactor: fine-tune friction layout vectors', sizeOrAge: '2 hours ago' },
      { name: 'components/', isFolder: true, commitMessage: 'feat: add interactive cursor handling matrix', sizeOrAge: 'Yesterday' },
      { name: 'index.ts', isFolder: false, commitMessage: 'initial: map data simulation variables', sizeOrAge: '3 days ago' },
      { name: 'package.json', isFolder: false, commitMessage: 'chore: update production core matrix build dependencies', sizeOrAge: '4 days ago' },
      { name: 'README.md', isFolder: false, commitMessage: 'docs: clarify baseline vector operational guide', sizeOrAge: 'Just now' }
    ]
  },
  {
    id: 2,
    type: 'Research Paper',
    title: 'Bidirectional Linkage Architectures in Distributed Knowledge Vaults',
    date: 'April 14, 2026',
    excerpt: 'Abstract telemetry analysis regarding local terminal database structures and structural memory retention models observed over dynamic client nodes.',
    privacy: 'followers',
    isFolderExpanded: false,
    showDotsMenu: false,
    isInlineEditing: false,
    attachedFiles: [
      { name: 'manuscript_draft_v2.pdf', isFolder: false, commitMessage: 'Final submission proof document file', sizeOrAge: '1.4 MB' },
      { name: 'telemetry_dataset.csv', isFolder: false, commitMessage: 'Raw logs data output table sheets', sizeOrAge: '24.8 MB' }
    ]
  }
])

const activePostType = ref<'Code' | 'Research Paper' | 'Academic Paper'>('Code')
const newPostTitle = ref('')
const newPostExcerpt = ref('')
const newPostPrivacy = ref('public')
const simulatedUploadedFiles = ref<RepositoryFile[]>([])
const fileInputLabelPlaceholder = ref('No documents uploaded')

const simulateFileUploadToggle = () => {
  if (activePostType.value === 'Code') {
    simulatedUploadedFiles.value = [
      { name: 'src/', isFolder: true, commitMessage: 'Automated initial directory push node', sizeOrAge: 'now' },
      { name: 'main.py', isFolder: false, commitMessage: 'Write execution module hook', sizeOrAge: 'now' },
      { name: 'requirements.txt', isFolder: false, commitMessage: 'Declare operational matrix packages', sizeOrAge: 'now' }
    ]
    fileInputLabelPlaceholder.value = 'Attached: 1 Directory, 2 Document Files'
  } else {
    simulatedUploadedFiles.value = [
      { name: 'publication_core_dossier.docx', isFolder: false, commitMessage: 'Main text artifact asset', sizeOrAge: '480 KB' }
    ]
    fileInputLabelPlaceholder.value = 'Attached: publication_core_dossier.docx'
  }
}

const clearSimulatedUploads = () => {
  simulatedUploadedFiles.value = []
  fileInputLabelPlaceholder.value = 'No documents uploaded'
}

const addPostRecord = () => {
  if (!newPostTitle.value.trim() || !newPostExcerpt.value.trim()) return
  const now = new Date()
  const options: Intl.DateTimeFormatOptions = { year: 'numeric', month: 'short', day: 'numeric' }
  
  let finalFiles = [...simulatedUploadedFiles.value]
  if (finalFiles.length === 0) {
    if (activePostType.value === 'Code') {
      finalFiles = [
        { name: 'index.js', isFolder: false, commitMessage: 'Default system generated branch push', sizeOrAge: 'now' },
        { name: 'README.md', isFolder: false, commitMessage: 'Initialize document instructions layout', sizeOrAge: 'now' }
      ]
    } else {
      finalFiles = [{ name: 'document_attachment.pdf', isFolder: false, commitMessage: 'Verified publication node attachment', sizeOrAge: '1.1 MB' }]
    }
  }

  postsList.value.unshift({
    id: Date.now(),
    type: activePostType.value,
    title: newPostTitle.value.trim(),
    date: now.toLocaleDateString('en-US', options),
    excerpt: newPostExcerpt.value.trim(),
    privacy: newPostPrivacy.value,
    attachedFiles: finalFiles,
    isFolderExpanded: activePostType.value === 'Code', 
    showDotsMenu: false,
    isInlineEditing: false
  })

  newPostTitle.value = ''
  newPostExcerpt.value = ''
  clearSimulatedUploads()
  isCreatingPost.value = false 
}

const togglePostDotsMenu = (post: UserPost, event: Event) => {
  event.stopPropagation()
  postsList.value.forEach(p => { if(p.id !== post.id) p.showDotsMenu = false })
  post.showDotsMenu = !post.showDotsMenu
}

const toggleFolderExplorerView = (post: UserPost) => {
  post.isFolderExpanded = !post.isFolderExpanded
}

const setPostPrivacy = (post: UserPost, privacyValue: string) => {
  post.privacy = privacyValue
  post.showDotsMenu = false
}

const triggerInlineEditPost = (post: UserPost) => {
  post.isInlineEditing = true
  post.showDotsMenu = false
}

const saveInlineEditPost = (post: UserPost) => {
  post.isInlineEditing = false
}

const removePostRecord = (id: number) => {
  postsList.value = postsList.value.filter(post => post.id !== id)
}

const handleGlobalClick = (e: Event) => {
  postsList.value.forEach(p => p.showDotsMenu = false)
  
  if (!(e.target as HTMLElement).closest('.options-menu-container')) {
    showAcademicMenu.value = false
    showCertificatesMenu.value = false
  }
}

interface LocalNode { id: number; label: string; x: number; y: number; vx: number; vy: number }
interface LocalEdge { source: number; target: number }

const width = 800
const height = 230
const graphNodes = ref<LocalNode[]>([
  { id: 101, label: 'User Persona', x: 400, y: 115, vx: 0, vy: 0 },
  { id: 102, label: 'Academic Index', x: 230, y: 70, vx: 0, vy: 0 },
  { id: 103, label: 'Research Pool', x: 570, y: 150, vx: 0, vy: 0 },
  { id: 104, label: 'Certificates Stack', x: 300, y: 170, vx: 0, vy: 0 },
  { id: 105, label: 'Codebase Hub', x: 500, y: 60, vx: 0, vy: 0 }
])
const graphEdges = ref<LocalEdge[]>([
  { source: 101, target: 102 }, { source: 101, target: 103 },
  { source: 101, target: 104 }, { source: 101, target: 105 },
  { source: 102, target: 104 }, { source: 103, target: 105 }
])

let animationFrameId: number
const tickGraphPhysics = () => {
  const kCenter = 0.015
  const kRepel = 1200
  const kSpring = 0.03
  const lenSpring = 110

  graphNodes.value.forEach(n => {
    n.vx += (width / 2 - n.x) * kCenter
    n.vy += (height / 2 - n.y) * kCenter
    graphNodes.value.forEach(other => {
      if (n.id === other.id) return
      const dx = n.x - other.x
      const dy = n.y - other.y
      let dist = Math.sqrt(dx * dx + dy * dy) || 0.1
      if (dist < 150) {
        n.vx += (dx / dist) * (kRepel / (dist * dist))
        n.vy += (dy / dist) * (kRepel / (dist * dist))
      }
    })
  })

  graphEdges.value.forEach(e => {
    const s = graphNodes.value.find(n => n.id === e.source)
    const t = graphNodes.value.find(n => n.id === e.target)
    if (!s || !t) return
    const dx = t.x - s.x
    const dy = t.y - s.y
    const dist = Math.sqrt(dx * dx + dy * dy) || 0.1
    const f = (dist - lenSpring) * kSpring
    s.vx += (dx / dist) * f
    s.vy += (dy / dist) * f
    t.vx -= (dx / dist) * f
    t.vy -= (dy / dist) * f
  })

  graphNodes.value.forEach(n => {
    n.vx *= 0.82; n.vy *= 0.82
    n.x += n.vx; n.y += n.vy
    if (n.x < 30) n.x = 30
    if (n.x > width - 30) n.x = width - 30
    if (n.y < 30) n.y = 30
    if (n.y > height - 30) n.y = height - 30
  })

  animationFrameId = requestAnimationFrame(tickGraphPhysics)
}

onMounted(() => { 
  tickGraphPhysics()
  window.addEventListener('click', handleGlobalClick)
})
onUnmounted(() => { 
  cancelAnimationFrame(animationFrameId)
  window.removeEventListener('click', handleGlobalClick)
})
</script>

<template>
  <div class="view-container">
    
    <div class="panel-card centralized-profile-header">
      
      <div class="privacy-shifter-badge">
        <label for="privacy-select" class="privacy-label">👁️ Scope:</label>
        <select id="privacy-select" v-model="globalPrivacyMode" class="privacy-select-element">
          <option value="public">🌐 Public Profile</option>
          <option value="private">🔒 Private Vault</option>
          <option value="followers">👥 Followers Only</option>
        </select>
      </div>

      <div class="centered-identity-avatar-wrapper">
        <div class="centered-avatar-circle-aura">
          <div class="centered-avatar-circle">👤</div>
        </div>
        <h1 class="profile-username">{{ username || 'Operator Node' }}</h1>
        <p class="profile-index-hash">@{{ (username || 'operator').toLowerCase().replace(/\s+/g, '_') }}_dossier</p>
      </div>

      <div class="centered-bio-container">
        <textarea v-model="bio" class="editable-bio-textarea" placeholder="Provide system vector description profile information..."></textarea>
      </div>

      <div class="side-by-side-social-metrics">
        <div class="metric-counter-pill">
          <span class="pill-number">{{ followersCount.toLocaleString() }}</span>
          <span class="pill-title">followers</span>
        </div>
        <div class="metric-counter-pill">
          <span class="pill-number">{{ followingCount.toLocaleString() }}</span>
          <span class="pill-title">followed</span>
        </div>
      </div>
    </div>

    <div class="profile-dashboard-grid">
      
      <div class="dashboard-sidebar-column">
        
        <div class="panel-card metadata-box">
          <h5 class="box-heading">⚡ Core Demographics</h5>
          
          <div class="meta-field-row">
            <span class="field-glyph">🎂</span>
            <div class="field-details">
              <label>Birthdate</label>
              <input type="date" v-model="birthdate" class="meta-inline-input" />
            </div>
          </div>

          <div class="meta-field-row">
            <span class="field-glyph">📍</span>
            <div class="field-details">
              <label>Address</label>
              <input type="text" v-model="address" class="meta-inline-input" placeholder="No address verified." />
            </div>
          </div>
        </div>

        <div class="panel-card metadata-box">
          <h5 class="box-heading">🔗 Linked Platforms</h5>
          <p class="box-sub-narrative">Connected gateway profiles on external servers:</p>
          
          <div class="social-links-vertical-list">
            <a v-for="link in socialLinks" :key="link.id" :href="link.url" target="_blank" class="platform-hyperlink-anchor">
              <span class="platform-icon">{{ link.icon }}</span>
              <span class="platform-name">{{ link.platform }}</span>
              <span class="arrow-indicator">↗</span>
            </a>
          </div>
        </div>
      </div>

      <div class="dashboard-main-content-column">
        
        <div class="panel-card structured-list-box">
          <div class="list-box-header panel-header-flex">
            <div>
              <h5 class="box-heading">🎓 Academic Standing</h5>
              <span class="interactive-tag">List Sequence</span>
            </div>
            
            <div class="options-menu-container" @click.stop>
              <button class="icon-btn dots-btn" @click="showAcademicMenu = !showAcademicMenu">⋮</button>
              <div class="dropdown-menu" v-if="showAcademicMenu">
                <button @click="toggleAcademicEdit">
                  {{ isEditingAcademic ? '❌ Cancel Edit' : '✏️ Edit Information' }}
                </button>
              </div>
            </div>
          </div>
          
          <div v-if="academicList.length === 0" class="empty-list-indicator">
            No institutional records declared. Enter profile history parameters below.
          </div>
          
          <div v-if="!isEditingAcademic">
            <ul class="academic-vertical-ledger">
              <li v-for="item in academicList" :key="item.id" class="ledger-item-row read-only-item">
                <div class="item-icon-shield">🏫</div>
                <div class="item-main-details">
                  <span class="academic-tier-badge">{{ item.level }}</span>
                  <h6 class="institution-title">{{ item.institution }}</h6>
                  <p class="institution-timeframe">Timeframe: {{ item.timeframe }}</p>
                </div>
              </li>
            </ul>
          </div>

          <div v-else>
            <ul class="academic-vertical-ledger">
              <li v-for="item in academicList" :key="item.id" class="ledger-item-row">
                <div class="item-icon-shield">🏫</div>
                <div class="item-main-details edit-item-row">
                  <input type="text" v-model="item.level" class="meta-inline-input" placeholder="Level" style="font-size:0.75rem; color:#10b981; font-family:monospace; margin-bottom:4px; font-weight:700;" />
                  <input type="text" v-model="item.institution" class="meta-inline-input" placeholder="Institution" style="font-size:0.95rem; font-weight:700; color:#f1f5f9; margin-bottom:4px;" />
                  <input type="text" v-model="item.timeframe" class="meta-inline-input" placeholder="Timeframe" style="font-size:0.8rem; color:#64748b;" />
                </div>
                <button class="remove-record-trigger" @click="removeAcademicRecord(item.id)">✕</button>
              </li>
            </ul>

            <div class="append-list-node-form">
              <p class="form-title">⚡ Append Academic Milestone Node</p>
              <div class="form-inline-fields-grid">
                <select v-model="newAcademicLevel" class="form-input-control drop-select">
                  <option value="Elementary School">Elementary</option>
                  <option value="High School">High School</option>
                  <option value="College (BS)">College (BS)</option>
                  <option value="Graduate (MS)">Graduate (MS)</option>
                  <option value="Post-Doc (PhD)">Post-Doc (PhD)</option>
                </select>
                <input type="text" v-model="newInstitution" placeholder="Name of Institution / university" class="form-input-control text-field" />
                <input type="text" v-model="newTimeframe" placeholder="Timeframe or Current" class="form-input-control text-field" />
                <button class="append-action-button" @click="addAcademicRecord">+ Add</button>
              </div>
            </div>
            
            <button class="save-changes-btn" @click="toggleAcademicEdit">Save Changes</button>
          </div>
        </div>

        <div class="panel-card structured-list-box">
          <div class="list-box-header panel-header-flex">
            <div>
              <h5 class="box-heading">📜 Certificates (Seminar/Workshop)</h5>
              <span class="interactive-tag">Credentials Log</span>
            </div>
            
            <div class="options-menu-container" @click.stop>
              <button class="icon-btn dots-btn" @click="showCertificatesMenu = !showCertificatesMenu">⋮</button>
              <div class="dropdown-menu" v-if="showCertificatesMenu">
                <button @click="toggleCertificatesEdit">
                  {{ isEditingCertificates ? '❌ Cancel Edit' : '✏️ Edit Information' }}
                </button>
              </div>
            </div>
          </div>

          <div v-if="certificateList.length === 0" class="empty-list-indicator">
            No certificate logs uploaded.
          </div>

          <div v-if="!isEditingCertificates">
            <div class="certificates-grid-container">
              <div v-for="cert in certificateList" :key="cert.id" class="certificate-matrix-card">
                <div class="cert-glyph-header">🏅</div>
                <h6 class="cert-title-label">{{ cert.title }}</h6>
                <p class="cert-authority-issuer">{{ cert.issuer }} • <span class="year-stamp">{{ cert.year }}</span></p>
              </div>
            </div>
          </div>

          <div v-else>
            <div class="certificates-grid-container">
              <div v-for="cert in certificateList" :key="cert.id" class="certificate-matrix-card">
                <button class="remove-badge-corner" @click="removeCertificateRecord(cert.id)">✕</button>
                <div class="cert-glyph-header">🏅</div>
                <input type="text" v-model="cert.title" class="meta-inline-input" placeholder="Title" style="font-size:0.9rem; font-weight:700; color:#fff; margin-bottom:6px;" />
                <input type="text" v-model="cert.issuer" class="meta-inline-input" placeholder="Issuer" style="font-size:0.78rem; margin-bottom:4px;" />
                <input type="text" v-model="cert.year" class="meta-inline-input year-stamp" placeholder="Year" style="font-size:0.78rem;" />
              </div>
            </div>

            <div class="append-list-node-form">
              <p class="form-title">⚡ Register New Event Certificate Node</p>
              <div class="form-inline-fields-grid variant-tri-column">
                <input type="text" v-model="newCertTitle" placeholder="Certificate / Workshop Event Title" class="form-input-control text-field" />
                <input type="text" v-model="newCertIssuer" placeholder="Issuing Institution Authority" class="form-input-control text-field" />
                <input type="text" v-model="newCertYear" placeholder="Year" class="form-input-control text-field style-year-modifier" />
                <button class="append-action-button" @click="addCertificateRecord">+ Log</button>
              </div>
            </div>
            
            <button class="save-changes-btn" @click="toggleCertificatesEdit">Save Changes</button>
          </div>
        </div>

      </div>
    </div>

    <div class="panel-card personal-graph-panel-widget">
      <div class="panel-header-block-layout">
        <div>
          <h5 class="box-heading">🕸️ Personal Relational Knowledge Graph</h5>
          <p class="box-sub-narrative">Interactive relational map tracing metadata associations across data files and credentials.</p>
        </div>
      </div>
      <div class="embedded-graph-viewport-shroud">
        <svg :viewBox="`0 0 ${width} ${height}`" class="embedded-profile-svg" preserveAspectRatio="xMidYMid meet">
          <line v-for="(edge, idx) in graphEdges" :key="`le-${idx}`"
            :x1="graphNodes.find(n => n.id === edge.source)?.x" :y1="graphNodes.find(n => n.id === edge.source)?.y"
            :x2="graphNodes.find(n => n.id === edge.target)?.x" :y2="graphNodes.find(n => n.id === edge.target)?.y"
            class="embedded-edge-wire" />
          <g v-for="node in graphNodes" :key="node.id" :transform="`translate(${node.x}, ${node.y})`" class="embedded-node-g">
            <circle r="24" class="embedded-halo-pulse" />
            <circle r="10" :class="['embedded-core-circle', { 'center-nexus': node.id === 101 }]" />
            <text y="26" class="embedded-node-text-tag">{{ node.label }}</text>
          </g>
        </svg>
      </div>
    </div>

    <div class="panel-card posts-repository-stream-container">
      <div class="panel-header-block-layout">
        <div>
          <h5 class="box-heading">📝 Stream Feed Node Repository</h5>
          <p class="box-sub-narrative">Publish code architectures, peer research assets, and academic files to the global graph.</p>
        </div>
      </div>

      <div v-if="!isCreatingPost" class="post-trigger-placeholder" @click="isCreatingPost = true">
        <div class="trigger-avatar">👤</div>
        <div class="trigger-input-btn">
          Share an update, research breakthrough, or code snippet...
        </div>
      </div>

      <div v-if="isCreatingPost" class="append-list-node-form post-append-modifier">
        <div class="creator-header-row">
          <p class="form-title">🚀 Broadcast New Stream Publication Entry Node</p>
          <button class="close-creator-btn" @click="isCreatingPost = false">✕</button>
        </div>
        
        <div class="composer-type-toggle-row">
          <span class="toggle-row-lbl">Select Node Type:</span>
          <div class="pill-toggle-button-group">
            <button type="button" :class="['toggle-badge-btn', { active: activePostType === 'Code' }]" @click="activePostType = 'Code'">
              💻 Code Base Asset
            </button>
            <button type="button" :class="['toggle-badge-btn', { active: activePostType === 'Research Paper' }]" @click="activePostType = 'Research Paper'">
              🔬 Research Paper
            </button>
            <button type="button" :class="['toggle-badge-btn', { active: activePostType === 'Academic Paper' }]" @click="activePostType = 'Academic Paper'">
              📚 Academic Paper
            </button>
          </div>
        </div>

        <div class="post-form-row-block">
          <input type="text" v-model="newPostTitle" placeholder="Asset Heading / Project Publication Title" class="form-input-control text-field title-flex-mod" />
          
          <select v-model="newPostPrivacy" class="form-input-control sub-privacy-dropdown">
            <option value="public">🌐 Public</option>
            <option value="followers">👥 Followers</option>
            <option value="private">🔒 Private</option>
          </select>
        </div>

        <div class="file-uploader-simulation-bar">
          <div class="upload-feedback-meta">
            <span class="attachment-badge-icon">📂</span>
            <span class="attachment-file-string">{{ fileInputLabelPlaceholder }}</span>
          </div>
          <div class="upload-action-buttons-wrapper">
            <button v-if="simulatedUploadedFiles.length > 0" type="button" class="clear-upload-btn" @click="clearSimulatedUploads">Discard Attachments</button>
            <button type="button" class="trigger-mock-upload-btn" @click="simulateFileUploadToggle">
              📎 Simulate File/Folder Upload
            </button>
          </div>
        </div>

        <div class="post-form-row-block margin-top-adjustment">
          <textarea v-model="newPostExcerpt" placeholder="Provide description summary parameters or project abstract data code findings..." class="form-input-control text-field textarea-modifier"></textarea>
          <button class="append-action-button post-submit-btn-modifier" @click="addPostRecord">Publish Node</button>
        </div>
      </div>

      <div class="posts-timeline-vertical-stack">
        <div v-for="post in postsList" :key="post.id" class="timeline-post-card">
          
          <div class="three-dots-wrapper">
            <button class="dots-trigger-button" @click="togglePostDotsMenu(post, $event)" title="Post Parameters Options">⋮</button>
            
            <div v-if="post.showDotsMenu" class="dots-dropdown-popup-card" @click="$event.stopPropagation()">
              <div class="popup-section-title">⚙️ Post Parameters</div>
              
              <div class="privacy-selector-menu-block">
                <span class="sub-label">Scope Visibility:</span>
                <div class="privacy-option-mini-grid">
                  <button :class="['mini-p-btn', { active: post.privacy === 'public' }]" @click="setPostPrivacy(post, 'public')">🌐 Public</button>
                  <button :class="['mini-p-btn', { active: post.privacy === 'followers' }]" @click="setPostPrivacy(post, 'followers')">👥 Friends</button>
                  <button :class="['mini-p-btn', { active: post.privacy === 'private' }]" @click="setPostPrivacy(post, 'private')">🔒 Private</button>
                </div>
              </div>

              <div class="dropdown-action-divider"></div>
              
              <button class="dropdown-action-item-btn edit-btn" @click="triggerInlineEditPost(post)">
                ✏️ Edit Content Details
              </button>
              <button class="dropdown-action-item-btn delete-btn" @click="removePostRecord(post.id)">
                ❌ Delete Post Node
              </button>
            </div>
          </div>

          <div class="post-card-header">
            <div class="post-metadata-left">
              <span class="post-type-tag" :class="post.type.toLowerCase().replace(/\s+/g, '-')">{{ post.type }}</span>
              
              <div v-if="post.isInlineEditing" class="inline-edit-title-container">
                <input type="text" v-model="post.title" class="form-input-control text-field inline-title-input" />
              </div>
              <h6 v-else class="post-main-title">{{ post.title }}</h6>
            </div>
            
            <div class="post-metadata-right">
              <span class="privacy-indicator-pill-badge">
                {{ post.privacy === 'public' ? '🌐' : post.privacy === 'followers' ? '👥' : '🔒' }} {{ post.privacy }}
              </span>
              <span class="post-timestamp-lbl">{{ post.date }}</span>
            </div>
          </div>

          <div class="post-description-click-box" @click="toggleFolderExplorerView(post)" title="Click box container frame to toggle attached workspace documents folder tree matrix views">
            
            <div v-if="post.isInlineEditing" class="inline-edit-body-container" @click="$event.stopPropagation()">
              <textarea v-model="post.excerpt" class="form-input-control text-field inline-textarea-modifier"></textarea>
              <button class="append-action-button save-edit-btn" @click="saveInlineEditPost(post)">Save Update</button>
            </div>
            
            <p v-else class="post-abstract-excerpt">{{ post.excerpt }}</p>
            
            <div class="click-expansion-hint-flag">
              <span class="hint-arrow">{{ post.isFolderExpanded ? '▼' : '▲' }}</span>
              {{ post.isFolderExpanded ? 'Click to collapse workspace file assets' : 'Click to expand attached folder tree (' + post.attachedFiles.length + ' assets)' }}
            </div>
          </div>

          <div v-if="post.isFolderExpanded" class="github-repo-explorer-layout">
            <div class="repo-header-commit-bar">
              <div class="commit-profile-identity">
                <span class="mini-avatar">👤</span>
                <span class="commit-author-name"><b>{{ username || 'Operator' }}</b> uploaded file systems data</span>
              </div>
              <div class="repo-branch-tag">Branch: <span>main</span></div>
            </div>

            <div class="repo-files-table-container">
              <div class="repo-file-row parent-directory-row" v-if="post.type === 'Code'">
                <div class="file-name-cell"><span class="icon">📁</span> <span class="blue-link">..</span></div>
                <div class="commit-message-cell">Return to upper parent cluster branch folder structure</div>
                <div class="time-size-cell">-</div>
              </div>

              <div v-for="(file, fIdx) in post.attachedFiles" :key="fIdx" class="repo-file-row">
                <div class="file-name-cell">
                  <span class="icon">{{ file.isFolder ? '📁' : '📄' }}</span>
                  <span :class="[file.isFolder ? 'blue-link' : 'white-file-txt']">{{ file.name }}</span>
                </div>
                <div class="commit-message-cell truncate-txt">
                  {{ file.commitMessage }}
                </div>
                <div class="time-size-cell">
                  {{ file.sizeOrAge }}
                </div>
              </div>
            </div>
          </div>

        </div>
      </div>
    </div>

  </div>
</template>

<style scoped>
*, *::before, *::after {
  box-sizing: border-box;
}

.view-container {
  display: flex;
  flex-direction: column;
  gap: 28px;
  width: 100%;
  color: #cbd5e1;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
  padding-bottom: 40px;
}

.panel-card {
  background: rgba(10, 10, 16, 0.65);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  border: 1px solid rgba(16, 185, 129, 0.12);
  border-radius: 16px;
  box-shadow: 0 12px 40px -12px rgba(0, 0, 0, 0.6), 0 2px 4px rgba(0, 0, 0, 0.3);
  transition: transform 0.3s cubic-bezier(0.25, 0.8, 0.25, 1), 
              border-color 0.3s cubic-bezier(0.25, 0.8, 0.25, 1), 
              box-shadow 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.panel-card:hover {
  border-color: rgba(16, 185, 129, 0.25);
  box-shadow: 0 16px 45px -10px rgba(16, 185, 129, 0.08), 0 12px 40px -12px rgba(0, 0, 0, 0.8);
}

.centralized-profile-header {
  width: 100%;
  padding: 50px 30px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  position: relative;
  background: radial-gradient(circle at 50% 0%, rgba(16, 185, 129, 0.08) 0%, rgba(6, 6, 9, 0.85) 100%);
}

.privacy-shifter-badge {
  position: absolute;
  top: 20px;
  right: 24px;
  display: flex;
  align-items: center;
  gap: 8px;
  background: rgba(0, 0, 0, 0.4);
  padding: 6px 14px;
  border-radius: 30px;
  border: 1px solid rgba(16, 185, 129, 0.2);
}

.privacy-label {
  font-size: 0.7rem;
  font-family: monospace;
  color: #10b981;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.privacy-select-element {
  background: transparent;
  border: none;
  color: #34d399;
  font-size: 0.8rem;
  padding: 2px 4px;
  outline: none;
  cursor: pointer;
  font-weight: 600;
}

.centered-identity-avatar-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.centered-avatar-circle-aura {
  padding: 8px;
  background: rgba(16, 185, 129, 0.03);
  border-radius: 50%;
  border: 1px solid rgba(16, 185, 129, 0.1);
  margin-bottom: 16px;
  transition: transform 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.centralized-profile-header:hover .centered-avatar-circle-aura {
  transform: scale(1.04);
  background: rgba(16, 185, 129, 0.06);
  border-color: rgba(16, 185, 129, 0.25);
}

.centered-avatar-circle {
  width: 105px;
  height: 105px;
  background: #000000;
  border: 2px solid #10b981;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 3rem;
  box-shadow: 0 0 30px rgba(16, 185, 129, 0.15);
}

.profile-username {
  font-size: 2rem;
  font-weight: 800;
  color: #ffffff;
  margin: 0;
  letter-spacing: -0.5px;
  background: linear-gradient(135deg, #ffffff 30%, #a7f3d0 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.profile-index-hash {
  font-family: monospace;
  color: #059669;
  font-size: 0.85rem;
  margin: 6px 0 0 0;
  opacity: 0.85;
}

.centered-bio-container {
  max-width: 620px;
  width: 100%;
  margin-bottom: 24px;
}

.editable-bio-textarea {
  width: 100%;
  background: rgba(0, 0, 0, 0.25);
  border: 1px solid rgba(255, 255, 255, 0.02);
  color: #94a3b8;
  font-size: 0.92rem;
  line-height: 1.6;
  text-align: center;
  resize: none;
  height: 56px;
  outline: none;
  border-radius: 10px;
  padding: 6px 12px;
  transition: all 0.25s ease;
}

.editable-bio-textarea:focus {
  border-color: rgba(16, 185, 129, 0.35);
  background: rgba(0, 0, 0, 0.5);
  color: #cbd5e1;
}

.side-by-side-social-metrics {
  display: flex;
  gap: 16px;
  justify-content: center;
}

.metric-counter-pill {
  display: flex;
  align-items: center;
  gap: 10px;
  background: rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(255, 255, 255, 0.05);
  padding: 8px 20px;
  border-radius: 30px;
  transition: border-color 0.2s;
}

.metric-counter-pill:hover {
  border-color: rgba(16, 185, 129, 0.2);
}

.pill-number {
  font-size: 0.95rem;
  font-weight: 700;
  color: #fff;
  font-family: monospace;
}

.pill-title {
  font-size: 0.75rem;
  color: #64748b;
  text-transform: uppercase;
  font-family: monospace;
  letter-spacing: 0.5px;
}

.profile-dashboard-grid {
  display: grid;
  grid-template-columns: 310px 1fr;
  gap: 28px;
  align-items: start;
}

@media (max-width: 900px) {
  .profile-dashboard-grid {
    grid-template-columns: 1fr;
    gap: 24px;
  }
}

.dashboard-sidebar-column, .dashboard-main-content-column {
  display: flex;
  flex-direction: column;
  gap: 28px;
}

.box-heading {
  margin: 0;
  font-size: 0.88rem;
  font-weight: 700;
  color: #f8fafc;
  font-family: monospace;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.box-sub-narrative {
  margin: 0 0 16px 0;
  font-size: 0.82rem;
  color: #64748b;
  line-height: 1.5;
}

.metadata-box, .structured-list-box {
  padding: 26px;
}

.meta-field-row {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 12px 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.04);
}

.meta-field-row:last-child {
  border-bottom: none;
}

.field-glyph {
  font-size: 1.2rem;
  opacity: 0.8;
}

.field-details {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.field-details label {
  font-size: 0.65rem;
  color: #059669;
  text-transform: uppercase;
  font-family: monospace;
  margin-bottom: 4px;
  font-weight: 600;
}

.meta-inline-input {
  background: transparent;
  border: none;
  color: #e2e8f0;
  font-size: 0.9rem;
  outline: none;
  width: 100%;
  transition: color 0.2s;
}

.meta-inline-input:focus {
  color: #fff;
}

.social-links-vertical-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.platform-hyperlink-anchor {
  display: flex;
  align-items: center;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.03);
  padding: 12px 16px;
  border-radius: 12px;
  text-decoration: none;
  color: #94a3b8;
  transition: all 0.25s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.platform-hyperlink-anchor:hover {
  border-color: rgba(16, 185, 129, 0.3);
  background: rgba(16, 185, 129, 0.04);
  color: #34d399;
  transform: translateX(4px);
}

.platform-icon {
  margin-right: 12px;
  font-size: 1.05rem;
}

.platform-name {
  font-size: 0.88rem;
  font-weight: 600;
  flex: 1;
}

.arrow-indicator {
  font-size: 0.8rem;
  opacity: 0.4;
  transition: transform 0.25s;
}

.platform-hyperlink-anchor:hover .arrow-indicator {
  transform: translate(2px, -2px);
  opacity: 0.9;
}

.panel-header-flex {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  border-bottom: 1px solid rgba(255, 255, 255, 0.04);
  padding-bottom: 14px;
  margin-bottom: 20px;
}

.options-menu-container {
  position: relative;
}

.dots-btn {
  background: transparent;
  border: none;
  color: #94a3b8;
  font-size: 1.5rem;
  line-height: 1;
  cursor: pointer;
  padding: 4px 10px;
  border-radius: 6px;
  transition: all 0.2s ease;
}

.dots-btn:hover {
  color: #10b981;
  background: rgba(16, 185, 129, 0.1);
}

.dropdown-menu {
  position: absolute;
  right: 0;
  top: 110%;
  background: #09090e;
  border: 1px solid rgba(16, 185, 129, 0.3);
  border-radius: 8px;
  padding: 6px 0;
  min-width: 170px;
  z-index: 50;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.8);
}

.dropdown-menu button {
  display: block;
  width: 100%;
  text-align: left;
  padding: 10px 16px;
  background: transparent;
  border: none;
  color: #cbd5e1;
  font-size: 0.82rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
}

.dropdown-menu button:hover {
  background: rgba(16, 185, 129, 0.15);
  color: #10b981;
}

.interactive-tag {
  font-family: monospace;
  font-size: 0.7rem;
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.2);
  color: #34d399;
  padding: 2px 8px;
  border-radius: 4px;
  margin-top: 6px;
  display: inline-block;
}

.empty-list-indicator {
  font-size: 0.85rem;
  color: #475569;
  padding: 14px;
  text-align: center;
}

.academic-vertical-ledger {
  list-style: none;
  padding: 0;
  margin: 0 0 24px 0;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.ledger-item-row {
  display: flex;
  align-items: center;
  gap: 16px;
  background: rgba(255, 255, 255, 0.01);
  border: 1px solid rgba(255, 255, 255, 0.03);
  padding: 14px 18px;
  border-radius: 12px;
  position: relative;
  transition: background 0.2s, border-color 0.2s;
}

.ledger-item-row:hover {
  background: rgba(16, 185, 129, 0.02);
  border-color: rgba(16, 185, 129, 0.15);
}

.item-icon-shield {
  width: 42px;
  height: 42px;
  background: rgba(0, 0, 0, 0.4);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid rgba(255, 255, 255, 0.02);
}

.item-main-details {
  flex: 1;
  min-width: 0;
}

.academic-tier-badge {
  font-size: 0.65rem;
  font-family: monospace;
  color: #10b981;
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.5px;
}

.institution-title {
  margin: 4px 0 0 0;
  font-size: 0.95rem;
  font-weight: 700;
  color: #f1f5f9;
}

.institution-timeframe {
  margin: 3px 0 0 0;
  font-size: 0.8rem;
  color: #64748b;
}

.remove-record-trigger {
  background: transparent;
  border: none;
  color: #ef4444;
  cursor: pointer;
  opacity: 0.3;
  transition: opacity 0.2s;
  font-size: 0.9rem;
}

.remove-record-trigger:hover {
  opacity: 1;
}

.read-only-item {
  border-color: transparent !important;
  background: transparent !important;
  padding: 8px 0;
  border-bottom: 1px solid rgba(255,255,255,0.05) !important;
  border-radius: 0;
}

.read-only-item:last-child {
  border-bottom: none !important;
}

.read-only-item:hover {
  background: transparent !important;
  border-color: transparent !important;
}

.append-list-node-form {
  background: rgba(0, 0, 0, 0.3);
  border: 1px solid rgba(255, 255, 255, 0.04);
  padding: 20px;
  border-radius: 14px;
  margin-top: 20px;
}

.form-title {
  margin: 0 0 14px 0;
  font-size: 0.78rem;
  font-family: monospace;
  color: #34d399;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  font-weight: bold;
}

.form-inline-fields-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  width: 100%;
}

.form-inline-fields-grid .drop-select {
  flex: 1 1 150px;
}

.form-inline-fields-grid .text-field {
  flex: 2 1 200px;
}

.form-inline-fields-grid .style-year-modifier {
  max-width: 90px;
  flex: 1 1 70px;
}

.form-input-control {
  background: #050507;
  border: 1px solid rgba(16, 185, 129, 0.2);
  color: #fff;
  padding: 10px 14px;
  border-radius: 8px;
  font-size: 0.85rem;
  outline: none;
  transition: all 0.25s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.form-input-control:focus {
  border-color: #10b981;
  box-shadow: 0 0 12px rgba(16, 185, 129, 0.15);
  background: #000;
}

.append-action-button {
  background: #10b981;
  color: #032c1e;
  border: none;
  padding: 10px 20px;
  font-size: 0.82rem;
  font-weight: 700;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.append-action-button:hover {
  background: #34d399;
  transform: translateY(-1px);
}

.save-changes-btn {
  margin-top: 16px;
  background: rgba(16, 185, 129, 0.1);
  color: #10b981;
  border: 1px solid #10b981;
  padding: 12px 16px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 700;
  width: 100%;
  transition: all 0.2s;
}

.save-changes-btn:hover {
  background: #10b981;
  color: #fff;
  box-shadow: 0 0 12px rgba(16, 185, 129, 0.4);
}

.certificates-grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 14px;
  margin-bottom: 24px;
}

.certificate-matrix-card {
  background: rgba(255, 255, 255, 0.01);
  border: 1px solid rgba(255, 255, 255, 0.03);
  border-radius: 14px;
  padding: 20px;
  position: relative;
  display: flex;
  flex-direction: column;
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
}

.certificate-matrix-card:hover {
  background: rgba(16, 185, 129, 0.02);
  border-color: rgba(16, 185, 129, 0.15);
  transform: translateY(-2px);
}

.remove-badge-corner {
  position: absolute;
  top: 10px;
  right: 10px;
  background: transparent;
  border: none;
  color: #ef4444;
  cursor: pointer;
  opacity: 0.2;
  transition: opacity 0.2s;
}

.remove-badge-corner:hover {
  opacity: 1;
}

.cert-glyph-header {
  font-size: 1.3rem;
  margin-bottom: 6px;
}

.cert-title-label {
  margin: 0;
  font-size: 0.9rem;
  font-weight: 700;
  color: #fff;
  line-height: 1.4;
}

.cert-authority-issuer {
  margin: 6px 0 0 0;
  font-size: 0.78rem;
  color: #64748b;
}

.year-stamp {
  color: #34d399;
  font-weight: 600;
  font-family: monospace;
}

.personal-graph-panel-widget {
  padding: 26px;
  width: 100%;
}

.embedded-graph-viewport-shroud {
  width: 100%;
  height: 230px;
  background: #040406;
  border-radius: 14px;
  border: 1px solid rgba(255, 255, 255, 0.03);
  overflow: hidden;
}

.embedded-edge-wire {
  stroke: rgba(16, 185, 129, 0.12);
  stroke-width: 1.5;
  transition: stroke 0.3s;
}

.personal-graph-panel-widget:hover .embedded-edge-wire {
  stroke: rgba(16, 185, 129, 0.22);
}

.embedded-halo-pulse {
  fill: rgba(16, 185, 129, 0.015);
  transition: fill 0.3s;
}

.embedded-node-g:hover .embedded-halo-pulse {
  fill: rgba(16, 185, 129, 0.05);
}

.embedded-core-circle {
  fill: #022c22;
  stroke: #10b981;
  stroke-width: 2.5;
}

.center-nexus {
  fill: #451a03 !important;
  stroke: #fbbf24 !important;
}

.embedded-node-text-tag {
  fill: #57687e;
  font-size: 10px;
  font-family: monospace;
  text-anchor: middle;
  font-weight: bold;
  transition: fill 0.2s;
}

.embedded-node-g:hover .embedded-node-text-tag {
  fill: #cbd5e1;
}

.posts-repository-stream-container {
  padding: 30px;
  width: 100%;
}

.post-append-modifier {
  background: rgba(0, 0, 0, 0.25);
  border: 1px solid rgba(16, 185, 129, 0.15);
  margin-bottom: 28px;
}

.post-trigger-placeholder {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 16px 20px;
  margin-bottom: 28px;
  background: rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(16, 185, 129, 0.15);
  border-radius: 12px;
  cursor: text;
  transition: all 0.25s ease;
}

.post-trigger-placeholder:hover {
  background: rgba(16, 185, 129, 0.05);
  border-color: #10b981;
}

.trigger-avatar {
  font-size: 1.4rem;
  background: rgba(16, 185, 129, 0.1);
  width: 42px;
  height: 42px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  border: 1px solid rgba(16, 185, 129, 0.3);
}

.trigger-input-btn {
  flex: 1;
  color: #64748b;
  font-size: 0.9rem;
  pointer-events: none;
}

.post-trigger-placeholder:hover .trigger-input-btn {
  color: #94a3b8;
}

.creator-header-row {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 12px;
}

.close-creator-btn {
  background: transparent;
  border: none;
  color: #64748b;
  font-size: 1.2rem;
  cursor: pointer;
  transition: color 0.2s;
  padding: 0;
  line-height: 1;
}

.close-creator-btn:hover {
  color: #ef4444;
}

.composer-type-toggle-row {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 18px;
  flex-wrap: wrap;
}

.toggle-row-lbl {
  font-size: 0.78rem;
  font-family: monospace;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.pill-toggle-button-group {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.toggle-badge-btn {
  background: rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(255, 255, 255, 0.04);
  color: #94a3b8;
  padding: 8px 16px;
  border-radius: 30px;
  font-size: 0.8rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.25s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.toggle-badge-btn:hover {
  border-color: rgba(16, 185, 129, 0.3);
  color: #fff;
}

.toggle-badge-btn.active {
  background: rgba(16, 185, 129, 0.12);
  border-color: #10b981;
  color: #34d399;
}

.post-form-row-block {
  display: flex;
  gap: 12px;
  width: 100%;
  margin-bottom: 14px;
  flex-wrap: wrap;
}

.post-form-row-block .title-flex-mod {
  flex: 1 1 300px;
}

.post-form-row-block .sub-privacy-dropdown {
  flex: 0 0 130px;
  cursor: pointer;
}

.file-uploader-simulation-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #050507;
  border: 1px dashed rgba(16, 185, 129, 0.2);
  padding: 12px 16px;
  border-radius: 10px;
  margin-bottom: 14px;
  flex-wrap: wrap;
  gap: 12px;
}

.upload-feedback-meta {
  display: flex;
  align-items: center;
  gap: 8px;
}

.attachment-file-string {
  font-size: 0.82rem;
  color: #84cc16;
  font-family: monospace;
}

.upload-action-buttons-wrapper {
  display: flex;
  gap: 10px;
}

.clear-upload-btn {
  background: transparent;
  border: 1px solid rgba(239, 68, 68, 0.3);
  color: #fca5a5;
  padding: 6px 12px;
  font-size: 0.78rem;
  border-radius: 6px;
  cursor: pointer;
}

.clear-upload-btn:hover {
  background: rgba(239, 68, 68, 0.1);
}

.trigger-mock-upload-btn {
  background: transparent;
  border: 1px solid rgba(16, 185, 129, 0.4);
  color: #34d399;
  padding: 6px 14px;
  font-size: 0.78rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
}

.trigger-mock-upload-btn:hover {
  background: rgba(16, 185, 129, 0.08);
}

.textarea-modifier {
  flex: 1 1 100%;
  height: 80px;
  resize: none;
  line-height: 1.5;
}

.post-submit-btn-modifier {
  flex: 0 0 auto;
  min-width: 150px;
  height: 40px;
  margin-left: auto;
}

.margin-top-adjustment {
  margin-bottom: 0;
}

.posts-timeline-vertical-stack {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.timeline-post-card {
  background: rgba(255, 255, 255, 0.005);
  border: 1px solid rgba(255, 255, 255, 0.03);
  border-radius: 16px;
  padding: 26px;
  position: relative;
  transition: all 0.3s ease;
}

.timeline-post-card:hover {
  background: rgba(255, 255, 255, 0.01);
  border-color: rgba(16, 185, 129, 0.15);
}

.three-dots-wrapper {
  position: absolute;
  top: 20px;
  right: 20px;
  display: inline-block;
  z-index: 10;
}

.dots-trigger-button {
  background: transparent;
  border: none;
  color: #475569;
  font-size: 1.5rem;
  cursor: pointer;
  padding: 0 8px;
  line-height: 1;
  transition: color 0.2s;
}

.timeline-post-card:hover .dots-trigger-button {
  color: #94a3b8;
}

.dots-trigger-button:hover {
  color: #fff !important;
}

.dots-dropdown-popup-card {
  position: absolute;
  top: 100%;
  right: 0;
  width: 240px;
  background: #09090e;
  border: 1px solid rgba(16, 185, 129, 0.3);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.85);
  border-radius: 12px;
  padding: 14px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  z-index: 50;
  margin-top: 4px;
}

.popup-section-title {
  font-size: 0.68rem;
  font-family: monospace;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  border-bottom: 1px solid rgba(255,255,255,0.04);
  padding-bottom: 6px;
}

.privacy-selector-menu-block {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.privacy-selector-menu-block .sub-label {
  font-size: 0.72rem;
  color: #94a3b8;
  font-family: monospace;
}

.privacy-option-mini-grid {
  display: flex;
  gap: 4px;
}

.mini-p-btn {
  flex: 1;
  background: #000;
  border: 1px solid rgba(255,255,255,0.05);
  color: #64748b;
  padding: 4px 0;
  font-size: 0.7rem;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}

.mini-p-btn:hover {
  color: #fff;
  border-color: rgba(255,255,255,0.1);
}

.mini-p-btn.active {
  background: rgba(16, 185, 129, 0.1);
  border-color: #10b981;
  color: #34d399;
}

.dropdown-action-divider {
  height: 1px;
  background: rgba(255,255,255,0.04);
  margin: 2px 0;
}

.dropdown-action-item-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  width: 100%;
  background: transparent;
  border: none;
  padding: 8px;
  font-size: 0.82rem;
  text-align: left;
  border-radius: 6px;
  cursor: pointer;
  color: #cbd5e1;
}

.dropdown-action-item-btn:hover {
  background: rgba(255,255,255,0.03);
}

.dropdown-action-item-btn.delete-btn {
  color: #ef4444;
}

.dropdown-action-item-btn.delete-btn:hover {
  background: rgba(239, 68, 68, 0.08);
}

.post-card-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 16px;
  padding-right: 24px;
  flex-wrap: wrap;
  gap: 12px;
}

.post-metadata-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.post-metadata-right {
  display: flex;
  align-items: center;
  gap: 12px;
  font-family: monospace;
  font-size: 0.8rem;
}

.privacy-indicator-pill-badge {
  color: #059669;
  text-transform: uppercase;
  font-size: 0.72rem;
  background: rgba(0, 0, 0, 0.3);
  padding: 2px 8px;
  border-radius: 4px;
}

.post-timestamp-lbl {
  color: #475569;
}

.post-type-tag {
  font-family: monospace;
  font-size: 0.68rem;
  font-weight: 700;
  text-transform: uppercase;
  padding: 4px 10px;
  border-radius: 6px;
}

.post-type-tag.code {
  background: rgba(59, 130, 246, 0.1);
  border: 1px solid rgba(59, 130, 246, 0.2);
  color: #60a5fa;
}

.post-type-tag.research-paper {
  background: rgba(16, 185, 129, 0.1);
  border: 1px solid rgba(16, 185, 129, 0.2);
  color: #34d399;
}

.post-type-tag.academic-paper {
  background: rgba(139, 92, 246, 0.1);
  border: 1px solid rgba(139, 92, 246, 0.2);
  color: #a78bfa;
}

.post-main-title {
  margin: 0;
  font-size: 1.1rem;
  font-weight: 700;
  color: #ffffff;
  letter-spacing: -0.3px;
}

.inline-edit-title-container {
  flex: 1;
}

.inline-title-input {
  width: 100%;
  font-size: 1rem;
  padding: 6px 12px;
}

.inline-edit-body-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
}

.inline-textarea-modifier {
  width: 100%;
  min-height: 70px;
  resize: vertical;
  line-height: 1.5;
}

.save-edit-btn {
  align-self: flex-end;
  padding: 6px 14px;
  font-size: 0.78rem;
}

.post-description-click-box {
  background: rgba(0, 0, 0, 0.18);
  border: 1px solid rgba(255, 255, 255, 0.02);
  border-radius: 10px;
  padding: 16px;
  cursor: pointer;
  transition: all 0.25s cubic-bezier(0.25, 0.8, 0.25, 1);
  margin-bottom: 14px;
}

.post-description-click-box:hover {
  background: rgba(0, 0, 0, 0.3);
  border-color: rgba(16, 185, 129, 0.18);
}

.post-abstract-excerpt {
  margin: 0;
  font-size: 0.92rem;
  color: #94a3b8;
  line-height: 1.6;
}

.click-expansion-hint-flag {
  margin-top: 12px;
  font-size: 0.72rem;
  color: #10b981;
  font-family: monospace;
  text-transform: uppercase;
  text-align: right;
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 6px;
  letter-spacing: 0.5px;
  opacity: 0.6;
  transition: opacity 0.2s;
}

.post-description-click-box:hover .click-expansion-hint-flag {
  opacity: 1;
}

.hint-arrow {
  transition: transform 0.25s;
}

.github-repo-explorer-layout {
  background: #0d1117;
  border: 1px solid #30363d;
  border-radius: 10px;
  overflow: hidden;
  margin-top: 16px;
}

.repo-header-commit-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #161b22;
  border-bottom: 1px solid #30363d;
  padding: 14px 16px;
  font-size: 0.8rem;
}

.commit-profile-identity {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #c9d1d9;
}

.commit-author-name b {
  color: #edf2f7;
}

.repo-branch-tag {
  color: #8b949e;
}

.repo-branch-tag span {
  color: #58a6ff;
  font-weight: bold;
}

.repo-files-table-container {
  display: flex;
  flex-direction: column;
  background: #0d1117;
}

.repo-file-row {
  display: grid;
  grid-template-columns: 220px 1fr 120px;
  gap: 16px;
  border-bottom: 1px solid #21262d;
  padding: 12px 16px;
  align-items: center;
  font-size: 0.8rem;
}

.repo-file-row:last-child {
  border-bottom: none;
}

.repo-file-row:hover {
  background: rgba(88, 166, 255, 0.04);
}

.parent-directory-row {
  background: rgba(0, 0, 0, 0.15);
}

.file-name-cell {
  display: flex;
  align-items: center;
  gap: 8px;
  min-width: 0;
}

.file-name-cell .icon {
  font-size: 0.95rem;
}

.blue-link {
  color: #58a6ff;
  cursor: pointer;
  font-weight: 500;
  text-decoration: none;
}

.blue-link:hover {
  text-decoration: underline;
}

.white-file-txt {
  color: #c9d1d9;
}

.commit-message-cell {
  color: #8b949e;
  min-width: 0;
}

.truncate-txt {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.time-size-cell {
  color: #8b949e;
  text-align: right;
  white-space: nowrap;
  font-family: monospace;
}
</style>
