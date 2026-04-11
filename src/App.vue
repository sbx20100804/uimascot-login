<template>
  <div id="app-root">
    <transition name="fade" mode="out-in">
      <LanguageSelector v-if="!showLogin" key="language" @language-selected="onLanguageSelected" :saved-language="savedLanguage" />
      <LoginPage v-else key="login" :language="selectedLanguage" />
    </transition>
    
    <button
      @click="showConfigPanel = true"
      class="fixed bottom-6 right-6 z-[100] w-14 h-14 rounded-2xl bg-brand-600 hover:bg-brand-700 text-white shadow-lg hover:shadow-xl transition-all duration-300 active:scale-95 flex items-center justify-center"
      :aria-label="'Open Configuration'"
    >
      <svg class="w-7 h-7 animate-spin-slow" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"/>
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
      </svg>
    </button>
    
    <ConfigPanel :show="showConfigPanel" @close="showConfigPanel = false" @config-changed="onConfigChanged" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import LoginPage from './components/LoginPage.vue'
import LanguageSelector from './components/LanguageSelector.vue'
import ConfigPanel from './components/ConfigPanel.vue'

const showLogin = ref(false)
const selectedLanguage = ref('en')
const savedLanguage = ref(null)
const showConfigPanel = ref(false)

function onLanguageSelected(lang) {
  selectedLanguage.value = lang
  localStorage.setItem('preferredLanguage', lang)
  showLogin.value = true
}

function onConfigChanged(config) {
  console.log('Configuration updated:', config)
  applyConfig(config)
}

function applyConfig(config) {
  const root = document.documentElement
  
  const hexToRgb = (hex) => {
    const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex)
    return result ? {
      r: parseInt(result[1], 16),
      g: parseInt(result[2], 16),
      b: parseInt(result[3], 16)
    } : null
  }
  
  const primary = hexToRgb(config.primaryColor)
  if (primary) {
    root.style.setProperty('--brand-50', `rgba(${primary.r}, ${primary.g}, ${primary.b}, 0.1)`)
    root.style.setProperty('--brand-100', `rgba(${primary.r}, ${primary.g}, ${primary.b}, 0.2)`)
    root.style.setProperty('--brand-200', `rgba(${primary.r}, ${primary.g}, ${primary.b}, 0.3)`)
    root.style.setProperty('--brand-300', `rgba(${primary.r}, ${primary.g}, ${primary.b}, 0.5)`)
    root.style.setProperty('--brand-400', `rgba(${primary.r}, ${primary.g}, ${primary.b}, 0.7)`)
    root.style.setProperty('--brand-500', config.primaryColor)
    root.style.setProperty('--brand-600', config.primaryColor)
    root.style.setProperty('--brand-700', `rgba(${Math.max(0, primary.r - 30)}, ${Math.max(0, primary.g - 30)}, ${Math.max(0, primary.b - 30)}, 1)`)
  }
}

function initDarkMode() {
  const savedTheme = localStorage.getItem('theme')
  const prefersDark = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches
  
  if (savedTheme === 'dark' || (!savedTheme && prefersDark)) {
    document.documentElement.classList.add('dark')
  } else {
    document.documentElement.classList.remove('dark')
  }
}

function loadSavedConfig() {
  const saved = localStorage.getItem('appConfig')
  if (saved) {
    try {
      const config = JSON.parse(saved)
      applyConfig(config)
    } catch (e) {
      console.error('Failed to load saved config:', e)
    }
  }
}

onMounted(() => {
  const savedLang = localStorage.getItem('preferredLanguage')
  if (savedLang) {
    savedLanguage.value = savedLang
  }
  initDarkMode()
  loadSavedConfig()
  
  window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
    if (!localStorage.getItem('theme')) {
      if (e.matches) {
        document.documentElement.classList.add('dark')
      } else {
        document.documentElement.classList.remove('dark')
      }
    }
  })
})
</script>

<style>
#app-root {
  min-height: 100vh;
  transition: background-color 0.3s ease;
}

.fade-enter-active,
.fade-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(20px);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}
</style>
