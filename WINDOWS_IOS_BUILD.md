# Windows 系统构建 iOS 应用指南

## 重要说明

⚠️ **iOS 本地构建限制**：
- iOS 本地构建（`pnpm build:ios:local`）**只能在 macOS 系统上进行**
- Windows 和 Linux **不支持** iOS 本地构建
- 在 Windows 上构建 iOS 应用，**必须使用 EAS 云端构建**

## 解决方案：使用 EAS 云端构建

### 步骤 1：登录 EAS（如果还没登录）

```bash
eas login
```

### 步骤 2：开始构建

```bash
# 使用 preview profile（推荐，适合内部分发和演示）
eas build -p ios --profile preview

# 或使用生产版本
pnpm build:ios
```

### 步骤 3：配置 Apple 账号

构建过程中，EAS 会询问：

```
? Do you want to log in to your Apple account? » (Y/n)
```

#### 情况 A：有付费 Apple Developer 账号（$99/年）

**建议选择 `Y`（是）**，因为：
- EAS 会自动管理证书和配置文件
- 无需手动配置复杂的证书
- 更安全可靠

**登录方式**：
- 使用 Apple ID 和密码
- 或使用 App-Specific Password（如果启用了两步验证）

#### 情况 B：只有免费 Apple ID

**如果出现错误**：
```
You have no team associated with your Apple account
```

这表示你的 Apple ID 没有注册 Apple Developer Program。

**解决方案**：

1. **选择不登录 Apple ID**（选择 `n`）
   - EAS 会引导你手动配置证书
   - 需要一些技术知识
   - 可能需要 macOS 协助

2. **注册付费开发者账号**（推荐）
   - 访问：https://developer.apple.com/programs/
   - 注册并支付 $99/年
   - 审核通过后重新构建

3. **使用 macOS + Xcode**（如果有 Mac）
   - 不需要付费账号
   - 使用 `pnpm ios` 直接运行

**详细说明请查看**：`FREE_APPLE_ID_SOLUTION.md`

### 步骤 4：等待构建完成

构建过程通常需要 10-20 分钟，取决于：
- 项目大小
- EAS 服务器负载
- 是否需要生成新证书

**查看构建进度**：
- 终端会显示实时日志
- 或访问 EAS 网站查看：https://expo.dev/accounts/[你的账号]/projects/[项目名]/builds

### 步骤 5：下载并安装到 iPhone

构建完成后，EAS 会提供：

1. **下载链接**（在终端中显示）
2. **二维码**（可以在 EAS 网站查看）

**安装步骤**：

1. **在 iPhone 上打开下载链接**
   - 使用 Safari 浏览器（不要用其他浏览器）
   - 打开 EAS 提供的下载链接

2. **下载应用**
   - 点击"安装"或"Download"按钮
   - 等待下载完成

3. **信任开发者（首次安装必须）**
   - 打开 iPhone 设置
   - 进入：**设置 → 通用 → VPN与设备管理**
   - 找到你的开发者证书（通常是企业开发者证书）
   - 点击证书名称
   - 点击"信任 [证书名称]"
   - 确认信任

4. **启动应用**
   - 回到主屏幕
   - 找到应用图标
   - 点击启动

## 常见问题

### 1. 构建失败：证书问题

**错误信息**：`Missing credentials` 或 `Certificate expired`

**解决方案**：
```bash
# 让 EAS 重新管理证书
eas credentials

# 或重新构建，选择登录 Apple 账号
eas build -p ios --profile preview
```

### 2. 无法安装：未信任开发者

**问题**：安装后提示"无法验证应用"

**解决方案**：
1. 设置 → 通用 → VPN与设备管理
2. 找到开发者证书
3. 点击"信任"

### 3. 下载链接打不开

**问题**：在 iPhone 上无法打开下载链接

**解决方案**：
- 确保使用 Safari 浏览器（不是 Chrome 或其他浏览器）
- 检查网络连接
- 尝试使用二维码扫描（在 EAS 网站查看）

### 4. 应用过期（免费 Apple ID）

**问题**：使用免费 Apple ID 构建的应用，7 天后无法打开

**说明**：
- 免费 Apple ID 构建的应用有效期只有 7 天
- 7 天后需要重新构建和安装
- 付费开发者账号（$99/年）构建的应用有效期 1 年

**解决方案**：
- 重新构建应用
- 或注册付费开发者账号

### 5. 构建很慢

**问题**：构建时间超过 30 分钟

**可能原因**：
- 首次构建需要生成证书（较慢）
- EAS 服务器负载高
- 项目较大

**解决方案**：
- 耐心等待（首次构建通常较慢）
- 后续构建会使用缓存，速度更快
- 可以在 EAS 网站查看构建日志

## 快速命令参考

```bash
# 登录 EAS
eas login

# 构建预览版本（内部分发）
eas build -p ios --profile preview

# 构建生产版本
pnpm build:ios

# 查看构建列表
eas build:list

# 查看构建详情
eas build:view [build-id]

# 管理证书
eas credentials
```

## 构建配置说明

### Preview Profile（预览版本）

- **用途**：内部分发、演示、测试
- **分发方式**：通过下载链接直接安装
- **有效期**：取决于证书类型（免费账号 7 天，付费账号 1 年）

### Production Profile（生产版本）

- **用途**：正式发布、App Store 提交
- **分发方式**：App Store 或 TestFlight
- **需要**：付费 Apple 开发者账号

## 推荐流程

### 最快演示方式

```bash
# 1. 登录 EAS
eas login

# 2. 构建预览版本
eas build -p ios --profile preview

# 3. 在 iPhone 上打开下载链接安装
# 4. 在设置中信任开发者证书
```

### 完整流程

1. **准备阶段**
   - 确保已登录 EAS：`eas login`
   - 检查项目配置：`npx expo-doctor`

2. **构建阶段**
   - 运行构建命令：`eas build -p ios --profile preview`
   - 选择登录 Apple 账号（推荐）
   - 等待构建完成（10-20 分钟）

3. **安装阶段**
   - 在 iPhone Safari 中打开下载链接
   - 下载并安装应用
   - 信任开发者证书

4. **验证阶段**
   - 启动应用
   - 测试功能
   - 检查是否正常工作

## 注意事项

1. **网络要求**：需要稳定的网络连接
2. **Apple 账号**：建议使用付费开发者账号（$99/年）以获得更好的体验
3. **设备限制**：免费账号最多注册 3 台设备
4. **应用有效期**：免费账号构建的应用 7 天后过期
5. **证书管理**：让 EAS 自动管理证书更简单可靠

## 相关文档

- EAS Build 文档：https://docs.expo.dev/build/introduction/
- iOS 构建文档：https://docs.expo.dev/build-reference/ios-builds/
- Apple 开发者账号：https://developer.apple.com/programs/

