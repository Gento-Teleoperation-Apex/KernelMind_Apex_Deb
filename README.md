# KernelMind_Apex_Deb

**KernelMind 遥操系统与 Apex 遥操作平台 ARM64 Debian 包集合**

> 本仓库收录了 KernelMind 舞肌灵巧手机器手控制及 VR 遥操作核心软件的 ARM64 Debian (.deb) 安装包，方便在机器人、教育、科研等场景中一键部署全部运行环境。

## Gento Apex v1.1.7.4g

- [完整安装与升级说明](docs/GENTO_INSTALLATION_v1.1.7.4g.md)：Core、Common Runtime、Camera、Tool 和 UI 的安装、配置及检查。
- [Release Notes](docs/RELEASE_NOTES_v1.1.7.4g.md)：版本更新与配套组件下载入口。

本版配套 Common Runtime `1.0.4`，Gento SDK `4.7.1` 随 Core 一并安装，无需独立安装。当前 Gento 部署请按以上版本说明执行；后文保留早期组件的通用说明。

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

