# FireflyApi hardware
### 3.3 Hardware interface
1. Enable watchdog
	```
	Function：public boolean watchDogEnable(boolean enable)  
	Description：Open/close watchdog  
	Parameters：enable is set to true, it means to open watchdog, otherwise, false to disenable 
	Return：true on success, false on failure  
	Remark：After opening the watchdog, you need to take care of feeding the watchdog once every 30s, otherwise, the watchdog will be closed.  
	Example：  

2. Feeding the dog
	```
    Function：public boolean watchDogFeed()
	Description：Feeding the dog once 
	Return：Returns true if it feeds the dog successfully  
	Example：  

3. Microphone switch（3128 board type is not supported）
	```
    Function：public boolean switchMic(int mic_type)  
	Description：Microphone is switched to onboard/headphone  
	Parameters：mic_type   TYPE_DEFAULT_MIC onboard mic   
                    TYPE_HEADSET_MIC headphone mic
	Return：Returns true if the switch is successful  
	Example：  

4. gpio control
	```
	Function：public boolean gpioCtrl(int gpio, String direction, int value)  
	Description：Control gpio  
	Parameters：gpio  
	　　　direction in, out, high, low high/low  
	　　　value 1/0  
	Return：true on success  
	Example：  
	boolean success = mFireflyApi.gpioCtrl(263,"out",1);//GPIO8_A7 gpio the node is 263
	```
5. Read the value of gpio
	```
    Function：public int gpioRead(int gpio)  
	Description：Read the value of gpio  
	Parameters：gpio  
	Return：Returns -1 when the read fails  
	Example：  
	int gpioValue = mFireflyApi.gpioRead(263);//GPIO8_A7 gpio the node is 263
	```
6. Check if the gpio port is occupied by the system
	```
	Function：public boolean isGpioOccupied(int gpio)  
	Description：Check if the gpio port is occupied by the system  
	Parameters：gpio  
	Return：Returns true if occupied  
	Example：  
	boolean occupied  = mFireflyApi.isGpioOccupied(263);//GPIO8_A7 gpio the node is 263
	```
7. Parse the node value of gpio
	```
	Function：public int gpioParse(String gpioStr)  
	Description：Parse the node value of gpio, for example GPIO8_A7 is converted to node 263  
	Parameters：gpioStr  
	Return：Returns -1 when the parse fails  
	Example：  
	int gpioValue= mFireflyApi.gpioParse("GPIO8_A7");//GPIO8_A7 gpio the node is 263
	```
8. Set USB power, choose to control OTG or USB interface
	```
	Function：public boolean setUsbPower(int type, boolean connect )  
	Description：Set USB power, choose to control OTG or USB interface  
	Parameters：type  TYPE_USBHOST //Control USB port  
	 　　　　　　TYPE_OTG     //Control OTG port  
     　　　　　　connect   
	Return：Returns to -1 if failed  
	Example：  
	mFireflyApi.setUsbPower("TYPE_USBHOST",ture);// connect USB HOST  
	mFireflyApi.setUsbPower("TYPE_USBHOST",false);// disconnect USB HOST  
	mFireflyApi.setUsbPower("TYPE_OTG",ture);// connect OTG  
	mFireflyApi.setUsbPower("TYPE_OTG",false);// disconnect OTG
	```
    ** Note：
    Due to different hardware design, the OTG interface of the device with 3128 board type cannot be powered off when connected to the host. **
    
    
9. The use of serial port  

* Import header file

*  Open the serial port according to the path and baud rate, and set the callback function  

*  Send a string to the serial port

*  Close the serial port
