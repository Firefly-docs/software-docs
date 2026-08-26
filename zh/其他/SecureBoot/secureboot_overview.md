# Linux 系统安全启动

说明：

1. 由于 Rockchip 平台默认的密钥签名不支持 extboot 格式的固件。所以目前在 device/rockchip 仓库的 defconfig 必须要添加 USE\_EXTBOOT=n 的选项来关闭 extboot 的编译。如果没有关闭 extboot 的编译就会出现 uboot 能正常加载，但是到了 kernel 启动阶段就会直接启动失败。
2. 不支持 rollback-index 回滚版本的设置。
3. RK\_SECURITY\_BURN\_KEY 的配置是在 spl 阶段将密钥烧入。此烧入操作是一次性的，烧入后芯片会熔断，后续无法更改。
4. 在 uboot 固件已经签名的情况下，kernel 没签名的话，则 uboot 阶段会对 kernel 进行签名检测，检测失败会自动进 loader 烧写模式。
