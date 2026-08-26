# Yocto RV1126B SDK Profile

This SDK uses the scarthgap Yocto/Poky branch, Linux 6.1, systemd, DEB packages, and NetworkManager. Package management is enabled by default, and the image includes adbd, Bluetooth service, and a rootfs resize helper.

Available machine configurations are aio-1126bjd4, aio-1126bjd4v0, aio-1126bq38, and cam-1126bq38. Each machine selects its own device tree and product configuration; this guide uses aio-1126bjd4 in its examples.

Every RV1126B machine includes the demo configuration for debug, display, Wi-Fi/Bluetooth, multimedia, and audio. The image enables OpenSSH. Default accounts are root / firefly and an unprivileged firefly / firefly user with sudo access. These passwords are for development only; change them or use key-based login in production images.
