# 安装 ROS 2 Humble Desktop

## 4. 安装完整 ROS 2 Humble

```bash
sudo env \
  DEBIAN_FRONTEND=noninteractive \
  NEEDRESTART_MODE=l \
  APT_LISTCHANGES_FRONTEND=none \
  apt-get -o Dpkg::Use-Pty=0 install -y --no-remove \
  ros-humble-desktop ros-dev-tools
```
