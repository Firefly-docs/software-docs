# FireflyApi2 wireless network

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

