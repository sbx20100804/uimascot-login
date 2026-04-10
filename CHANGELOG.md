# Changelog

All notable changes to this project will be documented in this file.

## [1.0.0] - 2026-04-10

### English

#### 🐛 Fixed
- **Eye Animation Bug**: Fixed the issue where eyes would disappear due to animation conflicts
- **Password→Email Animation Bug**: Fixed the issue where eyelids would not open when switching from password to email input
- **Language Selector Bug**: Fixed the missing click event on the "Continue" button
- **PasskeyModal Bug**: Fixed the issue where clicking the backdrop would not close the modal
- **PasskeyModal Hardcoded Text**: Fixed hardcoded English text, now using i18n translations
- **LoginPage Prop Error**: Fixed the issue where a non-existent prop was being passed
- **Password Validation Hack**: Fixed the hack where password length was replaced from 6 to 8 characters
- **UI Layout Issues**: Fixed layout jitter in the language selector
- **Responsive Design**: Fixed responsive issues on mobile devices

#### ✨ Added
- **Mouth Animations**: Added complete facial expressions (smile, laugh, frown, surprise) for all three mascots
- **Password Strength Meter**: Added real-time password strength validation (0-100 score) with 5 levels
- **Remember Me Feature**: Added auto-save email functionality
- **Security Features**: Added XSS protection and comprehensive password validation
- **Multi-language Support**: Added support for 8 languages (English, Chinese, Japanese, Spanish, French, German, Korean, Arabic)
- **Particle Effects**: Added background particle animations to language selector and login page
- **Eye Tracking**: Implemented mouse-follow eye tracking for all mascots
- **Smooth Transitions**: Added fluid page transitions and state changes
- **Password Requirements**: Added real-time checking for password requirements (uppercase, lowercase, number, special character)

#### 🔧 Improved
- **Animation System**: Rewrote the animation system in AbstractInteractiveHero.vue for better performance and reliability
- **State Management**: Improved state transitions and animation timing
- **Code Quality**: Cleaned up redundant code and improved code structure
- **Performance**: Optimized animations and reduced unnecessary calculations
- **Security**: Enhanced password validation and added input sanitization
- **User Experience**: Improved responsive design and added more interactive elements

### 中文

#### 🐛 修复
- **眼睛动画Bug**：修复了眼睛因动画冲突而消失的问题
- **密码→邮箱动画Bug**：修复了从密码切换到邮箱输入时眼睑不打开的问题
- **语言选择器Bug**：修复了"继续"按钮缺少点击事件的问题
- **PasskeyModal Bug**：修复了点击背景无法关闭模态框的问题
- **PasskeyModal硬编码文本**：修复了硬编码英文文本，现在使用i18n翻译
- **LoginPage Prop错误**：修复了传递不存在prop的问题
- **密码验证Hack**：修复了密码长度从6改为8字符的hack
- **UI布局问题**：修复了语言选择器中的布局抖动
- **响应式设计**：修复了移动设备上的响应式问题

#### ✨ 新增
- **嘴巴动画**：为所有三个角色添加了完整的面部表情（微笑、大笑、皱眉、惊讶）
- **密码强度指示器**：添加了实时密码强度验证（0-100分），共5级
- **记住我功能**：添加了自动保存邮箱功能
- **安全功能**：添加了XSS防护和全面的密码验证
- **多语言支持**：添加了8种语言支持（英语、中文、日语、西班牙语、法语、德语、韩语、阿拉伯语）
- **粒子效果**：为语言选择器和登录页面添加了背景粒子动画
- **眼睛跟踪**：实现了所有角色的鼠标跟随眼睛跟踪
- **平滑过渡**：添加了流畅的页面过渡和状态变化
- **密码要求**：添加了实时检查密码要求（大写字母、小写字母、数字、特殊字符）

#### 🔧 改进
- **动画系统**：重写了AbstractInteractiveHero.vue中的动画系统，提高了性能和可靠性
- **状态管理**：改进了状态转换和动画时序
- **代码质量**：清理了冗余代码，改进了代码结构
- **性能**：优化了动画，减少了不必要的计算
- **安全性**：增强了密码验证，添加了输入sanitization
- **用户体验**：改进了响应式设计，添加了更多交互元素

## [0.1.0] - 2026-04-09

### English

#### 🎉 Initial Release
- **Animated Mascots**: Three cute characters with basic animations
- **Passkey Authentication**: GitHub-style passkey modal
- **Login Page**: Basic login form with email and password
- **Language Selection**: Multi-language support
- **Responsive Design**: Basic responsive layout

### 中文

#### 🎉 初始版本
- **动画角色**：三个具有基本动画的可爱角色
- **通行密钥认证**：GitHub风格的通行密钥模态框
- **登录页面**：带有邮箱和密码的基本登录表单
- **语言选择**：多语言支持
- **响应式设计**：基本的响应式布局

[1.0.0]: https://github.com/your-username/mascot-login/releases/tag/v1.0.0
[0.1.0]: https://github.com/your-username/mascot-login/releases/tag/v0.1.0
