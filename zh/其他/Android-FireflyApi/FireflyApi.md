# FireflyApi

## 1.概述
FireflyApi提供了部分系统接口以及封装了部分用户需要的功能接口，主要是为了让用户容易和简单的使用系统常用接口，
此文档只是对接口进行简单的说明，具体的使用案例如下描述。

* 支持rk3288 Android5.1平台所有系列机型
* 支持rk3399 Android7.1平台所有系列机型
* 支持rk3128 Android5.1平台所有系列机型

## 2.资源下载和使用

使用FireflyApi时先检查一下机器的固件是否是最新版本，在[[资源下载]](http://www.t-firefly.com/doc/download/page/id/4.html)
页面找到对应的机型查看固件是否最新，同时也可以同步SDK到最新提交,具体先选择对应机型的[[wiki]](http://wiki.t-firefly.com/)然后在Android开发->编译Android固件里面找到同步的方式。

FireflyApi使用的整套Demo:
* [[RK3288 firefly_sdkapi_demo.zip]](https://pan.baidu.com/s/1PWDezNtzI1bCNPZPunaeHQ)（提取码：jvy6）  
* [[RK3399 firefly_sdkapi_demo.zip]](https://pan.baidu.com/s/1-VOKnlXFUquGOcBCvzVQlQ)（提取码：icud） 
* [[RK3128 firefly_sdkapi_demo.zip]](https://pan.baidu.com/s/13E9koEDBau1bSONVIB88Og )（提取码：e5wt）

Demo主要包含firefly_sdkapi_demo.apk,firefly-api.jar,libfirefly_api.so和firefly_sdkapi_demo的源码等，关于apk和
库的使用如下小结描述
```
├── apk
│   └── firefly_sdkapi_demo.apk
├── lib
│   ├── armeabi-v7a
│   │   └── libfirefly_api.so
│   └── firefly-api.jar
├── Readme_CN.txt
├── Readme_EN.txt
└── src
    └── firefly_sdkapi_demo
```

### 2.1 firefly_sdkapi_demo apk 简述
firefly_sdkapi_demo apk 是基于FireflyApi的接口实现的Demo程序，用户可通过Demo中功能的实现来验证自己的程序或相关接口。
当使用最新版本的固件时，里面会内置firefly_sdkapi_demo的应用如图

![](../../../img/Android-FireflyApi/apk_1.png)

* 点击进入apk后会有相应的接口实现列表如图

![](../../../img/Android-FireflyApi/apk_2.png)

* 系统设置接口实现
 
![](../../../img/Android-FireflyApi/apk_3.png)

* 定时开关机接口实现

![](../../../img/Android-FireflyApi/apk_4.png)

* 硬件接口实现
 
![](../../../img/Android-FireflyApi/apk_5.png)

### 2.2 如何在eclipse中使用 FireflyApi
对于firefly_sdkapi_demo apk是给用户呈现出接口相应的功能，用户如需要编写自己的应用程序，
需要将firefly-api.jar放在libs目录下，libfirefly_api.so放到libs/armeabi-v7a目录下   
	
	|-libs  
     	|-firefly-api.jar  
     	|-armeabi-v7a  
      　　　　　|-libfirefly_api.so
	

添加方式如下

![](../../../img/Android-FireflyApi/pic_1.png)

![](../../../img/Android-FireflyApi/pic_2.png)

![](../../../img/Android-FireflyApi/pic_3.png)


## 3.FireflyApi接口说明
### 3.1 系统信息
1. FireflyApi版本信息  
	```
	函数：public String getFireflyApiVersion()  
	描述：FireflyApi 版本信息   
	范例：   
	String version = mFireflyApi.getFireflyApiVersion();
	```
2. 获取目前设备的型号
	```
	函数：public String getAndroidModel()   
	描述：获取目前设备的型号  
	范例：   
	String model= mFireflyApi.getAndroidModel(); 
	```
3. 获取目前设备的android系统的版本
	```
	函数：public String getAndroidVersion()   
	描述：获取目前设备的android系统的版本   
	范例：   
	String version= mFireflyApi.getAndroidVersion();
	```
4. 获取设备的RAM大小,单位为MB
	```
	函数：public long getRamSpace()   
	描述：获取设备的RAM大小,单位为MB   
	返回：返回RAM大小，单位为MB   
	范例：   
	long ram = mFireflyApi.getRamSpace();
	```
5. 获取设备的RAM大小，并格式化为String格式
	```
	函数：public String getFormattedRamSpace()   
	描述：获取设备的RAM大小，并格式化为String格式   
	返回: 设备的RAM大小，String格式（1.5GB）  
	范例：   
	String ram = mFireflyApi.getFormattedRamSpace();   
	```
6. 获取设备的内置Flash大小,单位为MB
	```
	函数：public long getFlashSpace()   
	描述：获取设备的内置Flash大小,单位为MB   
	返回: 返回Flash大小，单位为MB   
	范例：   
	long flash = mFireflyApi.getFlashSpace();   
	```
7. 获取设备内置Flash大小，并格式化为String格式
	```
	函数：public String getFormattedFlashSpace()   
	描述：获取设备内置Flash大小，并格式化为String格式   
	返回: 设备的Flash大小，String格式（15.5GB）   
	范例：   
	String ram = mFireflyApi.getFormattedFlashSpace();
	```
8. 获取设备的固件内核版本
	```
	函数：public String getFormattedKernelVersion()   
	描述：获取设备的固件内核版本   
	返回: 内核版本   
	范例：   
	String kernel_version= mFireflyApi.getFormattedKernelVersion();
	```
9. 获取设备的固件系统版本和编译日期  
	```
	函数：public String getAndroidDisplay()   
	描述：获取设备的固件系统版本和编译日期   
	返回:固件系统版本和编译日期   
	范例：   
	String version= mFireflyApi.getAndroidDisplay();   
	```

### 3.2 系统设置
1. 系统关机
	```
	函数：public void shutDown(boolean showConfirm)   
	描述：系统关机   
	参数：showConfirm是否显示关机框   
	范例：   
	mFireflyApi.shutDown(false);   
	```
2. 系统重启
	```
	函数：public void reboot()  
	描述：系统关机  
	范例：  
	mFireflyApi.reboot();   
	```
3. 系统休眠
	```
	函数：public void sleep()  
	描述：系统关机  
	范例：  
	mFireflyApi.sleep();   
	```
4. 截屏
	```
	函数：public boolean  takeScreenshot(String path,String name)  
	描述：截图并存储到指定路径  
	参数：path存储路径  
	　　　name 存储的文件名(暂时只支持png格式)  
	返回: 截图是否成功true/false  
	范例：  
	mFireflyApi.takeScreenshot("/sdcard/","123.png");   
	```
5. 屏幕旋转
	```
	函数：public boolean  setRotation(int rotation)  
	描述：旋转  
	屏幕参数：rotation 屏幕方向:  Surface.ROTATION_0  
                               Surface.ROTATION_90  
                               Surface.ROTATION_180  
                               Surface.ROTATION_270　　
	范例：mFireflyApi.setRotation(Surface.ROTATION_0);
	```
6. 获取屏幕方向
	```
	函数：public int  getRotation()  
	描述：获取屏幕方向  
	返回：rotation 屏幕方向:      Surface.ROTATION_0  
                                Surface.ROTATION_90  
                                Surface.ROTATION_180  
                                Surface.ROTATION_270  
	范例：  
	int rotation = mFireflyApi.getRotation();
	```
7. 取消屏幕旋转
	```
	函数：public void  thawRotation()  
	描述：取消屏幕旋转  
	范例：  
	mFireflyApi.thawRotation();
	```
8. 状态栏显示/隐藏
	```
	函数：public void  setStatusBar(boolean show)  
	描述：显示/隐藏状态栏  
	参数：show 为true显示状态栏，为false隐藏状态栏  
	范例：  
	mFireflyApi.setStatusBar(false);
	```
9. 背光开关
	```
	函数：public void  setLcdBackLight(boolean on)  
	描述：只关背光，却不进入休眠，软件继续运行  
	参数：on 为true开背光，为false关背光  
	范例：  
	mFireflyApi.setLcdBackLight(false);
	```
10. 是否存在背光
	```
	函数：public boolean  hasLcdBackLight()  
	描述：判断是否存在背光  
	返回：为true存在背光，为false不存在背光  
	范例：  
	boolean has = mFireflyApi.hasLcdBackLight();
	```
11. 设置屏幕亮度
	```
	函数：public boolean setBrightness(int brightness)  
	描述：设置屏幕亮度  
	参数：brightness亮度取值范围0-255  
	返回：为true设置成功，为false 设置失败  
	范例：  
	boolean success= mFireflyApi.setBrightness(130);
	```
12. 获取屏幕亮度
	```
	函数：public int getBrightness()  
	描述：获取屏幕亮度  
	返回：brightness屏幕亮度，获取失败时返回-1  
	范例：  
	int brightness= mFireflyApi.getBrightness();
	```
13. 设置系统时间
	```
	函数：public boolean setTime( int year, int month, int day, int hour,  
    	 int minute,int second)  
	描述：设置系统时间  
	参数：year     年  
	　　　month    月  
	　　　day      日  
	　　　hour     时  
	　　　minute   分  
	　　　second   秒  
	返回：为true设置成功，为false设置失败  
	范例：  
	mFireflyApi.setTime(2018,5,18,5,30,45);
	```
14. 静默安装应用
	```
	函数：public boolean silentInstall(String path)  
	描述：静默安装应用  
	参数：path apk地址  
	返回：为true安装成功  
	范例：  
	mFireflyApi.silentInstall("/sdcard/123.apk");
	```
15. 静默卸载应用
	```
	函数：public boolean silentUnInstall(String package_name)  
	描述：静默卸载应用  
	参数：package_name 为需要卸载应用的包名  
	返回：卸载成功返回true,卸载失败返回false  
	备注：只支持卸载安装的应用，不知道卸载内置应用  
	范例：  
	mFireflyApi.silentUnInstall("com.android.settings");
	```
16. 运行shell命令
	```
    函数：public Command execCmd(String cmd)  
	描述：运行shell命令  
	参数：cmd shell命令  
	返回：Command　 input 输入命令  
                   output　输出结果  
                   exitStatus　shell运行状态，为0是正常退出  
	范例：  
	mFireflyApi.execCmd("ls");
	```
17. su权限运行shell命令
	```
    函数：public static Command execSuCmd(String cmd)  
	描述：su权限运行shell命令  
	参数：cmd shell命令  
	返回：Command   input 输入命令  
	  　　　　　　　　output　输出结果  
	  　　　　　　　　exitStatus　shell运行状态，为0是正常退出  
	范例：  
	mFireflyApi.execSuCmd("cat init.rk30board.rc");//init.rk30board.rc默认权限750
	```
18. 保存系统logcat
	```
	函数：public void saveLogcat(String folderPath,String fileName)
	描述：抓取Android层的LOG并保存相应目录 
	参数：folderPath 保存路径 　　
    　　　fileName　　保存文件名
	范例：  
	mFireflyApi.saveLogcat("/mnt/sdcard/","123.log");
	```
19. 获取Hdmiin当前连接状态
	```
    函数：public　String getHdmiinStatus()
	描述：获取Hdmiin当前连接状态 
	返回：STATUS_HDMI_IN_CONNECT＝"1"　hdmiin已连接 　　
    　　　STATUS_HDMI_IN_NO_CONNECT＝"0"　hdmiin未连接   　　
	范例：  
	String hdmiin_status = mFireflyApi.getHdmiinStatus();
	```
20. 获取当前的屏幕数量
	```
    函数：public　int getScreenNumber(Context context)
	描述：获取当前的屏幕数量
	返回：失败时返回0  　　
	范例：  
	int screen_number = mFireflyApi.getScreenNumber(context);
	```
### 3.3 硬件接口
1. 使能看门狗
	```
	函数：public boolean watchDogEnable(boolean enable)  
	描述：打开/关闭看门狗  
	参数：enable为true启用，为false关闭  
	返回：为true设置成功，为false设置失败  
	备注：打开看门狗后，每隔30s需要喂狗一次，负责看门狗会关闭  
	范例：  
	boolean success = mFireflyApi.watchDogEnable(true);
	```
2. 看门狗喂狗
	```
    函数：public boolean watchDogFeed()
	描述：喂狗一次  
	返回：为true喂狗成功  
	范例：  
	boolean success = mFireflyApi.watchDogFeed();
	```
3. 麦克风切换（3128板型不支持）
	```
    函数：public boolean switchMic(int mic_type)  
	描述：麦克风切换到板载/耳机  
	参数：mic_type   TYPE_DEFAULT_MIC 板载mic   
                    TYPE_HEADSET_MIC 耳机mic
	返回：为true切换成功  
	范例：  
	boolean success = mFireflyApi.switchMic(FireflyApi.TYPE_DEFAULT_MIC);
	```
4. gpio控制
	```
	函数：public boolean gpioCtrl(int gpio, String direction, int value)  
	描述：控制gpio  
	参数：gpio  
	　　　direction in, out, high, low high/low  
	　　　value 1/0  
	返回：为true设置成功  
	范例：  
	boolean success = mFireflyApi.gpioCtrl(263,"out",1);//GPIO8_A7的gpio节点为263
	```
5. gpio读值
	```
    函数：public int gpioRead(int gpio)  
	描述：读取gpio的值  
	参数：gpio  
	返回：读取失败时返回-1  
	范例：  
	int gpioValue = mFireflyApi.gpioRead(263);//GPIO8_A7的gpio节点为263
	```
6. 检查gpio口是否被系统占用
	```
	函数：public boolean isGpioOccupied(int gpio)  
	描述：检查gpio口是否被系统占用  
	参数：gpio  
	返回：为true被占用  
	范例：  
	boolean occupied  = mFireflyApi.isGpioOccupied(263);//GPIO8_A7的gpio节点为263
	```
7. 解析gpio的节点值
	```
	函数：public int gpioParse(String gpioStr)  
	描述：解析gpio的节点值,例GPIO8_A7转换为节点263  
	参数：gpioStr  
	返回：解析失败返回-1  
	范例：  
	int gpioValue= mFireflyApi.gpioParse("GPIO8_A7");//GPIO8_A7的gpio节点为263
	```
8. 设置USB电源,选择控制OTG或USB接口
	```
	函数：public boolean setUsbPower(int type, boolean connect )  
	描述：设置USB电源,选择控制OTG或USB接口  
	参数：type  TYPE_USBHOST //控制USB口  
	 　　　　　　TYPE_OTG     //控制OTG口  
     　　　　　　connect   
	返回：获取失败返回-1  
	范例：  
	mFireflyApi.setUsbPower("TYPE_USBHOST",ture);//连接USB HOST  
	mFireflyApi.setUsbPower("TYPE_USBHOST",false);//断开USB HOST  
	mFireflyApi.setUsbPower("TYPE_OTG",ture);//连接OTG  
	mFireflyApi.setUsbPower("TYPE_OTG",false);//断开OTG
	```
    **注意：
    由于硬件设计不同，3128板型机器OTG接口连接主机时无法断电。**
    
    
9. 串口使用  

* 导入头文件
	```
	import com.firefly.api.serialport.SerialPort;  
	import com.firefly.api.serialport.SerialPort.Callback;  
	import com.firefly.api.serialport.SerialPortFinder;
	```
*  根据路径和波特率打开串口，并设置回调函数  
	```
	private boolean openSerialPort(String path,int baudrate)
	{
			try {
				SerialPort mSerialPort = new SerialPort(new File(path), baudrate, 0);
				mSerialPort.setCallback(new Callback() {
					@Override
					public void onDataReceived(byte[] buffer, int size) {
						// TODO Auto-generated method stub
						String result = new String(buffer, 0, size);
						Log.d(TAG, "onDataReceived:"+result);
					}
            	});
			} catch (SecurityException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
				Log.d(TAG, "open serialport("+path +") error:"+e.toString());
				return false;
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
				Log.d(TAG, "open serialport("+path +") error:"+e.toString());
				return false;
			}
			return true;
	}
	```
*  向串口发送字符串
	```
	mSerialPort.sendMsg("112233445566");
	```
*  关闭串口
	```
	mSerialPort.closeSerialPort();
	```
### 3.4 安装升级 
1. 本地ota包升级
	```
	函数：public void installPackage(String path) 
	描述：将重启进行ota包升级，目前仅支持放置在内置存储根目录，即/sdcard/下
	参数：path  ota包的绝对路径
	范例：  
	mFireflyApi.installPackage("/mnt/sdcard/update.zip");
	```
2. 重启进入Recovery
	```
	函数：public void rebootRecovery() 
	描述：重启进入Recovery
	范例：  
	mFireflyApi.rebootRecovery();
	```
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
### 3.7 定时开关机　　
定时开关机已经在系统里面内置了一个定时开关机apk，通过与其交互实现了几种定时开关机的功能

注：rk3399 平台不支持预置定时开关机功能
#### 3.7.1 预置定时开关机功能
1. 设置一次定时开机
	```
	函数：public void setPowerOnAlarm(boolean enabled,long alarm_time)
	描述：指定UTC时间，设置定时开机，仅一次不会重复
    参数：enabled    开启/关闭
         alarm_time 开机时间(UTC时间)
	范例：  
	mFireflyApi.setPowerOnAlarm(true,System.currentTimeMillis()+60);
    //设置１分钟后开机
	```
2. 设置重复定时开机
	```
	函数：setPowerOnAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	描述：以周为单位，设置循环的定时开机的时间
    参数：enabled    开启/关闭
         hour　开机时间的小时
         minutes　开机时间的分钟
         daysOfWeek　以周为单位重复，设置定时开机的日期
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
4. 设置一次定时关机
	```
	函数：public void setPowerOffAlarm(boolean enabled,long alarm_time)
	描述：指定UTC时间，设置定时关机，仅一次不会重复
    参数：enabled    开启/关闭
         alarm_time 关机时间(UTC时间)
	范例：  
	mFireflyApi.setPowerOffAlarm(true,System.currentTimeMillis()+60);
    //设置１分钟后开机
	```
5. 设置重复定时关机
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
    
如果以上的功能无法满足您的需求，可以使用一下接口，自己做逻辑实现功能。此接口只是最基本的定时开关机功能，由传入的id控制开启/关闭，重启后失效需要手动再次设置

#### 3.7.2 自定义开关机接口

1. 设置定时开机
	```
	函数：public void setSchedulePowerOn(int id,boolean enabled,long alarm_time)
	描述：设置定时开机,id由用户定义，用于开启和关闭定时开机时使用，重启后失效需要重新设置
    参数：id    定时开机id
    	 enabled    开启/关闭　　
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
    参数：id    定时关机id
    	 enabled    开启/关闭　　
         alarm_time 关机时间(UTC时间)
    备注: 通过多组定时关机id,可以实现多组定时关机功能
	范例：  
	mFireflyApi.setSchedulePowerOff("12",true,System.currentTimeMillis()+60);
    //设置１分钟后关机，id为12
    mFireflyApi.setSchedulePowerOff("12",true,0);
    //取消id为"12"操作
	```