# FireflyApi2 CAN

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
