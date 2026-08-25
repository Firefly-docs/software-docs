# Boot logo animation modification 
## The first stage 
### Code modification method 
* Prepare the logo 
    * Make a new logo.bmp 
    	* The image attributes are the same as the default logo.bmp, otherwise an upside-down exception will occur 
    	* Both width and height are even numbers 
* Replace logo 
    * Replace `kernel/logo.bmp` 

## Second stage 
### Temporary modification method 
* Prepare bootanimation.zip 
	* Prepare png file 
		* Both width and height are even numbers 
	* Prepare desc.txt file, such as 
	
		```shell
        800 1280 30
		p 0 0 part0
        ```
        * `800 1280 30`Meaning: the first two numbers represent the pixel width and height of the picture, 30 represents the number of frames, which is the number of pictures played in 1 second 
        * `p 0 0 part0`Meaning: p stands for flag bit, 0 stands for infinite loop, 0 stands for the interval time between stages is 0, part0 stands for the corresponding folder 
        * desc.txt text format: Unix+UTF-8 
	* Make bootanimation.zip 
		* Compressed into a zip file in **storage method** 
		
* Push bootanimation.zip

    ```shell
    adb shell setprop persist.sys.root_access 3
	adb root
	adb remount
    adb push bootanimation.zip system/media/bootanimation.zip
    adb shell reboot
    ```

### Code modification method 
* Prepare bootanimation.zip 
	* Put the prepared `bootanimation.zip` into the `device/rockchip/CPU_TYPE/` directory 
* In the `device/rockchip/CPU_TYPE/device.mk` file, add the following content: 

    ```
    PRODUCT_COPY_FILES += \
           device/rockchip/CPU_TYPE/bootanimation.zip:/system/media/bootanimation.zip
    ```

