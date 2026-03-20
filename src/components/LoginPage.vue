<template>
  <div ref="loginPageRef" class="min-h-screen flex">
    <div class="w-[45%] bg-gradient-to-br from-purple-50 to-indigo-100 flex items-center justify-center p-8 relative z-10 hidden md:flex">
      <div class="w-full max-w-md">
        <AbstractInteractiveHero
          ref="heroRef"
          :focus-state="focusState"
          :login-status="loginStatus"
          :is-button-hovered="isButtonHovered"
        />
      </div>
    </div>

    <div class="w-full md:w-[55%] flex items-center justify-center p-8 relative z-10">
      <div class="w-full max-w-md">
        <div class="flex items-center gap-3 mb-10">
          <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-purple-600 to-indigo-700 flex items-center justify-center shadow-xl">
            <span class="text-3xl text-white">✨</span>
          </div>
          <div>
            <span class="text-slate-800 text-2xl font-bold tracking-tight">MascotLogin</span>
            <p class="text-slate-500 text-sm">{{ t.welcomeBack }}</p>
          </div>
        </div>

        <h1 class="text-4xl font-bold text-slate-800 mb-3">{{ t.signIn }}</h1>
        <p class="text-slate-500 mb-10">{{ t.enterCredentials }}</p>

        <transition name="error">
          <div v-if="errorMessage" class="mb-6 p-4 bg-rose-50 border border-rose-200 rounded-xl flex items-center gap-3">
            <span class="text-xl">⚠️</span>
            <span class="text-rose-700 font-medium">{{ errorMessage }}</span>
          </div>
        </transition>

        <form @submit.prevent="handleSubmit" class="space-y-6">
          <div class="space-y-2">
            <label for="email" class="text-sm font-medium text-slate-700 ml-1">
              {{ t.emailAddress }}
            </label>
            <div class="relative group">
              <input
                id="email"
                v-model="email"
                type="email"
                :placeholder="t.emailPlaceholder"
                autocomplete="email"
                :aria-label="t.emailAddress"
                class="w-full px-5 py-4 pl-12 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
                @focus="handleEmailFocus"
                @blur="handleBlur"
              />
              <span class="absolute left-4 top-1/2 -translate-y-1/2 text-slate-400 text-lg pointer-events-none">✉️</span>
            </div>
          </div>

          <div class="space-y-2">
            <label for="password" class="text-sm font-medium text-slate-700 ml-1">
              {{ t.password }}
            </label>
            <div class="relative group">
              <input
                id="password"
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                :placeholder="t.passwordPlaceholder"
                autocomplete="current-password"
                :aria-label="t.password"
                class="w-full px-5 py-4 pl-12 pr-12 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
                @focus="handlePasswordFocus"
                @blur="handleBlur"
              />
              <span class="absolute left-4 top-1/2 -translate-y-1/2 text-slate-400 text-lg pointer-events-none">•</span>
              <button
                type="button"
                @click="showPassword = !showPassword"
                :aria-label="showPassword ? 'Hide password' : 'Show password'"
                class="absolute right-4 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 transition-colors p-1 rounded-lg hover:bg-slate-100"
              >
                {{ showPassword ? '🙈' : '👁️' }}
              </button>
            </div>
            <div v-if="password" class="space-y-2 mt-2">
              <div class="flex items-center justify-between">
                <span class="text-xs font-medium text-slate-600">{{ t.passwordStrength }}</span>
                <span class="text-xs font-bold" :class="passwordStrengthColor">{{ passwordStrengthLabel }}</span>
              </div>
              <div class="flex gap-1.5">
                <div v-for="i in 4" :key="i" class="h-1.5 flex-1 rounded-full transition-all duration-300" :class="getStrengthBarColor(i)"></div>
              </div>
            </div>
          </div>

          <div class="flex justify-end">
            <button type="button" @click="showForgotPassword = true" :aria-label="t.forgotPassword" class="text-sm text-purple-600 hover:text-purple-800 transition-colors font-medium hover:underline">
              {{ t.forgotPassword }}
            </button>
          </div>

          <div class="relative pt-4">
            <button
              ref="buttonRef"
              type="submit"
              :disabled="isLoading || isSuccess"
              :aria-label="t.signInButton"
              class="login-btn relative w-full py-4 rounded-xl font-bold text-lg text-white overflow-hidden transition-all duration-300 disabled:cursor-not-allowed disabled:opacity-70 bg-gradient-to-r from-purple-600 to-indigo-700 hover:shadow-lg hover:shadow-purple-500/30 hover:scale-[1.02] active:scale-[0.98]"
              @mouseenter="handleButtonHover"
              @mouseleave="handleButtonLeave"
            >
              <span class="relative z-10 flex items-center justify-center gap-2">
                <template v-if="isLoading">
                  <svg class="w-5 h-5 animate-spin" viewBox="0 0 24 24" fill="none">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
                  </svg>
                  {{ t.signingIn }}
                </template>
                <template v-else-if="isSuccess">
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
                  </svg>
                  {{ t.welcome }}
                </template>
                <template v-else>
                  {{ t.signInButton }}
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3"></path>
                  </svg>
                </template>
              </span>
            </button>
          </div>
        </form>

        <div class="mt-8">
          <div class="relative">
            <div class="absolute inset-0 flex items-center">
              <div class="w-full border-t border-slate-200"></div>
            </div>
            <div class="relative flex justify-center text-sm">
              <span class="px-4 bg-white text-slate-500">{{ t.orContinueWith }}</span>
            </div>
          </div>

          <button
            type="button"
            @click="handlePasskeyLogin"
            :aria-label="t.signInWithPasskey"
            class="w-full mt-6 py-4 rounded-xl font-bold text-lg text-slate-700 border-2 border-slate-200 bg-white hover:border-purple-400 hover:bg-purple-50 transition-all hover:scale-[1.02] active:scale-[0.98] flex items-center justify-center gap-3"
          >
            <svg class="w-6 h-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"></path>
              <path d="m9 12 2 2 4-4"></path>
            </svg>
            {{ t.signInWithPasskey }}
          </button>
        </div>

        <p class="mt-10 text-center text-slate-500">
          {{ t.noAccount }}
          <button type="button" @click="showCreateAccount = true" class="text-purple-700 font-bold hover:text-purple-900 transition-colors">
            {{ t.createAccount }}
          </button>
        </p>
      </div>
    </div>

    <div v-if="showForgotPassword" class="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="showForgotPassword = false">
      <div class="bg-white rounded-2xl p-6 sm:p-8 w-full max-w-md shadow-2xl" role="dialog" aria-modal="true" aria-labelledby="forgot-password-title">
        <div class="flex justify-between items-center mb-6">
          <h2 id="forgot-password-title" class="text-xl sm:text-2xl font-bold text-slate-800">{{ t.forgotPasswordTitle }}</h2>
          <button @click="showForgotPassword = false" :aria-label="'Close ' + t.forgotPasswordTitle" class="text-slate-400 hover:text-slate-600 text-2xl w-8 h-8 flex items-center justify-center rounded-lg hover:bg-slate-100 transition-colors">×</button>
        </div>
        <p class="text-slate-600 mb-6">{{ t.forgotPasswordDesc }}</p>
        <div class="space-y-4">
          <div>
            <label class="text-sm font-medium text-slate-700 ml-1">{{ t.emailAddress }}</label>
            <input
              v-model="resetEmail"
              type="email"
              :placeholder="t.emailPlaceholder"
              :aria-label="t.emailAddress"
              class="w-full px-5 py-4 mt-1 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
            />
          </div>
          <button
            @click="handleResetPassword"
            :aria-label="t.sendResetLink"
            class="w-full py-4 rounded-xl font-bold text-lg text-white bg-gradient-to-r from-purple-600 to-indigo-700 hover:shadow-lg hover:shadow-purple-500/30 transition-all hover:scale-[1.02] active:scale-[0.98]"
          >
            {{ t.sendResetLink }}
          </button>
        </div>
      </div>
    </div>

    <PasskeyModal
      ref="passkeyModalRef"
      :show="showPasskeyModal"
      @close="showPasskeyModal = false"
      @success="handlePasskeySuccess"
      @error="handlePasskeyError"
    />

    <div v-if="showCreateAccount" class="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="showCreateAccount = false">
      <div class="bg-white rounded-2xl p-6 sm:p-8 w-full max-w-md shadow-2xl max-h-[90vh] overflow-y-auto" role="dialog" aria-modal="true" aria-labelledby="create-account-title">
        <div class="flex justify-between items-center mb-6">
          <h2 id="create-account-title" class="text-xl sm:text-2xl font-bold text-slate-800">{{ t.createAccountTitle }}</h2>
          <button @click="showCreateAccount = false" :aria-label="'Close ' + t.createAccountTitle" class="text-slate-400 hover:text-slate-600 text-2xl w-8 h-8 flex items-center justify-center rounded-lg hover:bg-slate-100 transition-colors">×</button>
        </div>
        <div class="space-y-4">
          <div>
            <label class="text-sm font-medium text-slate-700 ml-1">{{ t.fullName }}</label>
            <input
              v-model="newAccount.name"
              type="text"
              :placeholder="t.fullNamePlaceholder"
              :aria-label="t.fullName"
              class="w-full px-5 py-4 mt-1 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
            />
          </div>
          <div>
            <label class="text-sm font-medium text-slate-700 ml-1">{{ t.emailAddress }}</label>
            <input
              v-model="newAccount.email"
              type="email"
              :placeholder="t.emailPlaceholder"
              :aria-label="t.emailAddress"
              class="w-full px-5 py-4 mt-1 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
            />
          </div>
          <div class="relative">
            <label class="text-sm font-medium text-slate-700 ml-1">{{ t.password }}</label>
            <input
              v-model="newAccount.password"
              :type="showNewPassword ? 'text' : 'password'"
              :placeholder="t.passwordPlaceholder"
              :aria-label="t.password"
              class="w-full px-5 py-4 pr-12 mt-1 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
            />
            <button
              type="button"
              @click="showNewPassword = !showNewPassword"
              :aria-label="showNewPassword ? 'Hide password' : 'Show password'"
              class="absolute right-4 top-[calc(50%+12px)] -translate-y-1/2 text-slate-400 hover:text-slate-600 transition-colors p-1 rounded-lg hover:bg-slate-100"
            >
              {{ showNewPassword ? '🙈' : '👁️' }}
            </button>
          </div>
          <div class="relative">
            <label class="text-sm font-medium text-slate-700 ml-1">{{ t.confirmPassword }}</label>
            <input
              v-model="newAccount.confirmPassword"
              :type="showConfirmPassword ? 'text' : 'password'"
              :placeholder="t.passwordPlaceholder"
              :aria-label="t.confirmPassword"
              class="w-full px-5 py-4 pr-12 mt-1 bg-white border-2 border-slate-200 rounded-xl text-slate-800 placeholder-slate-400 focus:outline-none focus:border-purple-400 focus:ring-4 focus:ring-purple-100 transition-all focus:scale-[1.01] focus:shadow-lg"
            />
            <button
              type="button"
              @click="showConfirmPassword = !showConfirmPassword"
              :aria-label="showConfirmPassword ? 'Hide password' : 'Show password'"
              class="absolute right-4 top-[calc(50%+12px)] -translate-y-1/2 text-slate-400 hover:text-slate-600 transition-colors p-1 rounded-lg hover:bg-slate-100"
            >
              {{ showConfirmPassword ? '🙈' : '👁️' }}
            </button>
          </div>
          <button
            @click="handleCreateAccount"
            :aria-label="t.createAccountButton"
            class="w-full py-4 rounded-xl font-bold text-lg text-white bg-gradient-to-r from-purple-600 to-indigo-700 hover:shadow-lg hover:shadow-purple-500/30 transition-all hover:scale-[1.02] active:scale-[0.98] mt-4"
          >
            {{ t.createAccountButton }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import gsap from 'gsap'
import AbstractInteractiveHero from './AbstractInteractiveHero.vue'
import PasskeyModal from './PasskeyModal.vue'
import { translations } from '../utils/i18n.js'

const props = defineProps({
  language: {
    type: String,
    default: 'en',
  },
})

const emit = defineEmits(['login'])

const email = ref('')
const password = ref('')
const showPassword = ref(false)
const focusState = ref('none')
const loginStatus = ref('idle')
const errorMessage = ref('')
const isLoading = ref(false)
const isSuccess = ref(false)
const isError = ref(false)
const isButtonHovered = ref(false)

const showForgotPassword = ref(false)
const showCreateAccount = ref(false)
const showPasskeyModal = ref(false)
const resetEmail = ref('')
const showNewPassword = ref(false)
const showConfirmPassword = ref(false)
const isPasskeyLoading = ref(false)
const newAccount = ref({
  name: '',
  email: '',
  password: '',
  confirmPassword: ''
})

const heroRef = ref(null)
const buttonRef = ref(null)
const loginPageRef = ref(null)
const passkeyModalRef = ref(null)

const t = computed(() => translations[props.language] || translations['en'])

const passwordStrength = computed(() => {
  const pwd = password.value
  if (!pwd) return 0
  let strength = 0
  if (pwd.length >= 6) strength++
  if (pwd.length >= 10) strength++
  if (/[A-Z]/.test(pwd) && /[a-z]/.test(pwd)) strength++
  if (/[0-9]/.test(pwd)) strength++
  if (/[^A-Za-z0-9]/.test(pwd)) strength++
  return Math.min(4, strength)
})

const passwordStrengthLabel = computed(() => {
  const strength = passwordStrength.value
  if (strength === 0) return ''
  if (strength === 1) return t.value.passwordWeak
  if (strength === 2) return t.value.passwordFair
  if (strength === 3) return t.value.passwordGood
  return t.value.passwordStrong
})

const passwordStrengthColor = computed(() => {
  const strength = passwordStrength.value
  if (strength <= 1) return 'text-rose-600'
  if (strength === 2) return 'text-amber-600'
  if (strength === 3) return 'text-blue-600'
  return 'text-emerald-600'
})

function getStrengthBarColor(index) {
  const strength = passwordStrength.value
  if (index <= strength) {
    if (strength <= 1) return 'bg-rose-500'
    if (strength === 2) return 'bg-amber-500'
    if (strength === 3) return 'bg-blue-500'
    return 'bg-emerald-500'
  }
  return 'bg-slate-200'
}

function handleEmailFocus() {
  focusState.value = 'email'
  errorMessage.value = ''
}

function handlePasswordFocus() {
  focusState.value = 'password'
  errorMessage.value = ''
}

function handleBlur() {
  focusState.value = 'none'
}

function handleButtonHover() {
  isButtonHovered.value = true
}

function handleButtonLeave() {
  isButtonHovered.value = false
}

function validateEmail(emailStr) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(emailStr)
}

function validateForm() {
  if (!email.value || !password.value) {
    errorMessage.value = t.value.pleaseEnterEmailPassword
    triggerError()
    return false
  }
  if (!validateEmail(email.value)) {
    errorMessage.value = t.value.pleaseEnterValidEmail
    triggerError()
    return false
  }
  if (password.value.length < 6) {
    errorMessage.value = t.value.passwordTooShort
    triggerError()
    return false
  }
  return true
}

function triggerError() {
  isError.value = true
  loginStatus.value = 'error'
  setTimeout(() => {
    isError.value = false
    loginStatus.value = 'idle'
  }, 2000)
}

async function handleSubmit() {
  if (!validateForm()) return

  isLoading.value = true
  errorMessage.value = ''
  loginStatus.value = 'idle'

  await new Promise(resolve => setTimeout(resolve, 1500))

  isLoading.value = false

  isSuccess.value = true
  loginStatus.value = 'success'
  setTimeout(() => {
    emit('login', { email: email.value, password: password.value })
  }, 800)
}

function handleResetPassword() {
  if (!resetEmail.value || !validateEmail(resetEmail.value)) {
    alert(t.value.pleaseEnterValidEmail)
    return
  }
  alert(t.value.resetLinkSent)
  showForgotPassword.value = false
  resetEmail.value = ''
}

function handleCreateAccount() {
  if (!newAccount.value.name || !newAccount.value.email || !newAccount.value.password || !newAccount.value.confirmPassword) {
    alert(t.value.pleaseFillAllFields)
    return
  }
  if (!validateEmail(newAccount.value.email)) {
    alert(t.value.pleaseEnterValidEmail)
    return
  }
  if (newAccount.value.password.length < 6) {
    alert(t.value.passwordTooShort)
    return
  }
  if (newAccount.value.password !== newAccount.value.confirmPassword) {
    alert(t.value.passwordsDoNotMatch)
    return
  }
  alert(t.value.accountCreated)
  showCreateAccount.value = false
  newAccount.value = { name: '', email: '', password: '', confirmPassword: '' }
}

function handlePasskeyLogin() {
  if (!window.PublicKeyCredential) {
    alert('Passkeys are not supported in this browser. Please use a modern browser that supports WebAuthn.')
    return
  }
  showPasskeyModal.value = true
}

function handlePasskeySuccess(credentialData) {
  isSuccess.value = true
  loginStatus.value = 'success'
  setTimeout(() => {
    emit('login', { method: 'passkey', credential: credentialData })
  }, 800)
}

function handlePasskeyError(error) {
  console.error('Passkey error:', error)
  errorMessage.value = 'Passkey authentication failed. Please try again.'
  triggerError()
}

onMounted(() => {
  gsap.fromTo(
    loginPageRef.value,
    { opacity: 0 },
    { opacity: 1, duration: 0.8, ease: 'power2.out' }
  )
})
</script>

<style scoped>
.error-enter-active,
.error-leave-active {
  transition: all 0.3s ease;
}

.error-enter-from,
.error-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

input {
  caret-color: #7C3AED;
}

input::placeholder {
  color: #94A3B8;
}
</style>
