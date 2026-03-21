# MascotLogin - Beautiful Passkey Authentication Component

<p align="center">
  <strong>✨ A beautiful, interactive login interface with adorable animated mascots and secure passkey authentication ✨</strong>
</p>

## 🚀 Features

### 🎨 Stunning UI Design
- **Beautiful Gradient Backgrounds**: Smooth, animated gradient backgrounds with decorative particles
- **Light Mode**: Clean, modern light theme design
- **NeuMorphism Design**: Soft shadows and blurred glass effects
- **Responsive Layout**: Beautiful on all screen sizes from mobile to desktop

### 🎭 Adorable Mascots
- **Three Animated Characters**: Circle, Square, and Capsule characters with charming animations
- **Interactive Reactions**: Characters react to user input (email focus, password focus, button hover)
- **Smooth Transitions**: GSAP-powered animations for a delightful user experience
- **Blinking Eyes**: Random blinking animations for added personality
- **Eye Tracking**: Eyes follow your mouse movement for immersive interaction

### 🔐 Passkey Authentication
- **WebAuthn API Integration**: Real passkey authentication implementation
- **Multiple Methods**: Support for Windows Hello, fingerprint, and security keys
- **8 Languages**: Support for English, 中文, 日本語, Español, Français, Deutsch, 한국어, العربية
- **RTL Support**: Right-to-left language support for Arabic

### 🎬 Enhanced Animations
- **Modal Open/Close**: Smooth animations for modal transitions
- **Step Transitions**: Beautiful step-by-step animations
- **Loading States**: Elegant loading animations
- **Success/Error States**: Engaging success and error animations

### ⚡ Advanced Features
- **Keyboard Navigation**: Full keyboard navigation
- **Local Storage**: Language preferences saved
- **Accessibility**: Complete ARIA labels and accessibility features

## 📦 Installation

```bash
# Clone the repository
git clone https://github.com/your-username/mascotlogin.git

# Install dependencies
npm install

# Start development server
npm run dev
```

## 🎯 Quick Start

### Basic Usage

```vue
<template>
  <div>
    <button @click="openPasskeyModal">Sign in with Passkey</button>
    <PasskeyModal
      ref="passkeyModal"
      :show="showModal"
      @close="showModal = false"
      @success="handleSuccess"
      @error="handleError"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import PasskeyModal from './components/PasskeyModal.vue'

const showModal = ref(false)
const passkeyModal = ref(null)

function openPasskeyModal() {
  showModal.value = true
}

function handleSuccess(credential) {
  console.log('Authentication successful:', credential)
}

function handleError(error) {
  console.error('Authentication failed:', error)
}
</script>
```

### Using the Login Page Component

```vue
<template>
  <LoginPage
    :language="'en'"
    @login="handleLogin"
  />
</template>

<script setup>
import LoginPage from './components/LoginPage.vue'

function handleLogin(data) {
  console.log('Login:', data)
}
</script>
```

## 🎨 Component Props

### PasskeyModal Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `show` | Boolean | `false` | Controls modal visibility |
| `language` | String | `'en'` | Language code (en, zh, ja, es, fr, de, ko, ar) |
| `rpId` | String | `window.location.hostname` | Relying Party ID |
| `challenge` | String | `'demo-challenge'` | Challenge for authentication |
| `timeout` | Number | `60000` | Authentication timeout in ms |
| `maxRetries` | Number | `3` | Maximum retry attempts |
| `autoDetectLanguage` | Boolean | `true` | Auto-detect browser language |
| `closeOnBackdrop` | Boolean | `true` | Close on backdrop click |
| `closeOnEsc` | Boolean | `true` | Close on ESC key |
| `closeOnSwipe` | Boolean | `true` | Close on swipe down |
| `saveLanguage` | Boolean | `true` | Save language preference |

### PasskeyModal Events

| Event | Payload | Description |
|-------|---------|-------------|
| `close` | - | Emitted when modal closes |
| `success` | `credentialData` | Emitted on successful authentication |
| `error` | `Error` | Emitted on authentication error |
| `timeout` | `Error` | Emitted when authentication times out |
| `languageChange` | `languageCode` | Emitted when language changes |

## 🔧 Exposed Methods

```javascript
// Open the modal
passkeyModal.value.open()

// Close the modal
passkeyModal.value.close()
```

## 🛠️ Tech Stack

- **Vue 3**: Modern Vue 3 Composition API
- **Vite**: Lightning-fast build tool
- **Tailwind CSS**: Utility-first CSS framework
- **GSAP**: Professional animation library
- **WebAuthn API**: Standard passkey authentication
- **i18n**: Custom multi-language support

## 📱 Browser Support

- Chrome 67+
- Edge 79+
- Firefox 60+
- Safari 13+
- Safari on iOS 13.5+

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by GitHub's passkey interface
- Built with ❤️ and Vue.js
- Beautiful animations powered by GSAP

---

<p align="center">
  Made with ✨ by MascotLogin Team
</p>
