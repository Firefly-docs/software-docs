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

#### 3.5.2 无线网络
##### 1. 打开设备热点
```
函数：public void startTethering(ITetheringCallback callback)
描述：打开设备热点
参数：callback 事件回调
范例：
mFireflyApi2.startTethering(new ITetheringCallback.Stub (){
    @Override
    public void onTetheringStarted() throws RemoteException {
        Log.v(TAG,"onTetheringStarted");
    }

    @Override
    public void onTetheringFailed() throws RemoteException {
        Log.v(TAG,"onTetheringFailed");
    }
});
```
##### 2. 关闭设备热点
```
函数：public void stopTethering()
描述：关闭设备热点
范例：
mFireflyApi2.stopTethering();
```
##### 3. 判断设备热点是否已经打开
```
函数：public boolean isWifiHotspotEnabled()
描述：判断设备热点是否已经打开
返回：true(开启)/false(关闭)
范例：
boolean isEnabled  = mFireflyApi2.isWifiHotspotEnabled();
```
##### 4. 获取设备热点配置信息
```
函数：public SoftApConfiguration getWifiHotspotConfig()
描述：获取设备热点配置信息
返回：SoftApConfiguration
范例：
SoftApConfiguration config = mFireflyApi2.getWifiHotspotConfig();
```
##### 5. 配置设备热点信息
```
函数：public void configWifiHotspot(String ssid, String password)
描述：配置设备热点信息(热点名称，密码)
参数：ssid 热点名称
　　　password 密码
返回：SoftApConfiguration
范例：
mFireflyApi2.configWifiHotspot("AndroidAP_Test","12345678");
```
##### 6. 自动关闭设备热点
```
函数：public void setWifiHotspotAutoShutdownEnabled(boolean enabled)
描述：自动关闭设备热点(无设备连接)
参数：enabled true(开启)/false(关闭)
范例：
mFireflyApi2.setWifiHotspotAutoShutdownEnabled(true);
```
##### 7. 判断设置热点是否开启自动关闭功能
```
函数：public boolean isWifiHotspotAutoShutdownEnabled()
描述：判断设置热点是否开启自动关闭功能
返回：true(开启)/false(关闭)
范例：
boolean mAutoOff = mFireflyApi2.isWifiHotspotAutoShutdownEnabled();
```
##### 8. 设备热点使用固定IP地址
```
函数：public void setWifiHostspotUseFixedIfaceAddr(boolean use)
描述：设备热点使用固定IP地址
参数：use true(开启)/false(关闭)
范例：
mFireflyApi2.setWifiHostspotUseFixedIfaceAddr("true");//默认IP地址为192.168.43.1/24
```

##### 9. 设置设备热点固定IP地址
```
函数：public void setWifiHostspotFixedIfaceAddr(String ifaceAddr)
描述：设置设备热点固定IP地址
参数：ifaceAddr IP地址
范例：
mFireflyApi2.setWifiHostspotFixedIfaceAddr("192.168.23.1/24");
```

##### 10. 获取设备热点IP地址
```
函数：public String getWifiHostspotFixedIfaceAddr()
描述：获取设备热点IP地址
范例：
String address = mFireflyApi2.getWifiHostspotFixedIfaceAddr();// address = "192.168.23.1/24"
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
<!--
### 3.6 外部存储相关
1. 获取外部存储U盘路径
	```
	函数：public String getUSBPath(int num)
	描述：获取外部存储U盘路径
    参数：num u盘的index
    返回：失败返回null
	范例：
	String usb1_path  = mFireflyApi.getUSBPath(1);
	```
2. 获取外部存储SD卡路径
	```
	函数：public String getSDcardPath()
	描述：获取外部存储SD卡路径
    返回：失败返回null
	范例：
	String sd_path  = mFireflyApi.getSDcardPath();
	```
3. 卸载外部存储
	```
	函数：public　void doUnmountVolume(String path,boolean force,boolean removeEncryption)
	描述：卸载外部存储
    参数：path 路径为volume mountPoint，负责会抛错
    　　　force　强制卸载,为false时被占用的设备不会卸载
       　removeEncryption　为ture时，包含了force为true,此时会强制卸载并移除加密映射
    /*
     * If force is not set, we do not unmount if there are
     * processes holding references to the volume about to be unmounted.
     * If force is set, all the processes holding references need to be
     * killed via the ActivityManager before actually unmounting the volume.
     * This might even take a while and might be retried after timed delays
     * to make sure we dont end up in an instable state and kill some core
     * processes.
     * If removeEncryption is set, force is implied, and the system will remove any encryption
     * mapping set on the volume when unmounting.
     */
    备注：调用此接口卸载外部存储时，先用StorageList.getVolumeState来检测path的挂载状态，为Environment.MEDIA_MOUNTED时再卸载
	范例：
    StorageList mStorageList = new StorageList(context);
    if(Environment.MEDIA_MOUNTED.equals(mStorageList.getVolumeState(path)))
		mFireflyApi.doUnmountVolume("/mnt/sdcard/",true,false);
	```
-->
### 3.6 定时开关机　　
#### 1. 设置一次定时开机
```
函数：public void setSchedulePowerOn(int id, boolean enabled, long alarm_time)
描述：指定UTC时间，设置定时开机，仅一次不会重复
参数：id 定时开机id
　　　enabled true(开启)/false(关闭)
　　　alarm_time 开机时间(UTC时间)
范例：
//设置１分钟后开机，id为11002
mFireflyApi2.setSchedulePowerOn(11002,true,System.currentTimeMillis()+60);
//取消id为11002的操作
mFireflyApi2.setSchedulePowerOn(11002,false,0);
```
#### 2. 设置一次定时关机
```
函数：public void setSchedulePowerOff(int id, boolean enabled, long alarm_time)
描述：指定UTC时间，设置定时关机，仅一次不会重复
参数：id 定时关机id
　　　enabled true(开启)/false(关闭)
　　　alarm_time 关机时间(UTC时间)
范例：
//设置１分钟后关机，id为11001
mFireflyApi2.setPowerOffAlarm(11001, true,System.currentTimeMillis()+60);
//取消id为11001的操作
mFireflyApi2.setPowerOffAlarm(11001, false,0);
```
#### 3. 设置一次定时重启
```
函数：public void setSchedulePowerReboot(int id, boolean enabled, long alarm_time)
描述：指定UTC时间，设置定时重启，仅一次不会重复
参数：id 定时重启id
　　　enabled true(开启)/false(关闭)
　　　alarm_time 重启时间(UTC时间)
范例：
//设置１分钟后重启，id 为11003
mFireflyApi2.setSchedulePowerReboot(11003, true,System.currentTimeMillis()+60);
//取消id 为11003的操作
mFireflyApi2.setSchedulePowerReboot(11003, false,0);
```
<!--
2. 设置重复定时开机
	```
	函数：setPowerOnAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	描述：以周为单位，设置循环的定时开机的时间
    参数：enabled 开启/关闭
          hour　开机时间的小时
          minutes　开机时间的分钟
          daysOfWeek 以周为单位重复，设置定时开机的日期
         　　　　　　　SUNDAY -> MONDAY
	 	               0b1111111
	范例：
	mFireflyApi.setPowerOnAlarmRepeat(true,10,30,0b1000011);
    //设置周一、二、日10点30分开机
	```

3. 获取定时开机状态
	```
	函数：public Alarm getPowerOnAlarm()
	描述：获取定时开机状态
	范例：
	Alarm　powerOnAlarm =mFireflyApi.getPowerOnAlarm();
	```
4. 设置重复定时关机
	```
	函数：setPowerOffAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	描述：以周为单位，设置循环的定时开机的时间
    参数：enabled    开启/关闭
         hour　开机时间的小时
         minutes　开机时间的分钟
         daysOfWeek　以周为单位重复，设置定时开机的日期
         　　　　　　　SUNDAY -> MONDAY
	 	            0b1111111
	范例：
	mFireflyApi.setPowerOffAlarmRepeat(true,10,30,0b1000011);
    //设置周一、二、日10点30分关机
	```
6. 获取定时关机状态
	```
	函数：public Alarm getPowerOffAlarm()
	描述：获取定时开机状态
	范例：
	Alarm　powerOffAlarm =mFireflyApi.getPowerOffAlarm();
	```
-->
如果以上的功能无法满足您的需求，可以使用一下接口，自己做逻辑实现功能。此接口只是最基本的定时开关机功能，由传入的id控制开启/关闭，**重启后失效需要手动再次设置**。

<!--
#### 3.6.2 自定义开关机接口

1. 设置定时开机
	```
	函数：public void setSchedulePowerOn(int id,boolean enabled,long alarm_time)
	描述：设置定时开机,id由用户定义，用于开启和关闭定时开机时使用，重启后失效需要重新设置
    参数：id 定时开机id
    	  enabled 开启/关闭　　
          alarm_time 开机时间(UTC时间)
    备注: 通过多组定时开机id,可以实现多组定时开机功能
	范例：
	mFireflyApi.setSchedulePowerOn("12",true,System.currentTimeMillis()+60);
    //设置１分钟后开机，id为12
    mFireflyApi.setSchedulePowerOn("12",true,0);
    //取消id为"12"操作
	```

2. 设置定时关机
	```
	函数：public void setSchedulePowerOff(int id,boolean enabled,long alarm_time)
	描述：设置定时关机,id由用户定义，用于开启和关闭定时关机时使用，重启后失效需要重新设置
    参数：id 定时关机id
    	  enabled 开启/关闭　　
          alarm_time 关机时间(UTC时间)
    备注: 通过多组定时关机id,可以实现多组定时关机功能
	范例：
	mFireflyApi.setSchedulePowerOff("12",true,System.currentTimeMillis()+60);
    //设置１分钟后关机，id为12
    mFireflyApi.setSchedulePowerOff("12",true,0);
    //取消id为"12"操作
	```
-->
