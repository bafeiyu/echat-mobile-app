---
description: 在云端构建 Android APK
---

# 在云端构建 Android APK

这是最简单的方法，适合 Windows 用户。

## 步骤

### 1. 检查环境

// turbo

```bash
npx expo-doctor
```

### 2. 登录 EAS（如果还没登录）

```bash
eas login
```

### 3. 构建 Android APK

```bash
pnpm build:android
```

或者使用带环境变量的命令：

```bash
dotenv -c -- eas build -p android --profile production
```

### 4. 等待构建完成

- 构建时间通常需要 10-15 分钟
- EAS 会在云端编译你的应用
- 构建完成后会显示下载链接

### 5. 下载并安装

- 在 Android 手机上打开提供的下载链接
- 下载 APK 文件
- 安装到手机上（可能需要允许"未知来源"应用）

## 注意事项

1. **免费账号限制**：每月有构建次数限制
2. **构建类型**：production profile 会生成 `.aab` 文件（用于 Google Play）
3. **如果需要 APK**：可以修改 eas.json 添加 `"buildType": "apk"`

## 修改构建类型为 APK

如果你需要直接生成 APK 而不是 AAB，可以在 `eas.json` 中添加：

```json
{
  "build": {
    "production": {
      "android": {
        "buildType": "apk"
      },
      "autoIncrement": true
    }
  }
}
```
