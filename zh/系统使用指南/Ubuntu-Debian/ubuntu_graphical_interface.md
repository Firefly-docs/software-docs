# Ubuntu/Debian 图形界面

部分使用场景不需要桌面环境，可将系统默认启动目标切换为 `multi-user.target`，下次启动后系统将进入命令行模式，不再自动启动图形界面：

```bash
sudo systemctl set-default multi-user.target
sudo reboot
```

如需恢复图形界面默认启动，执行：

```bash
sudo systemctl set-default graphical.target
sudo reboot
```
