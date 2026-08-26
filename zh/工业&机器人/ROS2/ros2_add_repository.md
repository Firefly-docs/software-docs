# 添加 ROS 2 官方软件源

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
