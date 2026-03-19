# 🎭 MascotLogin

A beautiful and interactive login page with animated mascot characters built with Vue 3, Vite, Tailwind CSS, and GSAP.

## ✨ Features

- 🎨 **Modern UI Design** - Clean and elegant purple/indigo color scheme
- 🎭 **Animated Characters** - Three adorable geometric mascots (circle, square, capsule)
- 👀 **Expressive Eyes** - Characters blink and look around naturally
- 🌐 **Multi-language Support** - English and Chinese (中文)
- 🔐 **Forgot Password** - Built-in password recovery flow
- 📝 **Create Account** - Sign up functionality
- ✨ **Smooth Animations** - Powered by GSAP for buttery-smooth transitions
- 📱 **Responsive Design** - Works beautifully on all screen sizes

## 🎬 Features Showcase

### Character Animations
- **Startup**: Characters pop in with elastic animations
- **Idle**: Gentle breathing and floating motions
- **Email Focus**: Characters lean in to "peek" at your input
- **Password Focus**: Characters close their eyes for privacy
- **Button Hover**: Characters get excited when you hover over the login button
- **Error**: Characters shake their heads in disappointment

## 🚀 Quick Start

### Prerequisites

- Node.js 16+ and npm

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd mascot-login

# Install dependencies
npm install
```

### Development

```bash
# Start development server
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

### Build for Production

```bash
# Build for production
npm run build

# Preview the build
npm run preview
```

## 🧪 Test Credentials

- **Email**: design@modern.com
- **Password**: password

## 🛠️ Tech Stack

- **Vue 3** - Progressive JavaScript framework (Composition API)
- **Vite** - Next generation frontend tooling
- **Tailwind CSS** - Utility-first CSS framework
- **GSAP** - GreenSock Animation Platform for complex animations

## 📁 Project Structure

```
mascot-login/
├── src/
│   ├── components/
│   │   ├── AbstractInteractiveHero.vue  # Animated characters
│   │   ├── LanguageSelector.vue          # Language selection
│   │   └── LoginPage.vue                 # Main login page
│   ├── utils/
│   │   └── i18n.js                       # Translations
│   ├── App.vue                            # Root component
│   ├── main.js                            # Entry point
│   └── style.css                          # Global styles
├── index.html
├── package.json
├── vite.config.js
├── tailwind.config.js
└── postcss.config.js
```

## 🎨 Customization

### Changing Colors

Edit `src/components/LoginPage.vue` and `src/components/AbstractInteractiveHero.vue` to customize the color scheme.

### Adding Languages

Add new translations to `src/utils/i18n.js`.

### Character Customization

Modify `src/components/AbstractInteractiveHero.vue` to adjust character animations, timings, and behaviors.

## 📝 License

MIT License - feel free to use this project for personal or commercial purposes!

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

## 💡 Acknowledgments

- Inspired by modern UI/UX design principles
- Built with love and GSAP ✨

---

Made with ❤️ by you
