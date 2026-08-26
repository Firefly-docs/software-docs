# Ubuntu/Debian Graphical Interface

Some use cases do not require a desktop environment. You can change the default boot target to `multi-user.target`. After the next reboot, the system will boot to command-line mode and will not start the graphical interface automatically:

```bash
sudo systemctl set-default multi-user.target
sudo reboot
```

To restore graphical startup by default, run:

```bash
sudo systemctl set-default graphical.target
sudo reboot
```
