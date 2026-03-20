<template>
  <div v-if="isVisible" class="fixed inset-0 z-50 overflow-y-auto">
    <div class="flex min-h-screen items-end justify-center px-4 pb-4 pt-4 sm:items-center sm:p-0">
      <div
        class="fixed inset-0 bg-black/50 backdrop-blur-sm transition-opacity"
        @click="close"
        :class="isOpen ? 'opacity-100' : 'opacity-0'"
      ></div>

      <div
        class="relative w-full max-w-sm transform overflow-hidden rounded-2xl bg-white px-6 py-6 text-left shadow-2xl transition-all sm:my-8"
        :class="isOpen ? 'scale-100 opacity-100' : 'scale-95 opacity-0'"
        role="dialog"
        aria-modal="true"
        aria-labelledby="passkey-modal-title"
      >
        <div class="absolute right-4 top-4">
          <button
            type="button"
            @click="close"
            class="inline-flex items-center justify-center rounded-md p-1 text-gray-400 hover:text-gray-500 hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
          >
            <span class="sr-only">Close</span>
            <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <div class="mt-2">
          <div v-if="currentStep === 'initial'" class="text-center">
            <div class="mx-auto flex h-20 w-20 items-center justify-center rounded-full bg-blue-50">
              <svg class="h-10 w-10 text-blue-600" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z" />
                <path stroke-linecap="round" stroke-linejoin="round" d="m9 12 2 2 4-4" />
              </svg>
            </div>
            <h3 id="passkey-modal-title" class="mt-4 text-xl font-semibold text-gray-900">
              Sign in with a passkey
            </h3>
            <p class="mt-2 text-sm text-gray-500">
              Use your device's biometrics or security key to sign in.
            </p>
          </div>

          <div v-else-if="currentStep === 'loading'" class="text-center py-4">
            <div class="flex items-center justify-center">
              <svg class="animate-spin h-8 w-8 text-blue-600" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
              </svg>
            </div>
            <p class="mt-4 text-sm text-gray-600">Waiting for security key...</p>
            <p class="mt-1 text-xs text-gray-400">Touch your security key or use biometrics</p>
          </div>

          <div v-else-if="currentStep === 'qrcode'" class="text-center">
            <div class="mx-auto flex h-12 w-12 items-center justify-center rounded-full bg-gray-100">
              <svg class="h-6 w-6 text-gray-600" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
              </svg>
            </div>
            <h3 class="mt-4 text-lg font-semibold text-gray-900">
              Scan with your phone
            </h3>
            <p class="mt-2 text-sm text-gray-500">
              Use your phone's camera to scan this QR code
            </p>
            
            <div class="mt-6 bg-white p-4 rounded-lg border border-gray-200 inline-block">
              <div class="w-48 h-48 bg-gray-50 rounded flex items-center justify-center">
                <div class="text-center">
                  <div class="text-6xl mb-2">📱</div>
                  <div class="text-xs text-gray-400">QR Code</div>
                </div>
              </div>
            </div>
          </div>

          <div v-else-if="currentStep === 'success'" class="text-center py-4">
            <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-green-50">
              <svg class="h-8 w-8 text-green-600" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
            </div>
            <h3 class="mt-4 text-lg font-semibold text-gray-900">
              Signed in successfully!
            </h3>
            <p class="mt-2 text-sm text-gray-500">
              Redirecting you...
            </p>
          </div>

          <div v-else-if="currentStep === 'error'" class="text-center py-4">
            <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-red-50">
              <svg class="h-8 w-8 text-red-600" fill="none" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </div>
            <h3 class="mt-4 text-lg font-semibold text-gray-900">
              Something went wrong
            </h3>
            <p class="mt-2 text-sm text-gray-500">
              {{ errorMessage }}
            </p>
          </div>

          <div class="mt-6 space-y-3">
            <template v-if="currentStep === 'initial'">
              <button
                type="button"
                @click="startAuthentication"
                :disabled="isAuthenticating"
                class="inline-flex w-full items-center justify-center rounded-md border border-transparent bg-blue-600 px-4 py-3 text-sm font-semibold text-white shadow-sm hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
              >
                <svg class="mr-2 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z" />
                </svg>
                Use security key
              </button>

              <button
                type="button"
                @click="showQRCode"
                class="inline-flex w-full items-center justify-center rounded-md border border-gray-300 bg-white px-4 py-3 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors"
              >
                <svg class="mr-2 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                </svg>
                Use a different device
              </button>
            </template>

            <template v-else-if="currentStep === 'qrcode'">
              <button
                type="button"
                @click="goToInitial"
                class="inline-flex w-full items-center justify-center rounded-md border border-gray-300 bg-white px-4 py-3 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors"
              >
                <svg class="mr-2 h-5 w-5" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M10.5 19.5L3 12m0 0l7.5-7.5M3 12h18" />
                </svg>
                Back
              </button>
            </template>

            <template v-else-if="currentStep === 'loading'">
              <button
                type="button"
                @click="cancelAuthentication"
                class="inline-flex w-full items-center justify-center rounded-md border border-gray-300 bg-white px-4 py-3 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors"
              >
                Cancel
              </button>
            </template>

            <template v-else-if="currentStep === 'error'">
              <button
                type="button"
                @click="goToInitial"
                class="inline-flex w-full items-center justify-center rounded-md border border-gray-300 bg-white px-4 py-3 text-sm font-semibold text-gray-700 shadow-sm hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-colors"
              >
                Try again
              </button>
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, nextTick } from 'vue'

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  rpId: {
    type: String,
    default: () => window.location.hostname
  },
  challenge: {
    type: String,
    default: 'demo-challenge'
  }
})

const emit = defineEmits(['close', 'success', 'error'])

const isVisible = ref(false)
const isOpen = ref(false)
const currentStep = ref('initial')
const isAuthenticating = ref(false)
const errorMessage = ref('')

watch(() => props.show, async (newVal) => {
  if (newVal) {
    isVisible.value = true
    await nextTick()
    isOpen.value = true
    currentStep.value = 'initial'
    errorMessage.value = ''
  } else {
    isOpen.value = false
    setTimeout(() => {
      isVisible.value = false
    }, 300)
  }
})

function close() {
  isOpen.value = false
  setTimeout(() => {
    isVisible.value = false
    emit('close')
  }, 300)
}

function goToInitial() {
  currentStep.value = 'initial'
  errorMessage.value = ''
}

function showQRCode() {
  currentStep.value = 'qrcode'
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

async function startAuthentication() {
  if (!window.PublicKeyCredential) {
    errorMessage.value = 'Passkeys are not supported in this browser.'
    currentStep.value = 'error'
    emit('error', new Error('WebAuthn not supported'))
    return
  }

  isAuthenticating.value = true
  currentStep.value = 'loading'

  try {
    const publicKeyCredentialRequestOptions = {
      challenge: base64UrlToUint8Array(props.challenge),
      rpId: props.rpId,
      userVerification: 'preferred',
      timeout: 60000
    }

    const credential = await navigator.credentials.get({
      publicKey: publicKeyCredentialRequestOptions
    })

    if (credential) {
      currentStep.value = 'success'
      
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
    
    if (error.name === 'NotAllowedError') {
      errorMessage.value = 'Authentication was cancelled. Please try again.'
    } else if (error.name === 'TimeoutError') {
      errorMessage.value = 'Authentication timed out. Please try again.'
    } else {
      errorMessage.value = error.message || 'Failed to authenticate with passkey.'
    }
    
    currentStep.value = 'error'
    emit('error', error)
  } finally {
    isAuthenticating.value = false
  }
}

function cancelAuthentication() {
  goToInitial()
}

defineExpose({
  open: () => {
    isVisible.value = true
    nextTick().then(() => {
      isOpen.value = true
    })
    currentStep.value = 'initial'
  },
  close: close
})
</script>

<style scoped>
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
.animate-spin {
  animation: spin 1s linear infinite;
}
</style>
