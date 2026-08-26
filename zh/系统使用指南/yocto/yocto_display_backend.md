# Yocto 显示后端

display.conf 默认使用 X11，并加入 x11-base 与 Xorg modesetting 驱动。切换为 Wayland 后，镜像会加入 weston、weston-init 和 weston-examples；具体切换命令见[编译 Yocto 映像](yocto_compile_image.md)。
