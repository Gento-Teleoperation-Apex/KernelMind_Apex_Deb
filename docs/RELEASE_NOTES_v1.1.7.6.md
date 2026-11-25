# Gento / Pro 统一 Core v1.1.7.6 — Release Notes

[中文](#中文) | [English](#english)

---

## 中文

- 状态：已发布（2026-11-25）
- Release tag：`v1.1.7.6`
- 基线：Gento `v1.1.7.4gento`（2026-11-05）/ Pro `v1.1.7.2pro`（2026-10-22）
- 平台：ARM64（Jetson Orin / Thor）、AMD64（x86_64）
- 本版为统一 Core：同一 Core Deb 覆盖 Gento 与 Pro，按机型与配置选择。

### Gento 线：v1.1.7.4gento → v1.1.7.6

- 新增 Skye V2 696 / V4 809 机型支持。
- 新增手部 scale 增量控制、Skye height-only，teleop modes 与 calibration 改为配置化。
- 优化绝对模式接管、头部/颈部标定与头显反馈。
- 修复 Home 在 planning 限位外或软限位过冲时无法恢复。
- 修复多轮循环回放重启失败。
- 夹爪回放保真：回放录制的夹爪命令，不再出现“录制 0.5、回放 100% 闭合”。
- 增强反馈失败、license 与模块启动诊断。

### Pro 线：v1.1.7.2pro → v1.1.7.6

- 支持独立单臂控制与平滑接管。
- B 键 / VLA 操作员介入支持 held grip：按住侧键直接介入，无需先松开再按。
- 完善 Pro 配置消费：Home、自定义支架、QP/阻抗、Tool、存储根与录制白名单。
- 兼容旧 Home 配置升级；Home 可从限位外或软限位过冲恢复。
- 修复多轮循环回放重启失败与夹爪回放过闭合。
- 增加每关节电机温度与反馈失败诊断。

### 共同说明

- 从本版起 Gento / Pro Core 合流到统一主分支，后续修复同步。
- UI / Camera / Tool 为独立组件，不包含在 Core Deb。
- 本版 Core 资产为已验收的 RC8 构建，仅统一命名，未重建。

### 配套版本

| 组件 | 版本 / 要求 | 获取入口 |
|---|---|---|
| Gento Apex Core | `v1.1.7.6`（内部包版本 `1.1.7.6-unified-rc.8` / `...amd64-rc.8`） | 本 Release 资产 |
| Common Runtime（ARM64） | `v1.0.4`，ABI `humble-r2` | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-runtime/1407924047?tag=1.0.4) |
| Common Runtime（AMD64） | `v1.0.5` | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages) |
| Gento SDK | `v4.7.1`，随 Core 一并安装，无需单独安装 | 本 Release 内置 |
| Camera Deb | [v1.3.3](https://github.com/Gento-Teleoperation-Apex/Apex_Camera_Deb/releases/tag/v1.3.3) | [Apex_Camera_Deb Releases](https://github.com/Gento-Teleoperation-Apex/Apex_Camera_Deb/releases) |
| Camera Runtime（Orin） | [1.0.0-orin-cuda12](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1407924750?tag=1.0.0-orin-cuda12) | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages) |
| Camera Runtime（Thor） | [1.0.0-thor-cuda13](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1408159699?tag=1.0.0-thor-cuda13) | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages) |
| Apex Tool | [v2.0.1](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) | [Apex-tool-deb Releases](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) |
| Apex UI | [v1.1.7.3](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Web_Deb/releases)（现场如使用更新 UI 构建，请按其自身 Release 安装） | [KernelMind_Apex_Web_Deb Releases](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Web_Deb/releases) |
| VR 头显 APK | [v1.1.0.3](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_VR_Apk/releases) | [KernelMind_Apex_VR_Apk Releases](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_VR_Apk/releases) |

### 安装与升级

完整步骤见 [安装与升级说明](docs/GENTO_INSTALLATION_v1.1.7.6.md)。升级前请结束录制、回放和遥操并停止相关模块；保存配置并 Apply 后恢复运行。

### 发布资产

| 架构 | 资产 | 大小 | SHA256 |
|---|---|---|---|
| ARM64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | 302,136,284 B | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| AMD64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | 302,427,900 B | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

> 资产按 Release 统一命名；构建产物原名为
> `..._v1.1.7.6-unified-rc.8_humble_arm64.deb` /
> `..._v1.1.7.6-unified-amd64-rc.8_humble_amd64.deb`，SHA256 不变。
>
> **版本口径（A 方案，2026-11-25 用户确认）**：发布已验收的 RC8 构建；
> Deb 内部包版本为 `1.1.7.6-unified-rc.8` / `1.1.7.6-unified-amd64-rc.8`，
> Release tag 为 `v1.1.7.6`。安装后 `dpkg-query` 显示 `...-rc.8` 属于预期。

### 验证

- Jetson Orin、Jetson Thor 与 AMD64 平台均已安装验证；Gento / Pro 核心功能与夹爪回放已实机验收。
- 本 Release 资产即上述已验收构建（仅改名，不重建）。

### 注意事项

- 共享控制器场景下，同一时间只允许一台主机运行 Robot/Teleop。
- 旧回放包若无夹爪命令 topic，仍走反馈兼容路径，可能出现过度闭合；建议使用包含命令 topic 的新包。
- Core Deb 只覆盖 Core；UI / Camera / Tool 按各自交付安装，不要混装不配套的旧包。
- 升级前备份配置与当前 Deb；回滚使用上一稳定版本 Deb。

### 发布说明

- 本 Release 在 `KernelMind_Apex_Deb` 创建，Deb 作为 release assets；
  源码 tag 在 `Apex_Deploy`，两者分开记录。
- 2026-11-25 用户确认 A 方案并授权发布。

---

## English

- Status: Published (2026-11-25)
- Release tag: `v1.1.7.6`
- Baselines: Gento `v1.1.7.4gento` (2026-11-05) / Pro `v1.1.7.2pro` (2026-10-22)
- Platforms: ARM64 (Jetson Orin / Thor), AMD64 (x86_64)
- This is a unified Core release: one Core Deb covers both Gento and Pro, selected by robot model and configuration.

### Gento line: v1.1.7.4gento → v1.1.7.6

- Added Skye V2 696 / V4 809 support.
- Added incremental hand-scale control and Skye height-only mode; teleop modes and calibration are now configuration-driven.
- Improved absolute-mode takeover, head/neck calibration and headset feedback.
- Fixed Home recovery when outside planning limits or beyond soft-limit overshoot.
- Fixed loop-replay restart failures across multiple rounds.
- Gripper replay fidelity: replay recorded gripper commands instead of “recorded 0.5 but replayed 100% closed”.
- Improved feedback-failure, license and module-startup diagnostics.

### Pro line: v1.1.7.2pro → v1.1.7.6

- Added independent single-arm control with smooth engagement.
- B-key / VLA operator intervention supports held grip: hold the side button to intervene directly.
- Completed Pro configuration consumption: Home, custom brackets, QP/impedance, Tool, storage root and recording allowlist.
- Compatible upgrade of legacy Home configuration; Home recovers from outside limits or soft-limit overshoot.
- Fixed loop-replay restart failures and gripper over-closing on replay.
- Added per-joint motor temperature and feedback-failure diagnostics.

### Common notes

- Starting with this release, Gento and Pro Core are merged onto one unified main branch, with fixes synchronized thereafter.
- UI / Camera / Tool remain independent components and are not bundled in the Core Deb.
- The Core assets are the accepted RC8 builds, renamed only and not rebuilt.

### Companion versions

| Component | Version / requirement | Where to get it |
|---|---|---|
| Gento Apex Core | `v1.1.7.6` (internal package version `1.1.7.6-unified-rc.8` / `...amd64-rc.8`) | Assets of this Release |
| Common Runtime (ARM64) | `v1.0.4`, ABI `humble-r2` | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-runtime/1407924047?tag=1.0.4) |
| Common Runtime (AMD64) | `v1.0.5` | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages) |
| Gento SDK | `v4.7.1`, installed with Core, no separate install | Bundled in this Release |
| Camera Deb | [v1.3.3](https://github.com/Gento-Teleoperation-Apex/Apex_Camera_Deb/releases/tag/v1.3.3) | [Apex_Camera_Deb Releases](https://github.com/Gento-Teleoperation-Apex/Apex_Camera_Deb/releases) |
| Camera Runtime (Orin) | [1.0.0-orin-cuda12](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1407924750?tag=1.0.0-orin-cuda12) | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages) |
| Camera Runtime (Thor) | [1.0.0-thor-cuda13](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1408159699?tag=1.0.0-thor-cuda13) | [GitHub Packages](https://github.com/orgs/Gento-Teleoperation-Apex/packages) |
| Apex Tool | [v2.0.1](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) | [Apex-tool-deb Releases](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) |
| Apex UI | [v1.1.7.3](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Web_Deb/releases) (if a newer site build is used, install it from its own release) | [KernelMind_Apex_Web_Deb Releases](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Web_Deb/releases) |
| VR headset APK | [v1.1.0.3](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_VR_Apk/releases) | [KernelMind_Apex_VR_Apk Releases](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_VR_Apk/releases) |

### Installation and upgrade

See the [installation and upgrade guide](docs/GENTO_INSTALLATION_v1.1.7.6.md). Before upgrading, finish recording, replay and teleoperation and stop the relevant modules; save and apply the configuration, then resume operation.

### Release assets

| Architecture | Asset | Size | SHA256 |
|---|---|---|---|
| ARM64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | 302,136,284 B | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| AMD64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | 302,427,900 B | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

> Assets are named uniformly for this Release. Original build artifacts were
> `..._v1.1.7.6-unified-rc.8_humble_arm64.deb` /
> `..._v1.1.7.6-unified-amd64-rc.8_humble_amd64.deb`; SHA256 is unchanged.
>
> **Version convention (Plan A, confirmed by the user on 2026-11-25)**: the
> accepted RC8 builds are published; internal package versions are
> `1.1.7.6-unified-rc.8` / `1.1.7.6-unified-amd64-rc.8`, while the Release tag is
> `v1.1.7.6`. `dpkg-query` showing `...-rc.8` after installation is expected.

### Verification

- Installed and verified on Jetson Orin, Jetson Thor and AMD64; core Gento / Pro functions and gripper replay were accepted on real hardware.
- The assets of this Release are the accepted builds described above (renamed only, not rebuilt).

### Notes

- With a shared controller, only one host may run Robot/Teleop at a time.
- Old replay bags without a gripper command topic still use the feedback fallback path and may over-close; use new bags that contain the command topic.
- The Core Deb covers Core only; install UI / Camera / Tool from their own deliveries, and do not mix incompatible older packages.
- Back up the configuration and current Deb before upgrading; roll back with the previous stable Deb.

### Publication notes

- This Release is created in `KernelMind_Apex_Deb`, with the Debs as release assets;
  the source tag lives in `Apex_Deploy` and is recorded separately.
- The user confirmed Plan A and authorized publication on 2026-11-25.
