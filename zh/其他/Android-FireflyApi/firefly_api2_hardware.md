# FireflyApi2 硬件接口

#### 3.3.1 GPIO
##### 1. gpio 控制
```
函数：public boolean gpioCtrl(int gpio, String direction, int value)
描述：控制 gpio
参数：gpio gpio 编号
　　　direction in, out
　　　value 1/0
返回：true (成功)/ false(失败)
范例：
boolean success = mFireflyApi2.gpioCtrl(150,"out",1);//GPIO4_C6的 gpio 节点为150
```
##### 2. gpio 读值
```
函数：public int gpioRead(int gpio)
描述：读取 gpio 的值
参数：gpio gpio 编号
返回：读取失败时返回-1
范例：
int gpioValue = mFireflyApi2.gpioRead(150);//GPIO4_C6的 gpio 节点为150
```
##### 3. 检查gpio口是否被系统占用
```
函数：public boolean isGpioOccupied(int gpio)
描述：检查 gpio 口是否被系统占用
参数：gpio gpio 编号
返回：true(被占用)/false(空闲)
范例：
boolean occupied  = mFireflyApi2.isGpioOccupied(150);//GPIO4_C6的 gpio 节点为150
```
##### 4. 解析gpio的节点值
```
函数：public int gpioParse(String gpioStr)
描述：解析 gpio 的节点值，例 GPIO4_C6 转换为节点150
参数：gpioStr
返回：解析失败返回-1
范例：
int gpioValue= mFireflyApi2.gpioParse("GPIO4_C6");//GPIO4_C6的 gpio 节点为150
```

##### 5. remove 控制的gpio 节点
```
函数：public boolean gpioRemove(int gpio)
描述：remove 申请控制的gpio节点
参数：gpio
返回：移除失败返回-1
范例：
boolean success = mFireflyApi2.gpioRemove(150);//GPIO4_C6的 gpio 节点为150
```


#### 3.3.2 串口使用
* 导入头文件
	```
	import com.firefly.api2.serialport.SerialHelper2;
	```
* 根据路径和波特率打开串口，并设置回调函数
	```
    private SerialHelper2 serialHelper;
	private boolean openSerialPort(String path,int baudrate)
	{
        ......
        serialHelper = new SerialHelper2(path, baudrate) {
            @Override
            protected void onDataReceived(final byte[] data) {
                //16进制
                Log.v(TAG,serialHelper.getPort()+" onDataReceived:"+ ByteUtil.ByteArrToHex(data).trim());
                //字符串
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
* 向串口发送字符串
	```
	serialHelper.sendTxt("112233445566");
	```
* 向串口发送16进制
   ```
   //如 0x11,0x22,0x33,0x44,0x55,0x66
   serialHelper.sendHex("112233445566");
   ```
* 关闭串口
	```
	serialHelper.close();
	```

<!--
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

-->
