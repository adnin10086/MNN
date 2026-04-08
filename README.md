# MNN Inference(MNN-Cli)

> 本项目从 [Alibaba/MNN](https://github.com/alibaba/MNN) 项目中剥离 `MNNCli` 组件，旨在构建一个轻量级、易部署的推理服务器。

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux-lightgrey.svg)]()
[![Architecture](https://img.shields.io/badge/Architecture-x86__64-orange.svg)]()

## 📖 项目简介

本项目致力于解决原版 MNN 在特定场景下部署过重、编译复杂的问题。通过剥离核心推理组件，我们提供了一个专注于服务器端部署的解决方案。

## 🎯 一期目标 (Phase 1)

本项目第一阶段主要聚焦于以下核心问题的解决与优化：

### 1. 仓库精简

- 移除不必要的依赖与模块，显著减小仓库体积，提升克隆与编译效率。

### 2. 多平台编译支持 (X86 架构)

重点解决 **X86 架构** 下 **Windows** 和 **Linux** 环境的编译兼容性问题。

> **注意**：Apple 芯片 (ARM) 的 MacOS 支持为低优先级；**不支持** x86 架构的 MacOS 编译。

#### 支持的环境列表

| 操作系统 | 版本/环境 | 架构 | 状态 | 编译指引 |
| :--- | :--- | :--- | :--- | :--- |
| **Windows** | Windows 11 + VS2022 | x64 | ✅ 支持 | [指引](./docs/compile/mnn-windows-vs2022-build-guide.md) |
| **Windows** | Windows 11 + VS2026 | x64 | ✅ 支持 | [指引](./docs/compile/mnn-windows-vs2026-build-guide.md) |
| **CentOS** | 7 | x64 | ❌ 不支持 | |
| **CentOS** | 8 | x64 | ❌ 不支持 | |
| **CentOS** | Stream | x64 | ❌ 不支持 | |
| **Rocky Linux** | 9 | x64 | ❌ 不支持 | |
| **Rocky Linux** | 10 | x64 | ❌ 不支持 | |
| **Ubuntu** | 20.04 | x64 | ❌ 不支持 | |
| **Ubuntu** | 22.04 | x64 | ❌ 不支持 | |
| **Ubuntu** | 24.04 | x64 | ❌ 不支持 | |
| **Ubuntu** | 26.04 | x64 | ❌ 不支持 | |
| **Debian** | 11 | x64 | ❌ 不支持 | |
| **Debian** | 12 | x64 | ❌ 不支持 | |

### 3. 媒体格式支持

- **[未解决] Omni 模型**：修复不支持读取 **WebP** 格式图片的问题。
- **[未解决] 音视频处理**：针对音频和视频输入，计划采用 `ffmpeg` 进行强制转码以统一格式。

### 4. HTTP/HTTPS 接口完善

- **[未解决] 身份鉴权**：支持 API 访问鉴权机制。
- **[未解决] 安全传输**：原生支持 HTTPS 协议。
- **[未解决] 用量统计**：(低优先级) 支持请求消耗量统计。

---

## 🛠 技术栈说明

- **关于 GoLang 的说明**:
  鉴于 GoLang 调用 C++ 库的复杂性与性能开销，本项目**不计划**改用 GoLang 重写 HTTP 服务器部分，坚持使用 C++ 以保持链路统一与性能最优。

---

## 🚀 使用指引

编译完成后，您可以按照以下步骤启动服务并进行调用。

### 1. 启动服务

可执行文件通常位于 `build/` 或 `bin/` 目录下。使用命令行参数配置模型路径和服务端口。

```bash
# Linux 示例
mnncli -c ./models/llm_config.json --host 0.0.0.0 --port 8080 --auth_key "your_secret_key"

# Windows 示例
mnncli -c ./models/llm_config.json --host 0.0.0.0 --port 8080 --auth_key "your_secret_key"
```

---

## 📚 编译指引

请根据您的操作系统环境选择对应的编译指南：


### Windows

1. [Windows 11 + VS2022 x64 编译指南](./docs/build_win11_vs2022.md)
2. [Windows 11 + VS2026 x64 编译指南](./docs/build_win11_vs2026.md)

### Linux - CentOS/Rocky

3. [CentOS 7 x64 编译指南](./docs/build_centos7.md)
4. [CentOS 8 x64 编译指南](./docs/build_centos8.md)
5. [CentOS Stream x64 编译指南](./docs/build_centos_stream.md)
6. [Rocky Linux 9 x64 编译指南](./docs/build_rocky9.md)
7. [Rocky Linux 10 x64 编译指南](./docs/build_rocky10.md)

### Linux - Ubuntu/Debian

8. [Ubuntu 20.04 x64 编译指南](./docs/build_ubuntu2004.md)
9. [Ubuntu 22.04 x64 编译指南](./docs/build_ubuntu2204.md)
10. [Ubuntu 24.04 x64 编译指南](./docs/build_ubuntu2404.md)
11. [Ubuntu 26.04 x64 编译指南](./docs/build_ubuntu2604.md)
12. [Debian 11 x64 编译指南](./docs/build_debian11.md)
13. [Debian 12 x64 编译指南](./docs/build_debian12.md)
    

---

## 🔗 原始项目参考

本项目基于 [Alibaba/MNN](https://github.com/alibaba/MNN)  开发，更多底层技术细节请参考原始项目文档：

- [English (Readme_EN.md)](https://github.com/alibaba/MNN/blob/master/README.md)
- [简体中文 (Readme_CN.md)](https://github.com/alibaba/MNN/blob/master/README_CN.md)
- [日本語 (Readme_JP.md)](https://github.com/alibaba/MNN/blob/master/README_JP.md)

---

## ☕ 赞助与支持

如果本项目对您的工作或研究有所帮助，欢迎通过 **GitHub Sponsors** 支持我，或者帮忙向社区推广本项目。

> 💡 如果阿里巴巴官方愿意赞助，那就最好了 [狗头]

---
