# Buildroot Ethernet Configuration

Buildroot's network configuration needs to use the `/etc/network/interfaces` configuration file. After the configuration is complete, run `/etc/init.d/S40network restart` to restart the network. Manual debugging can directly use `ifdown -a` and `ifup -a` to restart the network.

## Common configuration

Configuration file example: The following configuration file sets the eth0 network card to a dynamic IP address and eth1 to a static IP address

Note: The file format of `/etc/network/interfaces` is strict. If you encounter `Error: either "local" is duplicate, or "/24" is a garbage.`, it is very likely that there is one more space in the configuration file.

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
gateway 168.168.0.1
```

(1) inet static: Define a static IP address. The supported options are:

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

scope Address validity scope. Possible values: global, link, host
```

(2) inet dhcp: Obtain an IP address through the DHCP protocol. The supported options are:

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

(3) inet manual: No IP address is defined for the interface. Typically used by an interface that is a member of a bridge or aggregation, an interface that needs to operate in promiscuous mode (*for example, port mirroring or network TAP*), or an interface that has a VLAN device configured on it. This is one way to keep the interface without an IP address. The supported options are:

```
hwaddress address
        Link local address or "random".

mtu size
        MTU size
```

## Advanced settings

`/etc/network/interfaces` supports setting to run Linux command line commands when the network card is shut down/started. Since `/etc/network/interfaces` supports relatively limited functionality, this can be very helpful when configuring network configurations such as static routes, default routes, etc.

Supported optional options are: pre-up, up, post-up, pre-down, down, post-down, after these options, add the command line.

```
pre-up    : Action before network card is up
up        : Action when network card is up
post-up	  : Action after network card is up
pre-down  : Action before network card is down
down      : Action when network card is down
post-down : Action after network card is down
```

Configuration example: configure a static route for the eth1 network card

```
auto eth1
iface eth1 inet static
address 192.168.3.1
netmask 255.255.255.0
broadcast 192.168.3.255
post-up ip route add 192.168.4.0/24 via 192.168.3.2 dev $IFACE
```

Configuration example: create a bridge, bind eth0 and eth1 to the bridge, and use it as a LAN port

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
