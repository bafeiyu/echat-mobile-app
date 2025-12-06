---
description: 使用 Android Studio 在本地构建 Android APK
---

# 使用 Android Studio 在本地构建 Android APK

这种方法可以在 Windows 上完全本地构建 APK，不需要依赖云服务。

## 前置要求

1. **安装 Android Studio**

   - 下载地址：https://developer.android.com/studio
   - 安装时选择安装 Android SDK

2. **配置环境变量**
   在 PowerShell 中设置（或添加到系统环境变量）：
   ```powershell
   $env:ANDROID_HOME = "C:\Users\你的用户名\AppData\Local\Android\Sdk"
   $env:PATH += ";$env:ANDROID_HOME\platform-tools"
   $env:PATH += ";$env:ANDROID_HOME\tools"
   ```

## 构建步骤

### 1. 安装项目依赖

// turbo

```bash
pnpm install
```

### 2. 生成原生 Android 项目

```bash
npx expo prebuild --platform android --clean
```

这会在项目根目录生成 `android` 文件夹，包含完整的 Android 原生项目。

### 3. 使用 Android Studio 打开项目

1. 打开 Android Studio
2. 选择 "Open an Existing Project"
3. 选择项目根目录下的 `android` 文件夹
4. 等待 Gradle 同步完成

### 4. 构建 APK

在 Android Studio 中：

1. 点击菜单 **Build** → **Build Bundle(s) / APK(s)** → **Build APK(s)**
2. 等待构建完成
3. 点击通知中的 "locate" 查看 APK 文件

### 5. 查找生成的 APK

APK 文件位置：

```
android/app/build/outputs/apk/release/app-release.apk
```

或者 debug 版本：

```
android/app/build/outputs/apk/debug/app-debug.apk
```

## 安装到手机

### 方法 1：使用 ADB

```bash
# 连接手机（确保已启用 USB 调试）
adb devices

# 安装 APK
adb install android/app/build/outputs/apk/release/app-release.apk
```

### 方法 2：手动传输

1. 将 APK 文件复制到手机
2. 在手机上打开文件管理器
3. 找到 APK 文件并点击安装
4. 如果提示需要允许"未知来源"，在设置中允许

## 常见问题

### 1. Gradle 同步失败

- 检查网络连接
- 尝试使用镜像源（配置 gradle.properties）
- 清理并重新构建：Build → Clean Project

### 2. 签名问题

- Debug 版本会自动使用调试签名
- Release 版本需要配置签名密钥

### 3. 内存不足

- 增加 Gradle 内存：在 `android/gradle.properties` 中添加：
  ```
  org.gradle.jvmargs=-Xmx4096m
  ```

## 优点和缺点

**优点：**

- ✅ 完全本地构建，不依赖云服务
- ✅ 可以自定义构建配置
- ✅ 生成标准的 APK 文件
- ✅ 可以调试原生代码

**缺点：**

- ❌ 需要安装和配置 Android Studio
- ❌ 首次构建较慢
- ❌ 需要手动管理签名密钥（Release 版本）
