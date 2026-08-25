# FireflyApi2 系统设置

#### 1. 系统关机
```
函数：public void shutDown(boolean showConfirm)
描述：系统关机
参数：showConfirm 是否显示关机框
范例：
mFireflyApi2.shutDown(false);
```
#### 2. 系统重启
```
函数：public void reboot()
描述：系统重启
范例：
mFireflyApi2.reboot();
```
#### 3. 系统休眠
```
函数：public void sleep()
描述：系统休眠
范例：
mFireflyApi2.sleep();
```
#### 4. 截屏
```
函数：public boolean  takeScreenshot(String path,String name)
描述：截图并存储到指定路径
参数：path 存储路径
　　　name 存储的文件名(暂时只支持png格式)
返回：截图是否成功true/false
范例：
mFireflyApi2.takeScreenshot("/sdcard/","123.png");
```
#### 5. 屏幕旋转
```
函数：public void setRotation(int rotation)
描述：屏幕旋转
屏幕参数：rotation 屏幕方向
　　　Surface.ROTATION_0
　　　Surface.ROTATION_90
　　　Surface.ROTATION_180
　　　Surface.ROTATION_270　　
范例：mFireflyApi2.setRotation(Surface.ROTATION_0);
```
#### 6. 获取屏幕方向
```
函数：public int getRotation()
描述：获取屏幕方向
返回：rotation 屏幕方向
　　　Surface.ROTATION_0
　　　Surface.ROTATION_90
　　　Surface.ROTATION_180
　　　Surface.ROTATION_270
范例：
int rotation = mFireflyApi2.getRotation();
```
#### 7. 设置系统日期
```
函数：public void setSysDate( int year, int month, int day)
描述：设置系统日期
参数：year 年
　　　month 月
　　　day 日
范例：
mFireflyApi2.setSysDate(2022,7,21);
```
#### 8. 设置系统时间
```
函数：public void setSysTime( int hour,int minute,int second)
描述：设置系统时间
参数：hour 时
　　　minute 分
　　　second 秒
范例：
mFireflyApi2.setSysTime(5,30,45);
```
#### 9. 同步NTP服务器时间
```
函数：public boolean syncNtpTime( String ntpServer, int timeout)
描述：同步NTP服务器时间
参数：ntpServer 服务器
　　　timeout 超时
返回：为true设置成功，为false设置失败
范例：
mFireflyApi2.syncNtpTime("time.windows.com", 30000);
```

#### 10. 静默安装应用
```
函数：public void silentInstal(String path, IPackageInstallObserver observer)
描述：静默安装应用
参数：path apk路径
　　　IPackageInstallObserver 处理返回的安装结果
范例：
mFireflyApi2.silentInstall("/sdcard/***.apk", new IPackageInstallObserver.Stub(){
    @Override
    public void packageInstalled(String packageName, int returnCode) {
        Log.v("debug", "packageInstalled packageName:" + packageName + ",returnCode:" + returnCode);
    }
});
```
#### 11. 静默卸载应用
```
函数：public void silentUnInstall(String package_name, IPackageDeleteObserver observer)
描述：静默卸载应用
参数：package_name 为需要卸载应用的包名
　　　IPackageDeleteObserver   处理返回的卸载结果
备注：只支持卸载安装的应用，不支持卸载内置应用
范例：
mFireflyApi2.silentUnInstall("com.android.settings", new IPackageDeleteObserver.Stub() {
    @Override
    public void packageDeleted(String packageName, int returnCode) {
        Log.v("debug", "packageDeleted packageName:" + packageName + ",returnCode:" + returnCode);
    }
});
```
#### 12. 运行shell命令
```
函数：public Command execCmd(String cmd)
描述：运行shell命令
参数：cmd shell命令
返回：Command input 输入命令
　　　output 输出结果
　　　exitStatus shell运行状态，为0是正常退出
范例：
mFireflyApi2.execCmd("ls");
```
#### 13. su权限运行shell命令
```
函数：public static Command execSuCmd(String cmd)
描述：su权限运行shell命令
参数：cmd shell命令
返回：Command input 输入命令
　　　output 输出结果
　　　exitStatus shell运行状态，为0是正常退出
范例：
mFireflyApi2.execSuCmd("cat init.rk30board.rc");//init.rk30board.rc 默认权限750
```
#### 14. 应用开机自启动
```
函数：public void setAppStartup(String packageName)
描述：第三方应用开机自启动
参数：packageName app 包名
范例：
mFireflyApi2.setAppStartup("com.tchip.testscheduleonoff");
```
#### 15. 取消应用开机自启动
```
函数：public void cleanAppStartup()
描述：取消应用开机自启动
范例：
mFireflyApi2.cleanAppStartup();
```

#### 16. 状态栏显示/隐藏
```
函数：public void setStatusBar(boolean show)
描述：显示/隐藏状态栏
参数：show 为true显示状态栏，为false隐藏状态栏
范例：
mFireflyApi.setStatusBar(false);
```

#### 17. 获取FService状态
```
函数：public boolean getFSeviceState()
描述：获取FServervice状态，当处于off时api不可用
返回：true/false online/offline
范例：
boolean isOnline = mFireflyApi.getFSeviceState();
```

* 通过回调获取Fservice状态
```
public class MainActivity implements FireflyApi2.FServiceStateChangeListener{
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		FireflyApi2.getInstance().setServiceStateChangeListener(this);
	}

	@Override
	public void onServiceStateChange(boolean online) {
		new Handler(Looper.getMainLooper()).post(() ->
				Toast.makeText(MainActivity.this, "Service status is " + (online ? "on" : "off"), Toast.LENGTH_SHORT).show()
		);
	}
}
```


<!--
7. 取消屏幕旋转
	```
	函数：public void  thawRotation()
	描述：取消屏幕旋转
	范例：
	mFireflyApi.thawRotation();
	```
16. 背光开关
	```
	函数：public void  setLcdBackLight(boolean on)
	描述：只关背光，却不进入休眠，软件继续运行
	参数：on 为true开背光，为false关背光
	范例：
	mFireflyApi.setLcdBackLight(false);
	```
17. 是否存在背光
	```
	函数：public boolean  hasLcdBackLight()
	描述：判断是否存在背光
	返回：为true存在背光，为false不存在背光
	范例：
	boolean has = mFireflyApi.hasLcdBackLight();
	```
18. 设置屏幕亮度
	```
	函数：public boolean setBrightness(int brightness)
	描述：设置屏幕亮度
	参数：brightness亮度取值范围0-255
	返回：为true设置成功，为false 设置失败
	范例：
	boolean success= mFireflyApi.setBrightness(130);
	```
19. 获取屏幕亮度
	```
	函数：public int getBrightness()
	描述：获取屏幕亮度
	返回：brightness屏幕亮度，获取失败时返回-1
	范例：
	int brightness= mFireflyApi.getBrightness();
	```
	20. 保存系统logcat
	```
	函数：public void saveLogcat(String folderPath,String fileName)
	描述：抓取Android层的LOG并保存相应目录
	参数：folderPath 保存路径 　　
    　　　fileName 保存文件名
	范例：
	mFireflyApi.saveLogcat("/mnt/sdcard/","123.log");
	```
21. 获取Hdmiin当前连接状态
	```
    函数：public　String getHdmiinStatus()
	描述：获取Hdmiin当前连接状态
	返回：STATUS_HDMI_IN_CONNECT＝"1"　hdmiin已连接 　　
    　　　STATUS_HDMI_IN_NO_CONNECT＝"0"　hdmiin未连接   　　
	范例：
	String hdmiin_status = mFireflyApi.getHdmiinStatus();
	```
22. 获取当前的屏幕数量
	```
    函数：public int getScreenNumber(Context context)
	描述：获取当前的屏幕数量
	返回：失败时返回0  　　
	范例：
	int screen_number = mFireflyApi.getScreenNumber(context);
	```
-->

