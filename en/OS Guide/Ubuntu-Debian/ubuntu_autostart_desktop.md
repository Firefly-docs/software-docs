# Ubuntu/Debian Desktop Autostart

Place the `.desktop` file in the user autostart directory. The system starts the program after that user logs in to the desktop:

```bash
mkdir -p ~/.config/autostart
vim ~/.config/autostart/myapp.desktop
```

Write the following content and change `Exec` to the actual application path:

```
[Desktop Entry]
Type=Application
Name=My App
Exec=/home/firefly/myapp/myapp
Terminal=false
```

To start the same program for all desktop users, place the `.desktop` file in the system directory:

```bash
sudo cp ~/.config/autostart/myapp.desktop /etc/xdg/autostart/
```
