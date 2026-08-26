# 配置 ROS 2 环境

## 5. 配置 ROS 2 环境

让当前终端立即使用 ROS 2：

```bash
source /opt/ros/humble/setup.bash
```

配置以后打开的终端自动加载 ROS 2：

```bash
grep -Fqx 'source /opt/ros/humble/setup.bash' "$HOME/.bashrc" || \
  printf '\n# ROS 2 Humble\nsource /opt/ros/humble/setup.bash\n' >> "$HOME/.bashrc"
```
