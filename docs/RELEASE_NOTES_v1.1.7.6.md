# Gento / Pro 统一 Core v1.1.7.6 — Release Notes

- 状态：已发布（2026-11-25）
- Release tag：`v1.1.7.6`（`KernelMind_Apex_Deb`）
- 源码：`Apex_Deploy`
  - ARM64 主分支 `feature/pro-gento-unified`
  - AMD64 主分支 `feature/pro-gento-unified-amd`
  - 已验收构建源码：ARM `v1.1.7.6-unified-rc.8` @ `afb3cb9`；
    AMD `v1.1.7.6-unified-amd64-rc.8` @ `0afc639`
- 配套：Core Deb + Common Runtime
  - ARM64：Runtime `1.0.4` / Humble / Gento SDK `4.7.1`
  - AMD64：Runtime `1.0.5` / Humble / Gento SDK `4.7.1`

## 发布资产

| 架构 | 资产 | 大小 | SHA256 |
|---|---|---|---|
| ARM64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_arm64.deb` | 302,136,284 B | `cf33f844e1ba8d547e02a6621dd04b2f9b3780e3ea35e47349a0b2b1b7201e48` |
| AMD64 | `Gento-Teleoperation-Apex_v1.1.7.6_humble_amd64.deb` | 302,427,900 B | `d3f9efe10802198ee0f045dd1b254de0e00b6fe363b79d08d1ac8c096dea4ce2` |

> 资产文件名已按 Release 统一命名；实际构建产物原名为
> `..._v1.1.7.6-unified-rc.8_humble_arm64.deb` /
> `..._v1.1.7.6-unified-amd64-rc.8_humble_amd64.deb`，SHA256 不变。
>
> **版本口径（A 方案，2026-11-25 用户确认）**：直接发布用户已验收的 RC8
> 构建；Deb 内部包版本为 `1.1.7.6-unified-rc.8` /
> `1.1.7.6-unified-amd64-rc.8`，Release tag 使用 `v1.1.7.6`。资产只做
> 正式命名，不重建、不改包内容；安装后 `dpkg-query` 会显示 `...-rc.8`，
> 这是预期行为。

## 主要更新（RC1–RC8）

### Pro / 控制器

- **RC1**：Gento QP takeover seed 与 planning 限位对齐。
- **RC2**：Gento 多机型支持（Skye V2 696 / V4 809）、hand scale 与 Skye
  height-only 配置、teleop modes/calibration 配置化、Home 软限位恢复、
  启动故障重置、反馈/许可诊断、Gento mesh 去重、QP 反馈日志节流。
- **RC4**：`joint_cmd_mux` 的 `source_action` 请求改为有界账本，避免 65536
  容量失败；vlahost 按 robot family 路由介入录制/源恢复；Backend 并入
  Apex_Deploy；Pro controller 校验与 Core 打包修正。
- **RC5**：Pro 操作员介入 held grip：B 键/VLA 介入时可按住侧键直接进入，
  无需先松开再按。
- **RC6**：Pro 完整配置消费链路：Home 位置/速度/加速度、自定义支架、
  QP 阻尼/任务代价、阻抗、Tool、存储根、录制白名单；含 RC6 Home 修复。
- **RC7**：Home 升级兼容（旧 Home 参数可读可纠正、candidate/applied 两份
  user stand 独立迁移、3600s 轨迹上限）；循环回放 `FEEDBACK_SAMPLE`
  有界重试。
- **RC8**：夹爪回放保真：回放录制的 `/control/gripperValueL/R` 命令，
  不再把 `gripper_feedback` 电机位置当命令，修复“录制 0.5、回放 100% 闭合”。

### UI / 其他组件

- Core Deb **不包含 UI**。RC7 期间的 UI 循环重启修复属于独立 UI 线
  （`Apex_Frontend` tag `v1.1.7.5-rtppeers2`），请按 UI 交付单独安装。
- Camera / Tool 仍为独立 Deb/模块，不随 Core Deb 覆盖。

## 验证状态

- 100（ARM64）：安装 RC8；Backend/Robot/Teleop/Tool running，aggregate True，
  config 8/8，Home 1.9。
- 121（ARM64）：安装 RC8；Backend healthy，config 18/18，Robot/Teleop 保持停止。
- 166（AMD64）：安装 RC8；Backend healthy，config 4/4，profiles preserved，
  Robot/Teleop 保持停止。
- 夹爪回放修复：用户 2026-11-25 报告 100/121/166 实机验收通过；
  CLI 未记录具体测量值。
- Fast CI（ARM64）：GitHub 自动 run `45545287568` **PASS**（124 runner，
  228s，14/14 套件 rc=0，artifact 上传成功）；100 runner 手动 run 亦 PASS
  （2514s）作为兜底证据。
- RC9 是对已验收 RC8 的 ARM64 主分支合并（产品树与 RC8 一致，仅新增
  Fast CI/测试），源码 tag `v1.1.7.6-unified-rc.9` @ `41b17c0`，
  **不产生新 Deb**。

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
