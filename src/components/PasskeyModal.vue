<template>
  <div v-if="isVisible" class="fixed inset-0 z-50 overflow-y-auto">
    <div class="flex min-h-screen items-end justify-center px-4 pb-4 pt-4 sm:items-center sm:p-0">
      <div
        ref="backdropRef"
        class="fixed inset-0 bg-gradient-to-br from-purple-900/60 via-blue-900/50 to-indigo-900/60 backdrop-blur-md dark:from-slate-900/70 dark:via-slate-800/60 dark:to-slate-900/70"
        @click="close"
      >
        <div ref="deco1" class="deco-circle absolute top-8 left-8 w-48 h-48 rounded-full bg-pink-400/20"></div>
        <div ref="deco2" class="deco-circle absolute bottom-8 right-8 w-40 h-40 rounded-full bg-blue-400/20"></div>
        <div ref="deco3" class="deco-circle absolute top-1/3 left-1/5 w-28 h-28 rounded-full bg-yellow-400/20"></div>
      </div>

      <div
        ref="modalRef"
        class="relative w-full max-w-md overflow-hidden rounded-3xl bg-white/95 dark:bg-slate-800/95 backdrop-blur-xl px-8 py-10 text-left shadow-2xl sm:my-8 border border-white/50 dark:border-slate-600/50"
        role="dialog"
        aria-modal="true"
        aria-labelledby="passkey-modal-title"
      >
        <div class="absolute top-0 right-0 w-40 h-40 bg-gradient-to-br from-purple-200/50 to-blue-200/30 dark:from-purple-500/20 dark:to-blue-500/10 rounded-bl-full -z-10"></div>
        <div class="absolute bottom-0 left-0 w-32 h-32 bg-gradient-to-tr from-pink-200/40 to-yellow-200/30 dark:from-pink-500/20 dark:to-yellow-500/10 rounded-tr-full -z-10"></div>
        
        <div class="absolute right-5 top-5">
          <button
            type="button"
            @click="close"
            class="inline-flex items-center justify-center rounded-full p-3 text-gray-400 dark:text-slate-400 hover:text-gray-600 dark:hover:text-slate-200 hover:bg-gray-100 dark:hover:bg-slate-700 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 transition-all duration-300 hover:scale-110"
            aria-label="Close"
          >
            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <div ref="contentRef" class="mt-2">
          <div v-if="currentStep === 'initial'" class="text-center">
            <div ref="iconRef" class="mx-auto flex h-32 w-32 items-center justify-center rounded-full bg-gradient-to-br from-purple-100 via-pink-100 to-blue-100 dark:from-purple-900/50 dark:via-pink-900/50 dark:to-blue-900/50 shadow-lg">
              <svg class="h-16 w-16 text-purple-600 dark:text-purple-400" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z" />
                <path stroke-linecap="round" stroke-linejoin="round" d="m9 12 2 2 4-4" />
              </svg>
            </div>
            <h3 id="passkey-modal-title" class="mt-6 text-2xl font-bold bg-gradient-to-r from-purple-700 via-pink-600 to-blue-700 dark:from-purple-400 dark:via-pink-400 dark:to-blue-400 bg-clip-text text-transparent">
              {{ t.signInWithPasskeyTitle }}
            </h3>
            <p class="mt-4 text-base text-gray-600 dark:text-slate-300 leading-relaxed">
              {{ t.signInWithPasskeyDesc }}
            </p>
          </div>

          <div v-else-if="currentStep === 'loading'" class="text-center py-4">
            <div class="flex items-center justify-center">
              <div ref="loadingRef" class="relative">
                <div class="absolute inset-0 rounded-full bg-purple-400/30 dark:bg-purple-500/30 blur-xl animate-pulse"></div>
                <svg class="relative h-20 w-20 text-purple-600 dark:text-purple-400" viewBox="0 0 24 24" fill="none">
                  <circle class="opacity-20" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-80" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
                </svg>
              </div>
            </div>
            <div class="mt-6 space-y-2">
              <p class="text-base text-gray-700 dark:text-slate-200 font-medium">{{ t.waitingForSecurityKey }}</p>
              <p class="text-sm text-gray-500 dark:text-slate-400">{{ t.touchYourSecurityKey }}</p>
            </div>
          </div>

          <div v-else-if="currentStep === 'qrcode'" class="text-center">
            <div class="mx-auto flex h-20 w-20 items-center justify-center rounded-full bg-gradient-to-br from-gray-100 to-gray-200 dark:from-slate-700 dark:to-slate-600 shadow-md">
              <svg class="h-10 w-10 text-gray-600 dark:text-slate-300" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
              </svg>
            </div>
            <h3 class="mt-5 text-xl font-bold text-gray-800 dark:text-white">
              {{ t.scanWithYourPhone }}
            </h3>
            <p class="mt-3 text-base text-gray-600 dark:text-slate-300">
              {{ t.useYourPhoneCamera }}
            </p>
            
            <div ref="qrRef" class="mt-8 bg-white dark:bg-slate-700 p-6 rounded-2xl border-2 border-dashed border-purple-200 dark:border-purple-400/30 inline-block shadow-lg">
              <div class="w-56 h-56 bg-gradient-to-br from-gray-50 via-purple-50 to-blue-50 dark:from-slate-800 dark:via-purple-900/30 dark:to-blue-900/30 rounded-xl flex items-center justify-center">
                <div class="text-center">
                  <div class="text-8xl mb-4">📱✨</div>
                  <div class="text-sm font-medium text-purple-600 dark:text-purple-400">{{ t.qrCode }}</div>
                </div>
              </div>
            </div>
          </div>

          <div v-else-if="currentStep === 'success'" class="text-center py-4">
            <div ref="successRef" class="mx-auto flex h-24 w-24 items-center justify-center rounded-full bg-gradient-to-br from-green-100 via-emerald-100 to-teal-100 dark:from-green-900/50 dark:via-emerald-900/50 dark:to-teal-900/50 shadow-lg">
              <svg class="h-12 w-12 text-emerald-600 dark:text-emerald-400" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
            </div>
            <h3 class="mt-5 text-xl font-bold text-gray-800 dark:text-white">
              {{ t.signedInSuccessfully }}
            </h3>
            <p class="mt-2 text-base text-gray-500 dark:text-slate-300">
              {{ t.redirectingYou }}
            </p>
            <div class="mt-6 flex justify-center gap-2">
              <span v-for="i in 3" :key="i" class="w-3 h-3 rounded-full bg-emerald-400" :style="{ animation: `bounce 1.4s ease-in-out infinite ${i * 0.15}s` }"></span>
            </div>
          </div>

          <div v-else-if="currentStep === 'error'" class="text-center py-4">
            <div ref="errorRef" class="mx-auto flex h-24 w-24 items-center justify-center rounded-full bg-gradient-to-br from-red-100 via-pink-100 to-rose-100 dark:from-red-900/50 dark:via-pink-900/50 dark:to-rose-900/50 shadow-lg">
              <svg class="h-12 w-12 text-rose-600 dark:text-rose-400" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </div>
            <h3 class="mt-5 text-xl font-bold text-gray-800 dark:text-white">
              {{ t.somethingWentWrong }}
            </h3>
            <p class="mt-3 text-base text-gray-500 dark:text-slate-300">
              {{ errorMessage }}
            </p>
          </div>

          <div class="mt-10 space-y-4">
            <template v-if="currentStep === 'initial'">
              <button
                type="button"
                @click="startAuthentication"
                :disabled="isAuthenticating"
                class="inline-flex w-full items-center justify-center rounded-2xl border border-transparent bg-gradient-to-r from-purple-600 via-pink-500 to-blue-600 px-6 py-4.5 text-base font-bold text-white shadow-lg hover:shadow-purple-500/40 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 disabled:opacity-60 disabled:cursor-not-allowed transition-all duration-300 hover:shadow-xl active:shadow-md"
              >
                <svg class="mr-3 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z" />
                </svg>
                {{ t.useSecurityKey }}
              </button>

              <button
                type="button"
                @click="showQRCode"
                class="inline-flex w-full items-center justify-center rounded-2xl border-2 border-purple-200 dark:border-purple-400/30 bg-white dark:bg-slate-700 px-6 py-4.5 text-base font-bold text-purple-700 dark:text-purple-300 shadow-md hover:bg-purple-50 dark:hover:bg-slate-600 hover:border-purple-300 dark:hover:border-purple-400/50 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 transition-all duration-300 hover:shadow-lg active:shadow-sm"
              >
                <svg class="mr-3 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                </svg>
                {{ t.useADifferentDevice }}
              </button>
            </template>

            <template v-else-if="currentStep === 'qrcode'">
              <button
                type="button"
                @click="goToInitial"
                class="inline-flex w-full items-center justify-center rounded-2xl border-2 border-gray-200 dark:border-slate-500 bg-white dark:bg-slate-700 px-6 py-4.5 text-base font-bold text-gray-700 dark:text-slate-200 shadow-md hover:bg-gray-50 dark:hover:bg-slate-600 hover:border-gray-300 dark:hover:border-slate-400 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition-all duration-300 hover:shadow-lg active:shadow-sm"
              >
                <svg class="mr-3 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M10.5 19.5L3 12m0 0l7.5-7.5M3 12h18" />
                </svg>
                {{ t.back }}
              </button>
            </template>

            <template v-else-if="currentStep === 'loading'">
              <button
                type="button"
                @click="cancelAuthentication"
                class="inline-flex w-full items-center justify-center rounded-2xl border-2 border-gray-200 dark:border-slate-500 bg-white dark:bg-slate-700 px-6 py-4.5 text-base font-bold text-gray-700 dark:text-slate-200 shadow-md hover:bg-gray-50 dark:hover:bg-slate-600 hover:border-gray-300 dark:hover:border-slate-400 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition-all duration-300 hover:shadow-lg active:shadow-sm"
              >
                {{ t.cancel }}
              </button>
            </template>

            <template v-else-if="currentStep === 'error'">
              <button
                type="button"
                @click="retryAuthentication"
                class="inline-flex w-full items-center justify-center rounded-2xl border-2 border-rose-200 dark:border-rose-400/30 bg-white dark:bg-slate-700 px-6 py-4.5 text-base font-bold text-rose-700 dark:text-rose-300 shadow-md hover:bg-rose-50 dark:hover:bg-slate-600 hover:border-rose-300 dark:hover:border-rose-400/50 focus:outline-none focus:ring-2 focus:ring-rose-500 focus:ring-offset-2 transition-all duration-300 hover:shadow-lg active:shadow-sm"
              >
                <svg class="mr-3 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                </svg>
                {{ retryCount > 0 ? `${t.tryAgain} (${retryCount + 1}/3)` : t.tryAgain }}
              </button>
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick, computed, onMounted } from 'vue'
import { translations } from '../utils/i18n.js'
import gsap from 'gsap'

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
  }
})

const emit = defineEmits(['close', 'success', 'error', 'timeout'])

const t = computed(() => translations[props.language] || translations['en'])
const isVisible = ref(false)
const currentStep = ref('initial')
const isAuthenticating = ref(false)
const errorMessage = ref('')
const retryCount = ref(0)
const authTimer = ref(null)
const backdropRef = ref(null)
const modalRef = ref(null)
const contentRef = ref(null)
const iconRef = ref(null)
const qrRef = ref(null)
const loadingRef = ref(null)
const successRef = ref(null)
const errorRef = ref(null)
const deco1 = ref(null)
const deco2 = ref(null)
const deco3 = ref(null)

watch(() => props.show, async (newVal) => {
  if (newVal) {
    isVisible.value = true
    currentStep.value = 'initial'
    errorMessage.value = ''
    retryCount.value = 0
    await nextTick()
    openModal()
  } else {
    closeModal()
  }
})

function openModal() {
  const tl = gsap.timeline()
  
  gsap.set(backdropRef.value, { opacity: 0 })
  gsap.set(modalRef.value, { opacity: 0, scale: 0.6, y: 50, rotation: -5 })
  gsap.set([deco1.value, deco2.value, deco3.value], { scale: 0, opacity: 0 })
  
  tl.to(backdropRef.value, {
    opacity: 1,
    duration: 0.4,
    ease: 'power2.out'
  })
  .to([deco1.value, deco2.value, deco3.value], {
    scale: 1,
    opacity: 1,
    duration: 0.5,
    stagger: 0.1,
    ease: 'back.out(1.5)'
  }, '-=0.2')
  .to(modalRef.value, {
    opacity: 1,
    scale: 1,
    y: 0,
    rotation: 0,
    duration: 0.6,
    ease: 'elastic.out(1, 0.6)'
  }, '-=0.3')
  
  if (iconRef.value) {
    tl.to(iconRef.value, {
      scale: [1, 1.1, 1],
      duration: 0.6,
      ease: 'back.out(1.4)'
    }, '-=0.2')
  }
}

function closeModal() {
  const tl = gsap.timeline()
  
  tl.to(modalRef.value, {
    opacity: 0,
    scale: 0.7,
    y: 30,
    duration: 0.35,
    ease: 'back.in(1.3)'
  })
  .to([deco1.value, deco2.value, deco3.value], {
    scale: 0,
    opacity: 0,
    duration: 0.25,
    stagger: -0.05,
    ease: 'power2.in'
  }, '-=0.15')
  .to(backdropRef.value, {
    opacity: 0,
    duration: 0.3,
    ease: 'power2.in'
  }, '-=0.15')
  .call(() => {
    isVisible.value = false
    clearAuthTimer()
  })
}

function close() {
  closeModal()
  setTimeout(() => {
    emit('close')
  }, 400)
}

function changeStep(newStep) {
  const tl = gsap.timeline()
  
  tl.to(contentRef.value, {
    opacity: 0,
    y: -20,
    scale: 0.95,
    duration: 0.2,
    ease: 'power2.in'
  })
  .call(() => {
    currentStep.value = newStep
  })
  .set(contentRef.value, { y: 20, scale: 0.95 })
  .to(contentRef.value, {
    opacity: 1,
    y: 0,
    scale: 1,
    duration: 0.3,
    ease: 'back.out(1.4)'
  })
  
  nextTick(() => {
    if (newStep === 'success' && successRef.value) {
      gsap.fromTo(successRef.value, 
        { scale: 0, rotation: -180 },
        { scale: 1, rotation: 0, duration: 0.6, ease: 'elastic.out(1, 0.5)' }
      )
    } else if (newStep === 'error' && errorRef.value) {
      gsap.fromTo(errorRef.value,
        { x: -20, opacity: 0 },
        { x: 0, opacity: 1, duration: 0.4, ease: 'back.out(1.4)' }
      )
      gsap.to(errorRef.value, {
        x: [0, -5, 5, -5, 5, 0],
        duration: 0.5,
        delay: 0.2,
        ease: 'power2.inOut'
      })
    } else if (newStep === 'qrcode' && qrRef.value) {
      gsap.fromTo(qrRef.value,
        { scale: 0.5, rotation: 10 },
        { scale: 1, rotation: 0, duration: 0.5, ease: 'back.out(1.5)' }
      )
    } else if (newStep === 'loading' && loadingRef.value) {
      gsap.fromTo(loadingRef.value,
        { scale: 0 },
        { scale: 1, duration: 0.4, ease: 'back.out(1.4)' }
      )
      gsap.to(loadingRef.value, {
        rotation: 360,
        duration: 2,
        repeat: -1,
        ease: 'none'
      })
    }
  })
}

function goToInitial() {
  changeStep('initial')
  errorMessage.value = ''
  retryCount.value = 0
  clearAuthTimer()
}

function showQRCode() {
  changeStep('qrcode')
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

defineExpose({
  open: () => {
    isVisible.value = true
    currentStep.value = 'initial'
    retryCount.value = 0
    nextTick().then(() => {
      openModal()
    })
  },
  close: close
})
</script>

<style scoped>
@keyframes bounce {
  0%, 80%, 100% {
    transform: scale(0);
  }
  40% {
    transform: scale(1);
  }
}

.deco-circle {
  pointer-events: none;
}
</style>
