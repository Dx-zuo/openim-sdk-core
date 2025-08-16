# GitHub Actions 自动发布指南

## 概述

本项目配置了自动化的 GitHub Actions 工作流，可以在推送特定标签时自动构建和发布 xcFramework 包。

## 发布流程

### 1. 创建发布标签

当你准备发布新版本时，创建一个以 `im-release-v` 开头的标签：

```bash
# 例如发布 v3.8.3 版本
git tag im-release-v3.8.3
git push origin im-release-v3.8.3
```

### 2. 自动构建触发

推送标签后，GitHub Actions 会自动触发 `IM Release Builder` 工作流，执行以下操作：

1. **环境准备**：
   - 使用 macOS 最新版本运行器
   - 安装 Go 1.21 环境
   - 配置 gomobile 和 gobind

2. **构建三种 xcFramework**：
   - **iOS Universal**：支持 iOS 设备 + iOS 模拟器
   - **macOS**：支持 macOS 原生应用
   - **Apple Universal**：支持所有 Apple 平台（iOS + macOS）

3. **自动发布**：
   - 创建 GitHub Release
   - 上传构建的 xcFramework 包
   - 生成详细的发布说明

### 3. 构建产物

每次发布会生成以下文件：

| 文件名 | 描述 | 支持平台 |
|--------|------|----------|
| `{项目名}_v{版本}_iOS_Universal_xcFramework.zip` | iOS 通用版本 | iPhone/iPad 设备 + 模拟器 |
| `{项目名}_v{版本}_macOS_xcFramework.zip` | macOS 版本 | macOS 应用程序 |
| `{项目名}_v{版本}_Apple_Universal_xcFramework.zip` | Apple 全平台版本 | iOS + macOS 全支持 |

### 4. 手动触发（可选）

如果需要手动触发构建，可以：

1. 访问 GitHub 仓库的 Actions 页面
2. 选择 "IM Release Builder" 工作流
3. 点击 "Run workflow"
4. 输入标签名称（例如：`im-release-v3.8.3`）

## 使用构建的 xcFramework

### 下载

1. 访问项目的 [Releases 页面](../../releases)
2. 找到对应版本的发布
3. 下载适合您项目的 xcFramework 包

### 集成到 Xcode 项目

1. **解压下载的 zip 文件**
2. **将 `OpenIMCore.xcframework` 拖拽到 Xcode 项目中**
3. **在目标设置中确保选择了 "Embed & Sign"**

### 选择合适的版本

- **iOS 应用开发**：选择 `iOS_Universal_xcFramework.zip`
- **macOS 应用开发**：选择 `macOS_xcFramework.zip`
- **跨平台开发**：选择 `Apple_Universal_xcFramework.zip`

## 版本命名规范

请遵循以下命名规范：

```
im-release-v{主版本}.{次版本}.{修订版本}

示例：
- im-release-v3.8.3
- im-release-v3.8.4-patch.1
- im-release-v3.9.0
```

## 故障排除

### 构建失败

如果 GitHub Actions 构建失败：

1. 检查 Actions 页面的详细日志
2. 确认标签名称格式正确
3. 验证代码可以在本地成功构建：
   ```bash
   make ios-universal
   make macos
   make apple-universal
   ```

### 发布权限

确保有足够的权限创建 releases：
- 需要对仓库有 write 权限
- `GITHUB_TOKEN` 会自动提供

## 工作流配置

工作流配置文件位于：`.github/workflows/im-release-builder.yml`

主要特性：
- ✅ 自动版本提取
- ✅ 多平台构建
- ✅ 自动发布到 GitHub Releases
- ✅ 详细的发布说明
- ✅ 错误处理和日志
- ✅ 手动触发支持

## 示例发布流程

```bash
# 1. 确保代码已提交并推送
git add .
git commit -m "feat: prepare for release v3.8.3"
git push origin main

# 2. 创建并推送发布标签
git tag im-release-v3.8.3
git push origin im-release-v3.8.3

# 3. 等待 GitHub Actions 完成构建（通常 10-15 分钟）

# 4. 在 GitHub Releases 页面查看发布结果
```

构建完成后，用户可以直接从 GitHub Releases 页面下载对应的 xcFramework 包，无需本地编译环境。