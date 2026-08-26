# FireflyApi2 无线网络

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
