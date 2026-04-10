<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-50 via-indigo-50 to-blue-50 flex items-center justify-center p-4 sm:p-8 relative overflow-hidden">
    <div class="absolute inset-0 pointer-events-none overflow-hidden">
      <div
        v-for="i in 12"
        :key="i"
        class="particle absolute rounded-full"
        :style="{
          left: `${Math.random() * 100}%`,
          top: `${Math.random() * 100}%`,
          width: `${Math.random() * 30 + 10}px`,
          height: `${Math.random() * 30 + 10}px`,
          background: `rgba(${Math.random() * 100 + 139}, ${Math.random() * 100 + 92}, ${Math.random() * 100 + 246}, ${Math.random() * 0.2 + 0.05})`,
          animation: `langFloat ${Math.random() * 15 + 10}s ease-in-out infinite`,
          animationDelay: `${Math.random() * 5}s`
        }"
      ></div>
    </div>

    <div class="w-full max-w-lg relative z-10">
      <div class="text-center mb-8 sm:mb-12">
        <div class="w-20 h-20 sm:w-24 sm:h-24 mx-auto rounded-3xl bg-gradient-to-br from-purple-600 via-indigo-600 to-blue-600 flex items-center justify-center shadow-2xl mb-4 sm:mb-6">
          <span class="text-4xl sm:text-5xl text-white">✨</span>
        </div>
        <h1 class="text-3xl sm:text-4xl font-bold text-slate-800 mb-2 sm:mb-3">MascotLogin</h1>
        <p class="text-slate-500 text-base sm:text-lg">{{ t.selectPreferredLanguage }}</p>
      </div>

      <div class="space-y-2 sm:space-y-3 mb-6 sm:mb-8">
        <button
          v-for="lang in languages"
          :key="lang.code"
          :class="[
            'w-full py-3 sm:py-4 px-4 sm:px-6 rounded-2xl text-left transition-all duration-200 border-2 flex items-center gap-3 sm:gap-4',
            selectedLang === lang.code 
              ? 'border-purple-500 bg-white shadow-lg ring-4 ring-purple-100' 
              : 'border-slate-200 bg-white hover:border-purple-300 hover:shadow-md'
          ]"
          @click="selectLanguage(lang.code)"
        >
          <span class="text-2xl sm:text-3xl flex-shrink-0">{{ lang.flag }}</span>
          <div class="flex-1 min-w-0">
            <div class="text-base sm:text-lg font-bold text-slate-800">{{ lang.name }}</div>
            <div class="text-xs sm:text-sm text-slate-500">{{ lang.native }}</div>
          </div>
          <div v-if="selectedLang === lang.code" class="ml-auto flex-shrink-0">
            <svg class="w-5 h-5 sm:w-6 sm:h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path>
            </svg>
          </div>
        </button>
      </div>

      <button
        :disabled="!selectedLang"
        @click="continueToLogin"
        class="w-full py-4 sm:py-5 px-6 sm:px-8 rounded-2xl font-bold text-lg sm:text-xl text-white bg-gradient-to-r from-purple-600 via-indigo-600 to-blue-600 hover:from-purple-700 hover:via-indigo-700 hover:to-blue-700 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-300 hover:shadow-xl hover:shadow-purple-500/40 active:scale-[0.98]"
      >
        {{ t.continue }}
      </button>
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
})

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
    opacity: 0.3;
  }
  50% {
    transform: translateY(-20px) scale(1.1);
    opacity: 0.6;
  }
}

.particle {
  will-change: transform, opacity;
}
</style>
