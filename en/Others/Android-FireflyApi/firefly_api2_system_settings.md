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
