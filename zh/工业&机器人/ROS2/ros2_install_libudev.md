# 安装匹配的 libudev 开发包

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
