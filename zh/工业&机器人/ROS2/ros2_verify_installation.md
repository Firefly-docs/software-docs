# 验证 ROS 2 安装

## 7. 验证安装

重新加载环境：

```bash
source /opt/ros/humble/setup.bash
```

运行 ROS 2 检查：

```bash
ros2 doctor
```

正常情况下会看到：

```text
All 5 checks passed
```

确认 RViz2 已安装：

```bash
command -v rviz2
rviz2 --help
```

在目标机图形桌面中启动 RViz2：

```bash
rviz2
```
