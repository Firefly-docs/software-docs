# Buildroot SSH

官方发布的 SDK 默认已开启 SSH，用户为 `root`，密码为 `firefly`。如果不需要修改用户登录密码，可以跳过此章节。

## 修改方法

- 使能 SSH 相关选项

  - openssh

  ```
  BR2_PACKAGE_OPENSSH=y
  ```

  - 配置登录账户 root 和密码

  ```
  BR2_TARGET_ENABLE_ROOT_LOGIN=y
  BR2_TARGET_GENERIC_ROOT_PASSWD="firefly"
  ```

- 修改配置文件

  - 修改板卡里的 `/etc/ssh/sshd_config` 文件

  ```
  PermitRootLogin yes
  ```
