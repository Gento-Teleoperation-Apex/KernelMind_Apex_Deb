# KernelMind_Apex_Deb

**KernelMind 遥操系统与 Apex 遥操作平台 ARM64 / AMD64 Debian 包集合**

> 本仓库收录了 KernelMind 舞肌灵巧手机器手控制及 VR 遥操作核心软件的 Debian (.deb) 安装包，方便在机器人、教育、科研等场景中一键部署全部运行环境。

## Gento Apex v1.1.7.6

- [安装与升级说明](docs/GENTO_INSTALLATION_v1.1.7.6.md)
- [Release Notes](docs/RELEASE_NOTES_v1.1.7.6.md)

| 资产 | 平台 | SHA256 |
|---|---|---|
| `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | Jetson Orin/Thor（ARM64） | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | x86_64（AMD64） | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

本版 Core 已内置配套 Common Runtime 与 Gento SDK，无需单独安装 SDK。UI、Camera、Tool 为独立组件，请按各自交付安装。

> 资产是用户已验收的 RC8 构建；Deb 内部版本为 `1.1.7.6-unified-rc.8` / `1.1.7.6-unified-amd64-rc.8`，Release tag 为 `v1.1.7.6`。安装后 `dpkg-query` 显示 `...-rc.8` 属于预期。

上一版本：[v1.1.7.4g 安装说明](docs/GENTO_INSTALLATION_v1.1.7.4g.md) / [Release Notes](docs/RELEASE_NOTES_v1.1.7.4g.md)

[中文](#中文) | [English](#english)

---

## 中文

### 📝 项目简介

- 提供 marvin pro机械臂 + 舞肌灵巧手的核心驱动和遥操作控制系统。
- 支持 VR 头显人机交互、实时远程视频与二次开发。
- 针对 ARM64 NVIDIA Jetson开发。

### 📦 已含主要 Deb 包

| 包名                  | 版本   | 架构 | 描述                     |
|---------------------|-------|------|------------------------|
| apex-teleop         | 0.1.0 | ARM64| VR遥操作系统上位机主程序，实时视频流、远程遥控 |
| kernelmind-apex     | 1.0.4 | ARM64| KernelMind Apex 遥操系统 |
| kernelmind-wujihand | 1.0.3 | ARM64| 舞肌灵巧手驱动与控制库     |
| wujihandcpp         | 1.5.1 | ARM64| 舞肌灵巧手 C++ 开发接口    |

### 🚀 安装说明

#### 系统环境要求

- **架构**: ARM64 (aarch64)
- **系统**: Ubuntu 22.04
- **存储**: 至少 300MB

#### 步骤

1. 从release页面下载最新deb包

2. 安装遥操作主程序 `.deb` 包
   ```bash
   sudo apt install ./kernelmind-apex_x.x.x_arm64.deb
   ```
3. 如需安装 wuji 扩展包
   ```bash
      sudo apt install ./wujihandcpp-1.5.1-arm64.deb
      sudo apt install ./kernelmind-wujihand_1.0.3_arm64.deb
   ```
4. 自动修复依赖（如有）
   ```bash
   sudo apt update
   sudo apt install -f
   ```

#### 卸载

```bash
sudo apt remove apex-teleop kernelmind-apex kernelmind-wujihand wujihandcpp
```

### ⚙️ 功能亮点

- 🎮 支持 VR 头显（如 Pico、Meta）遥控机械臂与灵巧手
- 🦾 提供完整机械臂控制驱动栈
- 🖐️ 多指灵巧手协调控制
- 📹 实时视觉/视频流返回
- 🧰 C++/Python 二次开发友好接口

### 🚦 常见问题排查

| 问题                     | 解决方法                      |
|------------------------|-----------------------------|
| dpkg: dependency problems | 运行 `sudo apt install -f` |
| 系统架构不兼容             | 用 `uname -m` 检查, 必须为 `aarch64`|
| 安装包损坏                | 重新下载相应 .deb 文件         |
| 权限错误                  | 装包命令加 `sudo`            |

### 🔗 相关信息

- **支持系统**: ARM64 Linux
- **开发语言**: C++/Python（仓库为二进制发行）
- **应用场景**: 机器人本体控制、远程 VR 遥操作、教育科研

---

