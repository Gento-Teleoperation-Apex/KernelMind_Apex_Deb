# Gento / Pro 统一 Core v1.1.7.6 — Installation & Upgrade Guide

[中文](#中文) | [English](#english)

---

## 中文

### Gento / Pro 统一 Core v1.1.7.6 — 安装与升级说明

- 状态：已发布（2026-11-25）
- 适用 Release：`KernelMind_Apex_Deb` `v1.1.7.6`
- Core 版本：已验收的 RC8 构建
  - ARM64：`1.1.7.6-unified-rc.8`
  - AMD64：`1.1.7.6-unified-amd64-rc.8`

#### 1. 资产

| 平台 | 资产 | SHA256 |
|---|---|---|
| Jetson Orin / Thor（ARM64） | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| x86_64（AMD64） | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

核心 Deb 已内置配套 Common Runtime 与 Gento SDK，无需单独安装 SDK。
UI、Camera、Tool 是独立组件，不包含在本 Core Deb 中。

#### 2. 安装前检查

```bash
# 当前版本/活动 release
dpkg-query -W -f='${Version}\n' gento-apex
readlink -f /opt/kernelmind/apex/current

# 备份配置和当前 Deb（示例）
sudo cp -a ~/.tj_config/apex-beta ~/apex_config_backup_$(date +%Y%m%d-%H%M%S)
sudo cp -a /etc/apex/apex.env ~/apex_env_backup_$(date +%Y%m%d-%H%M%S)
```

- 机器人静止、急停可触及、控制源唯一。
- 共享控制器时，同一时间只允许一台主机运行 Robot/Teleop。
- 建议在维护窗口内停止 Robot/Teleop/Tool 后安装 Core。

#### 3. 安装/升级

ARM64（Jetson Orin / Thor）：

```bash
sudo apt install ./Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb
# 或
sudo dpkg -i Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb
sudo apt install -f   # 如有依赖
```

AMD64（x86_64）：

```bash
sudo apt install ./Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb
```

安装后按模块管理方式启动 Backend/Robot/Teleop/Tool。使用 Tool overlay 的现场，
通过 systemd 管理：

```bash
sudo systemctl status apex-tool-overlay.service
sudo systemctl start apex-tool-overlay.service
```

不要用 `docker stop apex-tool-module` 代替 systemd 管理，否则会杀死 overlay 主进程。

#### 4. 安装后核验

```bash
# 包身份
dpkg-query -W -f='${db:Status-Status} ${Version}\n' gento-apex
readlink -f /opt/kernelmind/apex/current

# Backend/模块健康
curl -fsS http://127.0.0.1:8080/api/v1/system/health
curl -fsS http://127.0.0.1:8080/api/v1/robot/status
```

期望：活动 release 指向本次安装的版本；Backend 健康；机器人、遥操、Tool
按现场配置处于预期运行状态，配置 revision 与安装前一致或按现场重新 Apply。

#### 5. 回滚

```bash
# 停止 Robot/Teleop（按现场方式）
sudo dpkg -i <上一稳定版本 Deb>
readlink -f /opt/kernelmind/apex/current
```

配置备份在 `~/apex_config_backup_*`；不要删除旧 release 与旧 Deb，直到验收完成。

#### 6. 注意

- Core Deb 只覆盖 Core；UI/Camera/Tool 需按各自交付安装，不要混装不配套的旧包。
- 夹爪回放修复只对回放了 `gripperValue` 命令 topic 的 bag 生效；
  旧 bag 的反馈兼容路径仍可能过闭合。
- 共享控制器时不要同时运行多台主机的 Robot/Teleop，避免控制器抢占。
- 本 Release 采用 A 方案：资产是已验收的 RC8 构建，
  `dpkg-query` 会显示 `1.1.7.6-unified-rc.8` / `1.1.7.6-unified-amd64-rc.8`；
  Release tag 为 `v1.1.7.6`，不重建、不改包内容。

---

## English

### Gento / Pro Unified Core v1.1.7.6 — Installation and Upgrade Guide

- Status: Published (2026-11-25)
- Applies to: `KernelMind_Apex_Deb` `v1.1.7.6`
- Core version: accepted RC8 builds
  - ARM64: `1.1.7.6-unified-rc.8`
  - AMD64: `1.1.7.6-unified-amd64-rc.8`

#### 1. Assets

| Platform | Asset | SHA256 |
|---|---|---|
| Jetson Orin / Thor (ARM64) | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| x86_64 (AMD64) | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

The Core Deb carries the matching Common Runtime and Gento SDK; no separate SDK
installation is required. UI, Camera and Tool are independent components and are
not included in this Core Deb.

#### 2. Pre-installation checks

```bash
# Current version / active release
dpkg-query -W -f='${Version}\n' gento-apex
readlink -f /opt/kernelmind/apex/current

# Back up configuration and the current Deb (example)
sudo cp -a ~/.tj_config/apex-beta ~/apex_config_backup_$(date +%Y%m%d-%H%M%S)
sudo cp -a /etc/apex/apex.env ~/apex_env_backup_$(date +%Y%m%d-%H%M%S)
```

- Robot at rest, emergency stop reachable, single control source.
- With a shared controller, only one host may run Robot/Teleop at a time.
- Prefer installing Core in a maintenance window with Robot/Teleop/Tool stopped.

#### 3. Installation / upgrade

ARM64 (Jetson Orin / Thor):

```bash
sudo apt install ./Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb
# or
sudo dpkg -i Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb
sudo apt install -f   # if dependencies are reported
```

AMD64 (x86_64):

```bash
sudo apt install ./Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb
```

After installation, start Backend/Robot/Teleop/Tool through the module management
flow used on site. Where the Tool overlay is used, manage it with systemd:

```bash
sudo systemctl status apex-tool-overlay.service
sudo systemctl start apex-tool-overlay.service
```

Do not use `docker stop apex-tool-module` instead of systemd management; it kills
the overlay main process.

#### 4. Post-installation checks

```bash
# Package identity
dpkg-query -W -f='${db:Status-Status} ${Version}\n' gento-apex
readlink -f /opt/kernelmind/apex/current

# Backend / module health
curl -fsS http://127.0.0.1:8080/api/v1/system/health
curl -fsS http://127.0.0.1:8080/api/v1/robot/status
```

Expected: the active release points to the installed version; Backend is healthy;
Robot, teleoperation and Tool are in the expected state for the site
configuration, with the configuration revision unchanged or re-applied as needed.

#### 5. Rollback

```bash
# Stop Robot/Teleop (per site procedure)
sudo dpkg -i <previous stable Deb>
readlink -f /opt/kernelmind/apex/current
```

Configuration backups are under `~/apex_config_backup_*`; keep old releases and
Debs until acceptance is complete.

#### 6. Notes

- The Core Deb covers Core only; install UI/Camera/Tool from their own deliveries
  and do not mix incompatible older packages.
- The gripper replay fix applies to bags that replay the `gripperValue` command
  topic; old bags using the feedback fallback may still over-close.
- With a shared controller, do not run Robot/Teleop on multiple hosts at once.
- This Release follows Plan A: the assets are the accepted RC8 builds, so
  `dpkg-query` shows `1.1.7.6-unified-rc.8` / `1.1.7.6-unified-amd64-rc.8`
  while the Release tag is `v1.1.7.6`. Packages are not rebuilt or modified.
