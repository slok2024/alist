# 🚀 AList Ultimate GUI (Windows 7 兼容版)

![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.8.10-3776ab.svg?style=for-the-badge&logo=python)
![Platform](https://img.shields.io/badge/Platform-Windows%207%20|%2010%20|%2011-0078d7.svg?style=for-the-badge&logo=windows)

这是一款专为 **AList** 打造的轻量化图形管理面板。本项目基于 Python 3.8.10 开发，特别针对 **Windows 7** 系统环境进行了深度兼容性优化，旨在提供一站式的 AList 内核管理、进程守护与账户配置体验。
<img width="802" height="684" alt="image" src="https://github.com/user-attachments/assets/e0fb8603-8f2b-4554-b374-62ff43400bbe" />

---

## ✨ 核心特性

* **🛠️ 智能内核匹配**：启动时自动识别系统架构（x86/AMD64），智能匹配并调用目录下的 `alist32.exe` 或 `alist64.exe`。
* **🛡️ 进程强力守护**：
    * 集成 `taskkill` 强制清理逻辑。
    * 在停止服务或关闭管理面板时，自动终结内核残留进程，确保系统无僵尸进程。
* **👤 账户便捷管理**：图形化查看管理员信息、随机重置密码、手动自定义密码或取消 2FA 认证。
* **💾 数据目录强制化**：默认开启 `--force-bin-dir` 选项，确保所有配置与数据文件均保存在程序同级目录下。
* **📝 实时日志监控**：内置带 ANSI 颜色过滤的滚动日志窗口，实时掌控内核运行状态。

---

## 🛠️ 编译指南 (Windows 7 专用)

### 1. 准备工作
* **环境依赖**：Python 3.8.10、GO 魔改版编译环境、GCC 编译环境。
* **源码准备**：
    1.  从 [AList Releases](https://github.com/AlistGo/alist/releases) 下载最新源码。
    2.  从 [AList-web Releases](https://github.com/AlistGo/alist-web/releases) 下载最新 Web 文件。
    3.  解压并将 Web 文件拷贝到源码目录的 `\public\dist` 中。

### 2. 内核编译步骤 (CGO 模式)

根据您的系统环境安装对应的 [TDM-GCC](https://jmeubank.github.io/tdm-gcc/)，并执行以下脚本：

#### **🟦 编译 32 位内核 (alist32.exe)**
```batch
:: 设置 32 位 GCC 路径 (根据实际安装路径修改)
set PATH=%PATH%;C:\TDM-GCC-32\bin
set CGO_ENABLED=1
set GOOS=windows
set GOARCH=386
set CC=gcc

:: 清理并执行正式编译
go clean -cache
go mod tidy
go build -o alist32.exe -tags="jsoniter,builtalists" -ldflags="-s -w" main.go

:: 设置 64 位 GCC 路径 (根据实际安装路径修改)
set PATH=C:\TDM-GCC-64\bin;%PATH%
set CGO_ENABLED=1
set GOOS=windows
set GOARCH=amd64
set CC=gcc

:: 清理并执行正式编译
go clean -cache
go mod tidy
go build -o alist64.exe -tags="jsoniter,builtalists" -ldflags="-s -w" main.go

📦 打包与分发
1. 使用 UPX 压缩
建议使用 UPX 对编译出的内核进行深度压缩以减少体积：

Bash
upx --best alist32.exe
upx --best alist64.exe

2. 生成管理面板 EXE在 Python 3.8.10 环境下，使用 PyInstaller 将 al.py 打包为单文件：Bashpyinstaller -F -w -i "logo.ico" --clean al.py
参数含义说明-F生成单文件将所有依赖库压缩入一个独立的 .exe-w窗口模式运行时隐藏黑色命令行窗口-i图标后面接图标路径，例如 logo.ico--clean清理缓存每次打包前清除旧的临时文件
