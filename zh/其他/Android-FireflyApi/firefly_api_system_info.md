# FireflyApi system information
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
