<template>
  <div v-if="isVisible" class="fixed inset-0 z-[100] overflow-y-auto" :dir="isRTL ? 'rtl' : 'ltr'">
    <div class="flex min-h-screen items-end justify-center px-4 pb-4 pt-4 sm:items-center sm:p-0">
      <div
        ref="backdropRef"
        class="fixed inset-0 overflow-hidden bg-black/40"
        @click="handleBackdropClick"
        @touchstart="handleTouchStart"
        @touchend="handleTouchEnd"
      >
      </div>

      <div
        ref="modalRef"
        class="glass-card relative w-full max-w-lg overflow-hidden text-left shadow-apple-xl sm:my-8 rounded-2xl"
        role="dialog"
        aria-modal="true"
        aria-labelledby="passkey-modal-title"
        aria-describedby="passkey-modal-description"
        @keydown="handleKeydown"
      >
        <div class="px-6 py-5 sm:px-8 sm:py-6">
          <div class="flex items-start justify-between">
            <div ref="iconRef" class="flex h-14 w-14 sm:h-16 sm:w-16 items-center justify-center bg-gradient-to-br from-brand-500 to-brand-700 rounded-2xl">
              <svg class="h-7 w-7 sm:h-8 sm:w-8 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/>
                <path d="m9 12 2 2 4-4"/>
              </svg>
            </div>
            <button
              type="button"
              @click="close"
              ref="closeBtnRef"
              class="inline-flex items-center justify-center p-2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-200 hover:bg-slate-100 dark:hover:bg-neutral-800 rounded-lg focus:outline-none transition-colors"
              aria-label="Close"
              tabindex="0"
            >
              <svg class="h-5 w-5 sm:h-6 sm:w-6" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>

          <div ref="contentRef" class="mt-6">
            <div v-if="currentStep === 'initial'">
              <h2 id="passkey-modal-title" class="text-xl sm:text-2xl font-semibold text-slate-900 dark:text-white">
                {{ t.signInWithPasskeyTitle }}
              </h2>
              <p id="passkey-modal-description" class="mt-2 text-slate-600 dark:text-neutral-400">
                {{ t.signInWithPasskeyDesc }}
              </p>

              <div class="mt-6 space-y-3">
                <button
                  type="button"
                  @click="startAuthentication"
                  ref="primaryBtnRef"
                  class="w-full flex items-center gap-3 sm:gap-4 px-3 sm:px-4 py-3 sm:py-4 border-2 border-slate-200 dark:border-neutral-600 hover:border-brand-500 dark:hover:border-brand-500 bg-transparent hover:bg-brand-50 dark:hover:bg-brand-900/20 transition-all focus:outline-none focus:ring-2 focus:ring-brand-500 focus:ring-offset-2 rounded-xl"
                  tabindex="0"
                  @mouseenter="handleButtonHover('primary')"
                  @mouseleave="handleButtonLeave"
                >
                  <div class="flex h-9 w-9 sm:h-10 sm:w-10 items-center justify-center bg-slate-100 dark:bg-neutral-700 rounded-lg">
                    <svg class="h-4 w-4 sm:h-5 sm:w-5 text-slate-600 dark:text-neutral-300" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                      <rect x="4" y="4" width="16" height="16" rx="2" ry="2"/>
                      <rect x="9" y="9" width="6" height="6"/>
                    </svg>
                  </div>
                  <div class="text-left flex-1">
                    <div class="font-medium text-slate-900 dark:text-white text-sm sm:text-base">{{ t.useSecurityKey }}</div>
                    <div class="text-xs sm:text-sm text-slate-500 dark:text-neutral-400">{{ t.securityKeyDesc }}</div>
                  </div>
                  <svg class="h-4 w-4 sm:h-5 sm:w-5 text-slate-400" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7"/>
                  </svg>
                </button>
              </div>
            </div>

            <div v-else-if="currentStep === 'loading'" class="text-center py-4">
              <div ref="loadingRef" class="mx-auto flex h-16 w-16 sm:h-20 sm:w-20 items-center justify-center">
                <svg class="h-10 w-10 sm:h-12 sm:w-12 text-brand-500 animate-spin" viewBox="0 0 24 24" fill="none">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
                </svg>
              </div>
              <h3 class="mt-5 sm:mt-6 text-lg sm:text-xl font-semibold text-slate-900 dark:text-white">
                {{ t.waitingForSecurityKey }}
              </h3>
              <p class="mt-2 text-slate-600 dark:text-neutral-400 text-sm sm:text-base">
                {{ t.touchYourSecurityKey }}
              </p>
              <div class="mt-4">
                <button
                  type="button"
                  @click="cancelAuthentication"
                  class="px-4 py-2 text-slate-600 dark:text-neutral-300 hover:text-slate-800 dark:hover:text-white transition-colors text-sm"
                >
                  {{ t.cancel }}
                </button>
              </div>
            </div>

            <div v-else-if="currentStep === 'success'" class="text-center py-4">
              <div ref="successRef" class="mx-auto flex h-16 w-16 sm:h-20 sm:w-20 items-center justify-center bg-green-100 dark:bg-green-900/30 rounded-full">
                <svg class="h-8 w-8 sm:h-10 sm:w-10 text-green-600 dark:text-green-400" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                </svg>
              </div>
              <h3 class="mt-5 sm:mt-6 text-lg sm:text-xl font-semibold text-slate-900 dark:text-white">
                {{ t.signedInSuccessfully }}
              </h3>
              <p class="mt-2 text-slate-600 dark:text-neutral-400 text-sm sm:text-base">
                {{ t.redirectingYou }}
              </p>
            </div>

            <div v-else-if="currentStep === 'error'" class="text-center py-4">
              <div ref="errorRef" class="mx-auto flex h-16 w-16 sm:h-20 sm:w-20 items-center justify-center bg-red-100 dark:bg-red-900/30 rounded-full">
                <svg class="h-8 w-8 sm:h-10 sm:w-10 text-red-600 dark:text-red-400" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                </svg>
              </div>
              <h3 class="mt-5 sm:mt-6 text-lg sm:text-xl font-semibold text-slate-900 dark:text-white">
                {{ t.somethingWentWrong }}
              </h3>
              <p class="mt-2 text-slate-600 dark:text-neutral-400 text-sm sm:text-base">
                {{ errorMessage }}
              </p>
              <div class="mt-5 sm:mt-6 space-y-3">
                <button
                  type="button"
                  @click="retryAuthentication"
                  class="w-full px-4 py-3 bg-brand-600 hover:bg-brand-700 text-white font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-brand-500 focus:ring-offset-2 rounded-xl text-sm sm:text-base"
                >
                  {{ t.tryAgain }}
                </button>
                <button
                  type="button"
                  @click="goToInitial"
                  class="w-full px-4 py-3 text-slate-600 dark:text-neutral-300 hover:text-slate-800 dark:hover:text-white transition-colors text-sm sm:text-base"
                >
                  {{ t.back }}
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick, computed, onMounted, onUnmounted } from 'vue'
import { translations } from '../utils/i18n.js'
import gsap from 'gsap'

const STORAGE_KEY = 'passkey-modal-language'

const availableLanguages = [
  { code: 'en', nativeName: 'English', englishName: 'English' },
  { code: 'zh', nativeName: '中文', englishName: 'Chinese' },
  { code: 'ja', nativeName: '日本語', englishName: 'Japanese' },
  { code: 'es', nativeName: 'Español', englishName: 'Spanish' },
  { code: 'fr', nativeName: 'Français', englishName: 'French' },
  { code: 'de', nativeName: 'Deutsch', englishName: 'German' },
  { code: 'ko', nativeName: '한국어', englishName: 'Korean' },
  { code: 'ar', nativeName: 'العربية', englishName: 'Arabic' }
]

const rtlLanguages = ['ar', 'he', 'fa', 'ur']

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  language: {
    type: String,
    default: 'en'
  },
  rpId: {
    type: String,
    default: () => window.location.hostname
  },
  challenge: {
    type: String,
    default: 'demo-challenge'
  },
  timeout: {
    type: Number,
    default: 60000
  },
  maxRetries: {
    type: Number,
    default: 3
  },
  autoDetectLanguage: {
    type: Boolean,
    default: true
  },

  closeOnBackdrop: {
    type: Boolean,
    default: true
  },
  closeOnEsc: {
    type: Boolean,
    default: true
  },
  closeOnSwipe: {
    type: Boolean,
    default: true
  },
  saveLanguage: {
    type: Boolean,
    default: true
  }
})

const emit = defineEmits(['close', 'success', 'error', 'timeout', 'languageChange'])

const currentLanguage = ref(props.language)
const t = computed(() => translations[currentLanguage.value] || translations['en'])
const isRTL = computed(() => rtlLanguages.includes(currentLanguage.value))
const isVisible = ref(false)
const currentStep = ref('initial')
const isAuthenticating = ref(false)
const errorMessage = ref('')
const retryCount = ref(0)
const authTimer = ref(null)
const previousActiveElement = ref(null)
const touchStartY = ref(0)
const hoveredButton = ref(null)

const backdropRef = ref(null)
const modalRef = ref(null)
const contentRef = ref(null)
const iconRef = ref(null)
const loadingRef = ref(null)
const successRef = ref(null)
const errorRef = ref(null)
const primaryBtnRef = ref(null)
const closeBtnRef = ref(null)

function loadSavedLanguage() {
  if (props.saveLanguage) {
    try {
      const saved = localStorage.getItem(STORAGE_KEY)
      if (saved && translations[saved]) {
        currentLanguage.value = saved
      }
    } catch (e) {
      console.warn('Failed to load saved language:', e)
    }
  }
}

function saveLanguageToStorage(lang) {
  if (props.saveLanguage) {
    try {
      localStorage.setItem(STORAGE_KEY, lang)
    } catch (e) {
      console.warn('Failed to save language:', e)
    }
  }
}

function detectLanguage() {
  if (props.autoDetectLanguage && !localStorage.getItem(STORAGE_KEY)) {
    const browserLang = navigator.language.split('-')[0]
    if (translations[browserLang]) {
      currentLanguage.value = browserLang
    }
  }
}

function preventBackgroundScroll(prevent) {
  if (prevent) {
    document.body.style.overflow = 'hidden'
  } else {
    document.body.style.overflow = ''
  }
}

function focusFirstInteractiveElement() {
  if (primaryBtnRef.value) {
    primaryBtnRef.value.focus()
  } else if (closeBtnRef.value) {
    closeBtnRef.value.focus()
  }
}

function restoreFocus() {
  if (previousActiveElement.value) {
    previousActiveElement.value.focus()
  }
}

function handleKeydown(event) {
  if (event.key === 'Escape' && props.closeOnEsc) {
    close()
  }
}

function handleBackdropClick() {
  if (props.closeOnBackdrop) {
    close()
  }
}

function handleTouchStart(event) {
  touchStartY.value = event.touches[0].clientY
}

function handleTouchEnd(event) {
  if (!props.closeOnSwipe) return
  
  const touchEndY = event.changedTouches[0].clientY
  const diff = touchStartY.value - touchEndY
  
  if (diff > 50) {
    close()
  }
}

function handleButtonHover(buttonId) {
  hoveredButton.value = buttonId
  if (buttonId === 'primary' && primaryBtnRef.value) {
    gsap.to(primaryBtnRef.value, {
      scale: 1.02,
      duration: 0.2,
      ease: 'power2.out'
    })
  }
}

function handleButtonLeave() {
  hoveredButton.value = null
  if (primaryBtnRef.value) {
    gsap.to(primaryBtnRef.value, {
      scale: 1,
      duration: 0.2,
      ease: 'power2.out'
    })
  }
}

watch(() => props.show, async (newVal) => {
  if (newVal) {
    previousActiveElement.value = document.activeElement
    isVisible.value = true
    currentStep.value = 'initial'
    errorMessage.value = ''
    retryCount.value = 0
    loadSavedLanguage()
    detectLanguage()
    preventBackgroundScroll(true)
    await nextTick()
    openModal()
    focusFirstInteractiveElement()
  } else {
    closeModal()
  }
})

watch(() => props.language, (newVal) => {
  if (translations[newVal]) {
    currentLanguage.value = newVal
  }
})

function openModal() {
  const tl = gsap.timeline()
  
  gsap.set(backdropRef.value, { opacity: 0 })
  gsap.set(modalRef.value, { opacity: 0, scale: 0.95, y: 20 })
  gsap.set(iconRef.value, { scale: 0, rotation: -10 })
  
  tl.to(backdropRef.value, {
    opacity: 1,
    duration: 0.2,
    ease: 'power2.out'
  })
  .to(modalRef.value, {
    opacity: 1,
    scale: 1,
    y: 0,
    duration: 0.25,
    ease: 'power2.out'
  }, '-=0.1')
  .to(iconRef.value, {
    scale: 1,
    rotation: 0,
    duration: 0.4,
    ease: 'back.out(1.5)'
  }, '-=0.15')
}

function closeModal() {
  const tl = gsap.timeline()
  
  tl.to(modalRef.value, {
    opacity: 0,
    scale: 0.95,
    y: 20,
    duration: 0.2,
    ease: 'power2.in'
  })
  .to(backdropRef.value, {
    opacity: 0,
    duration: 0.15,
    ease: 'power2.in'
  }, '-=0.1')
  .call(() => {
    isVisible.value = false
    preventBackgroundScroll(false)
    clearAuthTimer()
    restoreFocus()
  })
}

function close() {
  closeModal()
  setTimeout(() => {
    emit('close')
  }, 300)
}

function changeStep(newStep) {
  const tl = gsap.timeline()
  
  tl.to(contentRef.value, {
    opacity: 0,
    y: -10,
    duration: 0.15,
    ease: 'power2.in'
  })
  .call(() => {
    currentStep.value = newStep
  })
  .set(contentRef.value, { y: 10 })
  .to(contentRef.value, {
    opacity: 1,
    y: 0,
    duration: 0.2,
    ease: 'power2.out'
  })
  
  nextTick(() => {
    if (newStep === 'success' && successRef.value) {
      gsap.fromTo(successRef.value, 
        { scale: 0, rotation: -180 },
        { scale: 1, rotation: 0, duration: 0.5, ease: 'back.out(1.5)' }
      )
    } else if (newStep === 'error' && errorRef.value) {
      gsap.fromTo(errorRef.value,
        { x: -20, opacity: 0 },
        { x: 0, opacity: 1, duration: 0.3, ease: 'power2.out' }
      )
    } else if (newStep === 'loading' && loadingRef.value) {
      gsap.fromTo(loadingRef.value,
        { scale: 0.5, opacity: 0 },
        { scale: 1, opacity: 1, duration: 0.3, ease: 'back.out(1.5)' }
      )
    }
    
    if (modalRef.value) {
      const firstBtn = modalRef.value.querySelector('button, [tabindex]:not([tabindex="-1"])')
      if (firstBtn) {
        firstBtn.focus()
      }
    }
  })
}

function goToInitial() {
  changeStep('initial')
  errorMessage.value = ''
  retryCount.value = 0
  clearAuthTimer()
}

function base64UrlToUint8Array(base64UrlString) {
  const base64 = base64UrlString.replace(/-/g, '+').replace(/_/g, '/')
  const padLength = (4 - (base64.length % 4)) % 4
  const padded = base64.padEnd(base64.length + padLength, '=')
  const binary = atob(padded)
  const bytes = new Uint8Array(binary.length)
  for (let i = 0; i < binary.length; i++) {
    bytes[i] = binary.charCodeAt(i)
  }
  return bytes
}

function uint8ArrayToBase64Url(bytes) {
  const binary = String.fromCharCode(...bytes)
  const base64 = btoa(binary)
  return base64.replace(/\+/g, '-').replace(/\//g, '_').replace(/=+$/, '')
}

function setAuthTimer() {
  clearAuthTimer()
  authTimer.value = setTimeout(() => {
    errorMessage.value = t.value.authTimedOut
    changeStep('error')
    emit('timeout', new Error('Authentication timed out'))
  }, props.timeout)
}

function clearAuthTimer() {
  if (authTimer.value) {
    clearTimeout(authTimer.value)
    authTimer.value = null
  }
}

async function startAuthentication() {
  if (!window.PublicKeyCredential) {
    errorMessage.value = t.value.passkeyNotSupported
    changeStep('error')
    emit('error', new Error('WebAuthn not supported'))
    return
  }

  isAuthenticating.value = true
  changeStep('loading')
  setAuthTimer()

  try {
    const publicKeyCredentialRequestOptions = {
      challenge: base64UrlToUint8Array(props.challenge),
      rpId: props.rpId,
      userVerification: 'preferred',
      timeout: props.timeout
    }

    const credential = await navigator.credentials.get({
      publicKey: publicKeyCredentialRequestOptions
    })

    clearAuthTimer()

    if (credential) {
      changeStep('success')
      
      const credentialData = {
        id: credential.id,
        rawId: uint8ArrayToBase64Url(new Uint8Array(credential.rawId)),
        type: credential.type,
        response: {
          clientDataJSON: uint8ArrayToBase64Url(new Uint8Array(credential.response.clientDataJSON)),
          authenticatorData: uint8ArrayToBase64Url(new Uint8Array(credential.response.authenticatorData)),
          signature: uint8ArrayToBase64Url(new Uint8Array(credential.response.signature)),
          userHandle: credential.response.userHandle 
            ? uint8ArrayToBase64Url(new Uint8Array(credential.response.userHandle))
            : null
        }
      }

      setTimeout(() => {
        emit('success', credentialData)
        close()
      }, 1500)
    }
  } catch (error) {
    console.error('Passkey authentication failed:', error)
    clearAuthTimer()
    
    if (error.name === 'NotAllowedError') {
      errorMessage.value = t.value.authCancelled
    } else if (error.name === 'TimeoutError') {
      errorMessage.value = t.value.authTimedOut
    } else {
      errorMessage.value = error.message || t.value.authFailed
    }
    
    changeStep('error')
    emit('error', error)
  } finally {
    isAuthenticating.value = false
  }
}

function retryAuthentication() {
  if (retryCount.value < props.maxRetries - 1) {
    retryCount.value++
    startAuthentication()
  } else {
    retryCount.value = 0
    goToInitial()
  }
}

function cancelAuthentication() {
  clearAuthTimer()
  goToInitial()
}

onMounted(() => {
  loadSavedLanguage()
  detectLanguage()
})

onUnmounted(() => {
  preventBackgroundScroll(false)
  clearAuthTimer()
})

defineExpose({
  open: () => {
    isVisible.value = true
    currentStep.value = 'initial'
    retryCount.value = 0
    nextTick().then(() => {
      openModal()
      focusFirstInteractiveElement()
    })
  },
  close: close
})
</script>

<style scoped>
button {
  will-change: transform;
}
</style>