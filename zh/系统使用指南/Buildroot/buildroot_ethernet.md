# Buildroot 以太网配置

Buildroot 的网络配置需要使用 `/etc/network/interfaces` 配置文件，配置完成之后，运行 `/etc/init.d/S40network restart` 即可重启网络。手动调试可以直接使用 `ifdown -a` 和 `ifup -a` 来重启网络。

## 常用配置

配置文件举例：如下配置文件将 eth0 网卡设置为动态 IP 地址，将 eth1 设置为静态 IP 地址。

注意：`/etc/network/interfaces` 的文件格式要求比较严格，如果遇到 `Error: either "local" is duplicate, or "/24" is a garbage.`，那么很有可能是配置文件中多了一个空格。

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
address 168.168.110.137
netmask 255.255.0.0
broadcast 168.168.1.255
gateway  168.168.0.1
```

（1）inet static：定义静态 IP 地址。支持的选项有：

```
address address
        Address (dotted quad/netmask) required

netmask mask
        Netmask (dotted quad or number of bits) deprecated

broadcast broadcast_address
        Broadcast address (dotted quad, + or -) deprecated. Default value: "+"

metric metric
        Routing metric for default gateway (integer)

gateway address
        Default gateway (dotted quad)

pointopoint address
        Address of other end point (dotted quad). Note the spelling of "point-to".

hwaddress address
        Link local address or "random".

mtu size
        MTU size

scope  Address validity scope. Possible values: global, link, host
```

（2）inet dhcp：通过 DHCP 协议获取 IP 地址。支持的选项有：

```
hostname hostname
        Hostname to be requested (pump, dhcpcd, udhcpc)

metric metric
        Metric for added routes (dhclient)

leasehours leasehours
        Preferred lease time in hours (pump)

leasetime leasetime
        Preferred lease time in seconds (dhcpcd)

vendor vendor
        Vendor class identifier (dhcpcd)

client client
        Client identifier (dhcpcd)

hwaddress address
        Hardware address.
```

（3）inet manual：没有为接口定义 IP 地址。通常由作为桥接或聚合成员的接口，需要以混杂模式运行的接口（*例如，端口镜像或网络 TAP*）或在其上配置了 VLAN 设备的接口使用。这是保持接口不带 IP 地址的一种方法。支持的选项有：

```
hwaddress address
        Link local address or "random".

mtu size
        MTU size
```

## 高级设置

`/etc/network/interfaces` 支持设置在网卡关闭或启动时运行 Linux 命令行指令。由于 `/etc/network/interfaces` 支持的功能相对有限，这在配置静态路由、默认路由等网络配置时将会非常有帮助。

支持的可选选项有：pre-up、up、post-up、pre-down、down、post-down，在这些选项之后加上命令行即可。

```
pre-up	网卡启用前的动作
up	启用时候的动作
post-up	启用后的动作
pre-down	关闭前的动作
down	关闭时动作
post-down	关闭后动作
说明：$IFACE自适应对于相应的网卡节点
```

配置举例：给 eth1 网卡配置一条静态路由。

```
auto eth1
iface eth1 inet static
address 192.168.3.1
netmask 255.255.255.0
broadcast 192.168.3.255
post-up ip route add 192.168.4.0/24 via 192.168.3.2 dev $IFACE
```

配置举例：创建一个网桥，将 eth0、eth1 绑定到网桥，将其作为 LAN 口。

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet manual
pre-up ifconfig $IFACE up
post-down ifconfig $IFACE down

auto eth1
iface eth1 inet manual
pre-up ifconfig $IFACE up
post-down ifconfig $IFACE down

auto br0
iface br0 inet static
address 192.168.2.1
netmask 255.255.255.0
broadcast 192.168.2.255
pre-up brctl addbr $IFACE
pre-up brctl addif $IFACE eth0
pre-up brctl addif $IFACE eth1
bridge_ports eth0 eth1
post-down brctl delif $IFACE eth0
post-down brctl delif $IFACE eth1
post-down ifconfig $IFACE down
post-down brctl delbr $IFACE
```
