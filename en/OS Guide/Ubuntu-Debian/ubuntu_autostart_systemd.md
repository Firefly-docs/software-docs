# Ubuntu/Debian systemd Service Autostart

For applications that should run as a background service, create a systemd unit.

Create `/etc/systemd/system/myapp.service`:

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

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
sudo systemctl status myapp.service
```

View logs:

```bash
journalctl -u myapp.service -f
```

If the service starts a GUI program, set the required display environment and permissions, for example:

```ini
Environment=DISPLAY=:0
Environment=XAUTHORITY=/home/firefly/.Xauthority
```
