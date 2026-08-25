# FireflyApi2 系统信息

#### 1. FireflyApi2版本信息
```
函数：public String getApiVersion()
描述：FireflyApi2 版本信息
范例：
String version = mFireflyApi2.getApiVersion();
```
#### 2. 获取设备型号
```
函数：public String getAndroidModel()
描述：获取设备型号
范例：
String model= mFireflyApi2.getAndroidModel();
```
#### 3. 获取设备Android系统的版本
```
函数：public String getAndroidVersion()
描述：获取设备Android系统的版本
范例：
String version= mFireflyApi2.getAndroidVersion();
```
#### 4. 获取设备RAM大小(MB)
```
函数：public long getRamSpace()
描述：获取设备RAM大小,单位为MB
返回：返回RAM大小，单位为MB
范例：
long ram = mFireflyApi2.getRamSpace();
```
#### 5. 获取设备RAM大小(GB)
```
函数：public String getFormattedRamSpace()
描述：获取设备RAM大小，并格式化为String
返回：设备的RAM大小，String格式（1.5GB）
范例：
String ram = mFireflyApi2.getFormattedRamSpace();
```
#### 6. 获取设备内置Flash大小(MB)
```
函数：public long getFlashSpace()
描述：获取设备内置Flash大小,单位为MB
返回：返回Flash大小，单位为MB
范例：
long flash = mFireflyApi2.getFlashSpace();
```
#### 7. 获取设备内置Flash大小(GB)
```
函数：public String getFormattedFlashSpace()
描述：获取设备内置Flash大小，并格式化为String
返回：设备的Flash大小，String格式（15.5GB）
范例：
String ram = mFireflyApi2.getFormattedFlashSpace();
```
#### 8. 获取设备固件内核版本
```
函数：public String getFormattedKernelVersion()
描述：获取设备固件内核版本
返回：内核版本
范例：
String kernel_version= mFireflyApi2.getFormattedKernelVersion();
```
#### 9. 获取设备固件系统版本
```
函数：public String getAndroidDisplay()
描述：获取设备固件系统版本
返回：固件系统版本
范例：
String version= mFireflyApi2.getAndroidDisplay();
```

