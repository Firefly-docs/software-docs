# FireflyApi storage
### 3.6 External storage related 
1. Get external storage path for USB-flash
	```
	Function：public String getUSBPath(int num)
	Description：Get external storage path for USB-flash
    Parameters：index of num u disk
    Return：Returns null if failed
	Example：  

2. Get the external SD card path
	```
	Function：public String getSDcardPath()
	Description：Get the external SD card path
    Return：Returns null if failed 
	Example：  

3. Uninstall external storage
	```
	Function：public　void doUnmountVolume(String path,boolean force,boolean removeEncryption)
	Description：Uninstall external storage
    Parameters：path is volume mountPoint, it will throw an exception
    　　　Force uninstall, when it is false, the occupied device will not be uninstalled
       　 When removeEncryption is true, it contains force that is true, which will force uninstall and remove the crypto map.

    Remark：When calling this interface to uninstall external storage, use StorageList.getVolumeState to detect the mount state of path, and then uninstall it when it is Environment.MEDIA_MOUNTED
	Example：
