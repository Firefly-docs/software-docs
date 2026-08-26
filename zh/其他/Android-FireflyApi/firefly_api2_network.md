# FireflyApi2 网络

网络功能需要添加以下权限:

```
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```
#### 3.5.1 以太网

由于`Android14`对比`Android11/Android12`的接口有部分差异, 下面分别对以太网的api分开说明

##### 3.5.1.1 Android11/Android12

##### 1. 判断是否为双以太网口
```
函数：public boolean isDualEth()
描述：获取设备是否为双以太网口
返回：true 双以太网口
　　　false 单以太网口
范例：
boolean isDual =  = mFireflyApi2.isDualEth();
```
##### 2. 获取设备主以太网口的MAC地址
```
函数：public  String getEthMacAddress()
描述：获取设备主以太网口的MAC地址
返回：失败返回null
范例：
String ethMac  = mFireflyApi2.getEthMacAddress();
```
##### 3. 获取设备主以太网口的信息
```
函数：public EthernetInfo getEthConfiguration()
描述：获取设备主以太网口的信息(包含:ip,netmask,gateway,dns1,dns2)
范例：
IpConfig info  = mFireflyApi2.getEthConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 4. 设置设备主以太网口的IP地址
```
函数：public boolean setEthConfiguration(boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
描述：设置设备以太网口的IP地址
参数：isStatic true 为静态 IP 时其他参数才有效，否则 false 时为动态 IP 自动获取 IP
返回：失败返回 false
范例：
boolean set_static_ip  = mFireflyApi2.setEthConfiguration(true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthConfiguration(false,null,null,null,null,null);
```
##### 5. 设置主以太网口开启/关闭
```
函数：public void setEthEnable(boolean enabled)
描述：设置主以太网开启/关闭
参数：enabled true(开启)/false(关闭)
范例：
mFireflyApi2.setEthEnable(true);
```
##### 6. 获取设备副以太网口的MAC地址
```
函数：public  String getEthAuxMacAddress()
描述：获取设备副以太网口的 MAC 地址
返回：失败返回 null
范例：
String auxEthMac  = mFireflyApi2.getEthAuxMacAddress();
```
##### 7. 获取设备副以太网口的信息
```
函数：public EthernetInfo getEthAuxConfiguration()
描述：获取设备副以太网口的信息(包含:ip,netmask,gateway,dns1,dns2)
范例：
IpConfig info  = mFireflyApi2.getEthAuxConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 8. 设置设备副以太网口的IP地址
```
函数：public boolean setEthAuxConfiguration(boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
描述：设置设备副以太网的IP地址
参数：isStatic true 为静态 IP 时其他参数才有效，否则false时为动态 IP 自动获取 IP
返回：失败返回false
范例：
boolean set_static_ip  = mFireflyApi2.setEthAuxConfiguration(true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthAuxConfiguration(false,null,null,null,null,null);
```

##### 3.5.1.2 Android14

##### 1. 获取设备可用的以太网卡
```
函数：public String[] getEthAvailableInterfaces()
描述：获取设备可用的以太网卡
返回：String[], 如果为空则没有可用的以太网卡
范例：
String[] ifaces = mFireflyApi2.getEthAvailableInterfaces();
```

##### 2. 获取设备以太网口的MAC地址
```
函数：public  String getEthMacAddress(String iface)
参数：iface 网卡名如eth0,eth1
描述：获取设备太网口的MAC地址
返回：失败返回null
范例：
String ethMac  = mFireflyApi2.getEthMacAddress("eth0");
```
##### 3. 获取设备以太网口的信息
```
函数：public EthernetInfo getEthConfiguration(String iface)
参数：iface 网卡名如eth0,eth1
描述：获取设备以太网口的信息(包含:ip,netmask,gateway,dns1,dns2)
范例：
IpConfig info  = mFireflyApi2.getEthConfiguration("eth0");
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 4. 设置设备以太网口的IP地址
```
函数：public boolean setEthConfiguration(String iface, boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
描述：设置设备以太网口的IP地址
参数:	iface网卡名如eth0,eth1
	isStatic 为静态 IP 时其他参数才有效，否则 false 时为动态 IP 自动获取 IP
返回：失败返回 false
范例：
boolean set_static_ip  = mFireflyApi2.setEthConfiguration("eth0", true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthConfiguration("eth0", false,null,null,null,null,null);
```
##### 5. 设置以太网口开启/关闭
```
函数：public void setEthEnable(boolean enabled)
描述：设置以太网开启/关闭
参数：enabled true(开启)/false(关闭)
范例：
mFireflyApi2.setEthEnable();
```

<!--
1. 以太网当前是否连接
	```
	函数：public boolean isEthConnect()
	描述：以太网当前是否连接
	范例：
	boolean connect  = mFireflyApi.isEthConnect();
	```
6. 获取当前网络连接的类型
	```
	函数：public String getCurNetworkType()
	描述：获取当前网络连接的类型
    返回：UNKNOWN/WIFI/ETHERNET/MOBILE
    备注：使用BroadcatReceiver通过监听ETHERNET_STATE_CHANGED_ACTION = "android.net.ethernet.ETHERNET_STATE_CHANGED"
    　　　来实现对以太网ConnectState变化的监听.
    　　　int connectState=intent.getIntExtra("ethernet_state", -1)
	范例：
	String netType  = mFireflyApi.getCurNetworkType();
	```
	-->
