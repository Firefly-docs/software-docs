# FireflyApi2 hardware

#### 3.3.1 GPIO
##### 1. Gpio control
```
Function：public boolean gpioCtrl(int gpio, String direction, int value)
Description：Control gpio
Parameters：gpio  Gpio number
　　　　　　direction in, out
　　　　　　value 1/0
Return：true (success)/ false(false)
Example：
boolean success = mFireflyApi2.gpioCtrl(150,"out",1);//GPIO4_C6 gpio the node is 150
```
##### 2. Read the value of gpio
```
Function：public int gpioRead(int gpio)
Description：Read the value of gpio
Parameters：gpio Gpio number
Return：Returns -1 when the read fails
Example：
int gpioValue = mFireflyApi2.gpioRead(150);//GPIO4_C6 gpio the node is 150
```
##### 3. Check if the gpio port is occupied by the system
```
Function：public boolean isGpioOccupied(int gpio)
Description：Check if the gpio port is occupied by the system
Parameters：gpio Gpio number
Return：true(occupied)/false(free)
Example：
boolean occupied  = mFireflyApi2.isGpioOccupied(150);//GPIO4_C6 gpio the node is 150
```
##### 4. Parse the node value of gpio
```
Function：public int gpioParse(String gpioStr)
Description：Parse the node value of gpio, for example GPIO4_C6 is converted to node 150
Parameters：gpioStr
Return：Returns -1 when the parse fails
Example：
int gpioValue= mFireflyApi2.gpioParse("GPIO4_C6");//GPIO4_C6 gpio the node is 150
```
##### 5. Remove gpio node
```
Function：public boolean gpioRemove(int gpio)
Description：Remove gpio node
Parameters：gpio
Return：remove fail return -1
Example：
boolean success = mFireflyApi2.gpioRemove(150);//GPIO4_C6 node is 150
```

#### 3.3.2 Serial port usage
* Import header file
	```
	import com.firefly.api2.serialport.SerialHelper2;
	```
* Open the serial port according to the path and baud rate, and set the callback function
	```
    private SerialHelper2 serialHelper;
	private boolean openSerialPort(String path,int baudrate)
	{
        ......
        serialHelper = new SerialHelper2(path, baudrate) {
            @Override
            protected void onDataReceived(final byte[] data) {
                //Hexadecimal
                Log.v(TAG,serialHelper.getPort()+" onDataReceived:"+ ByteUtil.ByteArrToHex(data).trim());
                //Strings
                Log.v(TAG,serialHelper.getPort()+" onDataReceived-String:"+ new String(data, 0,data.length).trim());
            }
        };

        try {
            serialHelper.open();
            return  serialHelper.isOpen();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return false;
    }
	```
* Send a string to the serial port
	```
	serialHelper.sendTxt("112233445566");
	```
* Send hexadecimal to serial port
   ```
   //such as 0x11,0x22,0x33,0x44,0x55,0x66
   serialHelper.sendHex("112233445566");
   ```
* Close the serial port
	```
	serialHelper.close();
	```
