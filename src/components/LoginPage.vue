<template>
  <div ref="loginPageRef" class="min-h-screen flex flex-col md:flex-row overflow-hidden relative animated-gradient">
    <div class="flex md:flex md:w-[45%] items-center justify-center p-4 sm:p-6 md:p-8 relative z-10">
      <div class="glass-card rounded-3xl p-4 sm:p-6 md:p-8 w-full max-w-md">
        <AbstractInteractiveHero
          ref="heroRef"
          :focus-state="focusState"
          :login-status="loginStatus"
          :is-button-hovered="isButtonHovered"
        />
        <div class="mt-3 sm:mt-4 text-center">
          <div class="glass-pill inline-flex items-center gap-2 text-slate-600 dark:text-neutral-300">
            <svg class="w-4 h-4 text-green-500 animate-pulse-soft" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
            </svg>
            {{ t.secureConnection }}
          </div>
        </div>
      </div>
    </div>

    <div class="w-full md:w-[55%] flex items-center justify-center p-4 sm:p-6 md:p-8 relative z-10 overflow-y-auto md:min-h-screen">
      <div class="w-full max-w-md py-4">
        <div class="flex items-center justify-between mb-6 sm:mb-8 md:mb-12">
          <div class="flex items-center gap-2 sm:gap-3">
            <div class="w-8 h-8 sm:w-10 sm:h-10 md:w-11 md:h-11 rounded-xl bg-brand-600 flex items-center justify-center">
              <svg class="w-4 h-4 sm:w-5 sm:h-5 md:w-6 md:h-6 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z" />
              </svg>
            </div>
            <div>
              <span class="text-slate-900 dark:text-white text-lg sm:text-xl md:text-2xl font-bold tracking-tight">MascotLogin</span>
              <p class="text-slate-500 dark:text-neutral-400 text-xs sm:text-sm">{{ t.welcomeBack }}</p>
            </div>
          </div>
          <button
            type="button"
            @click="toggleDarkMode"
            :aria-label="isDarkMode ? t.lightMode : t.darkMode"
            class="w-8 h-8 sm:w-10 sm:h-10 rounded-xl flex items-center justify-center transition-all duration-200 hover:bg-slate-100 dark:hover:bg-neutral-800 active:scale-95"
          >
            <svg v-if="isDarkMode" class="w-4 h-4 sm:w-5 sm:h-5 text-amber-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
            </svg>
            <svg v-else class="w-4 h-4 sm:w-5 sm:h-5 text-slate-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
            </svg>
          </button>
        </div>

        <h1 class="text-2xl sm:text-3xl md:text-4xl font-bold text-slate-900 dark:text-white mb-2">{{ t.signIn }}</h1>
        <p class="text-slate-500 dark:text-neutral-400 text-sm sm:text-base mb-6 sm:mb-8 md:mb-10">{{ t.enterCredentials }}</p>

        <transition name="error">
          <div v-if="errorMessage" class="mb-5 p-3.5 bg-red-50 dark:bg-red-900/20 border border-red-200 dark:border-red-800/50 rounded-xl flex items-center gap-3">
            <svg class="w-5 h-5 text-red-500 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
            </svg>
            <span class="text-red-700 dark:text-red-300 font-medium text-sm">{{ errorMessage }}</span>
          </div>
        </transition>

        <Transition name="toast">
          <div v-if="showToast" class="fixed top-4 right-4 z-[100] max-w-sm w-full">
            <div :class="[
              'p-4 rounded-2xl shadow-apple-xl border flex items-start gap-3',
              toastType === 'success' ? 'bg-green-50 dark:bg-green-900/20 border-green-200 dark:border-green-800/50' :
              toastType === 'warning' ? 'bg-amber-50 dark:bg-amber-900/20 border-amber-200 dark:border-amber-800/50' :
              'bg-blue-50 dark:bg-blue-900/20 border-blue-200 dark:border-blue-800/50'
            ]">
              <svg v-if="toastType === 'success'" class="w-5 h-5 text-green-600 flex-shrink-0 mt-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              <svg v-else-if="toastType === 'warning'" class="w-5 h-5 text-amber-600 flex-shrink-0 mt-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
              </svg>
              <svg v-else class="w-5 h-5 text-blue-600 flex-shrink-0 mt-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              <p :class="[
                'font-medium text-sm flex-1',
                toastType === 'success' ? 'text-green-800 dark:text-green-200' :
                toastType === 'warning' ? 'text-amber-800 dark:text-amber-200' :
                'text-blue-800 dark:text-blue-200'
              ]">{{ toastMessage }}</p>
              <button @click="showToast = false" class="text-slate-400 hover:text-slate-600 dark:hover:text-neutral-200">
                <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
              </button>
            </div>
          </div>
        </Transition>

        <Transition name="modal">
          <div v-if="showAnomalyAlert" class="fixed inset-0 z-[90] flex items-center justify-center p-4 bg-black/40 backdrop-blur-sm" @click.self="showAnomalyAlert = false">
            <div class="glass-card rounded-2xl p-6 sm:p-8 w-full max-w-md animate-slide-up">
              <div class="text-center mb-6">
                <div class="w-14 h-14 mx-auto rounded-full bg-amber-100 dark:bg-amber-900/30 flex items-center justify-center mb-4">
                  <svg class="w-7 h-7 text-amber-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" />
                  </svg>
                </div>
                <h3 class="text-xl font-bold text-slate-900 dark:text-white mb-2">{{ t.anomalyAlert }}</h3>
                <p class="text-slate-500 dark:text-neutral-400 text-sm">
                  {{ anomalyType === 'newDevice' ? t.newDeviceDetected : t.unusualLocationDetected }}
                </p>
              </div>
              <div class="flex gap-3">
                <button @click="showAnomalyAlert = false" class="flex-1 btn-secondary py-3">{{ t.dismiss }}</button>
                <button @click="acknowledgeAnomaly" class="flex-1 btn-primary py-3">{{ t.acknowledge }}</button>
              </div>
            </div>
          </div>
        </Transition>

        <Transition name="modal">
          <div v-if="show2FA" class="fixed inset-0 z-[90] flex items-center justify-center p-4 bg-black/40 backdrop-blur-sm" @click.self="show2FA = false">
            <div class="glass-card rounded-2xl p-4 sm:p-6 md:p-8 w-full max-w-md animate-slide-up">
              <div class="text-center mb-4 sm:mb-6">
                <div class="w-12 h-12 sm:w-14 sm:h-14 mx-auto rounded-full bg-brand-100 dark:bg-brand-900/30 flex items-center justify-center mb-3 sm:mb-4">
                  <svg class="w-6 h-6 sm:w-7 sm:h-7 text-brand-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
                  </svg>
                </div>
                <h3 class="text-lg sm:text-xl font-bold text-slate-900 dark:text-white mb-2">{{ t.twoFactorTitle }}</h3>
                <p class="text-slate-500 dark:text-neutral-400 text-sm">{{ t.twoFactorDesc }}</p>
                <p class="text-brand-600 dark:text-brand-400 text-sm font-medium mt-2">
                  {{ t.codeSentTo }} <span class="font-bold">{{ maskedEmail }}</span>
                </p>
              </div>
              <div class="flex justify-center gap-1.5 sm:gap-2 md:gap-3 mb-4 sm:mb-6" dir="ltr">
                <input
                  v-for="(_, idx) in 6"
                  :key="idx"
                  v-model="otpCode[idx]"
                  type="text"
                  maxlength="1"
                  pattern="[0-9]"
                  inputmode="numeric"
                  @input="handleOtpInput(idx, $event)"
                  @keydown="handleOtpKeydown(idx, $event)"
                  ref="otpInputs"
                  class="otp-input"
                  :aria-label="`${t.enterCode} ${idx + 1}`"
                />
              </div>
              <div class="flex gap-2 sm:gap-3">
                <button @click="show2FA = false" class="flex-1 btn-secondary py-2.5 sm:py-3">{{ t.back }}</button>
                <button @click="verify2FA" :disabled="otpCode.join('').length < 6 || isVerifying2FA" class="flex-1 btn-primary py-2.5 sm:py-3">
                  <span v-if="isVerifying2FA" class="flex items-center justify-center gap-2">
                    <svg class="w-4 h-4 sm:w-5 sm:h-5 animate-spin" viewBox="0 0 24 24" fill="none"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path></svg>
                    {{ t.verify }}
                  </span>
                  <span v-else>{{ t.verify }}</span>
                </button>
              </div>
              <div class="mt-3 sm:mt-4 text-center">
                <button @click="resendOtp" :disabled="resendCooldown > 0" class="text-sm text-brand-600 dark:text-brand-400 hover:underline disabled:opacity-50 disabled:cursor-not-allowed transition-colors">
                  {{ resendCooldown > 0 ? `${t.resendCode} (${resendCooldown}s)` : t.resendCode }}
                </button>
              </div>
            </div>
          </div>
        </Transition>

        <Transition name="modal">
          <div v-if="isLockedOut" class="fixed inset-0 z-[95] flex items-center justify-center p-4 bg-black/40 backdrop-blur-sm">
            <div class="glass-card rounded-2xl p-4 sm:p-6 md:p-8 w-full max-w-md animate-slide-up text-center">
              <div class="w-14 h-14 sm:w-16 sm:h-16 mx-auto rounded-full bg-red-100 dark:bg-red-900/30 flex items-center justify-center mb-3 sm:mb-4">
                <svg class="w-7 h-7 sm:w-8 sm:h-8 text-red-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
                </svg>
              </div>
              <h3 class="text-xl sm:text-2xl font-bold text-slate-900 dark:text-white mb-2">{{ t.accountLocked }}</h3>
              <p class="text-slate-500 dark:text-neutral-400 mb-2">{{ t.tooManyAttempts }}</p>
              <p class="text-lg sm:text-xl font-bold text-brand-600 dark:text-brand-400">
                {{ t.tryAgainIn }} {{ lockoutMinutes }} {{ t.minutes }} {{ lockoutSeconds }} {{ t.seconds }}
              </p>
            </div>
          </div>
        </Transition>

        <form @submit.prevent="handleSubmit" class="space-y-4 sm:space-y-5">
          <div class="space-y-2">
            <label for="identifier" class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.phoneOrEmail }}</label>
            <div class="relative">
              <input
                id="identifier"
                v-model="identifier"
                type="text"
                :placeholder="isPhoneInput ? t.phonePlaceholder : t.emailPlaceholder"
                autocomplete="username"
                :aria-label="t.phoneOrEmail"
                class="glass-input w-full pr-20"
                :class="getIdentifierInputClass()"
                @focus="handleIdentifierFocus"
                @blur="handleBlur"
                @input="validateIdentifier"
                @keyup.enter="focusPassword"
              />
              <div class="absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 pointer-events-none">
                <svg v-if="isPhoneInput" class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 18h.01M8 21h8a2 2 0 002-2V5a2 2 0 00-2-2H8a2 2 0 00-2 2v14a2 2 0 002 2z" />
                </svg>
                <svg v-else class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                </svg>
              </div>
              <button
                v-if="identifier"
                type="button"
                @click="clearIdentifier"
                :aria-label="t.clearInput"
                class="absolute right-10 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors p-0.5"
              >
                <svg class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
              </button>
              <button
                type="button"
                @click="toggleInputType"
                :aria-label="isPhoneInput ? t.emailAddress : t.phonePlaceholder"
                class="absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors p-0.5"
              >
                <svg v-if="isPhoneInput" class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                </svg>
                <svg v-else class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 18h.01M8 21h8a2 2 0 002-2V5a2 2 0 00-2-2H8a2 2 0 00-2 2v14a2 2 0 002 2z" />
                </svg>
              </button>
            </div>
            <transition name="strength">
              <div v-if="identifierValidationStatus !== 'idle'" class="mt-1.5 flex items-center gap-1.5 text-xs">
                <svg v-if="identifierValidationStatus === 'valid'" class="w-3.5 h-3.5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                <svg v-else class="w-3.5 h-3.5 text-red-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
                <span :class="identifierValidationStatus === 'valid' ? 'text-green-600 dark:text-green-400' : 'text-red-600 dark:text-red-400'">
                  {{ getIdentifierValidationMessage() }}
                </span>
              </div>
            </transition>
          </div>

          <div class="space-y-2">
            <label for="password" class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.password }}</label>
            <div class="relative">
              <input
                id="password"
                ref="passwordInput"
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                :placeholder="t.passwordPlaceholder"
                autocomplete="current-password"
                :aria-label="t.password"
                class="glass-input w-full pr-20"
                @focus="handlePasswordFocus"
                @blur="handleBlur"
                @input="checkPasswordStrength"
                @keyup.enter="handleSubmit"
              />
              <div class="absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 pointer-events-none">
                <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
                </svg>
              </div>
              <button
                v-if="password"
                type="button"
                @click="clearPassword"
                :aria-label="t.clearInput"
                class="absolute right-10 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors p-0.5"
              >
                <svg class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
              </button>
              <button
                type="button"
                @click="showPassword = !showPassword"
                :aria-label="showPassword ? t.hidePassword : t.showPassword"
                class="absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors p-0.5"
              >
                <svg v-if="showPassword" class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.88 9.88l-3.29-3.29m7.532 7.532l3.29 3.29M3 3l3.59 3.59m0 0A9.953 9.953 0 0112 5c4.478 0 8.268 2.943 9.542 7a10.025 10.025 0 01-4.132 5.411m0 0L21 21" />
                </svg>
                <svg v-else class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                </svg>
              </button>
            </div>

            <transition name="strength">
              <div v-if="password.length > 0" class="mt-2 sm:mt-3">
                <div class="flex items-center gap-2 mb-2">
                  <span class="text-xs font-medium text-slate-500 dark:text-neutral-400">{{ t.passwordStrength }}:</span>
                  <span :class="strengthTextClass" class="text-xs font-bold">{{ strengthLabel }}</span>
                </div>
                <div class="h-1.5 bg-slate-200 dark:bg-neutral-700 rounded-full overflow-hidden">
                  <div :class="strengthBarClass" class="h-full rounded-full transition-all duration-500 ease-out" :style="{ width: `${strengthPercent}%` }"></div>
                </div>
                <div class="mt-2 space-y-1">
                  <div v-for="(req, idx) in passwordRequirements" :key="idx" class="flex items-center gap-1.5">
                    <svg v-if="req.met" class="w-3.5 h-3.5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                    <svg v-else class="w-3.5 h-3.5 text-slate-300 dark:text-neutral-600" fill="none" viewBox="0 0 24 24" stroke="currentColor"><circle cx="12" cy="12" r="10" stroke-width="1.5"/></svg>
                    <span class="text-xs text-slate-500 dark:text-neutral-400">{{ req.text }}</span>
                  </div>
                </div>
              </div>
            </transition>
          </div>

          <div class="space-y-3">
            <div ref="captchaTrackRef" class="captcha-track">
              <div class="captcha-track-fill" :class="{ verified: captchaVerified }" :style="{ width: `${captchaX}%` }"></div>
              <div
                class="captcha-handle"
                :class="{ verified: captchaVerified, dragging: captchaDragging }"
                :style="{ left: captchaX > 0 ? `calc(${captchaX}% - 2.5rem + 2.5rem * ${captchaX / 100})` : '0' }"
                @mousedown="startCaptchaDrag"
                @touchstart="startCaptchaDrag"
              >
                <svg v-if="captchaVerified" class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                <svg v-else class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3"/></svg>
              </div>
              <div class="absolute inset-0 flex items-center justify-center pointer-events-none" :class="{ 'opacity-0': captchaDragging || captchaVerified }">
                <span class="text-xs font-medium text-slate-500 dark:text-neutral-400 select-none">
                  {{ captchaVerified ? t.captchaSuccess : t.slideToVerify }}
                </span>
              </div>
            </div>
            <div v-if="captchaVerified" class="flex justify-end">
              <button type="button" @click="resetCaptcha" class="text-xs text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors flex items-center gap-1">
                <svg class="w-3 h-3" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/></svg>
                {{ t.retry }}
              </button>
            </div>
          </div>

          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <input id="rememberMe" v-model="rememberMe" type="checkbox" class="h-4 w-4 rounded border-slate-300 dark:border-neutral-600 text-brand-600 focus:ring-brand-500 bg-white dark:bg-neutral-800" />
              <label for="rememberMe" class="text-sm text-slate-600 dark:text-neutral-400 flex items-center gap-1">
                {{ t.rememberMe }}
                <span v-if="rememberMe" class="text-[10px] text-brand-600 dark:text-brand-400 font-medium">{{ t.rememberMe7Days }}</span>
              </label>
            </div>
            <button type="button" @click="showForgotPassword = true" :aria-label="t.forgotPassword" class="text-sm text-brand-600 dark:text-brand-400 hover:underline font-medium">
              {{ t.forgotPassword }}
            </button>
          </div>

          <button ref="buttonRef" type="submit" :disabled="isLoading || isSuccess || isLockedOut || !captchaVerified" :aria-label="t.signInButton" class="btn-primary" @mouseenter="handleButtonHover" @mouseleave="handleButtonLeave">
            <span class="relative z-10 flex items-center justify-center gap-2">
              <template v-if="isLoading">
                <svg class="w-4 h-4 sm:w-5 sm:h-5 animate-spin" viewBox="0 0 24 24" fill="none"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path></svg>
                {{ t.signingIn }}
              </template>
              <template v-else-if="isSuccess">
                <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                {{ t.welcome }}
              </template>
              <template v-else>
                {{ t.signInButton }}
                <svg class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3"/></svg>
              </template>
            </span>
          </button>
        </form>

        <div class="mt-6 sm:mt-7">
          <div class="relative">
            <div class="absolute inset-0 flex items-center"><div class="w-full border-t border-slate-200 dark:border-neutral-700"></div></div>
            <div class="relative flex justify-center text-xs"><span class="px-3 bg-transparent text-slate-400 dark:text-neutral-500">{{ t.orContinueWith }}</span></div>
          </div>

          <div class="grid grid-cols-4 gap-2 sm:gap-3 mt-4">
            <button type="button" @click="handleSocialLogin('wechat')" :aria-label="t.loginWithWeChat" class="social-btn">
              <svg class="w-4 h-4 sm:w-5 sm:h-5 text-green-600 mx-auto" viewBox="0 0 24 24" fill="currentColor"><path d="M8.691 2.188C3.891 2.188 0 5.476 0 9.53c0 2.212 1.17 4.203 3.002 5.55a.59.59 0 0 1 .213.665l-.39 1.48c-.019.07-.048.141-.048.213 0 .163.13.295.29.295a.326.326 0 0 0 .167-.054l1.903-1.114a.864.864 0 0 1 .717-.098 10.16 10.16 0 0 0 2.837.403c.276 0 .543-.027.811-.05-.857-2.578.157-4.972 1.932-6.446 1.703-1.415 3.882-1.98 5.853-1.838-.576-3.583-4.196-6.348-8.596-6.348zM5.785 5.991c.642 0 1.162.529 1.162 1.18a1.17 1.17 0 0 1-1.162 1.178A1.17 1.17 0 0 1 4.623 7.17c0-.651.52-1.18 1.162-1.18zm5.813 0c.642 0 1.162.529 1.162 1.18a1.17 1.17 0 0 1-1.162 1.178 1.17 1.17 0 0 1-1.162-1.178c0-.651.52-1.18 1.162-1.18zm5.34 2.867c-1.797-.052-3.746.512-5.28 1.786-1.72 1.428-2.687 3.72-1.78 6.22.942 2.453 3.666 4.229 6.884 4.229.826 0 1.622-.12 2.361-.336a.722.722 0 0 1 .598.082l1.584.926a.272.272 0 0 0 .14.047c.134 0 .24-.111.24-.247 0-.06-.023-.12-.038-.177l-.327-1.233a.582.582 0 0 1-.023-.156.49.49 0 0 1 .201-.398C23.024 18.48 24 16.82 24 14.98c0-3.21-2.931-5.837-6.656-6.088V8.89c-.135-.01-.27-.02-.407-.032zm-2.53 3.274c.535 0 .969.44.969.982a.976.976 0 0 1-.969.983.976.976 0 0 1-.969-.983c0-.542.434-.982.97-.982zm4.844 0c.535 0 .969.44.969.982a.976.976 0 0 1-.969.983.976.976 0 0 1-.969-.983c0-.542.434-.982.969-.982z"/></svg>
            </button>
            <button type="button" @click="handleSocialLogin('qq')" :aria-label="t.loginWithQQ" class="social-btn">
              <svg class="w-4 h-4 sm:w-5 sm:h-5 text-sky-500 mx-auto" viewBox="0 0 24 24" fill="currentColor">
                <path d="M12.004 2c-2.266 0-6.291 1.364-6.291 7.324v1.195S3.551 14.96 3.551 17.474c0 .665.17 1.025.28 1.025.114 0 .902-.457.902-.457s-.057.72-.057 1.195c0 .456.398.82.882.82.48 0 2.892-.27 4.182-.27 1.29 0 3.703.27 4.183.27.48 0 .88-.364.88-.82 0-.475-.058-1.195-.058-1.195s.788.457.902.457c.11 0 .28-.36.28-1.025 0-2.514-2.16-6.954-2.16-6.954V9.324C18.295 3.364 14.27 2 12.004 2z"/>
                <circle cx="9.41" cy="13.304" r="1.356"/>
                <circle cx="14.6" cy="13.304" r="1.356"/>
                <ellipse cx="12" cy="17.164" rx="2.962" ry="0.995"/>
              </svg>
            </button>
            <button type="button" @click="handleSocialLogin('google')" :aria-label="t.loginWithGoogle" class="social-btn">
              <svg class="w-4 h-4 sm:w-5 sm:h-5 mx-auto" viewBox="0 0 24 24"><path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/><path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/><path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/><path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/></svg>
            </button>
            <button type="button" @click="handleSocialLogin('github')" :aria-label="t.loginWithGitHub" class="social-btn">
              <svg class="w-4 h-4 sm:w-5 sm:h-5 text-slate-800 dark:text-neutral-200 mx-auto" fill="currentColor" viewBox="0 0 24 24"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z"/></svg>
            </button>
          </div>

          <button type="button" @click="handlePasskeyLogin" :aria-label="t.signInWithPasskey" class="w-full mt-4 btn-secondary py-3">
            <span class="flex items-center justify-center gap-2">
              <svg class="w-4 h-4 sm:w-5 sm:h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/><path d="m9 12 2 2 4-4"/></svg>
              {{ t.signInWithPasskey }}
            </span>
          </button>
        </div>

        <div class="mt-6 sm:mt-7 space-y-2">
          <p class="text-center text-slate-500 dark:text-neutral-400 text-sm">
            {{ t.noAccount }}
            <button type="button" @click="showCreateAccount = true" class="text-brand-600 dark:text-brand-400 font-semibold hover:underline ml-1">{{ t.createAccount }}</button>
          </p>
          <p class="text-center text-slate-400 dark:text-neutral-500 text-xs">
            {{ t.agreeToTerms }}
            <button type="button" @click="openPrivacyPolicy" class="text-slate-500 dark:text-neutral-400 font-medium hover:underline">{{ t.privacyPolicy }}</button>
            {{ t.and }}
            <button type="button" @click="openTermsOfService" class="text-slate-500 dark:text-neutral-400 font-medium hover:underline">{{ t.termsOfService }}</button>
          </p>
        </div>
      </div>
    </div>

    <Transition name="modal">
      <div v-if="showForgotPassword" class="fixed inset-0 bg-black/40 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="showForgotPassword = false">
        <div class="glass-card rounded-2xl p-4 sm:p-5 md:p-8 w-full max-w-md animate-slide-up" role="dialog" aria-modal="true">
          <div class="flex justify-between items-center mb-4 sm:mb-5">
            <h2 class="text-base sm:text-lg md:text-xl font-bold text-slate-900 dark:text-white">{{ t.forgotPasswordTitle }}</h2>
            <button @click="showForgotPassword = false" :aria-label="t.close" class="text-slate-400 hover:text-slate-600 dark:hover:text-neutral-200 w-6 h-6 sm:w-7 sm:h-7 flex items-center justify-center rounded-lg hover:bg-slate-100 dark:hover:bg-neutral-800 transition-colors">
              <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
            </button>
          </div>
          <p class="text-slate-500 dark:text-neutral-400 mb-4 sm:mb-5 text-sm">{{ t.forgotPasswordDesc }}</p>
          <div class="space-y-3 sm:space-y-4">
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.emailAddress }}</label>
              <input v-model="resetEmail" type="email" :placeholder="t.emailPlaceholder" :aria-label="t.emailAddress" class="glass-input mt-1" />
            </div>
            <button @click="handleResetPassword" class="btn-primary">{{ t.sendResetLink }}</button>
          </div>
        </div>
      </div>
    </Transition>

    <PasskeyModal ref="passkeyModalRef" :show="showPasskeyModal" :language="language" @close="showPasskeyModal = false" @success="handlePasskeySuccess" @error="handlePasskeyError" />

    <Transition name="modal">
      <div v-if="showSocialLogin" class="fixed inset-0 bg-black/40 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="cancelSocialLogin">
        <div class="glass-card rounded-2xl p-4 sm:p-5 md:p-8 w-full max-w-sm animate-slide-up" role="dialog" aria-modal="true">
          <div class="text-center mb-4 sm:mb-6">
            <div class="w-12 h-12 sm:w-14 sm:h-14 mx-auto rounded-full flex items-center justify-center mb-3 sm:mb-4" :class="socialProviderConfig.bgClass">
              <div v-html="socialProviderConfig.icon"></div>
            </div>
            <h2 class="text-base sm:text-lg font-bold text-slate-900 dark:text-white mb-1">{{ socialProviderConfig.name }}</h2>
            <p class="text-slate-500 dark:text-neutral-400 text-sm">{{ t.redirectingTo }} {{ socialProviderConfig.name }}...</p>
          </div>
          <div v-if="socialLoginState === 'loading'" class="space-y-3 sm:space-y-4">
            <div class="flex items-center justify-center py-3 sm:py-4">
              <svg class="w-6 h-6 sm:w-8 sm:h-8 animate-spin text-brand-600" viewBox="0 0 24 24" fill="none"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path></svg>
            </div>
            <div class="space-y-2">
              <div class="h-2 bg-slate-200 dark:bg-neutral-700 rounded-full overflow-hidden">
                <div class="h-full bg-brand-500 rounded-full transition-all duration-300" :style="{ width: `${socialLoginProgress}%` }"></div>
              </div>
              <p class="text-xs text-center text-slate-400 dark:text-neutral-500">{{ t.connectingTo }} {{ socialProviderConfig.name }}...</p>
            </div>
          </div>
          <div v-else-if="socialLoginState === 'success'" class="text-center py-3 sm:py-4">
            <svg class="w-10 h-10 sm:w-12 sm:h-12 text-green-500 mx-auto mb-3" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
            <p class="text-slate-700 dark:text-neutral-200 font-medium">{{ t.socialLoginSuccess }}</p>
          </div>
          <div v-else-if="socialLoginState === 'error'" class="text-center py-3 sm:py-4">
            <svg class="w-10 h-10 sm:w-12 sm:h-12 text-red-500 mx-auto mb-3" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"/></svg>
            <p class="text-slate-700 dark:text-neutral-200 font-medium mb-3 sm:mb-4">{{ t.socialLoginFailed }}</p>
            <button @click="cancelSocialLogin" class="btn-secondary py-2 sm:py-2.5 text-sm">{{ t.tryAgain }}</button>
          </div>
          <div v-if="socialLoginState === 'loading'" class="mt-3 sm:mt-4 text-center">
            <button @click="cancelSocialLogin" class="text-sm text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors">{{ t.cancel }}</button>
          </div>
        </div>
      </div>
    </Transition>

    <Transition name="modal">
      <div v-if="showPrivacyPolicyModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="showPrivacyPolicyModal = false">
        <div class="glass-card rounded-2xl p-4 sm:p-5 md:p-8 w-full max-w-lg max-h-[85vh] flex flex-col animate-slide-up" role="dialog" aria-modal="true">
          <div class="flex justify-between items-center mb-4 sm:mb-5 flex-shrink-0">
            <h2 class="text-base sm:text-lg md:text-xl font-bold text-slate-900 dark:text-white">{{ t.privacyPolicy }}</h2>
            <button @click="showPrivacyPolicyModal = false" :aria-label="t.close" class="text-slate-400 hover:text-slate-600 dark:hover:text-neutral-200 w-6 h-6 sm:w-7 sm:h-7 flex items-center justify-center rounded-lg hover:bg-slate-100 dark:hover:bg-neutral-800 transition-colors">
              <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
            </button>
          </div>
          <div class="overflow-y-auto flex-1 text-sm text-slate-600 dark:text-neutral-300 space-y-3 sm:space-y-4 pr-1">
            <p class="font-semibold text-slate-800 dark:text-white">{{ t.privacyPolicyEffectiveDate }}</p>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">1. {{ t.privacyDataCollection }}</h3>
              <p>{{ t.privacyDataCollectionDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">2. {{ t.privacyDataUsage }}</h3>
              <p>{{ t.privacyDataUsageDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">3. {{ t.privacyDataProtection }}</h3>
              <p>{{ t.privacyDataProtectionDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">4. {{ t.privacyCookies }}</h3>
              <p>{{ t.privacyCookiesDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">5. {{ t.privacyThirdParty }}</h3>
              <p>{{ t.privacyThirdPartyDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">6. {{ t.privacyRights }}</h3>
              <p>{{ t.privacyRightsDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">7. {{ t.privacyContact }}</h3>
              <p>{{ t.privacyContactDesc }}</p>
            </div>
          </div>
          <div class="mt-4 sm:mt-5 flex-shrink-0">
            <button @click="showPrivacyPolicyModal = false" class="btn-primary">{{ t.agree }}</button>
          </div>
        </div>
      </div>
    </Transition>

    <Transition name="modal">
      <div v-if="showTermsModal" class="fixed inset-0 bg-black/40 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="showTermsModal = false">
        <div class="glass-card rounded-2xl p-4 sm:p-5 md:p-8 w-full max-w-lg max-h-[85vh] flex flex-col animate-slide-up" role="dialog" aria-modal="true">
          <div class="flex justify-between items-center mb-4 sm:mb-5 flex-shrink-0">
            <h2 class="text-base sm:text-lg md:text-xl font-bold text-slate-900 dark:text-white">{{ t.termsOfService }}</h2>
            <button @click="showTermsModal = false" :aria-label="t.close" class="text-slate-400 hover:text-slate-600 dark:hover:text-neutral-200 w-6 h-6 sm:w-7 sm:h-7 flex items-center justify-center rounded-lg hover:bg-slate-100 dark:hover:bg-neutral-800 transition-colors">
              <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
            </button>
          </div>
          <div class="overflow-y-auto flex-1 text-sm text-slate-600 dark:text-neutral-300 space-y-3 sm:space-y-4 pr-1">
            <p class="font-semibold text-slate-800 dark:text-white">{{ t.termsEffectiveDate }}</p>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">1. {{ t.termsAcceptance }}</h3>
              <p>{{ t.termsAcceptanceDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">2. {{ t.termsAccount }}</h3>
              <p>{{ t.termsAccountDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">3. {{ t.termsProhibited }}</h3>
              <p>{{ t.termsProhibitedDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">4. {{ t.termsLiability }}</h3>
              <p>{{ t.termsLiabilityDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">5. {{ t.termsTermination }}</h3>
              <p>{{ t.termsTerminationDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">6. {{ t.termsChanges }}</h3>
              <p>{{ t.termsChangesDesc }}</p>
            </div>
            <div>
              <h3 class="font-semibold text-slate-800 dark:text-white mb-1">7. {{ t.termsGoverning }}</h3>
              <p>{{ t.termsGoverningDesc }}</p>
            </div>
          </div>
          <div class="mt-4 sm:mt-5 flex-shrink-0">
            <button @click="showTermsModal = false" class="btn-primary">{{ t.agree }}</button>
          </div>
        </div>
      </div>
    </Transition>

    <Transition name="modal">
      <div v-if="showCreateAccount" class="fixed inset-0 bg-black/40 backdrop-blur-sm flex items-center justify-center z-50 p-4" @click.self="showCreateAccount = false">
        <div class="glass-card rounded-2xl p-4 sm:p-5 md:p-8 w-full max-w-md max-h-[90vh] overflow-y-auto animate-slide-up" role="dialog" aria-modal="true">
          <div class="flex justify-between items-center mb-4 sm:mb-5">
            <h2 class="text-base sm:text-lg md:text-xl font-bold text-slate-900 dark:text-white">{{ t.createAccountTitle }}</h2>
            <button @click="showCreateAccount = false" :aria-label="t.close" class="text-slate-400 hover:text-slate-600 dark:hover:text-neutral-200 w-6 h-6 sm:w-7 sm:h-7 flex items-center justify-center rounded-lg hover:bg-slate-100 dark:hover:bg-neutral-800 transition-colors">
              <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
            </button>
          </div>
          <div class="space-y-3 sm:space-y-4">
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.fullName }}</label>
              <input v-model="newAccount.name" type="text" :placeholder="t.fullNamePlaceholder" :aria-label="t.fullName" class="glass-input mt-1" />
            </div>
            <div>
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.emailAddress }}</label>
              <input v-model="newAccount.email" type="email" :placeholder="t.emailPlaceholder" :aria-label="t.emailAddress" class="glass-input mt-1" />
            </div>
            <div class="relative">
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.password }}</label>
              <input v-model="newAccount.password" :type="showNewPassword ? 'text' : 'password'" :placeholder="t.passwordPlaceholder" :aria-label="t.password" class="glass-input mt-1 pr-10" />
              <button type="button" @click="showNewPassword = !showNewPassword" :aria-label="showNewPassword ? t.hidePassword : t.showPassword" class="absolute right-3 top-[calc(50%+10px)] -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors p-0.5">
                <svg v-if="showNewPassword" class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.88 9.88l-3.29-3.29m7.532 7.532l3.29 3.29M3 3l3.59 3.59m0 0A9.953 9.953 0 0112 5c4.478 0 8.268 2.943 9.542 7a10.025 10.025 0 01-4.132 5.411m0 0L21 21"/></svg>
                <svg v-else class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/></svg>
              </button>
            </div>
            <div class="relative">
              <label class="text-sm font-medium text-slate-700 dark:text-neutral-300 ml-1">{{ t.confirmPassword }}</label>
              <input v-model="newAccount.confirmPassword" :type="showConfirmPassword ? 'text' : 'password'" :placeholder="t.passwordPlaceholder" :aria-label="t.confirmPassword" class="glass-input mt-1 pr-10" :class="passwordMatchStatus === 'valid' ? 'glass-input-valid' : passwordMatchStatus === 'invalid' ? 'glass-input-invalid' : ''" />
              <button type="button" @click="showConfirmPassword = !showConfirmPassword" :aria-label="showConfirmPassword ? t.hidePassword : t.showPassword" class="absolute right-3 top-[calc(50%+10px)] -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-neutral-300 transition-colors p-0.5">
                <svg v-if="showConfirmPassword" class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.88 9.88l-3.29-3.29m7.532 7.532l3.29 3.29M3 3l3.59 3.59m0 0A9.953 9.953 0 0112 5c4.478 0 8.268 2.943 9.542 7a10.025 10.025 0 01-4.132 5.411m0 0L21 21"/></svg>
                <svg v-else class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/></svg>
              </button>
            </div>
            <transition name="strength">
              <div v-if="passwordMatchStatus !== 'idle'" class="mt-2 flex items-center gap-1.5 text-xs">
                <svg v-if="passwordMatchStatus === 'valid'" class="w-3.5 h-3.5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                <svg v-else class="w-3.5 h-3.5 text-red-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
                <span :class="passwordMatchStatus === 'valid' ? 'text-green-600 dark:text-green-400' : 'text-red-600 dark:text-red-400'">
                  {{ passwordMatchStatus === 'valid' ? (t.passwordsMatch || 'Passwords match!') : (t.passwordsDoNotMatch || 'Passwords do not match!') }}
                </span>
              </div>
            </transition>
            <div class="flex items-start gap-3 mt-2">
              <input id="createPasskey" v-model="createPasskey" type="checkbox" class="h-4 w-4 mt-0.5 rounded border-slate-300 dark:border-neutral-600 text-brand-600 focus:ring-brand-500 bg-white dark:bg-neutral-800" />
              <div>
                <label for="createPasskey" class="text-sm font-medium text-slate-700 dark:text-neutral-300">{{ t.createPasskey }}</label>
                <p class="text-slate-500 dark:text-neutral-400 mt-0.5 text-xs">{{ t.passkeyDescription }}</p>
              </div>
            </div>
            <button @click="handleCreateAccount" class="btn-primary mt-2">{{ t.createAccountButton }}</button>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch, onUnmounted, nextTick } from 'vue'
import gsap from 'gsap'
import AbstractInteractiveHero from './AbstractInteractiveHero.vue'
import PasskeyModal from './PasskeyModal.vue'
import { translations } from '../utils/i18n.js'

const props = defineProps({
  language: { type: String, default: 'en' },
})

const emit = defineEmits(['login'])

const identifier = ref('')
const password = ref('')
const showPassword = ref(false)
const focusState = ref('none')
const loginStatus = ref('idle')
const errorMessage = ref('')
const isLoading = ref(false)
const isSuccess = ref(false)
const isError = ref(false)
const isButtonHovered = ref(false)
const rememberMe = ref(false)
const strengthPercent = ref(0)
const strengthLabel = ref('')
const isPhoneInput = ref(false)
const identifierValidationStatus = ref('idle')
const isDarkMode = ref(false)
const showForgotPassword = ref(false)
const showCreateAccount = ref(false)
const showPasskeyModal = ref(false)
const resetEmail = ref('')
const showNewPassword = ref(false)
const showConfirmPassword = ref(false)
const isPasskeyLoading = ref(false)
const createPasskey = ref(false)
const newAccount = ref({ name: '', email: '', password: '', confirmPassword: '' })
const captchaX = ref(0)
const captchaVerified = ref(false)
const captchaDragging = ref(false)
const showToast = ref(false)
const toastMessage = ref('')
const toastType = ref('info')
const show2FA = ref(false)
const otpCode = ref(['', '', '', '', '', ''])
const otpInputs = ref([])
const isVerifying2FA = ref(false)
const resendCooldown = ref(0)
const showAnomalyAlert = ref(false)
const anomalyType = ref('newDevice')
const isLockedOut = ref(false)
const loginAttempts = ref(0)
const lockoutTimer = ref(null)
const lockoutMinutes = ref(5)
const lockoutSeconds = ref(0)
const heroRef = ref(null)
const buttonRef = ref(null)
const loginPageRef = ref(null)
const passkeyModalRef = ref(null)
const passwordInput = ref(null)
const captchaTrackRef = ref(null)
const showSocialLogin = ref(false)
const socialLoginState = ref('loading')
const socialLoginProgress = ref(0)
const currentSocialProvider = ref('')
const socialLoginTimer = ref(null)
const showPrivacyPolicyModal = ref(false)
const showTermsModal = ref(false)

const t = computed(() => translations[props.language] || translations['en'])

const maskedEmail = computed(() => {
  const email = isPhoneInput.value ? 'user@example.com' : identifier.value
  const [local, domain] = email.split('@')
  if (!domain) return email
  return `${local.slice(0, 2)}***@${domain}`
})

const canSubmit = computed(() => {
  return identifier.value && password.value && identifierValidationStatus.value === 'valid' && captchaVerified.value && !isLockedOut.value
})

const passwordRequirements = computed(() => [
  { met: password.value.length >= 8, text: t.value.passwordMinLength },
  { met: /[A-Z]/.test(password.value), text: t.value.passwordRequiresUppercase },
  { met: /[a-z]/.test(password.value), text: t.value.passwordRequiresLowercase },
  { met: /[0-9]/.test(password.value), text: t.value.passwordRequiresNumber },
  { met: /[^A-Za-z0-9]/.test(password.value), text: t.value.passwordRequiresSpecial },
])

const strengthTextClass = computed(() => {
  if (strengthPercent.value < 20) return 'text-red-500'
  if (strengthPercent.value < 40) return 'text-orange-500'
  if (strengthPercent.value < 60) return 'text-yellow-500'
  if (strengthPercent.value < 80) return 'text-lime-500'
  return 'text-green-500'
})

const strengthBarClass = computed(() => {
  if (strengthPercent.value < 20) return 'bg-red-500'
  if (strengthPercent.value < 40) return 'bg-orange-500'
  if (strengthPercent.value < 60) return 'bg-yellow-500'
  if (strengthPercent.value < 80) return 'bg-lime-500'
  return 'bg-green-500'
})

const passwordsMatch = computed(() => {
  return newAccount.value.password && newAccount.value.confirmPassword && 
         newAccount.value.password === newAccount.value.confirmPassword
})

const passwordMatchStatus = computed(() => {
  if (!newAccount.value.password || !newAccount.value.confirmPassword) return 'idle'
  return passwordsMatch.value ? 'valid' : 'invalid'
})

const socialProviderConfig = computed(() => {
  const configs = {
    wechat: { name: 'WeChat', bgClass: 'bg-green-100 dark:bg-green-900/30', icon: '<svg class="w-7 h-7 text-green-600" viewBox="0 0 24 24" fill="currentColor"><path d="M8.691 2.188C3.891 2.188 0 5.476 0 9.53c0 2.212 1.17 4.203 3.002 5.55a.59.59 0 0 1 .213.665l-.39 1.48c-.019.07-.048.141-.048.213 0 .163.13.295.29.295a.326.326 0 0 0 .167-.054l1.903-1.114a.864.864 0 0 1 .717-.098 10.16 10.16 0 0 0 2.837.403c.276 0 .543-.027.811-.05-.857-2.578.157-4.972 1.932-6.446 1.703-1.415 3.882-1.98 5.853-1.838-.576-3.583-4.196-6.348-8.596-6.348zM5.785 5.991c.642 0 1.162.529 1.162 1.18a1.17 1.17 0 0 1-1.162 1.178A1.17 1.17 0 0 1 4.623 7.17c0-.651.52-1.18 1.162-1.18zm5.813 0c.642 0 1.162.529 1.162 1.18a1.17 1.17 0 0 1-1.162 1.178 1.17 1.17 0 0 1-1.162-1.178c0-.651.52-1.18 1.162-1.18zm5.34 2.867c-1.797-.052-3.746.512-5.28 1.786-1.72 1.428-2.687 3.72-1.78 6.22.942 2.453 3.666 4.229 6.884 4.229.826 0 1.622-.12 2.361-.336a.722.722 0 0 1 .598.082l1.584.926a.272.272 0 0 0 .14.047c.134 0 .24-.111.24-.247 0-.06-.023-.12-.038-.177l-.327-1.233a.582.582 0 0 1-.023-.156.49.49 0 0 1 .201-.398C23.024 18.48 24 16.82 24 14.98c0-3.21-2.931-5.837-6.656-6.088V8.89c-.135-.01-.27-.02-.407-.032zm-2.53 3.274c.535 0 .969.44.969.982a.976.976 0 0 1-.969.983.976.976 0 0 1-.969-.983c0-.542.434-.982.97-.982zm4.844 0c.535 0 .969.44.969.982a.976.976 0 0 1-.969.983.976.976 0 0 1-.969-.983c0-.542.434-.982.969-.982z"/></svg>' },
    qq: { name: 'QQ', bgClass: 'bg-blue-100 dark:bg-blue-900/30', icon: '<svg class="w-7 h-7 text-blue-500" viewBox="0 0 24 24" fill="currentColor"><path d="M12.003 2c-2.265 0-6.29 1.364-6.29 7.325v1.195S3.55 14.96 3.55 17.474c0 .665.17 1.025.28 1.025.114 0 .902-.457.902-.457s-.057.72-.057 1.195c0 .456.398.82.882.82.48 0 2.892-.27 4.182-.27 1.29 0 3.703.27 4.183.27.48 0 .88-.364.88-.82 0-.475-.058-1.195-.058-1.195s.788.457.902.457c.11 0 .28-.36.28-1.025 0-2.514-2.16-6.954-2.16-6.954V9.325C18.293 3.364 14.268 2 12.003 2zM9.41 11.95c.742 0 1.346.608 1.346 1.356 0 .75-.604 1.357-1.346 1.357-.743 0-1.347-.607-1.347-1.357 0-.748.604-1.356 1.347-1.356zm5.19 0c.742 0 1.346.608 1.346 1.356 0 .75-.604 1.357-1.347 1.357-.743 0-1.347-.607-1.347-1.357 0-.748.604-1.356 1.347-1.356zm-2.6 3.86c1.634 0 2.962.445 2.962.995 0 .548-1.328.992-2.962.992-1.632 0-2.96-.444-2.96-.992 0-.55.676-.995 1.327-.995.083-.007.178-.007.27-.007.09 0 .174 0 .26.007h1.102z"/></svg>' },
    google: { name: 'Google', bgClass: 'bg-red-100 dark:bg-red-900/30', icon: '<svg class="w-7 h-7" viewBox="0 0 24 24"><path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/><path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/><path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/><path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/></svg>' },
    github: { name: 'GitHub', bgClass: 'bg-slate-100 dark:bg-slate-800', icon: '<svg class="w-7 h-7 text-slate-800 dark:text-neutral-200" fill="currentColor" viewBox="0 0 24 24"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z"/></svg>' },
  }
  return configs[currentSocialProvider.value] || configs.google
})

function toggleDarkMode() {
  isDarkMode.value = !isDarkMode.value
  document.documentElement.classList.toggle('dark', isDarkMode.value)
  localStorage.setItem('theme', isDarkMode.value ? 'dark' : 'light')
}

function toggleInputType() {
  isPhoneInput.value = !isPhoneInput.value
  identifier.value = ''
  identifierValidationStatus.value = 'idle'
}

function validateIdentifier() {
  if (!identifier.value) { identifierValidationStatus.value = 'idle'; return }
  if (isPhoneInput.value) {
    identifierValidationStatus.value = /^1[3-9]\d{9}$/.test(identifier.value) ? 'valid' : 'invalid'
  } else {
    identifierValidationStatus.value = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(identifier.value) ? 'valid' : 'invalid'
  }
}

function getIdentifierInputClass() {
  if (identifierValidationStatus.value === 'valid') return 'glass-input-valid'
  if (identifierValidationStatus.value === 'invalid') return 'glass-input-invalid'
  return ''
}

function getIdentifierValidationMessage() {
  return identifierValidationStatus.value === 'valid'
    ? (isPhoneInput.value ? t.value.validPhone : t.value.validEmail)
    : (isPhoneInput.value ? t.value.invalidPhone : t.value.invalidEmail)
}

function clearIdentifier() { identifier.value = ''; identifierValidationStatus.value = 'idle' }
function clearPassword() { password.value = ''; strengthPercent.value = 0; strengthLabel.value = '' }

function checkPasswordStrength() {
  let score = 0
  const pwd = password.value
  if (pwd.length >= 8) score += 20
  if (pwd.length >= 12) score += 10
  if (/[A-Z]/.test(pwd)) score += 15
  if (/[a-z]/.test(pwd)) score += 15
  if (/[0-9]/.test(pwd)) score += 20
  if (/[^A-Za-z0-9]/.test(pwd)) score += 20
  strengthPercent.value = Math.min(100, score)
  if (strengthPercent.value < 20) strengthLabel.value = t.value.passwordStrengthVeryWeak
  else if (strengthPercent.value < 40) strengthLabel.value = t.value.passwordWeak
  else if (strengthPercent.value < 60) strengthLabel.value = t.value.passwordFair
  else if (strengthPercent.value < 80) strengthLabel.value = t.value.passwordGood
  else strengthLabel.value = t.value.passwordStrong
}

function startCaptchaDrag() {
  if (captchaVerified.value) return
  captchaDragging.value = true
  document.addEventListener('mousemove', handleCaptchaDrag)
  document.addEventListener('mouseup', stopCaptchaDrag)
  document.addEventListener('touchmove', handleCaptchaDrag, { passive: false })
  document.addEventListener('touchend', stopCaptchaDrag)
}

function handleCaptchaDrag(e) {
  if (!captchaDragging.value) return
  e.preventDefault?.()
  const clientX = e.touches ? e.touches[0].clientX : e.clientX
  const track = captchaTrackRef.value
  if (!track) return
  const rect = track.getBoundingClientRect()
  let x = ((clientX - rect.left) / rect.width) * 100
  captchaX.value = Math.max(0, Math.min(100, x))
}

function stopCaptchaDrag() {
  if (!captchaDragging.value) return
  captchaDragging.value = false
  document.removeEventListener('mousemove', handleCaptchaDrag)
  document.removeEventListener('mouseup', stopCaptchaDrag)
  document.removeEventListener('touchmove', handleCaptchaDrag)
  document.removeEventListener('touchend', stopCaptchaDrag)
  if (captchaX.value >= 90) {
    captchaX.value = 100
    captchaVerified.value = true
    showToastMessage(t.value.captchaSuccess, 'success')
  } else {
    captchaX.value = 0
  }
}

let blurTimeout = null
function handleIdentifierFocus() { if (blurTimeout) { clearTimeout(blurTimeout); blurTimeout = null }; focusState.value = 'email'; errorMessage.value = '' }
function handlePasswordFocus() { if (blurTimeout) { clearTimeout(blurTimeout); blurTimeout = null }; focusState.value = 'password'; errorMessage.value = '' }
function handleBlur() { blurTimeout = setTimeout(() => { focusState.value = 'none' }, 150) }
function handleButtonHover() { isButtonHovered.value = true }
function handleButtonLeave() { isButtonHovered.value = false }
function focusPassword() { passwordInput.value?.focus() }

function validateForm() {
  if (!identifier.value || !password.value) { errorMessage.value = t.value.pleaseEnterEmailPassword; triggerError(); return false }
  if (identifierValidationStatus.value !== 'valid') { errorMessage.value = isPhoneInput.value ? t.value.invalidPhone : t.value.pleaseEnterValidEmail; triggerError(); return false }
  if (password.value.length < 8) { errorMessage.value = t.value.passwordTooShort; triggerError(); return false }
  if (!captchaVerified.value) { errorMessage.value = t.value.slideToVerify; triggerError(); return false }
  return true
}

function triggerError() {
  isError.value = true
  loginStatus.value = 'error'
  setTimeout(() => { isError.value = false; loginStatus.value = 'idle' }, 2000)
}

function showToastMessage(message, type = 'info') {
  toastMessage.value = message
  toastType.value = type
  showToast.value = true
  setTimeout(() => { showToast.value = false }, 3000)
}

async function handleSubmit() {
  if (!validateForm()) return
  loginAttempts.value++
  if (loginAttempts.value >= 5) { startLockout(); return }
  isLoading.value = true
  errorMessage.value = ''
  loginStatus.value = 'idle'
  await new Promise(resolve => setTimeout(resolve, 2000))
  isLoading.value = false
  if (Math.random() > 0.7) { showAnomalyAlert.value = true; anomalyType.value = Math.random() > 0.5 ? 'newDevice' : 'unusualLocation'; return }
  show2FA.value = true
  startResendCooldown()
  nextTick(() => { otpInputs.value[0]?.focus() })
}

function acknowledgeAnomaly() {
  showAnomalyAlert.value = false
  show2FA.value = true
  startResendCooldown()
  nextTick(() => { otpInputs.value[0]?.focus() })
}

function startResendCooldown() {
  resendCooldown.value = 60
  const timer = setInterval(() => { resendCooldown.value--; if (resendCooldown.value <= 0) clearInterval(timer) }, 1000)
}

function resendOtp() { if (resendCooldown.value > 0) return; startResendCooldown(); showToastMessage(t.value.resendCode, 'info') }

function handleOtpInput(idx, e) {
  const value = e.target.value
  if (/^\d$/.test(value)) { otpCode.value[idx] = value; if (idx < 5) nextTick(() => otpInputs.value[idx + 1]?.focus()) }
  else { otpCode.value[idx] = '' }
}

function handleOtpKeydown(idx, e) {
  if (e.key === 'Backspace' && !otpCode.value[idx] && idx > 0) nextTick(() => otpInputs.value[idx - 1]?.focus())
}

async function verify2FA() {
  if (otpCode.value.join('').length < 6) return
  isVerifying2FA.value = true
  await new Promise(resolve => setTimeout(resolve, 1500))
  isVerifying2FA.value = false
  show2FA.value = false
  isSuccess.value = true
  loginStatus.value = 'success'
  loginAttempts.value = 0
  setTimeout(() => { emit('login', { identifier: identifier.value, password: password.value, rememberMe: rememberMe.value }) }, 800)
}

function startLockout() {
  isLockedOut.value = true
  lockoutMinutes.value = 5
  lockoutSeconds.value = 0
  lockoutTimer.value = setInterval(() => {
    lockoutSeconds.value--
    if (lockoutSeconds.value < 0) { lockoutSeconds.value = 59; lockoutMinutes.value-- }
    if (lockoutMinutes.value <= 0 && lockoutSeconds.value <= 0) { clearInterval(lockoutTimer.value); isLockedOut.value = false; loginAttempts.value = 0 }
  }, 1000)
}

function handleResetPassword() {
  if (!resetEmail.value) { showToastMessage(t.value.pleaseEnterValidEmail, 'warning'); return }
  showToastMessage(t.value.resetLinkSent, 'success')
  showForgotPassword.value = false
  resetEmail.value = ''
}

async function handleCreateAccount() {
  if (!newAccount.value.name || !newAccount.value.email || !newAccount.value.password || !newAccount.value.confirmPassword) { showToastMessage(t.value.pleaseFillAllFields, 'warning'); return }
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(newAccount.value.email)) { showToastMessage(t.value.pleaseEnterValidEmail, 'warning'); return }
  if (newAccount.value.password.length < 8) { showToastMessage(t.value.passwordTooShort, 'warning'); return }
  if (newAccount.value.password !== newAccount.value.confirmPassword) { showToastMessage(t.value.passwordsDoNotMatch, 'warning'); return }
  if (createPasskey.value) {
    if (!window.PublicKeyCredential) { showToastMessage(t.value.passkeyNotSupported, 'warning'); return }
    try {
      isPasskeyLoading.value = true
      const credential = await navigator.credentials.create({
        publicKey: {
          challenge: new Uint8Array(32),
          rp: { name: 'MascotLogin', id: window.location.hostname },
          user: { id: new TextEncoder().encode(newAccount.value.email), name: newAccount.value.email, displayName: newAccount.value.name },
          pubKeyCredParams: [{ alg: -7, type: 'public-key' }, { alg: -257, type: 'public-key' }],
          timeout: 60000,
          authenticatorSelection: { userVerification: 'preferred', requireResidentKey: false }
        }
      })
      if (credential) console.log('Passkey created:', credential)
    } catch (error) {
      console.error('Passkey creation failed:', error)
      showToastMessage(t.value.passkeyCreationFailed, 'warning')
      isPasskeyLoading.value = false
      return
    } finally { isPasskeyLoading.value = false }
  }
  showToastMessage(t.value.accountCreated, 'success')
  showCreateAccount.value = false
  newAccount.value = { name: '', email: '', password: '', confirmPassword: '' }
  createPasskey.value = false
}

function handleSocialLogin(provider) {
  currentSocialProvider.value = provider
  socialLoginState.value = 'loading'
  socialLoginProgress.value = 0
  showSocialLogin.value = true
  let progress = 0
  socialLoginTimer.value = setInterval(() => {
    progress += Math.random() * 15 + 5
    if (progress >= 100) {
      progress = 100
      clearInterval(socialLoginTimer.value)
      setTimeout(() => {
        if (Math.random() > 0.2) {
          socialLoginState.value = 'success'
          setTimeout(() => {
            showSocialLogin.value = false
            isSuccess.value = true
            loginStatus.value = 'success'
            setTimeout(() => { emit('login', { method: provider }) }, 800)
          }, 1200)
        } else {
          socialLoginState.value = 'error'
        }
      }, 500)
    }
    socialLoginProgress.value = Math.min(100, progress)
  }, 300)
}

function cancelSocialLogin() {
  showSocialLogin.value = false
  if (socialLoginTimer.value) clearInterval(socialLoginTimer.value)
  socialLoginState.value = 'loading'
  socialLoginProgress.value = 0
}

function resetCaptcha() {
  captchaVerified.value = false
  captchaX.value = 0
}

function handlePasskeyLogin() {
  if (!window.PublicKeyCredential) { showToastMessage(t.value.passkeyNotSupported, 'warning'); return }
  showPasskeyModal.value = true
}

function handlePasskeySuccess(credentialData) {
  isSuccess.value = true
  loginStatus.value = 'success'
  setTimeout(() => { emit('login', { method: 'passkey', credential: credentialData }) }, 800)
}

function handlePasskeyError(error) {
  console.error('Passkey error:', error)
  errorMessage.value = t.value.passkeyAuthFailed
  triggerError()
}

function openPrivacyPolicy() { showPrivacyPolicyModal.value = true }
function openTermsOfService() { showTermsModal.value = true }

onMounted(() => {
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark' || (!savedTheme && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    isDarkMode.value = true
    document.documentElement.classList.add('dark')
  }
  gsap.fromTo(loginPageRef.value, { opacity: 0 }, { opacity: 1, duration: 0.6, ease: 'power2.out' })
  const savedIdentifier = localStorage.getItem('savedIdentifier')
  if (savedIdentifier) { identifier.value = savedIdentifier; rememberMe.value = true }
})

onUnmounted(() => {
  if (lockoutTimer.value) clearInterval(lockoutTimer.value)
  document.removeEventListener('mousemove', handleCaptchaDrag)
  document.removeEventListener('mouseup', stopCaptchaDrag)
  document.removeEventListener('touchmove', handleCaptchaDrag)
  document.removeEventListener('touchend', stopCaptchaDrag)
})

watch(rememberMe, (newVal) => {
  if (newVal && identifier.value) localStorage.setItem('savedIdentifier', identifier.value)
  else localStorage.removeItem('savedIdentifier')
})

watch(identifier, (newVal) => {
  if (rememberMe.value) localStorage.setItem('savedIdentifier', newVal)
})
</script>

<style scoped>
input { caret-color: #007AFF; }
</style>
