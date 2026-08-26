# 创建 ROS 2 工作空间

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
