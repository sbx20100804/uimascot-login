<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-50 via-indigo-50 to-blue-50 flex items-center justify-center p-8 relative overflow-hidden">
    <div class="w-full max-w-lg relative z-10">
      <div class="text-center mb-12">
        <div class="w-24 h-24 mx-auto rounded-3xl bg-gradient-to-br from-purple-600 via-indigo-600 to-blue-600 flex items-center justify-center shadow-2xl mb-6">
          <span class="text-5xl text-white">✨</span>
        </div>
        <h1 class="text-4xl font-bold text-slate-800 mb-3">MascotLogin</h1>
        <p class="text-slate-500 text-lg">{{ t.selectLanguage }}</p>
      </div>

      <div class="max-h-[450px] overflow-y-auto space-y-3 mb-8 pr-2">
        <button
          v-for="lang in languages"
          :key="lang.code"
          :class="[
            'w-full py-5 px-6 rounded-2xl text-left transition-all duration-300 border-2 flex items-center gap-4 cursor-pointer',
            selectedLang === lang.code 
              ? 'border-purple-500 bg-purple-50 shadow-lg' 
              : 'border-transparent bg-white/80 hover:border-purple-200 hover:bg-purple-50/50 hover:shadow-md hover:scale-[1.02]'
          ]"
          @click="selectLanguage(lang.code)"
        >
          <span class="text-4xl flex-shrink-0">{{ lang.flag }}</span>
          <div class="flex-1">
            <div class="text-xl font-bold text-slate-800">{{ lang.name }}</div>
            <div class="text-sm text-slate-500">{{ lang.native }}</div>
          </div>
          <div v-if="selectedLang === lang.code" class="ml-auto">
            <svg class="w-7 h-7 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path>
            </svg>
          </div>
        </button>
      </div>

      <button
        :disabled="!selectedLang"
        class="w-full py-5 px-8 rounded-2xl font-bold text-xl text-white bg-gradient-to-r from-purple-600 via-indigo-600 to-blue-600 hover:from-purple-700 hover:via-indigo-700 hover:to-blue-700 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-300 hover:shadow-xl hover:shadow-purple-500/40 hover:scale-[1.02] active:scale-[0.98]"
        @click="continueToLogin"
      >
        {{ t.continue }}
      </button>

      <div class="mt-10 text-center">
        <div class="text-5xl mb-4">🎨</div>
        <p class="text-slate-400 text-sm">{{ t.selectPreferredLanguage }}</p>
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
  if (props.savedLanguage) {
    selectedLang.value = props.savedLanguage
    setTimeout(() => {
      emit('language-selected', props.savedLanguage)
    }, 500)
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
::-webkit-scrollbar {
  width: 6px;
}

::-webkit-scrollbar-track {
  background: transparent;
}

::-webkit-scrollbar-thumb {
  background: rgba(139, 92, 246, 0.3);
  border-radius: 3px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(139, 92, 246, 0.5);
}
</style>
