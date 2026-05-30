<script lang="ts">
import { ref } from 'vue'

export interface NoteNode {
  id: number
  label: string
  content: string
  x: number
  y: number
  vx: number
  vy: number
  isDragged?: boolean
  isRoot?: boolean
  fontSize?: string
  fontFamily?: string
  color?: string
  isBold?: boolean
  isItalic?: boolean
  isUnderline?: boolean
  isStrikethrough?: boolean
}

export interface NoteEdge {
  id: string
  source: number
  target: number
}

export const sharedNodes = ref<NoteNode[]>([
  { id: 1, label: 'Main Vault Core', content: 'This is the primary root node of your personal web knowledge vault setup.', x: 425, y: 200, vx: 0, vy: 0, isRoot: true, fontSize: '16px', fontFamily: 'monospace', color: '#cbd5e1', isBold: false, isItalic: false, isUnderline: false, isStrikethrough: false },
  { id: 2, label: 'Research Manifest', content: 'Subnode tracking system architecture, database performance records, and dynamic links.', x: 300, y: 150, vx: 0, vy: 0, fontSize: '16px', fontFamily: 'sans-serif', color: '#cbd5e1', isBold: false, isItalic: false, isUnderline: false, isStrikethrough: false },
  { id: 3, label: 'Social Graph Index', content: 'Subnode managing interface telemetry parameters, layout grids, and communication flows.', x: 550, y: 250, vx: 0, vy: 0, fontSize: '16px', fontFamily: 'serif', color: '#cbd5e1', isBold: false, isItalic: false, isUnderline: false, isStrikethrough: false },
])

export const sharedEdges = ref<NoteEdge[]>([
  { id: 'e1-2', source: 1, target: 2 },
  { id: 'e1-3', source: 1, target: 3 },
  { id: 'e2-3', source: 2, target: 3 }
])
</script>

<script setup lang="ts">
import { onMounted, onUnmounted, nextTick, computed } from 'vue'

defineProps<{ username: string }>()

const width = 850
const height = 400

const nodes = sharedNodes
const edges = sharedEdges

const draggedNode = ref<NoteNode | null>(null)
let animationFrameId: number

const globalScaleFactor = computed(() => {
  if (nodes.value.length <= 3) return 1.0
  return Math.max(0.45, 1.0 - (nodes.value.length - 3) * 0.035)
})

const coreRadius = computed(() => 18 * globalScaleFactor.value)
const haloRadius = computed(() => 35 * globalScaleFactor.value)
const innerRadius = computed(() => 6 * globalScaleFactor.value)
const labelOffset = computed(() => coreRadius.value + 14)

const focusedNodeId = ref<number | null>(null)

const handleNodeFocus = (node: NoteNode) => {
  focusedNodeId.value = node.id
}

const clearNodeFocus = () => {
  focusedNodeId.value = null
}

const highlightedNodes = computed(() => {
  if (focusedNodeId.value === null) return new Set<number>()
  const nodesSet = new Set<number>([focusedNodeId.value])
  edges.value.forEach(e => {
    if (e.source === focusedNodeId.value) nodesSet.add(e.target)
    if (e.target === focusedNodeId.value) nodesSet.add(e.source)
  })
  return nodesSet
})

const highlightedEdges = computed(() => {
  if (focusedNodeId.value === null) return new Set<string>()
  const edgesSet = new Set<string>()
  edges.value.forEach(e => {
    if (e.source === focusedNodeId.value || e.target === focusedNodeId.value) {
      edgesSet.add(e.id)
    }
  })
  return edgesSet
})

const tick = () => {
  const centerForce = 0.005
  const repelForce = 3000 * globalScaleFactor.value
  const springLength = 150 * globalScaleFactor.value
  const springForce = 0.02
  const friction = 0.85

  nodes.value.forEach(node => {
    if (node.isDragged) return
    
    node.vx += (width / 2 - node.x) * centerForce
    node.vy += (height / 2 - node.y) * centerForce

    nodes.value.forEach(otherNode => {
      if (node.id === otherNode.id) return
      const dx = node.x - otherNode.x
      const dy = node.y - otherNode.y
      let dist = Math.sqrt(dx * dx + dy * dy)
      if (dist === 0) dist = 0.1
      
      const interactionZone = 200 * globalScaleFactor.value
      if (dist < interactionZone) {
        const f = repelForce / (dist * dist)
        node.vx += (dx / dist) * f
        node.vy += (dy / dist) * f
      }
    })
  })

  edges.value.forEach(edge => {
    const sourceNode = nodes.value.find(n => n.id === edge.source)
    const targetNode = nodes.value.find(n => n.id === edge.target)
    if (!sourceNode || !targetNode) return

    const dx = targetNode.x - sourceNode.x
    const dy = targetNode.y - sourceNode.y
    const dist = Math.sqrt(dx * dx + dy * dy)
    
    const f = (dist - springLength) * springForce
    const fx = (dx / dist) * f
    const fy = (dy / dist) * f

    if (!sourceNode.isDragged) {
      sourceNode.vx += fx
      sourceNode.vy += fy
    }
    if (!targetNode.isDragged) {
      targetNode.vx -= fx
      targetNode.vy -= fy
    }
  })

  nodes.value.forEach(node => {
    if (node.isDragged) return
    node.vx *= friction
    node.vy *= friction
    node.x += node.vx
    node.y += node.vy

    const padding = coreRadius.value + 12
    if (node.x < padding) { node.x = padding; node.vx *= -1 }
    if (node.x > width - padding) { node.x = width - padding; node.vx *= -1 }
    if (node.y < padding) { node.y = padding; node.vy *= -1 }
    if (node.y > height - padding) { node.y = height - padding; node.vy *= -1 }
  })

  animationFrameId = requestAnimationFrame(tick)
}

const startDrag = (node: NoteNode, event: MouseEvent | TouchEvent) => {
  draggedNode.value = node
  node.isDragged = true
  handleNodeFocus(node)
  updateDragPosition(event)
  
  window.addEventListener('mousemove', handleDrag)
  window.addEventListener('touchmove', handleDrag, { passive: false })
  window.addEventListener('mouseup', stopDrag)
  window.addEventListener('touchend', stopDrag)
}

const handleDrag = (event: MouseEvent | TouchEvent) => {
  if (event.type === 'touchmove') event.preventDefault()
  updateDragPosition(event)
}

const updateDragPosition = (event: MouseEvent | TouchEvent) => {
  if (!draggedNode.value) return
  const svg = document.querySelector('.svg-layer') as SVGSVGElement
  if (!svg) return
  
  const pt = svg.createSVGPoint()
  let clientX, clientY
  
  if (event instanceof TouchEvent) {
    clientX = event.touches[0].clientX
    clientY = event.touches[0].clientY
  } else {
    clientX = event.clientX
    clientY = event.clientY
  }
  
  pt.x = clientX
  pt.y = clientY
  const svgP = pt.matrixTransform(svg.getScreenCTM()?.inverse())
  
  draggedNode.value.x = svgP.x
  draggedNode.value.y = svgP.y
}

const stopDrag = () => {
  if (draggedNode.value) {
    draggedNode.value.isDragged = false
    draggedNode.value = null
  }
  window.removeEventListener('mousemove', handleDrag)
  window.removeEventListener('touchmove', handleDrag)
  window.removeEventListener('mouseup', stopDrag)
  window.removeEventListener('touchend', stopDrag)
}

onMounted(() => {
  tick()
})

onUnmounted(() => {
  cancelAnimationFrame(animationFrameId)
})

const getSourceNode = (edge: NoteEdge) => nodes.value.find(n => n.id === edge.source)
const getTargetNode = (edge: NoteEdge) => nodes.value.find(n => n.id === edge.target)

const selectedNode = ref<NoteNode | null>(null)
const tempNoteLabel = ref('')
const tempNoteContent = ref('')

const currentSize = ref('16px')
const currentFont = ref('monospace')
const currentColor = ref('#cbd5e1')
const isBold = ref(false)
const isItalic = ref(false)
const isUnderline = ref(false)
const isStrikethrough = ref(false)

const openNoteModal = (node: NoteNode) => {
  selectedNode.value = node
  tempNoteLabel.value = node.label
  tempNoteContent.value = node.content
  
  currentSize.value = node.fontSize || '16px'
  currentFont.value = node.fontFamily || 'monospace'
  currentColor.value = node.color || '#cbd5e1'
  isBold.value = node.isBold || false
  isItalic.value = node.isItalic || false
  isUnderline.value = node.isUnderline || false
  isStrikethrough.value = node.isStrikethrough || false

  nextTick(() => {
    initCanvas()
  })
}

const closeModal = () => {
  selectedNode.value = null
  clearCanvas()
}

const saveNoteData = () => {
  if (selectedNode.value) {
    selectedNode.value.label = tempNoteLabel.value
    selectedNode.value.content = tempNoteContent.value
    
    selectedNode.value.fontSize = currentSize.value
    selectedNode.value.fontFamily = currentFont.value
    selectedNode.value.color = currentColor.value
    selectedNode.value.isBold = isBold.value
    selectedNode.value.isItalic = isItalic.value
    selectedNode.value.isUnderline = isUnderline.value
    selectedNode.value.isStrikethrough = isStrikethrough.value
  }
  closeModal()
}

const createRootNode = () => {
  const newId = Date.now()
  const newRootNode: NoteNode = {
    id: newId,
    label: `Primary Index Cluster #${nodes.value.filter(n => n.isRoot).length + 1}`,
    content: `Brand new root directory established. Double-click to begin formatting.`,
    x: width / 2 + (Math.random() * 40 - 20),
    y: height / 2 + (Math.random() * 40 - 20),
    vx: 0,
    vy: 0,
    isRoot: true,
    fontSize: '16px',
    fontFamily: 'monospace',
    color: '#cbd5e1',
    isBold: false,
    isItalic: false,
    isUnderline: false,
    isStrikethrough: false
  }
  nodes.value.push(newRootNode)
}

const createSubnoteNode = () => {
  if (!selectedNode.value) return

  const parentId = selectedNode.value.id
  const parentX = selectedNode.value.x
  const parentY = selectedNode.value.y
  const newSubnodeId = Date.now()

  const newSubnode: NoteNode = {
    id: newSubnodeId,
    label: `Sub-Branch Node`,
    content: `Interrelated structural sub-note generated under parent index ID: ${parentId}.`,
    x: parentX + (Math.random() * 80 - 40),
    y: parentY + 80, 
    vx: 0,
    vy: 0,
    fontSize: '16px',
    fontFamily: 'monospace',
    color: '#cbd5e1',
    isBold: false,
    isItalic: false,
    isUnderline: false,
    isStrikethrough: false
  }

  edges.value.push({
    id: `edge-${parentId}-${newSubnodeId}`,
    source: parentId,
    target: newSubnodeId
  })

  nodes.value.push(newSubnode)
  selectedNode.value.label = tempNoteLabel.value
  selectedNode.value.content = tempNoteContent.value
  openNoteModal(newSubnode)
}

const deleteCurrentNode = () => {
  if (!selectedNode.value) return
  const targetId = selectedNode.value.id

  edges.value = edges.value.filter(edge => edge.source !== targetId && edge.target !== targetId)
  nodes.value = nodes.value.filter(node => node.id !== targetId)
  closeModal()
}

const drawingCanvas = ref<HTMLCanvasElement | null>(null)
const isDrawing = ref(false)
let ctx: CanvasRenderingContext2D | null = null

const initCanvas = () => {
  if (!drawingCanvas.value) return
  const canvas = drawingCanvas.value
  const parent = canvas.parentElement
  if (parent) {
    canvas.width = parent.clientWidth
    canvas.height = parent.clientHeight
  }

  ctx = canvas.getContext('2d')
  if (ctx) {
    ctx.lineWidth = 2
    ctx.lineCap = 'round'
    ctx.lineJoin = 'round'
    ctx.strokeStyle = '#10b981'
  }
}

const startDrawing = (e: MouseEvent | TouchEvent) => {
  if (!ctx || !drawingCanvas.value) return
  isDrawing.value = true
  const pos = getCanvasPos(e)
  ctx.beginPath()
  ctx.moveTo(pos.x, pos.y)
}

const draw = (e: MouseEvent | TouchEvent) => {
  if (!isDrawing.value || !ctx || !drawingCanvas.value) return
  if (e instanceof TouchEvent) e.preventDefault()
  const pos = getCanvasPos(e)
  ctx.lineTo(pos.x, pos.y)
  ctx.stroke()
}

const stopDrawing = () => {
  isDrawing.value = false
  if (ctx) ctx.closePath()
}

const clearCanvas = () => {
  if (!ctx || !drawingCanvas.value) return
  ctx.clearRect(0, 0, drawingCanvas.value.width, drawingCanvas.value.height)
}

const getCanvasPos = (evt: MouseEvent | TouchEvent) => {
  const rect = drawingCanvas.value!.getBoundingClientRect()
  let clientX, clientY
  if (evt instanceof TouchEvent) {
    clientX = evt.touches[0].clientX
    clientY = evt.touches[0].clientY
  } else {
    clientX = evt.clientX
    clientY = evt.clientY
  }
  return {
    x: clientX - rect.left,
    y: clientY - rect.top
  }
}
</script>

<template>
  <div class="view-container">
    <header class="hub-header">
      <div class="header-titles">
        <h1 class="view-title">Terminal Home Hub</h1>
        <p class="view-tagline">Welcome back, {{ username || 'Operator' }}. System operating at baseline efficiency.</p>
      </div>
      <div class="quick-stats">
        <div class="stat-badge"><span class="badge-icon">📡</span> Ping: 12ms</div>
        <div class="stat-badge"><span class="badge-icon">🕸️</span> Nodes: {{ nodes.length }}</div>
      </div>
    </header>

    <div class="panel-card interactive-graph-panel">
      <div class="panel-header">
        <div class="header-left">
          <h5>Neural Graph Map</h5>
          <span class="status-pulse">Active Sync</span>
        </div>
        <button class="add-root-trigger" @click="createRootNode">
          ➕ Initialize Root Node
        </button>
      </div>
      
      <div class="graph-container">
        <div v-if="nodes.length === 0" class="empty-graph-matrix-overlay">
          <div class="terminal-glitch-text">⚠️ INTERFACE SYSTEM VAULT EMPTY</div>
          <p>All conceptual notes and connection edges have been dropped from runtime ledger.</p>
          <button class="init-root-btn" @click="createRootNode">
            🕸️ Initialize New Root Node Cluster
          </button>
        </div>

        <svg :viewBox="`0 0 ${width} ${height}`" class="svg-layer" preserveAspectRatio="xMidYMid meet" @click.self="clearNodeFocus">
          <defs>
            <pattern id="gridPattern" width="40" height="40" patternUnits="userSpaceOnUse">
              <path d="M 40 0 L 0 0 0 40" fill="none" stroke="rgba(16,185,129,0.05)" stroke-width="1" />
            </pattern>
          </defs>
          <rect width="100%" height="100%" fill="url(#gridPattern)" @click="clearNodeFocus" />

          <line 
            v-for="edge in edges" 
            :key="edge.id"
            :x1="getSourceNode(edge)?.x"
            :y1="getSourceNode(edge)?.y"
            :x2="getTargetNode(edge)?.x"
            :y2="getTargetNode(edge)?.y"
            :class="['graph-edge', { 'highlighted-edge': highlightedEdges.has(edge.id) }]"
          />

          <g 
            v-for="node in nodes" 
            :key="node.id"
            :transform="`translate(${node.x}, ${node.y})`"
            class="graph-node-group"
            @mousedown="startDrag(node, $event)"
            @touchstart.prevent="startDrag(node, $event)"
            @click.stop="handleNodeFocus(node)"
            @dblclick="openNoteModal(node)"
          >
            <circle :r="haloRadius" :class="['node-halo', { 'halo-active': highlightedNodes.has(node.id) }]" />
            <circle :r="coreRadius" :class="['node-core', { 'root-core': node.isRoot, 'highlighted-core': highlightedNodes.has(node.id) }]" />
            <circle :r="innerRadius" :class="['node-inner', { 'root-inner': node.isRoot, 'highlighted-inner': highlightedNodes.has(node.id) }]" />
            <text :y="labelOffset" class="node-label">{{ node.label }}</text>
          </g>
        </svg>

        <div class="graph-legend">
          <p><span>Single-Click</span> to trace node neighborhood</p>
          <p><span>Double-Click</span> Node to View Full-Screen Workspace</p>
          <p><span>Drag</span> to manipulate network matrix configurations</p>
        </div>
      </div>
    </div>

    <div class="grid-nodes-container">
      <div 
        v-for="node in nodes" 
        :key="`card-${node.id}`" 
        :class="['panel-card', 'node-card', { 'root-card': node.isRoot }]"
        @click="openNoteModal(node)"
      >
        <div class="node-card-header">
          <span class="node-icon">{{ node.isRoot ? '👑' : '📄' }}</span>
          <h6>{{ node.label }}</h6>
        </div>
        <p class="node-content">{{ node.content }}</p>
        <div class="node-footer">
          <span class="node-id">#ID: {{ node.id }}</span>
          <button class="view-btn">Inspect Data</button>
        </div>
      </div>
    </div>

    <Teleport to="body">
      <div v-if="selectedNode" class="fullscreen-modal-overlay">
        
        <div class="editor-control-dashboard">
          
          <div class="text-format-toolbar">
            <div class="tool-group">
              <label>Size</label>
              <select v-model="currentSize" class="toolbar-dropdown">
                <option value="12px">Small</option>
                <option value="16px">Normal</option>
                <option value="22px">Large</option>
                <option value="32px">Matrix</option>
              </select>
            </div>

            <div class="tool-group">
              <label>Font</label>
              <select v-model="currentFont" class="toolbar-dropdown">
                <option value="monospace">Monospace</option>
                <option value="sans-serif">Sans-Serif</option>
                <option value="serif">Classic Serif</option>
                <option value="'Courier New'">Courier Terminal</option>
              </select>
            </div>

            <div class="tool-group">
              <label>Color</label>
              <input type="color" v-model="currentColor" class="toolbar-color-picker" />
            </div>

            <div class="toolbar-divider"></div>

            <div class="format-buttons-wrapper">
              <button :class="['format-toggle-btn', { active: isBold }]" @click="isBold = !isBold" title="Bold Text">B</button>
              <button :class="['format-toggle-btn', { active: isItalic }]" @click="isItalic = !isItalic" title="Italic Text">I</button>
              <button :class="['format-toggle-btn', { active: isUnderline }]" @click="isUnderline = !isUnderline" title="Underline Text">U</button>
              <button :class="['format-toggle-btn', { active: isStrikethrough }]" @click="isStrikethrough = !isStrikethrough" title="Strikethrough Text">S</button>
            </div>
          </div>

          <div class="system-operations-toolbar">
            <button class="op-action-btn subnote-btn" @click="createSubnoteNode">
              <span class="btn-glyph">🌿</span> + Subnote Vertex
            </button>
            
            <button class="op-action-btn delete-node-btn" @click="deleteCurrentNode">
              <span class="btn-glyph">🗑️</span> Delete Node
            </button>
            
            <div class="toolbar-divider"></div>

            <button class="op-action-btn commit-btn" @click="saveNoteData">Commit Change</button>
            <button class="op-action-btn close-btn" @click="closeModal">✕</button>
          </div>
        </div>

        <div class="fullscreen-workspace-core">
          <div class="workspace-header-titleblock">
            <span class="indexing-tag">Active Index Entry Node #{{ selectedNode.id }} {{ selectedNode.isRoot ? '(PRIMARY ROOT)' : '' }}</span>
            <input 
              type="text" 
              v-model="tempNoteLabel" 
              class="workspace-main-title-input" 
              placeholder="Assign structural node text label..."
            />
          </div>

          <div class="notepad-flex-layout">
            <div class="text-editor-container">
              <label class="editor-label">Text Matrix Area</label>
              <textarea 
                v-model="tempNoteContent" 
                class="dynamic-notepad-textarea" 
                placeholder="Enter workspace core node content data parameters here..."
                :style="{
                  fontSize: currentSize,
                  fontFamily: currentFont,
                  color: currentColor,
                  fontWeight: isBold ? 'bold' : 'normal',
                  fontStyle: isItalic ? 'italic' : 'normal',
                  textDecoration: [
                    isUnderline ? 'underline' : '',
                    isStrikethrough ? 'line-through' : ''
                  ].filter(Boolean).join(' ') || 'none'
                }"
              ></textarea>
            </div>
            
            <div class="canvas-shroud-container">
              <label class="editor-label canvas-label">
                Sketch Sector Board
                <button class="clear-canvas-btn" @click="clearCanvas" title="Wipe Sketch Board Frame">🗑️ Clear Frame</button>
              </label>
              <canvas 
                ref="drawingCanvas"
                class="notepad-drawing-board"
                @mousedown="startDrawing"
                @mousemove="draw"
                @mouseup="stopDrawing"
                @mouseleave="stopDrawing"
                @touchstart.prevent="startDrawing"
                @touchmove.prevent="draw"
                @touchend.prevent="stopDrawing"
              ></canvas>
            </div>
          </div>
        </div>

      </div>
    </Teleport>
  </div>
</template>

<style scoped>
*, *::before, *::after { box-sizing: border-box; }

.view-container { display: flex; flex-direction: column; gap: 24px; }

.hub-header {
  display: flex; justify-content: space-between; align-items: flex-end;
  flex-wrap: wrap; gap: 15px; padding-bottom: 12px;
  border-bottom: 1px solid rgba(16, 185, 129, 0.15);
}
.header-titles { max-width: 100%; }
.quick-stats { display: flex; gap: 12px; }

.stat-badge {
  background: rgba(16, 185, 129, 0.08); border: 1px solid rgba(16, 185, 129, 0.2);
  padding: 6px 12px; border-radius: 8px; font-size: 0.8rem; color: #34d399; display: flex; align-items: center; gap: 6px;
}

.panel-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; }
.header-left { display: flex; align-items: center; gap: 12px; }
.panel-header h5 { margin: 0; font-size: 1.1rem; color: #f8fafc; }

.add-root-trigger {
  background: rgba(16, 185, 129, 0.08); border: 1px solid rgba(16, 185, 129, 0.4);
  color: #34d399; padding: 6px 14px; border-radius: 6px; font-size: 0.8rem;
  font-weight: 700; cursor: pointer; font-family: monospace; transition: all 0.25s ease;
}
.add-root-trigger:hover {
  background: rgba(16, 185, 129, 0.2); border-color: #10b981;
  color: #ffffff; box-shadow: 0 0 8px rgba(16, 185, 129, 0.3);
}

.status-pulse {
  font-size: 0.75rem; color: #10b981; background: rgba(16, 185, 129, 0.1);
  padding: 4px 10px; border-radius: 20px; animation: pulse-op 2s infinite alternate;
}
@keyframes pulse-op { from { opacity: 0.6; } to { opacity: 1; box-shadow: 0 0 8px rgba(16,185,129,0.3); } }

.interactive-graph-panel { padding: 24px; display: flex; flex-direction: column; background: rgba(10, 10, 16, 0.65); border: 1px solid rgba(16, 185, 129, 0.12); border-radius: 16px; }

.graph-container {
  width: 100%; height: 400px; background: #000000;
  border-radius: 12px; border: 1px solid rgba(16, 185, 129, 0.1);
  position: relative; overflow: hidden; box-shadow: inset 0 0 40px rgba(0, 0, 0, 0.8);
}

.empty-graph-matrix-overlay {
  position: absolute; top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(0, 0, 0, 0.9); display: flex; flex-direction: column;
  justify-content: center; align-items: center; padding: 20px;
  text-align: center; z-index: 10;
}
.terminal-glitch-text { color: #ef4444; font-family: monospace; font-weight: 800; font-size: 1.1rem; letter-spacing: 1px; margin-bottom: 8px; }
.empty-graph-matrix-overlay p { color: #64748b; font-size: 0.85rem; max-width: 440px; margin: 0 0 20px 0; }
.init-root-btn {
  background: #047857; border: 1px solid #10b981; color: #ffffff;
  font-weight: 700; padding: 10px 18px; border-radius: 6px;
  cursor: pointer; font-size: 0.85rem; box-shadow: 0 0 15px rgba(16, 185, 129, 0.25); transition: all 0.2s;
}
.init-root-btn:hover { background: #059669; box-shadow: 0 0 22px rgba(16, 185, 129, 0.4); }

.svg-layer { width: 100%; height: 100%; cursor: grab; }
.svg-layer:active { cursor: grabbing; }

.graph-edge { stroke: rgba(16, 185, 129, 0.3); stroke-width: 1.5px; transition: all 0.3s ease; }
.highlighted-edge { 
  stroke: #fbbf24 !important; 
  stroke-width: 2.5px !important; 
  filter: drop-shadow(0 0 6px rgba(251, 191, 36, 0.8));
}

.graph-node-group { cursor: pointer; }

.node-halo { fill: radial-gradient(circle, rgba(16,185,129,0.1) 0%, rgba(0,0,0,0) 70%); opacity: 0; transition: all 0.3s ease; }
.halo-active { opacity: 1 !important; fill: radial-gradient(circle, rgba(251, 191, 36, 0.15) 0%, rgba(0,0,0,0) 70%) !important; }
.graph-node-group:hover .node-halo { opacity: 1; }

.node-core { fill: #022c22; stroke: #10b981; stroke-width: 2; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
.node-inner { fill: #34d399; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
.graph-node-group:hover .node-core:not(.root-core) { fill: #064e3b; stroke: #34d399; filter: drop-shadow(0 0 8px rgba(16, 185, 129, 0.6)); }

.root-core { fill: #451a03 !important; stroke: #fbbf24 !important; stroke-width: 2.5px !important; }
.root-inner { fill: #f59e0b !important; }
.graph-node-group:hover .root-core {
  fill: #78350f !important; stroke: #fcd34d !important;
  filter: drop-shadow(0 0 10px rgba(251, 191, 36, 0.6));
}

.highlighted-core {
  stroke: #fbbf24 !important;
  filter: drop-shadow(0 0 12px rgba(251, 191, 36, 0.9)) !important;
}

.node-label { fill: #94a3b8; font-size: 11px; font-family: monospace; text-anchor: middle; pointer-events: none; transition: all 0.3s ease; }
.graph-node-group:hover .node-label { fill: #f8fafc; font-weight: bold; }

.graph-legend { position: absolute; bottom: 12px; right: 16px; text-align: right; pointer-events: none; }
.graph-legend p { margin: 4px 0; font-size: 0.75rem; color: #64748b; font-family: monospace; }
.graph-legend span { color: #10b981; font-weight: bold; }

.grid-nodes-container { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 20px; width: 100%; }

.node-card {
  cursor: pointer; transition: transform 0.2s, box-shadow 0.2s, border-color 0.2s;
  display: flex; flex-direction: column; height: 100%; word-wrap: break-word; overflow-wrap: break-word;
  background: rgba(10, 10, 16, 0.65); border: 1px solid rgba(16, 185, 129, 0.12); border-radius: 16px; padding: 20px;
}
.node-card:hover { transform: translateY(-4px); border-color: rgba(16, 185, 129, 0.4); box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4); }

.root-card { border-color: rgba(251, 191, 36, 0.3) !important; background: rgba(69, 26, 3, 0.15); }
.root-card:hover { border-color: #fbbf24 !important; box-shadow: 0 8px 24px rgba(251, 191, 36, 0.15) !important; }

.node-card-header { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; }
.node-card-header h6 { margin: 0; color: #e2e8f0; font-size: 1.05rem; }

.node-content { color: #94a3b8; font-size: 0.9rem; line-height: 1.5; flex: 1; margin: 0 0 16px 0; }
.node-footer { display: flex; justify-content: space-between; align-items: center; padding-top: 12px; border-top: 1px solid rgba(16, 185, 129, 0.1); }
.node-id { font-size: 0.75rem; color: #059669; font-family: monospace; }

.view-btn { background: transparent; border: none; color: #34d399; font-size: 0.8rem; font-weight: bold; cursor: pointer; padding: 4px 8px; border-radius: 4px; }
.view-btn:hover { background: rgba(16, 185, 129, 0.1); }

.fullscreen-modal-overlay {
  position: fixed; inset: 0; width: 100vw; height: 100vh; background: #040406; z-index: 9999;
  display: flex; flex-direction: column; overflow: hidden; animation: fullscreen-entry 0.25s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

@keyframes fullscreen-entry { from { opacity: 0; transform: scale(1.02); } to { opacity: 1; transform: scale(1); } }

.editor-control-dashboard {
  width: 100%; background: #0a0a0f; border-bottom: 1px solid rgba(16, 185, 129, 0.3);
  display: flex; justify-content: space-between; align-items: center; padding: 12px 24px; flex-wrap: wrap; gap: 16px;
}

.text-format-toolbar { display: flex; align-items: center; gap: 18px; flex-wrap: wrap; }
.tool-group { display: flex; flex-direction: column; gap: 4px; }
.tool-group label { font-family: monospace; font-size: 0.68rem; color: #059669; text-transform: uppercase; letter-spacing: 0.5px; }

.toolbar-dropdown {
  background: #111116; border: 1px solid rgba(16, 185, 129, 0.3); color: #fff; padding: 6px 10px;
  border-radius: 6px; font-size: 0.82rem; outline: none; cursor: pointer;
}
.toolbar-dropdown:focus { border-color: #10b981; }

.toolbar-color-picker { background: transparent; border: 1px solid rgba(16, 185, 129, 0.3); width: 40px; height: 28px; padding: 2px; border-radius: 6px; cursor: pointer; }
.toolbar-divider { width: 1px; height: 26px; background: rgba(16, 185, 129, 0.2); }

.format-buttons-wrapper { display: flex; gap: 6px; }
.format-toggle-btn {
  width: 32px; height: 32px; background: #111116; border: 1px solid rgba(16, 185, 129, 0.25); color: #94a3b8;
  border-radius: 6px; font-weight: bold; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: all 0.2s;
}
.format-toggle-btn:hover { background: rgba(16, 185, 129, 0.1); color: #34d399; }
.format-toggle-btn.active { background: rgba(16, 185, 129, 0.25); border-color: #10b981; color: #10b981; box-shadow: 0 0 8px rgba(16, 185, 129, 0.2); }

.system-operations-toolbar { display: flex; align-items: center; gap: 12px; }
.op-action-btn { padding: 8px 16px; font-size: 0.85rem; font-weight: 700; border-radius: 6px; cursor: pointer; display: flex; align-items: center; gap: 6px; transition: all 0.2s; background: transparent; border: 1px solid transparent; }

.subnote-btn { background: rgba(16, 185, 129, 0.05); border-color: rgba(16, 185, 129, 0.3); color: #34d399; }
.subnote-btn:hover { background: rgba(16, 185, 129, 0.15); border-color: #10b981; box-shadow: 0 0 10px rgba(16, 185, 129, 0.2); }
.delete-node-btn { background: rgba(239, 68, 68, 0.05); border-color: rgba(239, 68, 68, 0.3); color: #fca5a5; }
.delete-node-btn:hover { background: rgba(239, 68, 68, 0.15); border-color: #ef4444; box-shadow: 0 0 10px rgba(239, 68, 68, 0.2); }
.commit-btn { background: #10b981; color: #000; border-color: #10b981; }
.commit-btn:hover { background: #34d399; box-shadow: 0 0 12px rgba(16, 185, 129, 0.4); }
.close-btn { background: #22222a; color: #94a3b8; border-color: #33333f; padding: 8px 12px; }
.close-btn:hover { background: #33333f; color: #fff; }

.fullscreen-workspace-core { flex: 1; padding: 40px; display: flex; flex-direction: column; gap: 28px; width: 100%; max-width: 1600px; margin: 0 auto; }
.workspace-header-titleblock { display: flex; flex-direction: column; gap: 6px; }
.indexing-tag { font-family: monospace; color: #059669; font-size: 0.8rem; text-transform: uppercase; }

.workspace-main-title-input {
  background: transparent; border: none; border-bottom: 2px solid rgba(16, 185, 129, 0.15);
  padding: 8px 0; color: #fff; font-size: 2.2rem; font-weight: 800; outline: none; width: 100%; transition: border-color 0.2s;
}
.workspace-main-title-input:focus { border-color: #10b981; }

.notepad-flex-layout { display: flex; flex-wrap: wrap; gap: 30px; flex: 1; min-height: 0; }
.text-editor-container, .canvas-shroud-container { flex: 1 1 500px; display: flex; flex-direction: column; min-width: 0; height: 100%; }
.editor-label { font-size: 0.8rem; color: #10b981; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 10px; font-weight: bold; }
.canvas-label { display: flex; justify-content: space-between; align-items: center; }

.clear-canvas-btn { background: rgba(239, 68, 68, 0.1); border: 1px solid rgba(239, 68, 68, 0.3); color: #fca5a5; padding: 4px 10px; font-size: 0.75rem; border-radius: 4px; cursor: pointer; }
.clear-canvas-btn:hover { background: rgba(239, 68, 68, 0.2); border-color: #ef4444; }

.dynamic-notepad-textarea {
  flex: 1; background: rgba(8, 8, 12, 0.6); border: 1px solid rgba(16, 185, 129, 0.2);
  padding: 24px; border-radius: 12px; line-height: 1.7; resize: none; outline: none; width: 100%; height: 100%;
  word-wrap: break-word; white-space: pre-wrap; transition: border-color 0.25s;
}
.dynamic-notepad-textarea:focus { border-color: #10b981; box-shadow: inset 0 0 20px rgba(16, 185, 129, 0.05); }

.canvas-shroud-container { background: #000000; border: 1px solid rgba(16, 185, 129, 0.15); border-radius: 12px; position: relative; }
.notepad-drawing-board { display: block; background: #020204; cursor: crosshair; width: 100%; flex: 1; border-radius: 0 0 12px 12px; touch-action: none; }
</style>
