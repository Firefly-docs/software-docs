# Buildroot SSH

The officially released SDK has ssh enabled by default, the user is `root`, and the password is `firefly`. If you do not need to modify the user login password, you can skip this chapter.

## Modification method

- Enable SSH related options

  - openssh

   ````
   BR2_PACKAGE_OPENSSH=y
   ````

  - Configure the login account root and password

   ````
   BR2_TARGET_ENABLE_ROOT_LOGIN=y
   BR2_TARGET_GENERIC_ROOT_PASSWD="firefly"
   ````

- Modify configuration files

  - Modify the `/etc/ssh/sshd_config` file in the board

   ````
   PermitRootLogin yes
   ````
