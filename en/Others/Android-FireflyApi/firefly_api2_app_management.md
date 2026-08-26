# FireflyApi2 app management

#### 10. Silent installation
```
Function：public void silentInstal(String path, IPackageInstallObserver observer)
Description：Silent installation
Parameters：path Apk file path
　　　　　　IPackageInstallObserver Process the returned install result
Example：
mFireflyApi2.silentInstall("/sdcard/***.apk", new IPackageInstallObserver.Stub(){
    @Override
    public void packageInstalled(String packageName, int returnCode) {
        Log.v("debug", "packageInstalled packageName:" + packageName + ",returnCode:" + returnCode);
    }
});
```
#### 11. Silent installation
```
Function：public void silentUnInstall(String package_name, IPackageDeleteObserver observer)
Description：Silent installation
Parameters：package_name Is the package name of the application to be uninstalled
　　　　　　IPackageDeleteObserver   Process the returned uninstall result
Remark：Only supports apps installed manually, but not for built-in apps
Example：
mFireflyApi2.silentUnInstall("com.android.settings", new IPackageDeleteObserver.Stub() {
    @Override
    public void packageDeleted(String packageName, int returnCode) {
        Log.v("debug", "packageDeleted packageName:" + packageName + ",returnCode:" + returnCode);
    }
});
```
#### 12. Execute shell command
```
Function：public Command execCmd(String cmd)
Description：Execute shell command
Parameters：cmd shell command
Return：Command input  Input command
　　　　　　　　output  Output result
　　　　　　　　exitStatus  Shell running status, 0 is normal exit
Example：
mFireflyApi2.execCmd("ls");
```
#### 13. su permission to run shell command
```
Function：public static Command execSuCmd(String cmd)
Description：su permission to run shell command
Parameters：cmd shell command
Return：Command input  Input command
　　　　　　　　output  Output result
　　　　　　　　exitStatus  Shell running status, 0 is normal exit
Example：
mFireflyApi2.execSuCmd("cat init.rk30board.rc");//init.rk30board.rc Default permission 750
```
#### 14. Application startup
```
Function：public void setAppStartup(String packageName)
Description：The third-party application starts up automatically
Parameters：packageName  App package name
Example：
mFireflyApi2.setAppStartup("com.tchip.testscheduleonoff");
```
#### 15. Cancel application startup
```
Function：public void cleanAppStartup()
Description：Cancel application startup
Example：
mFireflyApi2.cleanAppStartup();
```
