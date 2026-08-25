# FireflyApi network
### 3.5 网络 
网络功能需要添加以下权限:

```
android.permission.ACCESS_NETWORK_STATE  

android.permission.CHANGE_NETWORK_STATE  

android.permission.WRITE_SETTINGS

```

1. 获取设备以太网的MAC地址
	```
	函数：public  String getEthMacAddress() 
	描述：获取设备以太网的MAC地址
    返回：失败返回null 
	范例：  
	String ethMac  = mFireflyApi.getEthMacAddress();
	```
2. 获取设备以太网的IP地址
	```
	函数：public String getEthIpAddress() 
	描述：获取设备以太网的IP地址
	范例：  
	String ip  = mFireflyApi.getEthIpAddress();
	```
3. 获取设备以太网的信息
	```
	函数：public EthernetInfo getEthInfo()
	描述：获取设备以太网的信息(包含:ip,netmask,gateway,dns1,dns2)
	范例：  
	EthernetInfo info  = mFireflyApi.getEthInfo();
    debug(info.isUseStatic()?"Static":"DHCP"+"\n");
	debug("IpAddress:"+info.getIpAddress()+"\n");
	debug("Netmask:"+info.getNetmask()+"\n");
	debug("Gateway:"+info.getGateway()+"\n");
	debug("Dns1:"+info.getDns1()+"\n");
	debug("Dns2:"+info.getDns2()+"\n");
	```
4. 设置设备以太网的IP地址
	```
	函数：public boolean setEthIPAddress(boolean use_static_ip,String mIpaddr, String mMask, String mGw, String mDns1,String mDns2)
	描述：设置设备以太网的IP地址
    参数：use_static_ip true为静态ip时其他参数才有效，否则false时为动态ip自动获取ip
    返回：失败返回false
	范例：  
	boolean set_static_ip  = mFireflyApi.setEthIPAddress(true,"192.168.31.1",.....);
    boolean set_dhcp_ip  = mFireflyApi.setEthIPAddress(false,null,null,null,null,null);
	```
5. 以太网当前是否连接
	```
	函数：public boolean isEthConnect()
	描述：以太网当前是否连接
	范例：  
	boolean connect  = mFireflyApi.isEthConnect();
	```
6. 设置以太网开启/关闭
	```
	函数：public boolean setEthernetEnabled(boolean enabled)
	描述：设置以太网开启/关闭
    参数：enabled true(开启)/false(关闭) 
	范例：  
	boolean connect  = mFireflyApi.isEthConnect();
	```
7. 获取当前网络连接的类型
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
