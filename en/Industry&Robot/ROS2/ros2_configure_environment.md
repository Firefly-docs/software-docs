# Configure the ROS 2 Environment

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
