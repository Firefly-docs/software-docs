# Weston 输入设备相关配置

Weston 服务默认需要至少一个输入设备，如无输入设备，则需要在 weston.ini 中的 core 段进行特殊设置：

```ini
# /etc/xdg/weston/weston.ini

[core]
require-input=false
```
