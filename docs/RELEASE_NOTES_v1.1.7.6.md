# Gento / Pro 统一 Core v1.1.7.6 — Release Notes

- 状态：已发布（2026-11-25）
- Release tag：`v1.1.7.6`（`KernelMind_Apex_Deb`）
- 源码：
  - ARM64 主分支 `feature/pro-gento-unified` @ `7db5c1a`（产品源码 = RC8 `afb3cb9`）
  - AMD64 主分支 `feature/pro-gento-unified-amd` @ `0afc639`
  - 源码 tag：`v1.1.7.6-unified-rc.9` @ `41b17c0`
- 基线（上一公开发布）：
  - Gento 线：`v1.1.7.4gento`（Core `v1.1.7.4g`，2026-11-05）
  - Pro 线：`v1.1.7.2pro`（Core `v1.1.7.2`，2026-10-22）
- 配套：Core Deb + Common Runtime
  - ARM64：Runtime `1.0.4` / Humble / Gento SDK `4.7.1`
  - AMD64：Runtime `1.0.5` / Humble / Gento SDK `4.7.1`

## 版本定位

`v1.1.7.6` 是 `v1.1.7.4gento` 与 `v1.1.7.2pro` 之后的**统一 Core** 版本：同一个 Core Deb 同时覆盖 Gento 与 Pro，按机器人和配置选择；ARM64 与 AMD64 分别交付。以下按两条上一公开版本分别列出增量。

## 一、Gento 线：v1.1.7.4gento → v1.1.7.6

### 遥操与机型

- 新增 **Skye V2 696 / V4 809** 遥操支持。
- 新增手部 scale 增量控制与 Skye height-only 配置。
- teleop modes 与 calibration 改为配置化。
- 绝对模式 hook 与头显反馈对齐 QP 躯干映射。
- 颈部模式去除重复头部偏移；重新标定时重置颈部目标平滑。
- Skye V1 / Luna V2 头部限位与 URDF 对齐，避免使能瞬间移动。

### Home / QP / 控制器

- QP takeover seed 与 planning 限位对齐。
- Home 可从 planning bounds 外、软限位过冲的有效位姿恢复。
- 限制重复 robot feedback sync 日志。
- 跳过不必要的 Gento 启动故障重置。
- 放宽 Pro 控制器家族校验，支持统一 Core 下的 Pro 机型。

### 诊断与启动

- Robot 反馈失败详细诊断。
- Teleop license 启动诊断。
- 部署 profile 选择修复；兼容 stock CycloneDDS publisher metadata；schema ownership metadata 修正。

### 录制 / 回放 / VLA

- 循环回放 `FEEDBACK_SAMPLE` 有界重试，修复多轮循环 restart 失败。
- 夹爪回放保真：回放录制的 `/control/gripperValueL/R`，不再把 `gripper_feedback` 电机位置当命令，修复“录制 0.5、回放 100% 闭合”。
- VLA 介入源恢复与 action dispatch 有界化；按 robot family 路由介入录制。
- `joint_cmd_mux` `source_action` 请求改为有界账本，避免容量失败。

### 交付与集成

- Backend 并入 `Apex_Deploy`；Tool profile apply 修正；Core 安装兼容修正。
- Gento mesh 去重；Luna V3 615 基础模型更新。
- Core 与 Camera / Tool 独立配置和交付。
- Fast CI 迁至 124 runner 并自动 PASS（源码门禁，不改变产品包）。

## 二、Pro 线：v1.1.7.2pro → v1.1.7.6

### 遥操

- 独立单臂控制与平滑接管。
- B 键 / VLA 操作员介入支持 **held grip**：按住侧键可直接介入，无需先松开再按。
- 启用按键丢失修复；绝对模式 hook 去除多余限位和姿态门控。
- 颈部模式重复偏移修复；重标定后重置颈部目标平滑。

### Home / 规划

- Pro 独立 Planner authority，`MoveJ` / `Home` 有界。
- Pro 完整配置消费：Home 位置/速度/加速度、自定义支架、QP 阻尼/任务代价、阻抗、Tool、存储根、录制白名单。
- Home 升级兼容：旧 Home 参数可读可纠正；candidate/applied 两份 user stand 独立迁移；3600s 轨迹上限。
- Home 从 planning bounds 外、软限位过冲恢复。

### 回放 / 夹爪

- 循环回放 `FEEDBACK_SAMPLE` 有界重试。
- 夹爪回放保真（同 Gento 线）；旧 bag 无 `gripperValue` 命令 topic 时仍走反馈兼容路径。

### 诊断 / 交付

- 每关节电机温度从 SDK 反馈发布。
- Robot 反馈失败详细诊断、Teleop license 诊断。
- 统一 Core：Pro 与 Gento 使用同一 Core 包与主分支；Core 与 Camera / Tool 独立交付。

## 三、两条线共同说明

- 从 `v1.1.7.6` 起 Gento / Pro 的 Core 修复同步到同一条 unified 主分支。
- UI / Camera / Tool 仍是独立组件，不包含在 Core Deb。
- Fast CI（ARM64）：自动 run `45545287568` **PASS**（124 runner，228s，14/14 套件 rc=0，artifact 上传成功）；100 runner 手动 run 2514s PASS 作为兜底证据。
- RC9 是 RC8 合并回 ARM64 主分支的源码门禁版本，不产生新 Deb。

## 发布资产

| 架构 | 资产 | 大小 | SHA256 |
|---|---|---|---|
| ARM64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | 302,136,284 B | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| AMD64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | 302,427,900 B | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

> 资产按 Release 统一命名；实际构建产物原名为
> `..._v1.1.7.6-unified-rc.8_humble_arm64.deb` /
> `..._v1.1.7.6-unified-amd64-rc.8_humble_amd64.deb`，SHA256 不变。
>
> **版本口径（A 方案，2026-11-25 用户确认）**：直接发布用户已验收的 RC8
> 构建；Deb 内部包版本为 `1.1.7.6-unified-rc.8` /
> `1.1.7.6-unified-amd64-rc.8`，Release tag 使用 `v1.1.7.6`。资产只做
> 正式命名，不重建、不改包内容；安装后 `dpkg-query` 会显示 `...-rc.8`，
> 这是预期行为。

## 验证状态

- 100（ARM64）：安装 RC8；Backend/Robot/Teleop/Tool running，aggregate True，
  config 8/8，Home 1.9。
- 121（ARM64）：安装 RC8；Backend healthy，config 18/18，Robot/Teleop 保持停止。
- 166（AMD64）：安装 RC8；Backend healthy，config 4/4，profiles preserved，
  Robot/Teleop 保持停止。
- 夹爪回放修复：用户 2026-11-25 报告 100/121/166 实机验收通过；
  CLI 未记录具体测量值。
- 本 Release 资产即上述已验收 RC8 构建（只改正式资产名，不重建）。

## 已知限制 / 安装注意

- 100 与 166 共用 190 控制器：同一时间只能一台主机运行 Robot/Teleop；
  166 安装/验收时其 Robot/Teleop 应保持停止。
- 121/166 无真实夹爪物理验收；夹爪回放验收在 100 完成。
- Core Deb 只覆盖 Core；UI、Camera、Tool 按各自交付安装，不能混装旧包。
- 旧 bag 若无 `gripperValue` 命令 topic，回放仍走反馈兼容路径；DM 夹爪上
  该路径可能过闭合，建议使用包含命令 topic 的新 bag。
- 安装/升级前备份现有配置与当前 Deb；回滚使用上一稳定版本 Deb。

## 安装/升级

见 `docs/GENTO_INSTALLATION_v1.1.7.6.md`：环境、备份、安装、
版本/健康检查、回滚、190 控制器注意事项。

## 发布说明

- 本 Release 在 `KernelMind_Apex_Deb` 创建，Deb 作为 release assets；
  源码 tag 在 `Apex_Deploy`，两者分开记录。
- 2026-11-25 用户确认 A 方案并授权发布；本文件随 Release 一并公开。
