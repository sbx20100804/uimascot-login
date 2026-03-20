<template>
  <div ref="languagePageRef" class="min-h-screen bg-gradient-to-br from-purple-50 to-indigo-100 flex items-center justify-center p-8">
    <div class="w-full max-w-md">
      <div class="text-center mb-12">
        <div class="w-20 h-20 mx-auto rounded-3xl bg-gradient-to-br from-purple-600 to-indigo-700 flex items-center justify-center shadow-xl mb-6">
          <span class="text-4xl text-white">✨</span>
        </div>
        <h1 class="text-3xl font-bold text-slate-800 mb-2">MascotLogin</h1>
        <p class="text-slate-500">{{ t.selectLanguage }}</p>
      </div>

      <div class="max-h-[400px] overflow-y-auto space-y-3 mb-8 pr-2">
        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'en' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('en')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇬🇧</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">English</div>
            </div>
            <div v-if="selectedLang === 'en'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'zh' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('zh')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇨🇳</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">中文</div>
            </div>
            <div v-if="selectedLang === 'zh'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'ja' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('ja')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇯🇵</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">日本語</div>
            </div>
            <div v-if="selectedLang === 'ja'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'es' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('es')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇪🇸</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">Español</div>
            </div>
            <div v-if="selectedLang === 'es'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'fr' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('fr')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇫🇷</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">Français</div>
            </div>
            <div v-if="selectedLang === 'fr'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'de' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('de')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇩🇪</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">Deutsch</div>
            </div>
            <div v-if="selectedLang === 'de'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'ko' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('ko')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇰🇷</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">한국어</div>
            </div>
            <div v-if="selectedLang === 'ko'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-5 rounded-2xl text-left px-5 transition-all duration-300 border-2', selectedLang === 'ar' ? 'border-purple-500 bg-purple-50' : 'border-transparent bg-white hover:border-purple-200 hover:bg-purple-50/50']"
          @click="selectLanguage('ar')"
        >
          <div class="flex items-center gap-4">
            <span class="text-3xl">🇸🇦</span>
            <div class="flex-1">
              <div class="text-lg font-bold text-slate-800">العربية</div>
            </div>
            <div v-if="selectedLang === 'ar'" class="ml-auto">
              <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>
      </div>

      <button
        :disabled="!selectedLang"
        class="w-full py-4 rounded-xl font-bold text-lg text-white bg-gradient-to-r from-purple-600 to-indigo-700 hover:shadow-lg hover:shadow-purple-500/30 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-300 hover:scale-[1.02] active:scale-[0.98]"
        @click="continueToLogin"
      >
        {{ t.continue }}
      </button>

      <div class="mt-8 text-center">
        <div class="text-4xl mb-4">🎨</div>
        <p class="text-slate-400 text-sm">{{ t.testCredentials }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import gsap from 'gsap'
import { translations } from '../utils/i18n.js'

const emit = defineEmits(['languageSelected'])

const selectedLang = ref('')
const languagePageRef = ref(null)

const t = computed(() => translations[selectedLang.value] || translations['en'])

function selectLanguage(lang) {
  selectedLang.value = lang
}

function continueToLogin() {
  if (selectedLang.value) {
    emit('languageSelected', selectedLang.value)
  }
}

onMounted(() => {
  gsap.fromTo(
    languagePageRef.value,
    { opacity: 0, y: 30 },
    { opacity: 1, y: 0, duration: 0.8, ease: 'power2.out' }
  )
})
</script>

<style scoped>
.language-option {
  cursor: pointer;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
}

.language-option:hover:not(.selected) {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}
</style>
