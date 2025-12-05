# iOS 和 Android 应用打包指南

## 项目概述

这是一个基于 **Expo SDK 52** 的 React Native 项目，使用 **EAS Build (Expo Application Services)** 进行应用打包。

## 打包方式

项目支持两种打包方式：

### 1. 本地构建（推荐用于演示）
- 在本地机器上构建应用
- 需要安装相应的开发环境（Xcode for iOS, Android Studio for Android）
- 构建速度取决于本地机器性能
- 适合快速测试和演示

### 2. 云端构建
- 在 Expo 的云端服务器上构建
- 不需要本地开发环境
- 需要 EAS 账号和网络连接
- 适合正式发布

## 前置准备

### 1. 安装必要工具

```bash
# 安装 EAS CLI
npm install -g eas-cli

# 登录 EAS（如果使用云端构建）
eas login

# 检查项目配置
npx expo-doctor
```

### 2. 配置环境变量

创建 `.env` 文件（项目根目录），包含以下变量：

```env
# EAS 项目 ID（已在 app.config.ts 中有默认值）
EXPO_PUBLIC_PROJECT_ID=0b1cd169-953e-4396-8eb2-5e46f61c56e1

# iOS Google Services 文件路径（可选，用于 Firebase）
EXPO_PUBLIC_IOS_GOOGLE_SERVICES_FILE=./GoogleService-Info.plist

# Android Google Services 文件路径（可选，用于 Firebase）
EXPO_PUBLIC_ANDROID_GOOGLE_SERVICES_FILE=./google-services.json

# Sentry 配置（可选，用于错误追踪）
EXPO_PUBLIC_SENTRY_PROJECT_NAME=your-project
EXPO_PUBLIC_SENTRY_ORG_NAME=your-org
```

### 3. iOS 构建准备（仅本地构建需要）

- **macOS 系统**（iOS 构建只能在 macOS 上进行）
- 安装 **Xcode**（从 App Store 安装最新版本）
- 安装 **Xcode Command Line Tools**：
  ```bash
  xcode-select --install
  ```
- **Apple 开发者账号**（用于签名，免费账号也可以用于开发构建）

### 4. Android 构建准备（仅本地构建需要）

- 安装 **Android Studio**
- 配置 **Android SDK**
- 设置环境变量：
  ```bash
  # Windows (PowerShell)
  $env:ANDROID_HOME = "C:\Users\YourName\AppData\Local\Android\Sdk"
  
  # macOS/Linux
  export ANDROID_HOME=$HOME/Library/Android/sdk
  export PATH=$PATH:$ANDROID_HOME/emulator
  export PATH=$PATH:$ANDROID_HOME/tools
  export PATH=$PATH:$ANDROID_HOME/tools/bin
  export PATH=$PATH:$ANDROID_HOME/platform-tools
  ```

## 构建命令

### Android 构建

#### ⚠️ Windows 系统限制

**重要提示**：EAS Build 的本地构建（`--local`）在 **Windows 上不支持 Android**，只能在 macOS 或 Linux 上使用。

在 Windows 上构建 Android 应用，请使用以下方法：

#### 方法 1：云端构建（推荐，最简单）

```bash
# 使用 EAS 云端构建（需要 EAS 账号，免费账号也可以）
pnpm build:android

# 构建完成后，EAS 会提供下载链接
# 可以直接在手机上通过链接下载安装
```

**优点**：
- 不需要本地 Android 开发环境
- 构建速度快
- 自动处理签名和配置

#### 方法 2：使用 Expo 直接运行（适合开发测试）

```bash
# 直接运行到连接的 Android 手机或模拟器
pnpm android

# 或使用完整命令
pnpm run:android
```

**前提条件**：
- 已安装 Android Studio
- 已配置 Android SDK
- 手机已启用 USB 调试并连接到电脑

#### 方法 3：使用 Android Studio 构建 APK

```bash
# 1. 首先生成原生 Android 项目
npx expo prebuild --platform android

# 2. 使用 Android Studio 打开 android 文件夹
# 3. 在 Android Studio 中：Build → Build Bundle(s) / APK(s) → Build APK(s)
```

**优点**：
- 完全本地构建
- 可以自定义构建配置
- 生成标准的 APK 文件

#### 方法 4：本地构建（仅限 macOS/Linux）

```bash
# 仅在 macOS 或 Linux 系统上可用
pnpm build:android:local
```

构建完成后，会在项目根目录生成 `.aab` 或 `.apk` 文件。

### iOS 构建

#### ⚠️ Windows 系统限制

**重要提示**：iOS 本地构建（`--local`）**只能在 macOS 系统上进行**，Windows 和 Linux 都不支持。

在 Windows 上构建 iOS 应用，**必须使用云端构建**。

#### 方法 1：云端构建（Windows/macOS/Linux 都可用，推荐）

```bash
# 使用 preview profile（内部分发，适合演示）
eas build -p ios --profile preview

# 或使用生产版本
pnpm build:ios
```

**优点**：
- 不需要 macOS 系统
- 不需要本地开发环境
- 自动处理证书和签名
- 构建完成后提供下载链接

**构建流程**：
1. EAS 会询问是否登录 Apple 账号（可选，但推荐）
2. 如果登录，EAS 会自动管理证书和配置文件
3. 构建完成后，会提供下载链接
4. 在 iPhone 上通过 Safari 打开链接即可安装

#### 方法 2：本地构建（仅限 macOS）

```bash
# 仅在 macOS 系统上可用
pnpm build:ios:local
```

构建完成后，会在项目根目录生成 `.ipa` 文件。

**前提条件**：
- macOS 系统
- 已安装 Xcode
- 已配置 Apple 开发者账号

### 同时构建两个平台

```bash
# 本地构建
pnpm build:all:local

# 云端构建
pnpm build:all
```

## 安装到手机

### Android 安装

#### 方法 1：直接安装 APK（最简单）

1. **生成 APK 而不是 AAB**：
   - 修改 `eas.json` 或使用命令行参数指定构建 APK
   - 或者使用 Android Studio 打开项目并直接构建 APK

2. **传输到手机**：
   - 通过 USB 连接手机
   - 将 APK 文件复制到手机
   - 在手机上打开文件管理器，找到 APK 文件并安装
   - 如果提示"未知来源"，需要在设置中允许安装未知应用

#### 方法 2：使用 ADB 安装

```bash
# 连接手机（确保已启用 USB 调试）
adb devices

# 安装 APK
adb install path/to/your-app.apk
```

#### 方法 3：使用 EAS Build 的内部分发

1. 使用 `preview` profile 构建：
   ```bash
   eas build -p android --profile preview
   ```

2. 构建完成后，EAS 会提供一个下载链接
3. 在手机上打开链接下载并安装

### iOS 安装

#### 方法 1：使用 Xcode 直接安装（最简单）

1. 使用本地构建生成 `.ipa` 文件
2. 打开 **Xcode** → **Window** → **Devices and Simulators**
3. 连接 iPhone
4. 选择设备，点击 **+** 号，选择 `.ipa` 文件安装

#### 方法 2：使用 EAS Build 的内部分发

1. 使用 `preview` profile 构建：
   ```bash
   eas build -p ios --profile preview
   ```

2. 构建完成后，EAS 会提供一个下载链接
3. 在 iPhone 上打开链接，通过 TestFlight 或直接安装

#### 方法 3：使用 TestFlight（需要 Apple 开发者账号）

1. 构建生产版本：
   ```bash
   pnpm build:ios
   ```

2. 提交到 App Store Connect：
   ```bash
   pnpm submit:ios
   ```

3. 在 App Store Connect 中配置 TestFlight 测试
4. 通过 TestFlight 应用安装

## 快速演示流程（推荐）

### Android 快速演示

#### Windows 系统（推荐方案）

1. **确保环境已配置**：
   ```bash
   # 检查环境
   npx expo-doctor
   ```

2. **选择构建方式**：

   **方案 A：云端构建（最简单，推荐）**
   ```bash
   # 需要先登录 EAS（如果还没登录）
   eas login
   
   # 云端构建
   pnpm build:android
   
   # 构建完成后，EAS 会提供下载链接，在手机上打开链接即可安装
   ```

   **方案 B：直接运行到手机（需要 Android Studio）**
   ```bash
   # 连接 Android 手机（启用 USB 调试）
   # 运行开发版本（会自动安装到手机）
   pnpm android
   ```

   **方案 C：使用 Android Studio 构建 APK**
   ```bash
   # 1. 生成原生项目
   npx expo prebuild --platform android
   
   # 2. 用 Android Studio 打开 android 文件夹
   # 3. Build → Build Bundle(s) / APK(s) → Build APK(s)
   # 4. APK 文件在 android/app/build/outputs/apk/ 目录
   ```

#### macOS/Linux 系统

1. **确保环境已配置**：
   ```bash
   npx expo-doctor
   ```

2. **本地构建 APK**：
   ```bash
   # 使用 Expo 直接运行（开发模式，可直接安装）
   pnpm android
   
   # 或者构建发布版本
   pnpm build:android:local
   ```

3. **安装到手机**：
   - 如果使用 `pnpm android`，应用会自动安装到连接的设备
   - 如果构建了 APK，通过 USB 或文件传输安装

### iOS 快速演示

#### Windows 系统（推荐方案）

1. **确保环境已配置**：
   ```bash
   # 检查环境
   npx expo-doctor
   ```

2. **使用云端构建**：
   ```bash
   # 确保已登录 EAS
   eas login
   
   # 使用 preview profile 构建（内部分发）
   eas build -p ios --profile preview
   
   # 构建过程中会询问是否登录 Apple 账号（推荐选择 Y）
   # 如果登录，EAS 会自动管理证书和配置文件
   ```

3. **安装到 iPhone**：
   - 构建完成后，EAS 会提供下载链接
   - 在 iPhone 的 Safari 浏览器中打开链接
   - 点击"安装"按钮
   - 首次安装需要在设置中信任开发者：
     - 设置 → 通用 → VPN与设备管理 → 信任开发者证书

#### macOS 系统

1. **确保环境已配置**：
   ```bash
   npx expo-doctor
   ```

2. **本地构建并运行**：
   ```bash
   # 直接运行到连接的 iPhone（开发模式）
   pnpm ios
   
   # 或者构建发布版本
   pnpm build:ios:local
   ```

3. **安装到手机**：
   - 如果使用 `pnpm ios`，应用会自动安装到连接的设备
   - 如果构建了 IPA，通过 Xcode 安装：
     - Xcode → Window → Devices and Simulators → 选择设备 → 点击 + → 选择 IPA

## 常见问题

### 1. 构建失败：缺少环境变量
- 确保创建了 `.env` 文件
- 检查必要的环境变量是否已设置

### 2. iOS 构建失败：证书问题
- 确保已登录 Apple 开发者账号
- 运行 `eas credentials` 配置证书
- 或使用本地构建时，Xcode 会自动处理证书

### 3. Android 构建失败：签名问题
- 首次构建时，EAS 会自动生成签名密钥
- 或使用本地构建时，可以配置本地签名

### 4. 本地构建很慢
- 首次构建需要下载依赖和工具，会比较慢
- 后续构建会使用缓存，速度会快很多
- 考虑使用云端构建

### 5. 无法安装到手机
- **Android**：确保已启用"未知来源"安装权限
- **iOS**：确保设备已信任开发者证书（设置 → 通用 → VPN与设备管理）

## 项目配置说明

### 应用标识符
- **iOS Bundle ID**: `com.echat.app`
- **Android Package**: `com.echat.app`
- **应用名称**: `EChat`

### 构建配置（eas.json）
- **development**: 开发客户端构建
- **production**: 生产环境构建（自动递增版本号）
- **preview**: 预览版本构建（内部分发）

## 推荐流程总结

### 最快演示方式

**Android**:
```bash
# 1. 连接 Android 手机（启用 USB 调试）
# 2. 运行开发版本（会自动安装）
pnpm android
```

**iOS** (macOS):
```bash
# 1. 连接 iPhone（信任电脑）
# 2. 运行开发版本（会自动安装）
pnpm ios
```

### 构建发布版本用于演示

**Android**:
```bash
# 本地构建 APK
pnpm build:android:local
# 然后通过 USB 或文件传输安装 APK
```

**iOS** (macOS):
```bash
# 本地构建 IPA
pnpm build:ios:local
# 然后通过 Xcode 安装 IPA
```

## 注意事项

1. **首次构建会比较慢**，需要下载依赖和工具
2. **iOS 构建只能在 macOS 上进行**
3. **Android 构建可以在 Windows、macOS、Linux 上进行**
4. **云端构建需要 EAS 账号**（免费账号也可以使用）
5. **生产环境构建需要配置签名证书**
6. **确保手机已启用开发者模式**（Android）或**信任开发者**（iOS）

