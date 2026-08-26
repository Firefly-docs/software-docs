# Install ROS 2 Humble Desktop

## 4. Install the Full ROS 2 Humble Desktop

```bash
sudo env \
  DEBIAN_FRONTEND=noninteractive \
  NEEDRESTART_MODE=l \
  APT_LISTCHANGES_FRONTEND=none \
  apt-get -o Dpkg::Use-Pty=0 install -y --no-remove \
  ros-humble-desktop ros-dev-tools
```
