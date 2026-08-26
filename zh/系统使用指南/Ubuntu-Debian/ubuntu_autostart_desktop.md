# Ubuntu/Debian 桌面环境自启动

可将 `.desktop` 文件放到用户自启动目录；系统会在该用户登录桌面后启动程序：

```bash
mkdir -p ~/.config/autostart
vim ~/.config/autostart/myapp.desktop
```

写入以下内容，请将 `Exec` 改为实际程序路径：

```
[Desktop Entry]
Type=Application
Name=My App
Exec=/home/firefly/myapp/myapp
Terminal=false
```

如需所有桌面用户都自动启动同一个程序，可将 `.desktop` 文件放到系统目录：

```bash
sudo cp ~/.config/autostart/myapp.desktop /etc/xdg/autostart/
```
