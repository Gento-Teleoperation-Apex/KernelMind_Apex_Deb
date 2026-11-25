# Gento / Pro 统一 Core v1.1.7.6 — 安装与升级说明

- 状态：已发布（2026-11-25）
- 适用 Release：`KernelMind_Apex_Deb` `v1.1.7.6`
- Core 版本：用户已验收的 RC8 构建
  - ARM64 内部包版本：`1.1.7.6-unified-rc.8`
  - AMD64 内部包版本：`1.1.7.6-unified-amd64-rc.8`

## 1. 资产

| 平台 | 资产 | SHA256 |
|---|---|---|
| Jetson Orin / Thor（ARM64） | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| x86_64（AMD64，166） | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

核心 Deb 已内置配套 Common Runtime 与 Gento SDK，无需单独安装 SDK。
UI、Camera、Tool 是独立组件，不包含在本 Core Deb 中。

## 2. 安装前检查

```bash
# 当前版本/活动 release
dpkg-query -W -f='${Version}\n' gento-apex
readlink -f /opt/kernelmind/apex/current

# 备份配置和当前 Deb（示例）
sudo cp -a ~/.tj_config/apex-beta ~/apex_config_backup_$(date +%Y%m%d-%H%M%S)
sudo cp -a /etc/apex/apex.env ~/apex_env_backup_$(date +%Y%m%d-%H%M%S)
```

- 机器人静止、急停可触及、控制源唯一。
- 100 与 166 共用 190 控制器：同一时间只允许一台主机运行 Robot/Teleop。
  166 安装/验收时保持其 Robot/Teleop 停止。
- 建议在维护窗口内停止 Robot/Teleop/Tool 后安装 Core。

## 3. 安装/升级

### ARM64（100 / 121）

```bash
sudo apt install ./Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb
# 或
sudo dpkg -i Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb
sudo apt install -f   # 如有依赖
```

### AMD64（166）

```bash
sudo apt install ./Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb
```

安装后按模块管理方式启动 Backend/Robot/Teleop/Tool；100 上 Tool 使用
`apex-tool-overlay.service` 管理：

```bash
sudo systemctl status apex-tool-overlay.service
sudo systemctl start apex-tool-overlay.service
```

不要用 `docker stop apex-tool-module` 代替 systemd 管理，否则会杀死
overlay 主进程。

## 4. 安装后核验

```bash
# 包身份
dpkg-query -W -f='${db:Status-Status} ${Version}\n' gento-apex
readlink -f /opt/kernelmind/apex/current

# Backend/模块健康
curl -fsS http://127.0.0.1:8080/api/v1/system/health
curl -fsS http://127.0.0.1:8080/api/v1/robot/status
```

期望：

- 100：Backend/Robot/Teleop/Tool running，aggregate True，config 8/8；
- 121：Backend healthy，config 18/18，Robot/Teleop 保持停止；
- 166：Backend healthy，config 4/4，profiles preserved，Robot/Teleop 停止；
- 活动 release 指向本次安装的版本。

## 5. 回滚

```bash
# 停止 Robot/Teleop（按现场方式）
sudo dpkg -i <上一稳定版本 Deb>
readlink -f /opt/kernelmind/apex/current
```

配置备份在 `~/apex_config_backup_*`；不要删除旧 release 与旧 Deb，直到验收完成。

## 6. 注意

- Core Deb 只覆盖 Core；UI/Camera/Tool 需按各自交付安装。
- RC8 的夹爪修复只对回放了 `gripperValue` 命令 topic 的 bag 生效；
  旧 bag 的反馈兼容路径仍可能过闭合。
- 100/166 不要同时运行 Robot/Teleop，避免 190 控制器抢占。
- 本 Release 采用 A 方案：资产是用户已验收的 RC8 构建，
  `dpkg-query` 会显示 `1.1.7.6-unified-rc.8` / `1.1.7.6-unified-amd64-rc.8`；
  Release tag 为 `v1.1.7.6`，不重建、不改包内容。
