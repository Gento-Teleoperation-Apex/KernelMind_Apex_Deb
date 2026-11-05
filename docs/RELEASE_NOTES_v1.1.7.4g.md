## v1.1.7.4g（2026-11-05）

本版本更新 **Camera / Tool 独立配置管理、Core 统一录制路径、模块状态与错误提示及升级配置保护**，并优化头部标定与遥操重新接管体验。

> Core 继续采用 **Core Deb + Common Runtime** 的安装方式，配套 **Common Runtime 1.0.4**。
>
> **Gento SDK 4.7.1 已随 Core 安装包一并交付和安装，无需独立安装 SDK。**

### 主要更新

- **模块配置独立管理**：Camera、Tool 通过各自配置页面完成参数校验、保存、应用和历史管理，Core 统一提供模块状态与启停入口。
- **统一录制路径**：录制根目录统一在系统配置中设置，配套 Camera 使用同一系统根保存视频，无需重复配置相机录像根目录。
- **更清楚的操作反馈**：模块启动、停止、重启或配置应用遇到问题时，前端可展示具体原因并提供日志入口。
- **升级配置保护**：保留已有部署配置和配置历史，自动迁移旧配置目录，并完善升级失败时的配置恢复流程。

### 控制改进

- 修复头部标定后重复叠加偏移的问题，改善静止接管时的头部目标一致性。
- 重新标定时同步更新机器人躯干、头部目标及平滑状态，避免沿用上一次遥操的目标。
- 保留双臂独立接管、松开保持和分组平滑恢复规则。

### 安装与升级

完整步骤见 [Gento Apex v1.1.7.4g 安装与升级说明](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Deb/blob/main/docs/GENTO_INSTALLATION_v1.1.7.4g.md)，包含环境准备、镜像导入、Core / Camera / Tool / UI 安装、配置应用及常见问题。

升级前请正常结束录制、回放和遥操，并按安装说明停止相关模块。保存配置后执行 Apply，再按现场操作流程恢复运行。

### 配套版本

| 组件 | 版本 / 要求 |
|---|---|
| Gento Apex Core | `v1.1.7.4g` |
| Common Runtime | [v1.0.4](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-runtime/1407924047?tag=1.0.4)，ABI `humble-r2` |
| Gento SDK | `v4.7.1`，ABI `4.7`，随 Core 一并安装，无需独立安装 |
| Camera Deb | [v1.3.3](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases)，按平台选择本版配套构建 |
| Camera Runtime | Orin：[1.0.0-orin-cuda12](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1407924750?tag=1.0.0-orin-cuda12) / Thor：[1.0.0-thor-cuda13](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1408159699?tag=1.0.0-thor-cuda13) |
| Apex Tool | [v2.0.1](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) |
| Apex UI | [v1.1.7.3](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Web_Deb/releases) |
| VR 头显 APK | [v1.1.0.3](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_VR_Apk/releases) |
