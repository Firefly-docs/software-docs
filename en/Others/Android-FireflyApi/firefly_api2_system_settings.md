# FireflyApi2 system settings

#### 1. System shutdown
```
Function：public void shutDown(boolean showConfirm)
Description：System shutdown
Parameters：showConfirm  Does showConfirm display the shutdown box(true/false)
Example：
mFireflyApi2.shutDown(false);
```
#### 2. System reboot
```
Function：public void reboot()
Description：System reboot
Example：
mFireflyApi2.reboot();
```
#### 3. System sleep
```
Function：public void sleep()
Description：System sleep
Example：
mFireflyApi2.sleep();
```
#### 4. Screenshot
```
Function：public boolean takeScreenshot(String path,String name)
Description：Take a screenshot and save it to the specified path
Parameters：path  Storage path
　　　　　　name File stored name (available only in PNG format)
Return： Whether a screenshot is successfully captured true/false
Example：
mFireflyApi2.takeScreenshot("/sdcard/","123.png");
```
#### 5. Screen rotation
```
Function：public void setRotation(int rotation)
Description：Screen rotation
Screen parameters：rotation Screen direction
　　　Surface.ROTATION_0
　　　Surface.ROTATION_90
　　　Surface.ROTATION_180
　　　Surface.ROTATION_270　　
Example：mFireflyApi2.setRotation(Surface.ROTATION_0);
```
#### 6. Get the screen direction
```
Function：public int getRotation()
Description：Get the screen direction
Return：rotation  Screen direction
　　　Surface.ROTATION_0
　　　Surface.ROTATION_90
　　　Surface.ROTATION_180
　　　Surface.ROTATION_270
Example：
int rotation = mFireflyApi2.getRotation();
```
#### 7. Set system date
```
Function：public void setSysDate( int year, int month, int day)
Description：Set system date
Parameters：year, month, day
Example：
mFireflyApi2.setSysDate(2022,7,21);
```
#### 8. Set system time
```
Function：public void setSysTime( int hour,int minute,int second)
Description：Set system time
Parameters：hour, minute, second
Example：
mFireflyApi2.setSysTime(5,30,45);
```
#### 9. Synchronize NTP server time
```
Function：public boolean syncNtpTime( String ntpServer, int timeout)
Description：Synchronize NTP server time
Parameters：ntpServer Server
　　　　　　timeout Timeout
Return：true(success)/false(fail)
Example：
mFireflyApi2.syncNtpTime("time.windows.com", 30000);
```

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

#### 16. Display/Hide status bar
```
Function：public void setStatusBar(boolean show)
Description：Display/Hide status bar
Parameters：true/false display/hide status bar
Example：
mFireflyApi.setStatusBar(false);
```

#### 17. Get FService status
```
Function：public boolean getFSeviceState()
Description：get FServervice status, API not available when in off state
Return：true/false online/offline
Example：
boolean isOnline = mFireflyApi.getFSeviceState();
```

* Obtain Fservice status via callback
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


