<template>
  <div>
    <transition name="page">
      <template v-if="!languageSelected">
        <LanguageSelector @languageSelected="handleLanguageSelected" />
      </template>
      <template v-else>
        <LoginPage :language="currentLanguage" />
      </template>
    </transition>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import LoginPage from './components/LoginPage.vue'
import LanguageSelector from './components/LanguageSelector.vue'

const languageSelected = ref(false)
const currentLanguage = ref('en')

function handleLanguageSelected(lang) {
  currentLanguage.value = lang
  languageSelected.value = true
}
</script>

<style>
.page-enter-active,
.page-leave-active {
  transition: all 0.4s ease;
}

.page-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}

.page-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>
