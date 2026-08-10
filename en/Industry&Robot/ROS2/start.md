# Install and Use ROS 2 Humble

Applicable environment:

- Firefly ARM64 development boards
- Ubuntu 22.04 Jammy
- Full ROS 2 Humble Desktop installation, including RViz2, RQt, rosbag2, and development tools

## 1. Install OpenGL

ROS visualization applications are built on GLX and therefore require OpenGL support. Follow the [OpenGL installation guide](../../Graphics/GPU/index.md).

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

## 3. Install the Matching Firefly libudev Development Package

Firefly Ubuntu images usually include a customized version of `libudev1`. ROS 2 Desktop requires the `libudev-dev` package with exactly the same version.

Temporarily enable the official Firefly repository:

```bash
printf '%s\n' \
  'deb https://wiki.t-firefly.com/firefly-ubuntu-repo jammy main' \
  | sudo tee /etc/apt/sources.list.d/firefly-ros2-install.list >/dev/null

sudo apt-get update
```

Install the version that exactly matches the current `libudev1` package:

```bash
libudev_version=$(dpkg-query -W -f='${Version}' libudev1)
echo "libudev version: $libudev_version"

sudo apt-get install -y --no-remove --no-upgrade \
  "libudev-dev=$libudev_version"
```

After installation, remove the temporary Firefly repository:

```bash
sudo rm -f /etc/apt/sources.list.d/firefly-ros2-install.list
sudo apt-get update
```

## 4. Install the Full ROS 2 Humble Desktop

```bash
sudo env \
  DEBIAN_FRONTEND=noninteractive \
  NEEDRESTART_MODE=l \
  APT_LISTCHANGES_FRONTEND=none \
  apt-get -o Dpkg::Use-Pty=0 install -y --no-remove \
  ros-humble-desktop ros-dev-tools
```

## 5. Configure the ROS 2 Environment

Make ROS 2 available in the current terminal:

```bash
source /opt/ros/humble/setup.bash
```

Configure ROS 2 to load automatically in newly opened terminals:

```bash
grep -Fqx 'source /opt/ros/humble/setup.bash' "$HOME/.bashrc" || \
  printf '\n# ROS 2 Humble\nsource /opt/ros/humble/setup.bash\n' >> "$HOME/.bashrc"
```

## 6. Initialize rosdep

```bash
sudo rosdep init
rosdep update --rosdistro humble
```

If `sudo rosdep init` reports that rosdep has already been initialized, ignore the message and continue with `rosdep update`.

If access to GitHub Raw times out, retry as follows:

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

## 7. Verify the Installation

Reload the environment:

```bash
source /opt/ros/humble/setup.bash
```

Run the ROS 2 checks:

```bash
ros2 doctor
```

Under normal conditions, the following output appears:

```text
All 5 checks passed
```

Confirm that RViz2 is installed:

```bash
command -v rviz2
rviz2 --help
```

Launch RViz2 from the graphical desktop on the target device:

```bash
rviz2
```

## 8. Test ROS 2 Communication

Open the first terminal:

```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_cpp talker
```

Open the second terminal:

```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_cpp listener
```

If the listener continuously prints messages like the following, ROS 2 communication is working correctly:

```text
[listener]: I heard: [Hello World: ...]
```

Press `Ctrl+C` to stop the talker and listener.

## 9. Create Your First ROS 2 Workspace

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

Run the node you just created:

```bash
ros2 run my_first_pkg hello_node
```

Whenever you use this workspace in the future, run:

```bash
source /opt/ros/humble/setup.bash
source "$HOME/ros2_ws/install/setup.bash"
```

You can also add the workspace environment to `~/.bashrc`:

```bash
grep -Fqx 'source "$HOME/ros2_ws/install/setup.bash"' "$HOME/.bashrc" || \
  printf '%s\n' 'source "$HOME/ros2_ws/install/setup.bash"' >> "$HOME/.bashrc"
```

ROS 2 Humble Desktop, RViz2, the development tools, and the workspace are now ready to use.
