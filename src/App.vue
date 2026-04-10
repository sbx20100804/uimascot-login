<template>
  <div>
    <transition name="fade" mode="out-in">
      <LanguageSelector v-if="!showLogin" key="language" @language-selected="onLanguageSelected" :saved-language="savedLanguage" />
      <LoginPage v-else key="login" :language="selectedLanguage" />
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
