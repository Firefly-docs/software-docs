# 部署 Rockchip EtherCAT 系统

## 8. Rockchip 平台支持

### 8.6 启动实时应用

将 CPU 设为性能模式：

```bash
for governor in /sys/devices/system/cpu/cpufreq/policy*/scaling_governor; do
  echo performance | sudo tee "$governor"
done
```

确认总线后再启动应用：

```bash
ethercat slaves
ethercat pdos
ethercat cstruct
sudo ./ethercat_app
```

### 8.7 双网口配置

双 GMAC 产品可以将一个网口专用于 EtherCAT，另一个保留为标准以太网。例如：

```text
gmac0 → EtherCAT IgH
gmac1 → TCP/IP、SSH 和普通网络业务
```

参考 RK3568 设备树通过专用 compatible 标记 EtherCAT GMAC：

```dts
gmac0: ethernet@fe2a0000 {
    compatible = "rockchip,rk3568-gmac-ethercat", "snps,dwmac-4.20a";
    reg = <0x0 0xfe2a0000 0x0 0x10000>;
};
```

使用双网口时注意：

- `main_devices` 必须填写 EtherCAT 网口的 MAC，而不是普通网口的 MAC。
- EtherCAT 网口不配置 IP，也不交给 NetworkManager 管理。
- 普通网口继续使用标准 `stmmac` 驱动。
- 两个 GMAC 的设备树、时钟、复位、PHY 和 pinctrl 必须分别正确配置。
- 专用 compatible 和驱动支持依赖对应 SDK 补丁，不能直接用于未适配的内核。
