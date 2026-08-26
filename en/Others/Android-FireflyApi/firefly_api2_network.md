# FireflyApi2 network

The network feature needs to add the following permissions:

```
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```
#### 3.5.1 Ethernet

Due to partial interface variations between `Android14` and `Android11/12`, the Ethernet APIs are explained separately below:

##### 3.5.1.1 Android11/Android12
##### 1. Whether it is a dual Ethernet port
```
Function：public boolean isDualEth()
Description：Get whether the device is a dual Ethernet port
Return：true  Dual Ethernet port
　　　　false Single Ethernet port
Example：
boolean isDual =  = mFireflyApi2.isDualEth();
```
##### 2. Get the MAC address of the device's  main Ethernet
```
Function：public  String getEthMacAddress()
Description：Get the MAC address of the device's  main Ethernet
Return：Returns null if failed
Example：
String ethMac  = mFireflyApi2.getEthMacAddress();
```
##### 3. Get device's main Ethernet information
```
Function：public EthernetInfo getEthConfiguration()
Description：Get device's main Ethernet information(including:ip,netmask,gateway,dns1,dns2)
Example：
IpConfig info  = mFireflyApi2.getEthConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 4. Set the IP address of the device's main Ethernet
```
Function：public boolean setEthConfiguration(boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
Description：Set the IP address of the device's main Ethernet
Parameters：isStatic When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
Return：Returns false if failed
Example：
boolean set_static_ip  = mFireflyApi2.setEthConfiguration(true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthConfiguration(false,null,null,null,null,null);
```
##### 5. Set main Ethernet on/off
```
Function：public void setEthEnable(boolean enabled)
Description：Set main Ethernet on/off
Parameters：enabled true(on)/false(off)
Example：
 mFireflyApi2.setEthEnable();
```
##### 6. Get the MAC address of the device's  secondary Ethernet
```
Function：public  String getEthAuxMacAddress()
Description：Get the MAC address of the device's  secondary Ethernet
Return：Returns null if failed
Example：
String auxEthMac  = mFireflyApi2.getEthAuxMacAddress();
```
##### 7. Get device's secondary Ethernet information
```
Function：public EthernetInfo getEthAuxConfiguration()
Description：Get device's main Ethernet information(including:ip,netmask,gateway,dns1,dns2)
Example：
IpConfig info  = mFireflyApi2.getEthAuxConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 8. Set the IP address of the device's secondary Ethernet
```
函数：public boolean setEthAuxConfiguration(boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
Description：Set the IP address of the device's secondary Ethernet
Parameters：isStatic When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
Return：Returns false if failed
Example：
boolean set_static_ip  = mFireflyApi2.setEthAuxConfiguration(true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthAuxConfiguration(false,null,null,null,null,null);
```

##### 3.5.1.2 Android14
##### 1. Get available Ethernet cards on the device
```
Function：public String[] getEthAvailableInterfaces()
Description：Get available Ethernet cards on the device
Return：String[],If null, there are no available Ethernet cards
Example：
String[] ifaces = mFireflyApi2.getEthAvailableInterfaces();
```
##### 2. Get the MAC address of the device's Ethernet
```
Function：public String getEthMacAddress(String iface)
Parameters：iface network card names such as eth0, eth1
Description：Get the MAC address of the device's Ethernet
Return：Returns null if failed
Example：
String ethMac  = mFireflyApi2.getEthMacAddress();
```
##### 3. Get device's Ethernet information
```
Function：public EthernetInfo getEthConfiguration(String iface)
Parameters：iface network card names such as eth0, eth1
Description：Get device's Ethernet information(including:ip,netmask,gateway,dns1,dns2)
Example：
IpConfig info  = mFireflyApi2.getEthConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 4. Set the IP address of the device's Ethernet
```
Function：public boolean setEthConfiguration(String iface, boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
Description：Set the IP address of the device's Ethernet
Parameters：iface network card names such as eth0, eth1
	isStatic When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
Return：Returns false if failed
Example：
boolean set_static_ip  = mFireflyApi2.setEthConfiguration("eth0", true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthConfiguration("eth0, "false,null,null,null,null,null);
```
##### 5. Set Ethernet on/off
```
Function：public void setEthEnable(boolean enabled)
Description：Set main Ethernet on/off
Parameters：enabled true(on)/false(off)
Example：
 mFireflyApi2.setEthEnable();
```
