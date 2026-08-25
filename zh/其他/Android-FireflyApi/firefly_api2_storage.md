# FireflyApi2 外部存储

1. 获取外部存储U盘路径
	```
	函数：public String getUSBPath(int num)
	描述：获取外部存储U盘路径
    参数：num u盘的index
    返回：失败返回null
	范例：
	String usb1_path  = mFireflyApi.getUSBPath(1);
	```
2. 获取外部存储SD卡路径
	```
	函数：public String getSDcardPath()
	描述：获取外部存储SD卡路径
    返回：失败返回null
	范例：
	String sd_path  = mFireflyApi.getSDcardPath();
	```
3. 卸载外部存储
	```
	函数：public　void doUnmountVolume(String path,boolean force,boolean removeEncryption)
	描述：卸载外部存储
    参数：path 路径为volume mountPoint，负责会抛错
    　　　force　强制卸载,为false时被占用的设备不会卸载
       　removeEncryption　为ture时，包含了force为true,此时会强制卸载并移除加密映射
    /*
     * If force is not set, we do not unmount if there are
     * processes holding references to the volume about to be unmounted.
     * If force is set, all the processes holding references need to be
     * killed via the ActivityManager before actually unmounting the volume.
     * This might even take a while and might be retried after timed delays
     * to make sure we dont end up in an instable state and kill some core
     * processes.
     * If removeEncryption is set, force is implied, and the system will remove any encryption
     * mapping set on the volume when unmounting.
     */
    备注：调用此接口卸载外部存储时，先用StorageList.getVolumeState来检测path的挂载状态，为Environment.MEDIA_MOUNTED时再卸载
	范例：
    StorageList mStorageList = new StorageList(context);
    if(Environment.MEDIA_MOUNTED.equals(mStorageList.getVolumeState(path)))
		mFireflyApi.doUnmountVolume("/mnt/sdcard/",true,false);
	```
-->
