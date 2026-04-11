# MascotLogin

> A modern, responsive login interface with animated mascot characters, multi-device adaptation, dark mode, captcha verification, and multi-language support.
> 
> 一个现代化的响应式登录界面，具有动画角色、多设备适配、暗黑模式、验证码验证和多语言支持。

## 📸 Screenshot

![MascotLogin Demo](https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=modern%20login%20interface%20with%20mascot%20characters%2C%20dark%20mode%20toggle%2C%20glass%20morphism%20design%2C%20clean%20ui&image_size=square_hd)

## 🎯 Features

### English
- **Animated Mascots**: Three cute characters (circle, square, capsule) with eye tracking and facial expressions
- **Responsive Layout**: Fully responsive design optimized for all screen sizes from mobile to desktop
- **Multi-device Adaptation**: Seamless experience on mobile phones, tablets, and desktops
- **Dark Mode**: Full dark mode support with smooth transitions (available on both language selection and login pages)
- **Captcha Verification**: Slide-to-verify captcha with responsive sizing
- **Passkey Authentication**: Real WebAuthn API integration for secure passwordless login
- **Multi-language Support**: 8 languages with auto-save language preference
- **Password Strength Meter**: Real-time password strength validation (0-100 score)
- **Password Match Validation**: Real-time confirmation for password matching during registration
- **Remember Me**: Auto-save email for convenience
- **Security**: XSS protection and comprehensive password validation
- **Fluid Animations**: Professional GSAP animations with GPU acceleration for smooth transitions
- **Glass Morphism**: Frosted glass UI with backdrop blur effects
- **OTP Verification**: One-time password input for two-factor authentication
- **Social Login**: Support for WeChat, QQ, Google, and GitHub
- **Configuration Panel**: Visual configuration tool to customize colors, social login links, and app settings

### 中文
- **动画角色**：三个可爱的角色（圆形、方形、胶囊形），支持眼睛跟踪和面部表情
- **响应式布局**：完全响应式设计，优化从移动到桌面的所有屏幕尺寸
- **多设备适配**：在手机、平板和桌面设备上提供无缝体验
- **暗黑模式**：完整的暗黑模式支持，平滑过渡（语言选择和登录页面都可用）
- **验证码验证**：滑动验证码，响应式尺寸适配
- **通行密钥认证**：基于WebAuthn API的真实安全无密码登录
- **多语言支持**：8种语言，自动保存语言偏好
- **密码强度指示器**：实时密码强度验证（0-100分）
- **密码匹配验证**：注册时实时验证密码是否匹配
- **记住我**：自动保存邮箱，方便用户
- **安全性**：XSS防护和全面的密码验证
- **流畅动画**：专业的GSAP动画，GPU加速实现平滑过渡
- **毛玻璃效果**：带有背景模糊的毛玻璃UI
- **OTP验证**：用于双因素认证的一次性密码输入
- **社交登录**：支持微信、QQ、Google和GitHub
- **配置面板**：可视化配置工具，可自定义颜色、社交登录链接和应用设置

## 🛠️ Technology Stack

| Technology | Version | Purpose |
|-----------|---------|---------|
| Vue 3 | 3.4.21 | Frontend Framework (Composition API) |
| Vite | 5.1.6 | Build Tool |
| Tailwind CSS | 3.4.1 | Styling & Responsive Design |
| GSAP | 3.12.5 | Animation Engine |
| WebAuthn API | - | Passkey Authentication |

## 📦 Installation

```bash
# Clone the repository
git clone https://github.com/your-username/mascot-login.git
cd mascot-login

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build
```

Open http://localhost:5173 in your browser

## 🎨 Components

| Component | Description |
|-----------|-------------|
| `AbstractInteractiveHero.vue` | Animated mascots with eye tracking, facial expressions, and responsive layout |
| `LanguageSelector.vue` | Multi-language selection with particle effects and dark mode toggle |
| `LoginPage.vue` | Main login interface with captcha, password strength meter, and modals |
| `PasskeyModal.vue` | GitHub-style passkey authentication modal |
| `ConfigPanel.vue` | Visual configuration panel for customizing colors, links, and settings |

## 🌍 Supported Languages

- English (en)
- 中文 (zh)
- 日本語 (ja)
- Español (es)
- Français (fr)
- Deutsch (de)
- 한국어 (ko)
- العربية (ar)

## 🔒 Security Features

- **XSS Protection**: Input sanitization to prevent cross-site scripting
- **Password Strength Validation**: Real-time validation with 5 levels
- **Password Match Check**: Instant feedback during account creation
- **Captcha Verification**: Slide-to-verify to prevent bot attacks
- **Passkey Authentication**: Secure WebAuthn API implementation
- **Password Requirements**: Minimum 8 characters, uppercase, lowercase, number, special character

## 📱 Responsive Design

| Breakpoint | Device | Layout |
|-----------|--------|--------|
| < 640px | Mobile | Vertical layout, compact spacing |
| 640px - 767px | Small Tablet | Vertical layout, medium spacing |
| 768px - 1024px | Tablet | Horizontal split, adaptive sizing |
| > 1024px | Desktop | Full horizontal split, spacious layout |

### Responsive Features
- **Adaptive Character Layout**: Vertical on mobile, horizontal on desktop
- **Responsive Typography**: Auto-adjusting font sizes per breakpoint
- **Adaptive Spacing**: Smart spacing for different screen sizes
- **Touch-friendly Controls**: Larger touch targets on mobile devices
- **Responsive Captcha**: Slider adapts to screen size
- **Modal Optimization**: Modals resize and reflow for each device

## 🎭 Animation Features

- **Eye Tracking**: Mascots follow your mouse cursor
- **Facial Expressions**: Smile, laugh, frown, and surprise animations
- **Idle Animations**: Gentle floating and blinking
- **State Transitions**: Smooth animations for email/password focus
- **Button Hover**: Interactive animations on hover
- **Dark Mode Transition**: Smooth color transitions
- **GPU Acceleration**: `will-change` hints and `translateZ(0)` for better performance

## 🚀 Usage

1. **Language Selection**: Choose your preferred language on the first screen
2. **Dark Mode Toggle**: Switch between light and dark themes anytime
3. **Email Input**: Enter your email address
4. **Captcha Verification**: Slide to verify
5. **Password Input**: Enter your password (8+ characters)
6. **Remember Me**: Check to save your email
7. **Sign In**: Click the sign in button
8. **Passkey**: Click "Sign in with a passkey" for secure authentication
9. **Configuration**: Click the gear icon in bottom-right corner to customize the app

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

MIT License - see the [LICENSE](LICENSE) file for details

## 🌟 Star History

If you find this project helpful, please give it a star ⭐

## 📞 Support

For questions or support, please open an issue in the GitHub repository.

---

## 📤 GitHub Publishing Guide

### Repository Summary (for GitHub)
**Summary**: MascotLogin is a modern, responsive login interface featuring animated mascot characters, dark mode, multi-language support, captcha verification, and passkey authentication. Built with Vue 3, Vite, Tailwind CSS, and GSAP for smooth animations. Includes a visual configuration panel for customizing colors and social login links.

**Description**: A modern, responsive login interface with animated mascot characters, multi-device adaptation, dark mode, captcha verification, and multi-language support. Built with Vue 3, Vite, Tailwind CSS, and GSAP.

### Steps to Publish

1. **Initialize Git Repository**
   ```bash
   git init
   git add .
   git commit -m "Initial commit - MascotLogin with all features"
   ```

2. **Create Repository on GitHub**
   - Go to https://github.com/new
   - Repository name: `mascot-login`
   - Description: Use the description above
   - Choose Public or Private
   - Don't initialize with README (we already have one)

3. **Push to GitHub**
   ```bash
   git remote add origin https://github.com/你的用户名/mascot-login.git
   git branch -M main
   git push -u origin main
   ```

4. **Add Topics (Optional)**
   - vue
   - login
   - animation
   - gsap
   - mascot
   - responsive
   - dark-mode
   - captcha
   - i18n
   - tailwindcss
