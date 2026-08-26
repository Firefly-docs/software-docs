# Add the ROS 2 Repository

## 2. Add the Official ROS 2 Repository

Install the download and signing tools:

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
```

Install the official ROS signing key:

```bash
curl -fsSL --retry 3 \
  https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  -o /tmp/ros-archive-keyring.gpg

sudo install -o root -g root -m 0644 \
  /tmp/ros-archive-keyring.gpg \
  /usr/share/keyrings/ros-archive-keyring.gpg

rm -f /tmp/ros-archive-keyring.gpg
```

Add the ROS 2 Humble repository:

```bash
arch=$(dpkg --print-architecture)

printf 'deb [arch=%s signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu jammy main\n' \
  "$arch" | sudo tee /etc/apt/sources.list.d/ros2.list >/dev/null

sudo apt-get update
```
