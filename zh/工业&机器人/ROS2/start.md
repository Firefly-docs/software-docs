# 安装与使用 ROS 2 Humble

适用环境：

- Firefly ARM64 开发板
- Ubuntu 22.04 Jammy
- 安装完整 ROS 2 Humble Desktop，包含 RViz2、RQt、rosbag2 和开发工具

## 1. 安装OpenGL

由于ROS的可视化软件是基于GLX编写的，所以需要OpenGL支持，前往[OpenGL安装教程安装](../../图形显示/GPU/index.md)。

## 2. 添加 ROS 2 官方软件源

安装下载和签名工具：

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
```

安装 ROS 官方签名密钥：

```bash
curl -fsSL --retry 3 \
  https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  -o /tmp/ros-archive-keyring.gpg

sudo install -o root -g root -m 0644 \
  /tmp/ros-archive-keyring.gpg \
  /usr/share/keyrings/ros-archive-keyring.gpg

rm -f /tmp/ros-archive-keyring.gpg
```

添加 ROS 2 Humble 软件源：

```bash
arch=$(dpkg --print-architecture)

printf 'deb [arch=%s signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu jammy main\n' \
  "$arch" | sudo tee /etc/apt/sources.list.d/ros2.list >/dev/null

sudo apt-get update
```

## 3. 安装 Firefly 匹配的 libudev 开发包

Firefly Ubuntu 镜像通常预装定制版 `libudev1`。ROS 2 Desktop 需要安装与它版本完全一致的 `libudev-dev`。

临时启用 Firefly 官方仓库：

```bash
printf '%s\n' \
  'deb https://wiki.t-firefly.com/firefly-ubuntu-repo jammy main' \
  | sudo tee /etc/apt/sources.list.d/firefly-ros2-install.list >/dev/null

sudo apt-get update
```

安装与当前 `libudev1` 完全匹配的版本：

```bash
libudev_version=$(dpkg-query -W -f='${Version}' libudev1)
echo "libudev version: $libudev_version"

sudo apt-get install -y --no-remove --no-upgrade \
  "libudev-dev=$libudev_version"
```

安装完成后移除临时 Firefly 软件源：

```bash
sudo rm -f /etc/apt/sources.list.d/firefly-ros2-install.list
sudo apt-get update
```

## 4. 安装完整 ROS 2 Humble

```bash
sudo env \
  DEBIAN_FRONTEND=noninteractive \
  NEEDRESTART_MODE=l \
  APT_LISTCHANGES_FRONTEND=none \
  apt-get -o Dpkg::Use-Pty=0 install -y --no-remove \
  ros-humble-desktop ros-dev-tools
```

## 5. 配置 ROS 2 环境

让当前终端立即使用 ROS 2：

```bash
source /opt/ros/humble/setup.bash
```

配置以后打开的终端自动加载 ROS 2：

```bash
grep -Fqx 'source /opt/ros/humble/setup.bash' "$HOME/.bashrc" || \
  printf '\n# ROS 2 Humble\nsource /opt/ros/humble/setup.bash\n' >> "$HOME/.bashrc"
```

## 6. 初始化 rosdep

```bash
sudo rosdep init
rosdep update --rosdistro humble
```

如果 `sudo rosdep init` 提示已经初始化，可以忽略并继续执行 `rosdep update`。

如果访问 GitHub Raw 超时，使用下面的方式重试：

```bash
curl -fL \
  --retry 8 \
  --retry-all-errors \
  --connect-timeout 20 \
  --max-time 300 \
  https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list \
  -o /tmp/20-default.list

sudo install -D -o root -g root -m 0644 \
  /tmp/20-default.list \
  /etc/ros/rosdep/sources.list.d/20-default.list

rm -f /tmp/20-default.list
rosdep update --rosdistro humble
```

## 7. 验证安装

重新加载环境：

```bash
source /opt/ros/humble/setup.bash
```

运行 ROS 2 检查：

```bash
ros2 doctor
```

正常情况下会看到：

```text
All 5 checks passed
```

确认 RViz2 已安装：

```bash
command -v rviz2
rviz2 --help
```

在目标机图形桌面中启动 RViz2：

```bash
rviz2
```

## 8. 测试 ROS 2 通信

打开第一个终端：

```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_cpp talker
```

打开第二个终端：

```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_cpp listener
```

如果 listener 持续输出下面的信息，说明 ROS 2 通信正常：

```text
[listener]: I heard: [Hello World: ...]
```

使用 `Ctrl+C` 停止 talker 和 listener。

## 9. 创建第一个 ROS 2 工作空间

```bash
mkdir -p "$HOME/ros2_ws/src"
cd "$HOME/ros2_ws/src"

ros2 pkg create \
  --build-type ament_python \
  --node-name hello_node \
  my_first_pkg

cd "$HOME/ros2_ws"
colcon build --symlink-install
source install/setup.bash
```

运行刚创建的节点：

```bash
ros2 run my_first_pkg hello_node
```

以后使用该工作空间时执行：

```bash
source /opt/ros/humble/setup.bash
source "$HOME/ros2_ws/install/setup.bash"
```

也可以将工作空间环境加入 `~/.bashrc`：

```bash
grep -Fqx 'source "$HOME/ros2_ws/install/setup.bash"' "$HOME/.bashrc" || \
  printf '%s\n' 'source "$HOME/ros2_ws/install/setup.bash"' >> "$HOME/.bashrc"
```

至此，ROS 2 Humble Desktop、RViz2、开发工具和工作空间均可正常使用。
