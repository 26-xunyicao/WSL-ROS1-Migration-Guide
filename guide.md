# WSL2 + ROS1 Noetic 从 C 盘迁移到 E 盘

本文介绍如何将 Windows 11 下的 WSL2 Ubuntu 20.04，以及其中的 ROS1 Noetic、catkin 工作空间、PX4、XTDrone 等内容，从 C 盘完整迁移到 E 盘。

---

## 1. 查看当前 WSL 发行版

打开 PowerShell：

```powershell
wsl -l -v
```

确认类似：

```text
NAME            STATE           VERSION
Ubuntu-20.04    Stopped         2
```

记住发行版名称，例如：

```text
Ubuntu-20.04
```

---

## 2. 关闭 WSL

```powershell
wsl --shutdown
```

再次检查：

```powershell
wsl -l -v
```

确保状态为：

```text
Stopped
```

---

## 3. 在 E 盘创建目录

```powershell
mkdir E:\WSL\Ubuntu20.04
```

---

## 4. 导出 Ubuntu 虚拟磁盘

```powershell
wsl --export Ubuntu-20.04 E:\WSL\Ubuntu20.04\ext4.vhdx --vhd
```

等待命令执行完成。

然后检查：

```text
E:\WSL\Ubuntu20.04
```

目录中是否出现：

```text
ext4.vhdx
```

---

## 5. 注销原来的 Ubuntu

> ⚠️ 一定要先确认 E 盘中的 `ext4.vhdx` 已经成功生成。

执行：

```powershell
wsl --unregister Ubuntu-20.04
```

---

## 6. 从 E 盘重新注册 Ubuntu

```powershell
wsl --import-in-place Ubuntu-20.04 E:\WSL\Ubuntu20.04\ext4.vhdx
```

---

## 7. 检查是否注册成功

```powershell
wsl -l -v
```

应该重新看到：

```text
Ubuntu-20.04
```

---

## 8. 启动 Ubuntu

```powershell
wsl -d Ubuntu-20.04
```

进入 Ubuntu 后检查系统版本：

```bash
lsb_release -a
```

---

## 9. 检查 ROS1 Noetic

```bash
echo $ROS_DISTRO
```

正常应输出：

```text
noetic
```

继续检查：

```bash
rosversion -d
```

以及：

```bash
which roscore
```

---

## 10. 测试 roscore

```bash
roscore
```

如果看到：

```text
started core service [/rosout]
```

说明 ROS Master 已正常启动。

---

## 11. 检查 catkin 工作空间

```bash
cd ~/catkin_ws
ls
```

一般应看到：

```text
build
devel
src
```

加载工作空间：

```bash
source ~/catkin_ws/devel/setup.bash
```

检查：

```bash
echo $ROS_PACKAGE_PATH
```

---

## 12. 检查 .bashrc

```bash
tail -n 30 ~/.bashrc
```

确认存在：

```bash
source /opt/ros/noetic/setup.bash
```

以及：

```bash
source ~/catkin_ws/devel/setup.bash
```

---

## 13. 迁移完成后的目录结构

```text
E:
└── WSL
    └── Ubuntu20.04
        └── ext4.vhdx
            └── Ubuntu 20.04
                ├── ROS Noetic
                ├── catkin_ws
                ├── PX4_Firmware
                ├── Livox-SDK2
                └── XTDrone
```

---

## 核心命令汇总

```powershell
wsl -l -v
wsl --shutdown
wsl --export Ubuntu-20.04 E:\WSL\Ubuntu20.04\ext4.vhdx --vhd
wsl --unregister Ubuntu-20.04
wsl --import-in-place Ubuntu-20.04 E:\WSL\Ubuntu20.04\ext4.vhdx
wsl -d Ubuntu-20.04
```

---

## 注意事项

- `wsl --unregister` 会删除原来的 WSL 发行版注册和对应虚拟磁盘。
- 一定要确认导出的 `ext4.vhdx` 正常存在后再注销。
- 整套 Ubuntu、ROS、工作空间和软件环境都会随虚拟磁盘一起迁移。
- 迁移后新增的软件和 ROS 文件主要都会继续写入 E 盘中的 `ext4.vhdx`。
