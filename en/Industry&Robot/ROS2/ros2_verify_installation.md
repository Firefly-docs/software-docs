# Verify the ROS 2 Installation

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
