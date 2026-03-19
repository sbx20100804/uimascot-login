<template>
  <div ref="languagePageRef" class="min-h-screen bg-[#F8FAFC] flex items-center justify-center p-8">
    <div class="w-full max-w-md">
      <div class="text-center mb-12">
        <div class="w-20 h-20 mx-auto rounded-3xl bg-gradient-to-br from-slate-700 to-slate-900 flex items-center justify-center shadow-xl neumorphic-button mb-6">
          <span class="text-4xl text-white">◈</span>
        </div>
        <h1 class="text-3xl font-bold text-slate-800 mb-2">Bauhaus</h1>
        <p class="text-slate-500">{{ t.selectLanguage }}</p>
      </div>

      <div class="space-y-4 mb-8">
        <button
          :class="['language-option w-full py-6 rounded-2xl text-left px-6 transition-all duration-300 neumorphic-button', selectedLang === 'en' ? 'selected' : '']"
          @click="selectLanguage('en')"
        >
          <div class="flex items-center gap-4">
            <span class="text-4xl">🇬🇧</span>
            <div>
              <div class="text-xl font-bold text-slate-800">English</div>
              <div class="text-sm text-slate-500">Continue in English</div>
            </div>
            <div v-if="selectedLang === 'en'" class="ml-auto">
              <svg class="w-8 h-8 text-slate-700" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>

        <button
          :class="['language-option w-full py-6 rounded-2xl text-left px-6 transition-all duration-300 neumorphic-button', selectedLang === 'zh' ? 'selected' : '']"
          @click="selectLanguage('zh')"
        >
          <div class="flex items-center gap-4">
            <span class="text-4xl">🇨🇳</span>
            <div>
              <div class="text-xl font-bold text-slate-800">中文</div>
              <div class="text-sm text-slate-500">用中文继续</div>
            </div>
            <div v-if="selectedLang === 'zh'" class="ml-auto">
              <svg class="w-8 h-8 text-slate-700" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
              </svg>
            </div>
          </div>
        </button>
      </div>

      <button
        :disabled="!selectedLang"
        class="w-full py-4 rounded-xl font-bold text-lg text-white bg-gradient-to-r from-slate-700 to-slate-900 hover:shadow-slate-700/30 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-300 neumorphic-button"
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
.neumorphic-button {
  box-shadow:
    6px 6px 12px rgba(0, 0, 0, 0.1),
    -6px -6px 12px rgba(255, 255, 255, 0.9),
    inset 1px 1px 2px rgba(255, 255, 255, 0.3),
    inset -1px -1px 2px rgba(0, 0, 0, 0.05);
}

.language-option {
  background: transparent;
  border: none;
  cursor: pointer;
}

.language-option:hover:not(.selected) {
  background: rgba(255, 255, 255, 0.5);
}

.language-option.selected {
  background: linear-gradient(135deg, rgba(30, 41, 59, 0.05), rgba(30, 41, 59, 0.1));
  box-shadow:
    inset 3px 3px 6px rgba(0, 0, 0, 0.1),
    inset -3px -3px 6px rgba(255, 255, 255, 0.8);
}
</style>
