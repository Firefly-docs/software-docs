# Test ROS 2 Communication

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
