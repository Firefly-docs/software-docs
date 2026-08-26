# FireflyApi2 应用管理

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
