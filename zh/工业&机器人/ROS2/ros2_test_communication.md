# 测试 ROS 2 通信

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
