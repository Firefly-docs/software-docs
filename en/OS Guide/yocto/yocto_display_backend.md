# Yocto Display Backend

display.conf selects X11 by default and adds x11-base with the Xorg modesetting driver. After switching to Wayland, the image adds weston, weston-init, and weston-examples; the procedure appears in [Build the Yocto image](yocto_compile_image.md).
