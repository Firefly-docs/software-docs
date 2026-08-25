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
#### 3.3.3 CAN使用
在使用之前需要了解 CanFilter，CanMessage，CanMessageListener，CanSocket 四个类的主要作用和用法。
##### CanFilter
```
构造函数：public CanFilter(final int mask, final int filter)
描述：用于存储过滤接收帧信息
参数：mask 掩码
　　　filter 帧id
范例：
//无接收过滤信息
CanFilter[] nofilters = new CanFilter[0];

//仅接收id为 0x400 和 0x410的帧
CanFilter[] filters = new CanFilter[]{new CanFilter(0xfff, 0x400), new CanFilter(0xfff, 0x410)};
```
##### CanMessage

```
构造函数：public CanMessage(final boolean isEFF, final boolean isRTR, final int id, final byte[] data)
描述：用于存储帧信息
参数：isEFF true 为扩展帧，false 为标准帧
　　　isRTR true 为远程帧，false 为数据帧
　　　id 帧ID
　　　data 帧数据
范例：
存储一帧信息，id 为0x100, 标准帧，数据帧，数据为{0x01,0x02,0x03,0x04,0x05,0x6,0x7},长度为7的帧信息
boolean isEFF = false;
boolean isRTR = false;
int canid = 0x100;
byte[] data = new byte[] {(byte)1, (byte)2, (byte)3, (byte)4, (byte)5, (byte)6,(byte)7};
CanMessage message = new CanMessage(isEFF, isRTR, canid, data);
```
* 获取帧信息 ID
	```
	函数：public final int getRawId()
	描述：获取帧 ID
	范例：
	int can_id = message.getRawId();
	```
*  获取帧信息数据
	```
	函数：public final byte[] getData()
	描述：获取帧信息
	范例：
	byte[] data = message.getData();
	```
* 帧信息
	```
	函数：public final String toString()
	描述：帧信息
	范例：
	Log.i("debug", message.toString());
	```

##### CanMessageListener
```
函数：public void rxedCanMessage()
描述：用于接收到 can 信息的处理接口函数
范例：
public class TestCanHelper implements CanMessageListener {
    public final void rxedCanMessage(final CanMessage message)
    {
        Log.i(TAG, message.toString());
    }
}
```
##### CanSocket
* 打开can设备
	```
	函数：public int canDeviceOpen(final String canDevice)throws IOException
	描述：打开 can 设备，获取 socket 套接字
	参数：canDevice can通道
	返回：socket 套接字，失败返回-1
	范例：
	int socket = mCanSocket.canDeviceOpen("can0");
	```
* 初始化接收任务
	```
	函数：public void initReceiveTask(final int socket, CanFilter[] filters)
	描述：初始化接收任务
    参数：socket 套接字
    　　　filters  过滤器
	范例：
	mCanSocket.initReceiveTask(mRxSocket, filters);
	```
* 开始接收任务
	```
	函数：public boolean startReceiveTask()
	描述：开始接收任务
	返回：true(成功)/false(失败)
	范例：
	boolean result =  mCanSocket.startReceiveTask();
	```
* 停止接收任务
	```
	函数：public boolean stopReceiveTask()
	描述：停止接收任务
	返回：true(成功)/false(失败)
	范例：
	boolean result =  mCanSocket.stopReceiveTask();
	```
* 发送帧信息
	```
	函数：public void transmit(CanMessage message, int socket)throws IOException
	描述：发送帧信息
	参数：message 帧信息
    　　　socket 套接字
	范例：
	mCanSocket.transmit(message, mTxSocket);
	```
* 获取接收任务状态
	```
	函数：public boolean RxTaskState()
	描述：获取接收任务状态
	返回：true 接收任务处于运行状态
    　　　false 接收任务处于停止状态
	范例：
	boolean state = mCanSocket.RxTaskState();
	```
* 关闭can设备
	```
	函数：public void closeDevice(final int socket, final String canDevice)
	描述：关闭can设备
	参数：canDevice can通道
	范例：
	mCanSocket.closeDevice(socket, "can0");
	```
* 添加接收监听器
	```
	函数：public void addMessageListener(final CanMessageListener canMessageListener)
	描述：添加接收监听器
	参数：canMessageListener 接收监听器
	范例：
	mCanSocket.addMessageListener(this);
	```
* 移除接收监听器
	```
	函数：public void removeMessageListener(final CanMessageListener canMessageListener)
	描述：移除接收监听器
	参数：canMessageListener 接收监听器
	范例：
	mCanSocket.removeMessageListener(this);
	```
* 清空接收监听器
	```
	函数：public void clearMessageListeners()
	描述：清空接收监听器
	范例：
	mCanSocket.clearMessageListeners();
	```
* 设置波特率
	```
	函数：public static boolean setCanBaudRate(String canDevice, String baudrate)
	描述：设置波特率
	参数：canDevice can设备
    　　　baudrate 波特率
	返回：true(成功)/false(失败)
	范例：
	boolean result = CanSocket.setCanBaudRate("can0", "500000"); // can0波特率为500Kbps
	```
* 获取can设备
	```
	函数：public static String[] getCanDeviceArr()
	描述：获取can设备
	返回：可用的can设备
	范例：
	String[] canDeviceArr = CanSocket.getCanDeviceArr();
	```
#####  简单使用范例
```
import android.util.Log;
import com.firefly.api2.can.CanFilter;
import com.firefly.api2.can.CanMessage;
import com.firefly.api2.can.CanMessageListener;
import com.firefly.api2.can.CanSocket;
import java.io.IOException;

public class CanTest implements CanMessageListener {
    private static String TAG = "CanTest";
    private CanSocket mCanSocket = new CanSocket();
    private int txSocket, rxSocket;

    public CanTest(){}

    public void send()throws IOException {
        boolean isEFF = false;
        boolean isRTR = false;
        int id = 0x100;
        byte[] data = new byte[] {(byte)1, (byte)2, (byte)3, (byte)4, (byte)5, (byte)6,(byte)7, (byte)8};
        CanMessage message = new CanMessage(isEFF, isRTR, id, data);
        CanSocket.setCanBaudRate("can0", "500000");
        txSocket = mCanSocket.canDeviceOpen("can0");
        mCanSocket.transmit(message,txSocket);
        mCanSocket.closeDevice(txSocket, "can0");
    }

    public void receive()throws IOException{
        CanFilter[] filter = new CanFilter[0];
        CanSocket.setCanBaudRate("can1", "500000");
        int rxSocket = mCanSocket.canDeviceOpen("can1");
        mCanSocket.initReceiveTask(rxSocket, filter);
        mCanSocket.clearMessageListeners();
        mCanSocket.addMessageListener(this);
        mCanSocket.startReceiveTask();
    }

    public void stopTask(){
        mCanSocket.stopReceiveTask();
        mCanSocket.removeMessageListener(this);
        mCanSocket.closeDevice(rxSocket, "can1");
    }

    @Override
    public void rxedCanMessage(final CanMessage message){
        Log.d(TAG, "rxedCanMessage: " + message.toString());
    }
}
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

