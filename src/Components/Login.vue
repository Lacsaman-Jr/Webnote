<script setup lang="ts">
import { ref } from 'vue'

// Emit hook para ipasa ang event sa App.vue kapag matagumpay ang login
const emit = defineEmits(['login-success'])

// State Management para sa Form Inputs at Feedback Loops
const emailOrUser = ref('')
const password = ref('')
const isLoading = ref(false)
const errorMsg = ref('')
const showPassword = ref(false)

const handleLoginSubmit = () => {
  // Simpleng validation check bago magpatuloy
  if (!emailOrUser.value.trim() || !password.value.trim()) {
    errorMsg.value = 'Mangyaring punan ang parehong fields.'
    return
  }

  errorMsg.value = ''
  isLoading.value = true

  // Kunwariang API Server Request Timeout (800ms) para magkaroon ng premium loading animation
  setTimeout(() => {
    isLoading.value = false
    
    // Dito pwedeng maglagay ng totoong authentication. Para sa demo, papasukin natin:
    emit('login-success', {
      user: emailOrUser.value,
      role: 'authenticated'
    })
  }, 1200)
}
</script>

<template>
  <div class="web-knowledge-auth-screen">
    
    <div class="spider-web-matrix-overlay">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1000 1000" preserveAspectRatio="xMidYMid slice" class="web-svg">
        <line x1="500" y1="500" x2="500" y2="0" class="web-strand" />
        <line x1="500" y1="500" x2="1000" y2="0" class="web-strand" />
        <line x1="500" y1="500" x2="1000" y2="500" class="web-strand" />
        <line x1="500" y1="500" x2="1000" y2="1000" class="web-strand" />
        <line x1="500" y1="500" x2="500" y2="1000" class="web-strand" />
        <line x1="500" y1="500" x2="0" y2="1000" class="web-strand" />
        <line x1="500" y1="500" x2="0" y2="500" class="web-strand" />
        <line x1="500" y1="500" x2="0" y2="0" class="web-strand" />

        <polygon points="500,50 950,50 950,500 950,950 500,950 50,950 50,500 50,50" class="web-ring" />
        <polygon points="500,150 850,150 850,500 850,850 500,850 150,850 150,500 150,150" class="web-ring" />
        <polygon points="500,250 750,250 750,500 750,750 500,750 250,750 250,500 250,250" class="web-ring" />
        <polygon points="500,350 650,350 650,500 650,650 500,650 350,650 350,500 350,350" class="web-ring" />
        <polygon points="500,430 570,430 570,500 570,570 500,570 430,570 430,500 430,430" class="web-ring" />
      </svg>
    </div>

    <div class="emerald-ambient-glow position-top"></div>
    <div class="emerald-ambient-glow position-bottom"></div>

    <div class="auth-card-wrapper animate-slide-up">
      
      <div class="brand-identity-block">
        <div class="brand-logo-hex">
          <span class="core-node">🕸️</span>
        </div>
        <h1 class="brand-title">Web Knowledge</h1>
        <p class="brand-tagline">Access the unified information network</p>
      </div>

      <div v-if="errorMsg" class="error-notice-banner animate-shake">
        <span class="warning-icon">⚠️</span>
        <p class="error-text">{{ errorMsg }}</p>
      </div>

      <form @submit.prevent="handleLoginSubmit" class="form-container">
        
        <div class="input-field-group">
          <label for="identifier">Username or Email Address</label>
          <div class="input-input-wrapper">
            <span class="field-icon">👤</span>
            <input 
              id="identifier"
              type="text" 
              v-model="emailOrUser" 
              placeholder="username" 
              :disabled="isLoading"
              autocomplete="username"
            />
          </div>
        </div>

        <div class="input-field-group">
          <div class="label-row-split">
            <label for="security-key">Security Access Password</label>
            <a href="#" class="forgot-link-node" @click.prevent="alert('System Notice: Contact network administrator to reset core keys.')">Forgot Key?</a>
          </div>
          <div class="input-input-wrapper">
            <span class="field-icon">🔒</span>
            <input 
              id="security-key"
              :type="showPassword ? 'text' : 'password'" 
              v-model="password" 
              placeholder="Enter your secure password" 
              :disabled="isLoading"
              autocomplete="current-password"
            />
            <button 
              type="button" 
              class="password-visibility-toggler" 
              @click="showPassword = !showPassword"
              tabindex="-1"
            >
              {{ showPassword ? '👁️' : '🙈' }}
            </button>
          </div>
        </div>

        <button type="submit" class="auth-action-trigger" :disabled="isLoading">
          <span v-if="!isLoading" class="btn-static-text">Synchronize Gateway ➔</span>
          <div v-else class="loading-spinner-ring">
            <div class="dot-spinner"></div>
            <span>Securing Connection...</span>
          </div>
        </button>

      </form>

      <div class="auth-card-footer">
        <p>New terminal location? <a href="#" class="register-link" @click.prevent="alert('Registration module restricted to certified terminal instances.')">Request Access Profile</a></p>
      </div>

    </div>

    <div class="terminal-status-metric">
      <span class="blink-dot"></span> SECURE SEC_CONN // PORT_ACTIVE_8080
    </div>

  </div>
</template>

<style scoped>
/* ==========================================================================
   1. CORE WRAPPER AND GRID HOUSING ARCHITECTURE (BLACK & DEEP EMERALD)
   ========================================================================== */
.web-knowledge-auth-screen {
  position: relative;
  width: 100vw;
  height: 100vh;
  background-color: #000000; /* Pure Black Core */
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
  overflow: hidden;
  padding: 20px;
}

/* ==========================================================================
   2. CUSTOM CSS SPIDER WEB GRAPHICS LAYERING
   ========================================================================== */
.spider-web-matrix-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0.22; /* Subtle structural appearance */
}

.web-svg {
  width: 110vmax;
  height: 110vmax;
  transform: rotate(15deg);
  animation: web-pulsate-glow 8s ease-in-out infinite alternate;
}

.web-strand {
  stroke: #046307; /* Deep Emerald Green line threads */
  stroke-width: 1.5;
}

.web-ring {
  fill: none;
  stroke: #059669; /* Vibrant Emerald Accent */
  stroke-width: 1.2;
  stroke-dasharray: 4, 4; /* Digital micro-grid aesthetic */
}

/* Ambient Background Lights */
.emerald-ambient-glow {
  position: absolute;
  width: 500px;
  height: 500px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(2, 44, 34, 0.45) 0%, rgba(0,0,0,0) 70%);
  filter: blur(60px);
  z-index: 2;
  pointer-events: none;
}

.position-top { top: -200px; right: -100px; }
.position-bottom { bottom: -200px; left: -100px; }

/* ==========================================================================
   3. AUTHENTICATION CARD STRUCTURAL LAYOUT
   ========================================================================== */
.auth-card-wrapper {
  position: relative;
  z-index: 10;
  width: 100%;
  max-width: 460px;
  background: linear-gradient(135deg, rgba(10, 10, 10, 0.85) 0%, rgba(2, 44, 34, 0.6) 100%);
  border: 1px solid rgba(16, 185, 129, 0.2); /* Soft Glowing Emerald Border */
  border-radius: 20px;
  padding: 45px 40px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.7),
              0 0 40px rgba(4, 99, 7, 0.15);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
}

/* Brand Styling Elements */
.brand-identity-block {
  text-align: center;
  margin-bottom: 35px;
}

.brand-logo-hex {
  width: 70px;
  height: 70px;
  background: #022c22;
  border: 2px solid #10b981;
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 16px auto;
  box-shadow: 0 0 20px rgba(16, 185, 129, 0.3);
  transform: rotate(45deg);
}

.core-node {
  font-size: 2rem;
  transform: rotate(-45deg); /* Re-align inside rotated wrapper */
}

.brand-title {
  color: #ffffff;
  font-size: 2.1rem;
  font-weight: 900;
  margin: 0 0 6px 0;
  letter-spacing: -0.5px;
  background: linear-gradient(180deg, #ffffff 40%, #10b981 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.brand-tagline {
  color: #a7f3d0; /* Muted Emerald Mint */
  font-size: 0.95rem;
  margin: 0;
  opacity: 0.8;
  font-weight: 500;
}

/* ==========================================================================
   4. FORM FIELDS AND INTERACTIVE UI COMPONENTS
   ========================================================================== */
.form-container {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.input-field-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.input-field-group label {
  color: #cbd5e1;
  font-size: 0.85rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.75px;
}

.label-row-split {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.input-input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.field-icon {
  position: absolute;
  left: 16px;
  font-size: 1.1rem;
  pointer-events: none;
  opacity: 0.7;
}

.input-input-wrapper input {
  width: 100%;
  padding: 15px 16px 15px 48px;
  background-color: rgba(0, 0, 0, 0.6);
  border: 2px solid rgba(4, 99, 7, 0.4); /* Deep Emerald Border */
  border-radius: 12px;
  font-size: 1rem;
  color: #ffffff;
  outline: none;
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
}

/* Input Focus States */
.input-input-wrapper input:focus {
  border-color: #10b981; /* Shift up to active neon emerald */
  background-color: rgba(2, 44, 34, 0.3);
  box-shadow: 0 0 15px rgba(16, 185, 129, 0.25);
}

/* Password Toggle Configs */
.password-visibility-toggler {
  position: absolute;
  right: 14px;
  background: transparent;
  border: none;
  color: #94a3b8;
  cursor: pointer;
  font-size: 1.2rem;
  padding: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: color 0.2s;
}
.password-visibility-toggler:hover {
  color: #10b981;
}

.forgot-link-node, .register-link {
  color: #10b981;
  font-size: 0.85rem;
  font-weight: 600;
  text-decoration: none;
  transition: color 0.2s;
}
.forgot-link-node:hover, .register-link:hover {
  color: #34d399;
  text-decoration: underline;
}

/* ==========================================================================
   5. DEEP EMERALD HIGH-TRANSITION ACTION TRIGGERS
   ========================================================================== */
.auth-action-trigger {
  width: 100%;
  padding: 16px;
  background: linear-gradient(180deg, #047857 0%, #064e3b 100%); /* Deep Emerald Core Gradient */
  border: 1px solid #10b981;
  border-radius: 12px;
  color: #ffffff;
  font-size: 1.05rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 4px 15px rgba(4, 120, 87, 0.3);
  margin-top: 10px;
}

.auth-action-trigger:hover:not(:disabled) {
  transform: translateY(-2px);
  background: linear-gradient(180deg, #059669 0%, #047857 100%);
  box-shadow: 0 6px 25px rgba(16, 185, 129, 0.45);
}

.auth-action-trigger:active:not(:disabled) {
  transform: translateY(0);
}

.auth-action-trigger:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* Form Action Loading Ring Sync State */
.loading-spinner-ring {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
}

.dot-spinner {
  width: 20px;
  height: 20px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-top-color: #ffffff;
  border-radius: 50%;
  animation: spinner-spin-rotation 0.8s linear infinite;
}

/* ==========================================================================
   6. ERROR STATE BANNER MESSAGES
   ========================================================================== */
.error-notice-banner {
  background-color: rgba(220, 38, 38, 0.15);
  border: 1px solid rgba(220, 38, 38, 0.4);
  padding: 12px 16px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 25px;
}

.warning-icon {
  font-size: 1.2rem;
}

.error-text {
  color: #fca5a5;
  font-size: 0.9rem;
  margin: 0;
  font-weight: 600;
  line-height: 1.4;
}

/* Footer elements */
.auth-card-footer {
  text-align: center;
  margin-top: 30px;
  padding-top: 25px;
  border-top: 1px solid rgba(16, 185, 129, 0.15);
  color: #94a3b8;
  font-size: 0.9rem;
}
.auth-card-footer p { margin: 0; }

/* Status Bar Text Information Display */
.terminal-status-metric {
  position: absolute;
  bottom: 20px;
  right: 25px;
  color: #059669;
  font-family: monospace;
  font-size: 0.75rem;
  letter-spacing: 1px;
  opacity: 0.8;
  display: flex;
  align-items: center;
  gap: 8px;
}

.blink-dot {
  width: 6px;
  height: 6px;
  background-color: #10b981;
  border-radius: 50%;
  animation: system-dot-blink 1s steps(2, start) infinite;
}

/* ==========================================================================
   7. SYSTEM KEYFRAME ANIMATION MODULES
   ========================================================================== */
@keyframes web-pulsate-glow {
  0% { transform: rotate(15deg) scale(1); opacity: 0.18; }
  100% { transform: rotate(17deg) scale(1.03); opacity: 0.26; }
}

@keyframes spinner-spin-rotation {
  to { transform: rotate(360deg); }
}

@keyframes system-dot-blink {
  to { visibility: hidden; }
}

.animate-slide-up {
  animation: component-slide-up 0.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

@keyframes component-slide-up {
  from { transform: translateY(30px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

.animate-shake {
  animation: structural-shake 0.4s ease-in-out;
}

@keyframes structural-shake {
  0%, 100% { transform: translateX(0); }
  20%, 60% { transform: translateX(-6px); }
  40%, 80% { transform: translateX(6px); }
}
</style>