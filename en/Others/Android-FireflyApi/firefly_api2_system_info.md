# FireflyApi2 system information

#### 1. FireflyApi2 version information
```
Function：public String getApiVersion()
Description：FireflyApi2 version information
Example：
String version = mFireflyApi2.getApiVersion();
```
#### 2. Get device model
```
Function：public String getAndroidModel()
Description：Get device model
Example：
String model= mFireflyApi2.getAndroidModel();
```
#### 3. Get  device Android system version
```
Function：public String getAndroidVersion()
Description：Get  device Android system version
Example：
String version= mFireflyApi2.getAndroidVersion();
```
#### 4. Get device RAM size(MB)
```
Function：public long getRamSpace()
Description：Get device RAM size, unit is MB
Return：Returns the RAM size in MB
Example：
long ram = mFireflyApi2.getRamSpace();
```
#### 5. Get device RAM size(GB)
```
Function：public String getFormattedRamSpace()
Description：Get device RAM size  and format it as String
Return：Device RAM size, String format (1.5GB)
Example：
String ram = mFireflyApi2.getFormattedRamSpace();
```
#### 6. Get the built-in Flash size of the device(MB)
```
Function：public long getFlashSpace()
Description：Get the built-in Flash size of the device,unit is MB
Return：Returns the Flash size in MB
Example：
long flash = mFireflyApi2.getFlashSpace();
```
#### 7. Get the built-in Flash size of the device(GB)
```
Function：public String getFormattedFlashSpace()
Description：Get the built-in Flash size of the device，and format it as String
Return：The Flash size of the device, String format (15.5GB)
Example：
String ram = mFireflyApi2.getFormattedFlashSpace();
```
#### 8. Get the firmware kernel version of the device
```
Function：public String getFormattedKernelVersion()
Description：Get the firmware kernel version of the device
Return：Kernel version
Example：
String kernel_version= mFireflyApi2.getFormattedKernelVersion();
```
#### 9. Get the firmware system version of the device
```
Function：public String getAndroidDisplay()
Description：Get the firmware system version of the device
Return：Firmware system version
Example：
String version= mFireflyApi2.getAndroidDisplay();
```

