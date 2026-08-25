# 开机 logo 动画修改
## 第一阶段
### 代码修改的方式
* 准备 logo
    * 制作新的 logo.bmp
    	* 图片属性和默认的 logo.bmp 一致，否则会出现颠倒异常
    	* width，height 都为偶数
* 替换 logo
    * 替换`kernel/logo.bmp`

## 第二阶段
### 临时修改的方式
* 准备 bootanimation.zip
	* 准备 png 文件
		* width，height 都为偶数
	* 准备 desc.txt 文件，比如
	
		```shell
        800 1280 30
		p 0 0 part0
        ```
        * `800 1280 30`含义：前两个数字代表图片的像素宽度和高度，30 代表帧数，也就是1秒播放的图片张数
        * `p 0 0 part0`含义：p 代表标志位，0 表示无限循环，0 表示阶段间隔时间为0，part0 表示对应文件夹
        * desc.txt 文本格式：Unix+UTF-8
	* 制作 bootanimation.zip
		* 以**存储方式**压缩成zip文件
		
* push bootanimation.zip

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    adb push bootanimation.zip system/media/bootanimation.zip
    adb shell reboot
    ```

### 代码修改的方式
* 准备 bootanimation.zip
	* 把制作好的`bootanimation.zip`放到`device/rockchip/CPU_TYPE/`目录下
* 在`device/rockchip/CPU_TYPE/device.mk`文件里，添加如下内容：

    ```
    PRODUCT_COPY_FILES += \
           device/rockchip/CPU_TYPE/bootanimation.zip:/system/media/bootanimation.zip
    ```

