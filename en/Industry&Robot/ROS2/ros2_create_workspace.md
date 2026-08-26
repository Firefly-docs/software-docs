# Create a ROS 2 Workspace

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
