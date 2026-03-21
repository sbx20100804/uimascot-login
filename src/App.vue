<template>
  <div>
    <transition name="fade">
      <div v-if="!showLogin" key="language">
        <LanguageSelector @language-selected="onLanguageSelected" :saved-language="savedLanguage" />
      </div>
      <div v-else key="login">
        <LoginPage :language="selectedLanguage" />
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import LoginPage from './components/LoginPage.vue'
import LanguageSelector from './components/LanguageSelector.vue'

const showLogin = ref(false)
const selectedLanguage = ref('en')
const savedLanguage = ref(null)

function onLanguageSelected(lang) {
  selectedLanguage.value = lang
  localStorage.setItem('preferredLanguage', lang)
  showLogin.value = true
}

onMounted(() => {
  const savedLang = localStorage.getItem('preferredLanguage')
  if (savedLang) {
    savedLanguage.value = savedLang
  }
})
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.fade-enter-from {
  opacity: 0;
  transform: scale(0.95);
}

.fade-leave-to {
  opacity: 0;
  transform: scale(1.05);
}
</style>
