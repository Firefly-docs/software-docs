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

#### 3.5.2 Wireless network
##### 1. Open device hotspot
```
Function：public void startTethering(ITetheringCallback callback)
Description：Open device hotspot
Parameter：callback  Event callbacks
Example：
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
##### 2. Close device hotspot
```
Function：public void stopTethering()
Description：Close device hotspot
Example：
mFireflyApi2.stopTethering();
```
##### 3. Is the device hotspot turned on
```
Function：public boolean isWifiHotspotEnabled()
Description：Is the device hotspot turned on
Return：true(on)/false(off)
Example：
boolean isEnabled  = mFireflyApi2.isWifiHotspotEnabled();
```
##### 4. Get device hotspot configuration information
```
Function：public SoftApConfiguration getWifiHotspotConfig()
Description：Get device hotspot configuration information
Return：SoftApConfiguration
Example：
SoftApConfiguration config = mFireflyApi2.getWifiHotspotConfig();
```
##### 5. Configure device hotspot information
```
Function：public void configWifiHotspot(String ssid, String password)
Description：Configure device hotspot information(Hotspot name, password)
Parameters：ssid Hotspot name
　　　　　　password password
Return：SoftApConfiguration
Example：
mFireflyApi2.configWifiHotspot("AndroidAP_Test","12345678");
```
##### 6. Automatically shutdown device hotspot
```
Function：public void setWifiHotspotAutoShutdownEnabled(boolean enabled)
Description：Automatically shutdown device hotspot(No device connection)
Parameters：enabled true(on)/false(off)
Example：
mFireflyApi2.setWifiHotspotAutoShutdownEnabled(true);
```
##### 7. whether the automatic shutdown function is enabled for the hotspot
```
Function：public boolean isWifiHotspotAutoShutdownEnabled()
Description：whether the automatic shutdown function is enabled for the hotspot
Return：true(on)/false(off)
Example：
boolean mAutoOff = mFireflyApi2.isWifiHotspotAutoShutdownEnabled();
```
##### 8. The device hotspot uses a fixed IP address
```
Function：public void setWifiHostspotUseFixedIfaceAddr(boolean use)
Description：The device hotspot uses a fixed IP address
Parameters：use true(on)/false(off)
Example：
mFireflyApi2.setWifiHostspotUseFixedIfaceAddr("true");//the default IP address is 192.168.43.1/24
```

##### 9. Set a fixed IP address for the device hotspot
```
Function：public void setWifiHostspotFixedIfaceAddr(String ifaceAddr)
Description：Set a fixed IP address for the device hotspot
Parameters：ifaceAddr IP
Example：
mFireflyApi2.setWifiHostspotFixedIfaceAddr("192.168.23.1/24");
```

##### 10. Obtain the IP address of the device's hotspot
```
Function：public String getWifiHostspotFixedIfaceAddr()
Description：Obtain the IP address of the device's hotspot
Example：
String address = mFireflyApi2.getWifiHostspotFixedIfaceAddr();// address = "192.168.23.1/24"
```

