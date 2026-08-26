# Ubuntu/Debian systemd 服务自启动

需要作为后台服务运行的应用，建议创建 systemd 服务。

创建 `/etc/systemd/system/myapp.service`：

```ini
[Unit]
Description=My Application
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=firefly
WorkingDirectory=/home/firefly/myapp
ExecStart=/home/firefly/myapp/myapp
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

启用并启动服务：

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
sudo systemctl status myapp.service
```

查看日志：

```bash
journalctl -u myapp.service -f
```

如果服务启动的是图形程序，需要补充显示环境和权限，例如：

```ini
Environment=DISPLAY=:0
Environment=XAUTHORITY=/home/firefly/.Xauthority
```
