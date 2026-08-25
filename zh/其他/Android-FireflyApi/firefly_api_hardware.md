# FireflyApi hardware
### 3.3 硬件接口
1. 使能看门狗
	```
	函数：public boolean watchDogEnable(boolean enable)  
	描述：打开/关闭看门狗  
	参数：enable为true启用，为false关闭  
	返回：为true设置成功，为false设置失败  
	备注：打开看门狗后，每隔30s需要喂狗一次，负责看门狗会关闭  
	范例：  
	boolean success = mFireflyApi.watchDogEnable(true);
	```
2. 看门狗喂狗
	```
    函数：public boolean watchDogFeed()
	描述：喂狗一次  
	返回：为true喂狗成功  
	范例：  
	boolean success = mFireflyApi.watchDogFeed();
	```
3. 麦克风切换（3128板型不支持）
	```
    函数：public boolean switchMic(int mic_type)  
	描述：麦克风切换到板载/耳机  
	参数：mic_type   TYPE_DEFAULT_MIC 板载mic   
                    TYPE_HEADSET_MIC 耳机mic
	返回：为true切换成功  
	范例：  
	boolean success = mFireflyApi.switchMic(FireflyApi.TYPE_DEFAULT_MIC);
	```
4. gpio控制
	```
	函数：public boolean gpioCtrl(int gpio, String direction, int value)  
	描述：控制gpio  
	参数：gpio  
	　　　direction in, out, high, low high/low  
	　　　value 1/0  
	返回：为true设置成功  
	范例：  
	boolean success = mFireflyApi.gpioCtrl(263,"out",1);//GPIO8_A7的gpio节点为263
	```
5. gpio读值
	```
    函数：public int gpioRead(int gpio)  
	描述：读取gpio的值  
	参数：gpio  
	返回：读取失败时返回-1  
	范例：  
	int gpioValue = mFireflyApi.gpioRead(263);//GPIO8_A7的gpio节点为263
	```
6. 检查gpio口是否被系统占用
	```
	函数：public boolean isGpioOccupied(int gpio)  
	描述：检查gpio口是否被系统占用  
	参数：gpio  
	返回：为true被占用  
	范例：  
	boolean occupied  = mFireflyApi.isGpioOccupied(263);//GPIO8_A7的gpio节点为263
	```
7. 解析gpio的节点值
	```
	函数：public int gpioParse(String gpioStr)  
	描述：解析gpio的节点值,例GPIO8_A7转换为节点263  
	参数：gpioStr  
	返回：解析失败返回-1  
	范例：  
	int gpioValue= mFireflyApi.gpioParse("GPIO8_A7");//GPIO8_A7的gpio节点为263
	```
8. 设置USB电源,选择控制OTG或USB接口
	```
	函数：public boolean setUsbPower(int type, boolean connect )  
	描述：设置USB电源,选择控制OTG或USB接口  
	参数：type  TYPE_USBHOST //控制USB口  
	 　　　　　　TYPE_OTG     //控制OTG口  
     　　　　　　connect   
	返回：获取失败返回-1  
	范例：  
	mFireflyApi.setUsbPower("TYPE_USBHOST",ture);//连接USB HOST  
	mFireflyApi.setUsbPower("TYPE_USBHOST",false);//断开USB HOST  
	mFireflyApi.setUsbPower("TYPE_OTG",ture);//连接OTG  
	mFireflyApi.setUsbPower("TYPE_OTG",false);//断开OTG
	```
    **注意：
    由于硬件设计不同，3128板型机器OTG接口连接主机时无法断电。**
    
    
9. 串口使用  

* 导入头文件
	```
	import com.firefly.api.serialport.SerialPort;  
	import com.firefly.api.serialport.SerialPort.Callback;  
	import com.firefly.api.serialport.SerialPortFinder;
	```
*  根据路径和波特率打开串口，并设置回调函数  
	```
	private boolean openSerialPort(String path,int baudrate)
	{
			try {
				SerialPort mSerialPort = new SerialPort(new File(path), baudrate, 0);
				mSerialPort.setCallback(new Callback() {
					@Override
					public void onDataReceived(byte[] buffer, int size) {
						// TODO Auto-generated method stub
						String result = new String(buffer, 0, size);
						Log.d(TAG, "onDataReceived:"+result);
					}
            	});
			} catch (SecurityException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
				Log.d(TAG, "open serialport("+path +") error:"+e.toString());
				return false;
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
				Log.d(TAG, "open serialport("+path +") error:"+e.toString());
				return false;
			}
			return true;
	}
	```
*  向串口发送字符串
	```
	mSerialPort.sendMsg("112233445566");
	```
*  关闭串口
	```
	mSerialPort.closeSerialPort();
	```
