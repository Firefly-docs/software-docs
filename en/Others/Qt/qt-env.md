# Qt environment support

If your Firefly device is using Ubuntu 22.04, then you can install Qt with apt
```bash
# Install basic env
apt update
apt install -y qtcreator qtbase5-dev

# Install additional plugins and dev pack
apt install -y libqt5multimedia5 qtmultimedia5-dev libqt5quick5 qtdeclarative5-dev
```
Then you can develop on deivce.

Ubuntu 18.04 Or Ubuntu 20.04 need to use PC to cross-compile, please read the following chapter:

