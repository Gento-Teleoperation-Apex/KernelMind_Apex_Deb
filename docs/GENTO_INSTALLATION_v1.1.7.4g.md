# Gento Apex v1.1.7.4g 安装与升级说明

日期：2026-11-05。本文包含 Core、Common Runtime、Camera、Tool 和 Apex UI 的首次安装、升级、配置及检查步骤。

**Gento SDK 4.7.1 随 Core 安装包一并交付和安装，无需独立安装 SDK。**

## 1. 版本与安装位置

| 组件 | 本轮版本 | 安装位置 / 获取入口 |
|---|---|---|
| Gento Apex Core | `1.1.7.4g` | 机器人主机，本 Release 的 Core Deb |
| Common Runtime | `1.0.4`，ABI `humble-r2` | 机器人主机的 Docker；[镜像入口](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-runtime/1407924047?tag=1.0.4) |
| Gento SDK | `4.7.1`，ABI `4.7` | 随 Core 一并安装 |
| Camera Deb | `1.3.3` | 机器人主机，按平台选择本轮配套构建；[发布入口](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) |
| Camera Runtime | Orin CUDA 12 / Thor CUDA 13，均为 `1.0.0` | 机器人主机的 Docker；见下方平台表 |
| Apex Tool | `2.0.1` | 机器人主机；[发布入口](https://github.com/Gento-Teleoperation-Apex/Apex-tool-deb/releases) |
| Apex UI | `1.1.7.3` | 运行操作界面的电脑；[发布入口](https://github.com/Gento-Teleoperation-Apex/KernelMind_Apex_Web_Deb/releases) |

安装顺序：**准备环境 → Common Runtime → Core → UI 与系统配置 → Camera / Tool → 模块配置和检查**。先完成 Core 运行配置，再安装本轮独立模块。

### 1.1 平台选择

| 项目 | Orin | Thor / N1100 |
|---|---|---|
| 主机环境 | 对应 JetPack / L4T 36.4、CUDA 12 | 对应 JetPack / L4T 38.4、CUDA 13 |
| Core 公共模块 | ARM64 Humble Core + Common Runtime `1.0.4` | ARM64 Humble Core + Common Runtime `1.0.4` |
| Camera Deb | `Apex-cam_v1.3.3o_humble_arm64.deb` | `Apex-cam_v1.3.3t_jazzy_arm64.deb` |
| Camera Runtime | [1.0.0-orin-cuda12](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1407924750?tag=1.0.0-orin-cuda12) | [1.0.0-thor-cuda13](https://github.com/orgs/Gento-Teleoperation-Apex/packages/container/teleoperation-apex-camera/1408159699?tag=1.0.0-thor-cuda13) |

两种 Runtime 分工不同：Common Runtime 运行 Core 公共模块，Camera Runtime 提供平台对应的相机环境。Thor 的 Core 使用 Humble 容器，其 Camera 使用 Jazzy / CUDA 13 环境。

Camera 须使用支持本版 Core 公共录制根的配套构建。特别是 Thor，请按本轮平台交付清单确认构建内容；旧同版本包不能仅凭 `1.3.3` 文件名认定兼容，需取得配套重建包并完成该平台检查。

### 1.2 准备文件

在机器人主机上的同一交付目录准备：

```text
teleoperation-apex-runtime_1.0.4_humble_base_arm64.tar
Gento-Teleoperation-Apex_v1.1.7.4o_humble_arm64.deb
gento-apex-tool_2.0.1o_humble_arm64.deb
```

按相机平台，再准备其中一组：

```text
# Orin
teleoperation-apex-camera_1.0.0_orin_cuda12_humble_arm64.tar
Apex-cam_v1.3.3o_humble_arm64.zip

# Thor / N1100
teleoperation-apex-camera_1.0.0_thor_cuda13_jazzy_arm64.tar
Apex-cam_v1.3.3t_jazzy_arm64.zip
```

Core 示例使用 Orin 交付文件名；其他平台按交付清单选择对应 Core 文件。Core Deb 内部版本为 `1.1.7.4g`。Tool 等文件名可能因交付批次带有不同后缀，以实际交付文件为准，安装前核对包内版本。

UI 安装文件放在操作电脑上：ARM64 Linux 使用 `Apex-UI_v1.1.7.3_arm64.deb`，x86_64 Linux 使用 `Apex-UI_v1.1.7.3_amd64.deb`；Windows、macOS 从 UI 发布页选择对应安装包。

Core Deb 从本 Release 附件取得，离线镜像归档从配套交付文件取得；GitHub Packages 链接提供镜像入口。若交付附有校验清单，先核对文件，Camera ZIP 内的校验步骤见第 6 节。

## 2. 检查机器人主机环境

目标主机需已完成对应 JetPack 基础环境，并具备 Docker Engine、Compose v2、NVIDIA Container Toolkit 和可用的软件源。在机器人主机执行：

```bash
uname -m
sudo docker version
sudo docker compose version
dpkg-query -W nvidia-container-toolkit
df -h .
```

确认架构为 `aarch64`，Docker 服务和 Compose v2 可用，磁盘有足够空间保存归档、解包文件和导入后的镜像。缺少基础容器环境时，先按设备的 JetPack 部署说明配置，再继续本手册。

安装命令默认在交付文件目录执行。机器人端的 Core、Camera 和 Tool 均使用 ARM64 包；UI 则按操作电脑的架构选择。

## 3. 升级前准备

首次安装跳过本节。已有设备先正常结束录制、回放和遥操，再停止业务模块。

```bash
sudo apexctl stop teleop
sudo apexctl stop robot
```

已安装 Tool 的设备，再通过模块管理页面停止 Tool。升级 Camera 前，停止已存在的相机执行器和健康检查服务：

```bash
for unit in apex-camera.service apex-camera-docker.service apex-camera-health.service; do
  if systemctl cat "$unit" >/dev/null 2>&1; then
    sudo systemctl stop "$unit"
  fi
done
```

Core 升级会备份配置并在失败时尝试恢复。建议另外保存现场配置备份；以下命令在**实际部署用户**的登录环境中执行：

```bash
apex_backup_dir="$HOME/apex-backups/$(date +%Y%m%d-%H%M%S)"
install -d -m 0700 "$apex_backup_dir"

if [ -d "$HOME/.tj_config" ]; then
  sudo tar --acls --xattrs -czf "$apex_backup_dir/tj-config.tar.gz" -C "$HOME" .tj_config
  sudo chmod 0600 "$apex_backup_dir/tj-config.tar.gz"
fi

if [ -d /etc/apex ]; then
  sudo tar --acls --xattrs -czf "$apex_backup_dir/etc-apex.tar.gz" -C /etc apex
  sudo chmod 0600 "$apex_backup_dir/etc-apex.tar.gz"
fi
```

以上备份包含配置，不会复制录像数据或 Docker 镜像。另行保留上一版配套安装文件、授权文件及 Runtime 归档。

## 4. 安装 Common Runtime 和 Core

### 4.1 导入 Common Runtime 1.0.4

```bash
sudo docker image load -i \
  ./teleoperation-apex-runtime_1.0.4_humble_base_arm64.tar

sudo docker image inspect \
  teleoperation-apex-runtime:1.0.4 --format '{{.Id}}'
```

若已经导入本次配套的正式镜像，可跳过重复导入。导入镜像后仍需安装 Core Deb，安装器才会完成镜像配对和容器更新。

### 4.2 安装或重装 Core

```bash
sudo apt update
sudo apt install --reinstall \
  ./Gento-Teleoperation-Apex_v1.1.7.4o_humble_arm64.deb
```

按安装提示确认部署信息，等待安装完成。安装器自动校验 Runtime、部署 Core 服务并安装 Gento SDK `4.7.1`，**无需额外安装 SDK 包**。

### 4.3 检查 Core

```bash
dpkg-query -W -f='${Package}\t${Version}\t${Status}\n' gento-apex
sudo apexctl health backend
curl -fsS http://127.0.0.1:8080/api/v1/system/health
```

确认 Core 版本为 `1.1.7.4g`、包状态为 `install ok installed`、Backend 健康状态正常。下文使用默认 Backend 端口 `8080`，自定义端口按实际配置替换。

## 5. 安装 UI，完成 Core 系统配置

### 5.1 在操作电脑安装 UI

UI 是桌面程序，安装在运行操作界面的电脑上；如果该电脑就是带桌面环境的机器人主机，也可直接安装在主机上。

ARM64 Linux：

```bash
sudo apt install --reinstall ./Apex-UI_v1.1.7.3_arm64.deb
```

x86_64 Linux，改用以下包：

```bash
sudo apt install --reinstall ./Apex-UI_v1.1.7.3_amd64.deb
```

通过应用菜单启动 Apex UI，或以普通桌面用户运行：

```bash
apexteleop
```

Windows 使用发布页的 `.exe`，macOS 使用对应 `.dmg`。UI 不安装进 Core 容器。

### 5.2 连接并应用系统配置

在 UI 中填写机器人主机 IP 和 Backend 端口，连接设备。在系统配置中核对：

1. 机器人平台、型号及机器人 IP。
2. ROS 通信网卡、Domain ID 等现场网络参数。
3. 系统录制根 `storage.bag_storage_root`，指向磁盘上实际存在、可写的目录；使用外置盘时，先确认该盘已挂载。
4. 依次执行校验、保存、应用，确认配置已应用。

如需从命令行核对 Core 配置状态，在机器人主机执行：

```bash
curl -fsS http://127.0.0.1:8080/api/v1/config |
  python3 -c 'import json,sys; d=json.load(sys.stdin)["data"]; print({k:d[k] for k in ("schema_version","revision","applied_revision","pending_restart")})'
```

本轮独立模块需要 Core 已应用的运行配置支持 schema 3。确认没有本次安装所需的待应用修改，再继续；只保存候选配置不足以代替 Apply。

## 6. 安装 Camera

Camera 应用版本为 `1.3.3`，Camera Runtime 版本仍为 `1.0.0`。按平台二选一，使用第 1 节要求的配套构建。解压操作在新的交付目录进行；若已有同名解压目录，换到空目录再操作。

### 6.1 Orin：Humble / CUDA 12

在机器人主机的交付目录执行：

```bash
sudo docker image load -i \
  ./teleoperation-apex-camera_1.0.0_orin_cuda12_humble_arm64.tar

sudo docker image inspect \
  teleoperation-apex-camera:1.0.0-orin-cuda12 --format '{{.Id}}'

unzip ./Apex-cam_v1.3.3o_humble_arm64.zip
```

进入解压目录，校验后安装：

```bash
cd Apex-cam_v1.3.3o_humble_arm64
sha256sum -c SHA256SUMS && \
  sudo apt install --reinstall ./Apex-cam_v1.3.3o_humble_arm64.deb
cd ..
```

### 6.2 Thor / N1100：Jazzy / CUDA 13

确认已取得本轮支持公共录制根的 Thor 配套构建，再执行：

```bash
sudo docker image load -i \
  ./teleoperation-apex-camera_1.0.0_thor_cuda13_jazzy_arm64.tar

sudo docker image inspect \
  teleoperation-apex-camera:1.0.0-thor-cuda13 --format '{{.Id}}'

unzip ./Apex-cam_v1.3.3t_jazzy_arm64.zip
```

进入解压目录，校验后安装：

```bash
cd Apex-cam_v1.3.3t_jazzy_arm64
sha256sum -c SHA256SUMS && \
  sudo apt install --reinstall ./Apex-cam_v1.3.3t_jazzy_arm64.deb
cd ..
```

如果已直接取得经过校验的 Deb，可在该 Deb 所在目录执行对应安装命令。校验或安装失败时停止后续步骤，修复后重新校验，再继续配置和启动。

### 6.3 配置相机与执行器

安装菜单中按实际硬件选择头部、腕部型号；升级时回车可保留已有型号。D405 物理端口绑定按 Camera 包内 `INSTALL.md` 操作。

Camera `1.3.3` 安装、升级或同版本重装后，默认选择 **Docker**。安装器与执行器切换工具均不会自动启动、重启相机或启用开机自启。刷新 UI 后，在 Camera 配置页核对型号、输入源及编码参数，依次校验、保存、应用。

**录制根只在系统配置中设置。** Camera 使用 Core 发布的 `BAG_STORAGE_ROOT` 准备挂载，不需要手动同步旧 `recorder.directory` 或 `recording_parent`。

在机器人主机检查公共根和 Docker 准备结果：

```bash
grep '^export BAG_STORAGE_ROOT=' /etc/apex/module-runtime.env
sudo /opt/kernelmind/apex_camera/bin/prepare_compose.py
```

`prepare_compose.py` 只准备运行配置，不启动采集。导入镜像、选择执行器、启动相机是三个不同操作。

需要切换执行器时，先结束录像并停止两个执行器：

```bash
sudo systemctl stop apex-camera.service apex-camera-docker.service
```

然后二选一。Docker 执行（安装默认）：

```bash
sudo /opt/kernelmind/apex_camera/bin/camera-select-executor docker
```

或选择 host：

```bash
sudo /opt/kernelmind/apex_camera/bin/camera-select-executor host
```

切换本身不启动相机。host 模式需要匹配的宿主 ROS、CUDA 和相机依赖，两种执行器应保持只有一种占用相机。

## 7. 安装 Tool

在机器人主机的交付目录执行：

```bash
sudo apt install --reinstall ./gento-apex-tool_2.0.1o_humble_arm64.deb
```

安装前须满足：Core Stack 已部署、Core 已应用运行配置支持 schema 3、Teleop 已停止。若文件名与示例不同，使用本轮实际交付的 `2.0.1` 包。

安装完成后刷新 UI，在 Tool 独立配置页选择实际工具及通信参数，依次校验、保存、应用。首次安装默认选择 `none`，升级保留已有独立配置。没有实际工具时可跳过 Tool 安装。

工具类型由 Tool 配置页管理，不再通过 Core 的旧 `deployment.tool_type` 配置入口修改。安装包完成不代表硬件驱动已启动，驱动需在配置完成后手动启动。

## 8. 启动与安装后检查

完成所有配置应用，再检查模块。Robot 和 Teleop 的启动按现场操作流程进行；恢复遥操前确认本机授权有效、机器人保持状态正常，并保证急停可用。

### 8.1 检查模块注册与状态

```bash
sudo apexctl health backend
sudo apexctl status camera
sudo apexctl status tool
curl -fsS http://127.0.0.1:8080/api/v1/modules
```

Camera、Tool 在安装后保持停止是正常状态；未安装的可选模块显示未安装。

### 8.2 启动并检查 Camera

相机型号、接线和运行配置核对完成后，通过 UI 模块管理启动 Camera，或在机器人主机执行：

```bash
sudo apexctl start camera
sudo apexctl health camera
```

打开视频窗口，确认实际启用的相机都有画面、左右关系正确。录制前确认系统存储根可用；存储未就绪时可以预览，但不能据此认定录像可用。

做一次短录制并正常停止，检查本次会话的记录文件及视频均位于系统根下的 `recorded_bags/<session>/`。Camera 视频目录由录制请求传入，不单独选择另一份默认根。单独视频检查与整机联合录制分别验证。

### 8.3 启动并检查 Tool

已连接实际工具并完成配置时，保持 Teleop 停止，通过 UI 启动 Tool，或执行：

```bash
sudo apexctl start tool
sudo apexctl health tool
```

核对工具供电、连接、左右设备及实际反馈。工具为 `none` 时跳过驱动启动。最后再按现场流程恢复 Robot、Teleop，完成遥操和录制回放检查。

## 9. 常见情况与日志

### Backend 无响应或安装未完成

```bash
sudo apexctl logs backend 200
sudo systemctl status apex-stack.service apex-container-control.service --no-pager
sudo journalctl -u apex-stack.service -u apex-container-control.service -n 100 --no-pager
sudo dpkg --audit
```

需要持续查看 Backend 日志时：

```bash
sudo apexctl logs-follow backend 200
```

UI 连接失败时先核对主机 IP、Backend 端口和网络连通性。`8080` 是默认 Backend API 端口，不是独立 UI 的安装入口。

### Runtime 缺失或配对校验失败

重新确认导入的是本次交付的 Common Runtime `1.0.4`，再查看安装输出中的实际原因。Camera 镜像与 Common Runtime 分开核对，Orin 和 Thor 的 Camera 镜像也需对应各自平台。

安装器校验镜像身份与兼容信息；仅修改 tag 或跳过校验不能替代配套镜像。

### Tool 提示先升级或应用 Core

先完成第 4、5 节，确认 Core 当前运行配置支持 schema 3 且已应用，并保持 Teleop 停止，然后重试安装 Tool。不要只改配置中的 schema 数字。

### Camera 可以预览但无法录像

检查系统录制根、实际磁盘挂载、共享目录及 Camera 服务账户的写权限。修复存储后，在结束录制的条件下通过受控重启重新准备挂载；不要通过另配相机根目录或把目录改为全员可写来处理。

Camera 日志与运行状态：

```bash
sudo apexctl logs camera 100
curl -fsS http://127.0.0.1:8080/api/v1/modules/camera/config/runtime
```

Tool 日志：

```bash
sudo apexctl logs tool 100
```

### 安装失败后重试与回退

Core 安装失败会尝试恢复上一版运行环境和配置。若日志报告配置恢复失败，保留日志提示的备份目录并联系维护人员。

安装失败后，包管理器记录的版本与实际运行版本可能不同，可同时检查：

```bash
dpkg-query -W -f='${Package}\t${Version}\t${Status}\n' gento-apex
readlink -f /opt/kernelmind/apex/current
sudo apexctl health backend
```

修复明确的镜像、依赖或配置问题后，保持业务模块停止，再使用同一交付包重试：

```bash
sudo apt install --reinstall \
  ./Gento-Teleoperation-Apex_v1.1.7.4o_humble_arm64.deb
```

保留现有配置及备份。手工降级需恢复配套的旧运行环境与配置快照，不应只修改配置版本号或仅替换一个旧包。
