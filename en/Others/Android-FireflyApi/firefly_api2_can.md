# FireflyApi2 CAN

#### 3.3.3 CAN usage
Before using, you need to understand the main functions and usage of `CanFilter`, `CanMessage`, `CanMessageListener` and `CanSocket`.
##### CanFilter
```
Constructor：public CanFilter(final int mask, final int filter)
Description：Used filter the received frame information
Parameters：mask  Mask
　　　　　　filter  Frame ID
Example：
//No filtering
CanFilter[] nofilters = new CanFilter[0];

//Only frames with ID 0x400 and 0x410 are received
CanFilter[] filters = new CanFilter[]{new CanFilter(0xfff, 0x400), new CanFilter(0xfff, 0x410)};
```
##### CanMessage

```
Constructor：public CanMessage(final boolean isEFF, final boolean isRTR, final int id, final byte[] data)
Description：Used to store frame information
Parameters：isEFF true(Extended frame)/false(Standard frame)
　　　　　　isRTR  true(Remote frame)/false( Data frame)
　　　　　　id  Frame Id
　　　　　　data Frame Data
Example：
//Store one frame of information, id is 0x100, Standard frame, Data frame,
//Data are {0x01,0x02,0x03,0x04,0x05,0x6,0x7}, Frame information with length of 7
boolean isEFF = false;
boolean isRTR = false;
int canid = 0x100;
byte[] data = new byte[] {(byte)1, (byte)2, (byte)3, (byte)4, (byte)5, (byte)6,(byte)7};
CanMessage message = new CanMessage(isEFF, isRTR, canid, data);
```
* Get the ID of frame information
	```
	Function：public final int getRawId()
	Description：Get the ID of frame information
	Example：
	int can_id = message.getRawId();
	```
*  Get the data of frame information
	```
	Function：public final byte[] getData()
	Description：Get the data of frame information
	Example：
	byte[] data = message.getData();
	```
* Frame information strings
	```
	Function：public final String toString()
	Description：Frame information strings
	Example：
	Log.i("debug", message.toString());
	```

##### CanMessageListener
```
Function：public void rxedCanMessage()
Description：Processing interface function for receiving can information
Example：
public class TestCanHelper implements CanMessageListener {
    public final void rxedCanMessage(final CanMessage message)
    {
        Log.i(TAG, message.toString());
    }
}
```
##### CanSocket
* Open CAN device
	```
	Function：public int canDeviceOpen(final String canDevice)throws IOException
	Description：Open CAN device，get socket
	Para：canDevice CAN device
	Return：socket, return -1 if failed
	Example：
	int socket = mCanSocket.canDeviceOpen("can0");
	```
* Initialize receive task
	```
	Function：public void initReceiveTask(final int socket, CanFilter[] filters)
	Description：Initialize receive task
    Parameters：socket Socket
    　　　　　　filters  Filters
	Example：
	mCanSocket.initReceiveTask(mRxSocket, filters);
	```
* Start receive task
	```
	Function：public boolean startReceiveTask()
	Description：Start receive task
	Return：true(success)/false(fail)
	Example：
	boolean result =  mCanSocket.startReceiveTask();
	```
* Stop receive task
	```
	Function：public boolean stopReceiveTask()
	Description：Stop receive task
	Return：true(success)/false(fail)
	Example：
	boolean result =  mCanSocket.stopReceiveTask();
	```
* Send frame information
	```
	Function：public void transmit(CanMessage message, int socket)throws IOException
	Description：Send frame information
	Parameters：message Frame information
    　　　　　　socket Socket
	Example：
	mCanSocket.transmit(message, mTxSocket);
	```
* Get the Status of receiving task
	```
	Function：public boolean RxTaskState()
	Description：Get the Status of receiving task
	Return：true Receive task is running
    　　　　false Receive task is stopped
	Example：
	boolean state = mCanSocket.RxTaskState();
	```
* Close CAN device
	```
	Function：public void closeDevice(final int socket, final String canDevice)
	Description：Close CAN device
	Parameters：canDevice CAN device
	Example：
	mCanSocket.closeDevice(socket, "can0");
	```
* Add receive listener
	```
	Function：public void addMessageListener(final CanMessageListener canMessageListener)
	Description：Add receive listener
	Parameters：canMessageListener Receive listener
	Example：
	mCanSocket.addMessageListener(this);
	```
* Remove receive listener
	```
	Function：public void removeMessageListener(final CanMessageListener canMessageListener)
	Description：Remove receive listener
	Parameters：canMessageListener Receive listener
	Example：
	mCanSocket.removeMessageListener(this);
	```
* Clear receive listener
	```
	Function：public void clearMessageListeners()
	Description：Clear receive listener
	Example：
	mCanSocket.clearMessageListeners();
	```
* Set baud rate
	```
	Function：public static boolean setCanBaudRate(String canDevice, String baudrate)
	Description：Set baud rate
	Parameters：canDevice CAN device
    　　　　　　baudrate baud rate
	Return：true(success)/false(fail)
	Example：
	boolean result = CanSocket.setCanBaudRate("can0", "500000"); // can0 baud rate is 500Kbps
	```
* Get CAN device
	```
	Function：public static String[] getCanDeviceArr()
	Description：Get CAN device
	Return：Available CAN devices
	Example：
	String[] canDeviceArr = CanSocket.getCanDeviceArr();
	```
#####  Simple usage example
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

