<template>
  <div class="fixed inset-0 z-[200] flex items-center justify-center p-4 bg-black/40 backdrop-blur-sm" v-if="showConfig">
    <div class="glass-card rounded-3xl p-6 sm:p-8 w-full max-w-2xl max-h-[90vh] overflow-y-auto animate-slide-up">
      <div class="flex items-center justify-between mb-6">
        <h2 class="text-xl font-bold text-slate-900 dark:text-white">⚙️ Configuration Panel</h2>
        <button @click="closeConfig" class="w-10 h-10 rounded-xl flex items-center justify-center transition-all hover:bg-slate-100 dark:hover:bg-neutral-800">
          <svg class="w-5 h-5 text-slate-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
          </svg>
        </button>
      </div>

      <div class="space-y-6">
        <div class="border-b border-slate-200 dark:border-neutral-700 pb-6">
          <h3 class="text-lg font-semibold text-slate-800 dark:text-white mb-4">🎨 Theme Colors</h3>
          <div class="grid grid-cols-2 gap-4">
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">Primary Color</label>
              <div class="flex items-center gap-3">
                <input type="color" v-model="config.primaryColor" class="w-12 h-10 rounded-lg cursor-pointer border-0" />
                <span class="text-sm text-slate-600 dark:text-neutral-400 font-mono">{{ config.primaryColor }}</span>
              </div>
            </div>
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">Secondary Color</label>
              <div class="flex items-center gap-3">
                <input type="color" v-model="config.secondaryColor" class="w-12 h-10 rounded-lg cursor-pointer border-0" />
                <span class="text-sm text-slate-600 dark:text-neutral-400 font-mono">{{ config.secondaryColor }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="border-b border-slate-200 dark:border-neutral-700 pb-6">
          <h3 class="text-lg font-semibold text-slate-800 dark:text-white mb-4">🔗 Social Login Links</h3>
          <div class="space-y-4">
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">WeChat Login URL</label>
              <input type="text" v-model="config.wechatUrl" class="glass-input w-full" placeholder="https://wechat.com/login" />
            </div>
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">QQ Login URL</label>
              <input type="text" v-model="config.qqUrl" class="glass-input w-full" placeholder="https://qq.com/login" />
            </div>
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">Google Login URL</label>
              <input type="text" v-model="config.googleUrl" class="glass-input w-full" placeholder="https://google.com/login" />
            </div>
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">GitHub Login URL</label>
              <input type="text" v-model="config.githubUrl" class="glass-input w-full" placeholder="https://github.com/login" />
            </div>
          </div>
        </div>

        <div class="border-b border-slate-200 dark:border-neutral-700 pb-6">
          <h3 class="text-lg font-semibold text-slate-800 dark:text-white mb-4">📱 App Settings</h3>
          <div class="space-y-4">
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 mb-2 block">App Name</label>
              <input type="text" v-model="config.appName" class="glass-input w-full" />
            </div>
            <div class="flex items-center justify-between">
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300">Enable Animations</label>
              <button @click="config.enableAnimations = !config.enableAnimations" :class="['w-12 h-6 rounded-full transition-all duration-300 relative', config.enableAnimations ? 'bg-brand-600' : 'bg-slate-300 dark:bg-neutral-600']">
                <span :class="['absolute w-5 h-5 bg-white rounded-full transition-all duration-300 top-0.5', config.enableAnimations ? 'left-6' : 'left-0.5']"></span>
              </button>
            </div>
            <div class="flex items-center justify-between">
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300">Show Captcha</label>
              <button @click="config.showCaptcha = !config.showCaptcha" :class="['w-12 h-6 rounded-full transition-all duration-300 relative', config.showCaptcha ? 'bg-brand-600' : 'bg-slate-300 dark:bg-neutral-600']">
                <span :class="['absolute w-5 h-5 bg-white rounded-full transition-all duration-300 top-0.5', config.showCaptcha ? 'left-6' : 'left-0.5']"></span>
              </button>
            </div>
          </div>
        </div>

        <div class="flex gap-3">
          <button @click="resetConfig" class="flex-1 btn-secondary py-3">
            Reset to Default
          </button>
          <button @click="saveConfig" class="flex-1 btn-primary py-3">
            Save Configuration
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const emit = defineEmits(['close', 'config-changed'])

const props = defineProps({
  show: { type: Boolean, default: false }
})

const defaultConfig = {
  primaryColor: '#007AFF',
  secondaryColor: '#AF52DE',
  wechatUrl: 'https://wechat.com/login',
  qqUrl: 'https://qq.com/login',
  googleUrl: 'https://google.com/login',
  githubUrl: 'https://github.com/login',
  appName: 'MascotLogin',
  enableAnimations: true,
  showCaptcha: true
}

const config = ref({ ...defaultConfig })
const showConfig = ref(props.show)

watch(() => props.show, (newVal) => {
  showConfig.value = newVal
})

onMounted(() => {
  loadConfig()
})

function loadConfig() {
  const saved = localStorage.getItem('appConfig')
  if (saved) {
    try {
      config.value = { ...defaultConfig, ...JSON.parse(saved) }
    } catch (e) {
      console.error('Failed to load config:', e)
    }
  }
}

function saveConfig() {
  localStorage.setItem('appConfig', JSON.stringify(config.value))
  applyConfig()
  emit('config-changed', config.value)
  closeConfig()
}

function resetConfig() {
  config.value = { ...defaultConfig }
}

function applyConfig() {
  const root = document.documentElement
  root.style.setProperty('--primary-color', config.value.primaryColor)
  root.style.setProperty('--secondary-color', config.value.secondaryColor)
}

function closeConfig() {
  showConfig.value = false
  emit('close')
}
</script>

<style scoped>
.config-panel {
  animation: slideUp 0.4s cubic-bezier(0.22, 1, 0.36, 1);
}
</style>
