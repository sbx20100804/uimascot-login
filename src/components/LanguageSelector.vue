<template>
  <div class="min-h-screen flex items-center justify-center p-4 sm:p-8 relative overflow-hidden animated-gradient">
    <div class="absolute inset-0 pointer-events-none overflow-hidden">
      <div
        v-for="i in 24"
        :key="i"
        class="particle absolute rounded-full"
        :style="{
          left: `${Math.random() * 100}%`,
          top: `${Math.random() * 100}%`,
          width: `${Math.random() * 50 + 10}px`,
          height: `${Math.random() * 50 + 10}px`,
          background: `rgba(${Math.random() * 60 + 100}, ${Math.random() * 60 + 130}, ${Math.random() * 60 + 200}, ${Math.random() * 0.12 + 0.04})`,
          animation: `langFloat ${Math.random() * 22 + 14}s ease-in-out infinite`,
          animationDelay: `${Math.random() * 10}s`
        }"
      ></div>
    </div>

    <div class="absolute top-[-20%] left-[-10%] w-[60%] h-[60%] rounded-full pointer-events-none"
      :style="{ background: 'radial-gradient(circle, rgba(0,122,255,0.08) 0%, transparent 70%)', animation: 'langOrb 18s ease-in-out infinite' }"></div>
    <div class="absolute bottom-[-20%] right-[-10%] w-[50%] h-[50%] rounded-full pointer-events-none"
      :style="{ background: 'radial-gradient(circle, rgba(175,82,222,0.08) 0%, transparent 70%)', animation: 'langOrb 22s ease-in-out infinite reverse' }"></div>

    <div class="w-full max-w-lg relative z-10">
      <div class="absolute top-4 right-4 sm:top-6 sm:right-6 z-20">
        <button
          type="button"
          @click="toggleDarkMode"
          :aria-label="isDarkMode ? 'Light Mode' : 'Dark Mode'"
          class="w-10 h-10 sm:w-11 sm:h-11 rounded-xl flex items-center justify-center transition-all duration-200 hover:bg-slate-100/60 dark:hover:bg-neutral-800/60 active:scale-95 bg-white/40 dark:bg-neutral-800/40 backdrop-blur-md border border-white/20 dark:border-neutral-700/30 shadow-sm"
        >
          <svg v-if="isDarkMode" class="w-5 h-5 sm:w-6 sm:h-6 text-amber-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
          </svg>
          <svg v-else class="w-5 h-5 sm:w-6 sm:h-6 text-slate-700 dark:text-neutral-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
          </svg>
        </button>
      </div>
      
      <div class="text-center mb-6 sm:mb-8 opacity-0" :class="animationPhase >= 1 ? 'animate-fadeInDown' : ''">
        <div class="w-16 h-16 sm:w-20 sm:h-20 mx-auto rounded-2xl bg-gradient-to-br from-brand-500 to-brand-700 flex items-center justify-center shadow-apple-lg mb-3 sm:mb-5 animate-pulse-glow">
          <svg class="w-8 h-8 sm:w-10 sm:h-10 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M13 10V3L4 14h7v7l9-11h-7z" />
          </svg>
        </div>
        <h1 class="text-2xl sm:text-3xl md:text-4xl font-bold text-slate-900 dark:text-white mb-1.5 sm:mb-2">MascotLogin</h1>
        <p class="text-slate-500 dark:text-neutral-400 text-sm sm:text-base">{{ t.selectPreferredLanguage }}</p>
      </div>

      <div class="glass-card rounded-3xl p-4 sm:p-6 md:p-8 opacity-0" :class="animationPhase >= 2 ? 'animate-fadeInScale' : ''">
        <div class="space-y-2 sm:space-y-2.5 mb-5 sm:mb-6">
          <button
            v-for="(lang, index) in languages"
            :key="lang.code"
            :class="[
              'lang-btn w-full py-2.5 sm:py-3 px-3 sm:px-4 rounded-xl text-left transition-all duration-300 border flex items-center gap-3 sm:gap-4',
              selectedLang === lang.code 
                ? 'border-brand-500 bg-brand-50 dark:bg-brand-900/20 shadow-apple-sm ring-2 ring-brand-500/20' 
                : 'border-slate-200/80 dark:border-neutral-600/50 bg-white/60 dark:bg-neutral-800/40 hover:border-brand-300 dark:hover:border-brand-600/50 hover:shadow-md hover:-translate-y-0.5',
            ]"
            :style="{ 
              animationDelay: `${index * 0.06}s`,
              opacity: animationPhase >= 2 ? 1 : 0,
              transform: animationPhase >= 2 ? 'translateY(0)' : 'translateY(12px)'
            }"
            @click="selectLanguage(lang.code)"
          >
            <span class="text-xl sm:text-2xl flex-shrink-0 transform transition-transform duration-300" :class="selectedLang === lang.code ? 'scale-110' : ''">{{ lang.flag }}</span>
            <div class="flex-1 min-w-0">
              <div class="text-sm sm:text-base font-semibold text-slate-800 dark:text-white">{{ lang.name }}</div>
              <div class="text-xs text-slate-500 dark:text-neutral-400">{{ lang.native }}</div>
            </div>
            <div v-if="selectedLang === lang.code" class="ml-auto flex-shrink-0">
              <svg class="w-4 h-4 sm:w-5 sm:h-5 text-brand-600 dark:text-brand-400 animate-check" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </button>
        </div>

        <button
          :disabled="!selectedLang"
          @click="continueToLogin"
          class="w-full py-3 sm:py-3.5 px-6 rounded-xl font-semibold text-sm sm:text-base text-white bg-brand-600 hover:bg-brand-700 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-300 hover:shadow-xl hover:shadow-brand-500/30 active:scale-[0.98]"
          :class="selectedLang ? 'animate-pulse-btn' : ''"
        >
          {{ t.continue }}
        </button>
      </div>

      <div class="mt-4 sm:mt-6 text-center opacity-0" :class="animationPhase >= 3 ? 'animate-fadeInUp' : ''">
        <div class="glass-pill inline-flex items-center gap-2 text-slate-500 dark:text-neutral-400">
          <svg class="w-3.5 h-3.5 text-green-500 animate-pulse-soft" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
          </svg>
          <span class="text-xs">Secure & Private</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { translations } from '../utils/i18n.js'

const props = defineProps({
  savedLanguage: {
    type: String,
    default: null
  }
})

const emit = defineEmits(['language-selected'])

const selectedLang = ref('en')
const animationPhase = ref(0)
const isDarkMode = ref(false)

const languages = [
  { code: 'en', name: 'English', native: 'English', flag: '🇬🇧' },
  { code: 'zh', name: '中文', native: '简体中文', flag: '🇨🇳' },
  { code: 'ja', name: '日本語', native: '日本語', flag: '🇯🇵' },
  { code: 'es', name: 'Español', native: 'Español', flag: '🇪🇸' },
  { code: 'fr', name: 'Français', native: 'Français', flag: '🇫🇷' },
  { code: 'de', name: 'Deutsch', native: 'Deutsch', flag: '🇩🇪' },
  { code: 'ko', name: '한국어', native: '한국어', flag: '🇰🇷' },
  { code: 'ar', name: 'العربية', native: 'العربية', flag: '🇸🇦' }
]

const t = computed(() => translations[selectedLang.value] || translations['en'])

onMounted(() => {
  if (props.savedLanguage && translations[props.savedLanguage]) {
    selectedLang.value = props.savedLanguage
  }
  
  initDarkMode()
  
  setTimeout(() => {
    animationPhase.value = 1
  }, 100)
  
  setTimeout(() => {
    animationPhase.value = 2
  }, 400)
  
  setTimeout(() => {
    animationPhase.value = 3
  }, 1000)
})

function initDarkMode() {
  const savedTheme = localStorage.getItem('theme')
  const prefersDark = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches
  
  if (savedTheme === 'dark' || (!savedTheme && prefersDark)) {
    document.documentElement.classList.add('dark')
    isDarkMode.value = true
  } else {
    document.documentElement.classList.remove('dark')
    isDarkMode.value = false
  }
}

function toggleDarkMode() {
  isDarkMode.value = !isDarkMode.value
  if (isDarkMode.value) {
    document.documentElement.classList.add('dark')
    localStorage.setItem('theme', 'dark')
  } else {
    document.documentElement.classList.remove('dark')
    localStorage.setItem('theme', 'light')
  }
}

function selectLanguage(lang) {
  selectedLang.value = lang
}

function continueToLogin() {
  if (selectedLang.value) {
    emit('language-selected', selectedLang.value)
  }
}
</script>

<style scoped>
@keyframes langFloat {
  0%, 100% {
    transform: translateY(0) scale(1);
    opacity: 0.25;
  }
  50% {
    transform: translateY(-35px) scale(1.12);
    opacity: 0.5;
  }
}

@keyframes langOrb {
  0%, 100% {
    transform: translate(0, 0) scale(1);
  }
  33% {
    transform: translate(30px, -20px) scale(1.05);
  }
  66% {
    transform: translate(-20px, 15px) scale(0.95);
  }
}

@keyframes fadeInDown {
  from {
    opacity: 0;
    transform: translateY(-25px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(15px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeInScale {
  from {
    opacity: 0;
    transform: scale(0.95) translateY(10px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

@keyframes pulseGlow {
  0%, 100% {
    box-shadow: 0 4px 20px rgba(0, 122, 255, 0.25);
  }
  50% {
    box-shadow: 0 4px 30px rgba(0, 122, 255, 0.45);
  }
}

@keyframes pulseBtn {
  0%, 100% {
    box-shadow: 0 8px 20px -4px rgba(0, 122, 255, 0.35);
  }
  50% {
    box-shadow: 0 8px 28px -4px rgba(0, 122, 255, 0.55);
  }
}

@keyframes check {
  from {
    transform: scale(0) rotate(-45deg);
    opacity: 0;
  }
  to {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
}

.particle {
  will-change: transform, opacity;
  transform: translateZ(0);
}

.animate-fadeInDown {
  animation: fadeInDown 0.5s cubic-bezier(0.22, 1, 0.36, 1) forwards;
  will-change: transform, opacity;
  transform: translateZ(0);
}

.animate-fadeInUp {
  animation: fadeInUp 0.5s cubic-bezier(0.22, 1, 0.36, 1) forwards;
  will-change: transform, opacity;
  transform: translateZ(0);
}

.animate-fadeInScale {
  animation: fadeInScale 0.6s cubic-bezier(0.22, 1, 0.36, 1) forwards;
  will-change: transform, opacity;
  transform: translateZ(0);
}

.animate-pulse-glow {
  animation: pulseGlow 2.5s ease-in-out infinite;
  will-change: box-shadow;
}

.animate-pulse-btn {
  animation: pulseBtn 2s ease-in-out infinite;
  will-change: box-shadow;
}

.animate-check {
  animation: check 0.3s cubic-bezier(0.22, 1, 0.36, 1) forwards;
  will-change: transform, opacity;
  transform: translateZ(0);
}

.lang-btn {
  transition: all 0.3s cubic-bezier(0.22, 1, 0.36, 1);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  will-change: transform, box-shadow, border-color, background-color;
}
</style>
