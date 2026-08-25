# FireflyApi system settings
### 3.2 System settings
1. System shutdown
	```
	Function：public void shutDown(boolean showConfirm)   
	Description：System shutdown   
	Parameters：Does showConfirm display the shutdown box   
	Example：   

2. System restart
	```
	Function：public void reboot()  
	Description：System shutdown  
	Example：  

3. System hibernation
	```
	Function：public void sleep()  
	Description：System shutdown  
	Example：  

4. Screen capture
	```
	Function：public boolean  takeScreenshot(String path,String name)  
	Description：Take a screenshot and save it to the specified path  
	Parameters：path storage path  
	　　　name file stored name (available only in PNG format)  
	Return: Whether a screenshot is successfully captured true/false  
	Example：  

5. Screen rotation
	```
	Function：public boolean  setRotation(int rotation)  
	Description：Rotation   
	Screen parameters：rotation Screen direction:  Surface.ROTATION_0  

                               Surface.ROTATION_270　　
	Example：mFireflyApi.setRotation(Surface.ROTATION_0);
	```
6. Get the screen direction
	```
	Function：public int  getRotation()  
	Description：Get the screen direction  
	Return：rotation Screen direction:      Surface.ROTATION_0  

	Example：  

7. Cancel screen rotation
	```
	Function：public void  thawRotation()  
	Description：Cancel screen rotation  
	Example：  

8. Display / hide status bar 
	```
	Function：public void  setStatusBar(boolean show)  
	Description：Display / hide status bar  
	Parameters：show is set to true, it means to display the status bar, otherwise, false to hide the status bar  
	Example：  

9. Backlight switch
	```
	Function：public void  setLcdBackLight(boolean on)  
	Description：Turns the backlight off without hibernating the device, the software still runs  
	Parameters：on is set to true, it means to turn on the backlight, otherwise, false to turn it off  
	Example：  

10. Is there a backlight
	```
	Function：public boolean  hasLcdBackLight()  
	Description：Determine if there is backlight  
	Return：The value is set to true, it means there is backlight, or false for no backlight otherwise  
	Example：  

11. Set the screen brightness
	```
	Function：public boolean setBrightness(int brightness)  
	Description：Set the screen brightness  
	Parameters：brightness range 0-255  
	Return：true on success, false on failure 
	Example：  

12. Get screen brightness
	```
	Function：public int getBrightness()  
	Description：Get screen brightness  
	Return：screen brightness，returns -1 when the failure occurs  
	Example：  

13. Set system time
	```
	Function：public boolean setTime( int year, int month, int day, int hour,  
    	 int minute,int second)  
	Description：Set system time  
	Parameters：year       
	　　　month    
	　　　day      
	　　　hour     
	　　　minute   
	　　　second   
	Return：true on success, false on failure  
	Example：  

14. Silent installation
	```
	Function：public boolean silentInstall(String path)  
	Description：Silent installation 
	Parameters：path apk address  
	Return：true on successful installation  
	Example：  

15. Silent uninstallation
	```
	Function：public boolean silentUnInstall(String package_name)  
	Description：Silent uninstallation  
	Parameters：package name the application to be uninstalled  
	Return：Returns true if the app is uninstalled successfully, or false otherwise 
	Remark：Only supports apps installed manually, but not for built-in apps  
	Example：  

16. Execute shell command
	```
    Function：public Command execCmd(String cmd)  
	Description：Execute shell command  
	Parameters：cmd shell command  
	Return：Command　 input command
                   output　results  
                   exitStatus　shell running status, 0 is normal exit  
	Example：  

17. su permission to run shell command
	```
    Function：public static Command execSuCmd(String cmd)  
	Description：su permission to run shell command  
	Parameters：cmd shell command  
	Return：Command   input command
	  　　　　　　　　output results  
	  　　　　　　　　exitStatus　shell running status, 0 is normal exit  
	Example：  
	mFireflyApi.execSuCmd("cat init.rk30board.rc");//init.rk30board.rc Default permission 750
	```
18. Save system logcat
	```
	Function：public void saveLogcat(String folderPath,String fileName)
	Description：Grab the LOG message in the Android layer and save it to the corresponding directory 
	Parameters：folderPath save path 　　
    　　　fileName　　save file name
	Example：  

19. Get Hdmiin current connection status
	```
    Function：public　String getHdmiinStatus()
	Description：Get Hdmiin current connection status 
	Return：STATUS_HDMI_IN_CONNECT＝"1"　hdmiin connected 　　
    　　　STATUS_HDMI_IN_NO_CONNECT＝"0"　hdmiin not connected   　　
	Example：  

20. Get the current number of screens
	```
    Function：public　int getScreenNumber(Context context)
	Description：Get the current number of screens
	Return：Returns 0 if it fails  　　
	Example：  
