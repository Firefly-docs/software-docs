# Qt 环境支持

Firefly 设备系统如果是 Ubuntu 22.04 可以直接通过 apt 安装 Qt 环境：
```bash
# 安装基础环境
apt update
apt install -y qtcreator qtbase5-dev

# 安装额外组件与开发环境，例如
apt install -y libqt5multimedia5 qtmultimedia5-dev libqt5quick5 qtdeclarative5-dev
```
安装后直接在设备上进行开发。

Ubuntu 18.04 或者 Ubuntu 20.04 需要借助电脑进行交叉编译，详情请看下一章

