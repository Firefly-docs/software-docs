# Yocto RV1126B SDK 概况

该 SDK 使用 scarthgap Yocto/Poky 分支、Linux 6.1、systemd、DEB 软件包格式与 NetworkManager；默认启用 package-management，并包含 adbd、蓝牙服务和根分区扩容工具。

支持的 machine 配置为 aio-1126bjd4、aio-1126bjd4v0、aio-1126bq38 和 cam-1126bq38。各 machine 会选择自身的设备树与产品配置；文中的命令以 aio-1126bjd4 为例。

所有 RV1126B machine 都引入 demo 配置，默认覆盖调试、显示、Wi-Fi/蓝牙、多媒体和音频。镜像启用 OpenSSH；默认账户为 root / firefly，同时创建具有 sudo 权限的普通用户 firefly / firefly。默认密码仅用于开发调试，量产镜像应修改密码或改用密钥登录。
