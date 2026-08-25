# FireflyApi system settings
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
