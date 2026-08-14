# FireflyApi2

## 1.Overview

FireflyApi2 provides some system interfaces and encapsulates the functional interface that users need, so that users can use the system's common interfaces in an easy and simple way. This document is only a brief description of the interface. For specific use, please refer to Source code of FireflyApi2Demo.

* Support all series models of rk3568/rk3566 Android11 platform.
* Support all series models of rk3588 Android12 platform.
* Support all series models of rk3588/rk3576 Android14 platform.

## 2.Resources download and usage

<font color="red">When using FireflyApi2, first check whether the firmware of the machine is the latest version. The latest version of the firmware has built-in FireflyApi2Demo application based on FireflyApi2. You can find the corresponding model on the [Resource Download](https://en.t-firefly.com/doc/download/140.html) page to view and download the latest firmware. At the same time, you can also synchronize the SDK to the latest submission. The specific steps are as follows: first, select the corresponding [wiki](https://en.t-firefly.com/wiki), then find the synchronized method in the Android `Development`-> `Build the Android Firmware`.</font>

FireflyApi2Demo source code download:
* [FireflyApi2Demo](https://drive.google.com/drive/folders/1qlPqRdS-lPrYwqaHnh5ofBe7q_Ic27Rt?usp=sharing)

The demo mainly contains fireflyapi2.aar, the source code of FireflyApi2Demo. Its structure directory is as follows:
```
fireflyapi2demo/
├── app
│   ├── build.gradle
│   ├── libs
│   │   └── fireflyapi2.aar
│   ├── proguard-rules.pro
│   └── src
├── build.gradle
├── gradle
├── gradle.properties
├── gradlew
├── gradlew.bat
├── local.properties
└── settings.gradle
```

### 2.1 Brief introduction of FireflyApi2Demo application

FireflyApi2Demo is a demo program based on the interface of FireflyApi2. Users can refer to the source code of the demo to realize the required functions.

* The application is as shown in the figure:

![](../../../img/Android-FireflyApi2/fireflyapi2demo_en.png)

* After clicking to enter the application, there will be a corresponding interface implementation list, as shown in the figure:

![](../../../img/Android-FireflyApi2/fireflyapi2demo_ui_en.png)

### 2.2 FireflyApi2 Usage
#### 2.2.1 Android Studio

Android studio is an Android integrated development tool launched by Google. Based on IntelliJ IDEA and similar to eclipse ADT, Android Studio provides integrated Android development tools for development and debugging.

#### 2.2.2 Import fireflyapi2.aar in the project

FireflyApi2Demo presents the corresponding functions of the interface to the user. If needs to write own application, it can put fireflyapi2.aar in the `libs` directory:
```
app/
├── build.gradle
├── libs
│   └── fireflyapi2.aar
```
Then configure build.gradle file, you can customize your application with FireflyApi2.

```diff
dependencies {
    ......
+   implementation files('libs/fireflyapi2.aar')
}
```

## 3.FireflyApi2 interface description

To use the interface of FireflyApi2, you need to import the dependency and obtain the instantiated object:
```
import com.firefly.api2.FireflyApi2;

public class *** {
	......
	private static FireflyApi2 mFireflyApi2 = FireflyApi2.getInstance();
	......
}
```

### 3.1 System information

#### 1. FireflyApi2 version information
```
Function：public String getApiVersion()
Description：FireflyApi2 version information
Example：
String version = mFireflyApi2.getApiVersion();
```
#### 2. Get device model
```
Function：public String getAndroidModel()
Description：Get device model
Example：
String model= mFireflyApi2.getAndroidModel();
```
#### 3. Get  device Android system version
```
Function：public String getAndroidVersion()
Description：Get  device Android system version
Example：
String version= mFireflyApi2.getAndroidVersion();
```
#### 4. Get device RAM size(MB)
```
Function：public long getRamSpace()
Description：Get device RAM size, unit is MB
Return：Returns the RAM size in MB
Example：
long ram = mFireflyApi2.getRamSpace();
```
#### 5. Get device RAM size(GB)
```
Function：public String getFormattedRamSpace()
Description：Get device RAM size  and format it as String
Return：Device RAM size, String format (1.5GB)
Example：
String ram = mFireflyApi2.getFormattedRamSpace();
```
#### 6. Get the built-in Flash size of the device(MB)
```
Function：public long getFlashSpace()
Description：Get the built-in Flash size of the device,unit is MB
Return：Returns the Flash size in MB
Example：
long flash = mFireflyApi2.getFlashSpace();
```
#### 7. Get the built-in Flash size of the device(GB)
```
Function：public String getFormattedFlashSpace()
Description：Get the built-in Flash size of the device，and format it as String
Return：The Flash size of the device, String format (15.5GB)
Example：
String ram = mFireflyApi2.getFormattedFlashSpace();
```
#### 8. Get the firmware kernel version of the device
```
Function：public String getFormattedKernelVersion()
Description：Get the firmware kernel version of the device
Return：Kernel version
Example：
String kernel_version= mFireflyApi2.getFormattedKernelVersion();
```
#### 9. Get the firmware system version of the device
```
Function：public String getAndroidDisplay()
Description：Get the firmware system version of the device
Return：Firmware system version
Example：
String version= mFireflyApi2.getAndroidDisplay();
```

### 3.2 System settings
#### 1. System shutdown
```
Function：public void shutDown(boolean showConfirm)
Description：System shutdown
Parameters：showConfirm  Does showConfirm display the shutdown box(true/false)
Example：
mFireflyApi2.shutDown(false);
```
#### 2. System reboot
```
Function：public void reboot()
Description：System reboot
Example：
mFireflyApi2.reboot();
```
#### 3. System sleep
```
Function：public void sleep()
Description：System sleep
Example：
mFireflyApi2.sleep();
```
#### 4. Screenshot
```
Function：public boolean takeScreenshot(String path,String name)
Description：Take a screenshot and save it to the specified path
Parameters：path  Storage path
　　　　　　name File stored name (available only in PNG format)
Return： Whether a screenshot is successfully captured true/false
Example：
mFireflyApi2.takeScreenshot("/sdcard/","123.png");
```
#### 5. Screen rotation
```
Function：public void setRotation(int rotation)
Description：Screen rotation
Screen parameters：rotation Screen direction
　　　Surface.ROTATION_0
　　　Surface.ROTATION_90
　　　Surface.ROTATION_180
　　　Surface.ROTATION_270　　
Example：mFireflyApi2.setRotation(Surface.ROTATION_0);
```
#### 6. Get the screen direction
```
Function：public int getRotation()
Description：Get the screen direction
Return：rotation  Screen direction
　　　Surface.ROTATION_0
　　　Surface.ROTATION_90
　　　Surface.ROTATION_180
　　　Surface.ROTATION_270
Example：
int rotation = mFireflyApi2.getRotation();
```
#### 7. Set system date
```
Function：public void setSysDate( int year, int month, int day)
Description：Set system date
Parameters：year, month, day
Example：
mFireflyApi2.setSysDate(2022,7,21);
```
#### 8. Set system time
```
Function：public void setSysTime( int hour,int minute,int second)
Description：Set system time
Parameters：hour, minute, second
Example：
mFireflyApi2.setSysTime(5,30,45);
```
#### 9. Synchronize NTP server time
```
Function：public boolean syncNtpTime( String ntpServer, int timeout)
Description：Synchronize NTP server time
Parameters：ntpServer Server
　　　　　　timeout Timeout
Return：true(success)/false(fail)
Example：
mFireflyApi2.syncNtpTime("time.windows.com", 30000);
```

#### 10. Silent installation
```
Function：public void silentInstal(String path, IPackageInstallObserver observer)
Description：Silent installation
Parameters：path Apk file path
　　　　　　IPackageInstallObserver Process the returned install result
Example：
mFireflyApi2.silentInstall("/sdcard/***.apk", new IPackageInstallObserver.Stub(){
    @Override
    public void packageInstalled(String packageName, int returnCode) {
        Log.v("debug", "packageInstalled packageName:" + packageName + ",returnCode:" + returnCode);
    }
});
```
#### 11. Silent installation
```
Function：public void silentUnInstall(String package_name, IPackageDeleteObserver observer)
Description：Silent installation
Parameters：package_name Is the package name of the application to be uninstalled
　　　　　　IPackageDeleteObserver   Process the returned uninstall result
Remark：Only supports apps installed manually, but not for built-in apps
Example：
mFireflyApi2.silentUnInstall("com.android.settings", new IPackageDeleteObserver.Stub() {
    @Override
    public void packageDeleted(String packageName, int returnCode) {
        Log.v("debug", "packageDeleted packageName:" + packageName + ",returnCode:" + returnCode);
    }
});
```
#### 12. Execute shell command
```
Function：public Command execCmd(String cmd)
Description：Execute shell command
Parameters：cmd shell command
Return：Command input  Input command
　　　　　　　　output  Output result
　　　　　　　　exitStatus  Shell running status, 0 is normal exit
Example：
mFireflyApi2.execCmd("ls");
```
#### 13. su permission to run shell command
```
Function：public static Command execSuCmd(String cmd)
Description：su permission to run shell command
Parameters：cmd shell command
Return：Command input  Input command
　　　　　　　　output  Output result
　　　　　　　　exitStatus  Shell running status, 0 is normal exit
Example：
mFireflyApi2.execSuCmd("cat init.rk30board.rc");//init.rk30board.rc Default permission 750
```
#### 14. Application startup
```
Function：public void setAppStartup(String packageName)
Description：The third-party application starts up automatically
Parameters：packageName  App package name
Example：
mFireflyApi2.setAppStartup("com.tchip.testscheduleonoff");
```
#### 15. Cancel application startup
```
Function：public void cleanAppStartup()
Description：Cancel application startup
Example：
mFireflyApi2.cleanAppStartup();
```

#### 16. Display/Hide status bar
```
Function：public void setStatusBar(boolean show)
Description：Display/Hide status bar
Parameters：true/false display/hide status bar
Example：
mFireflyApi.setStatusBar(false);
```

#### 17. Get FService status
```
Function：public boolean getFSeviceState()
Description：get FServervice status, API not available when in off state
Return：true/false online/offline
Example：
boolean isOnline = mFireflyApi.getFSeviceState();
```

* Obtain Fservice status via callback
```
public class MainActivity implements FireflyApi2.FServiceStateChangeListener{
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		FireflyApi2.getInstance().setServiceStateChangeListener(this);
	}

	@Override
	public void onServiceStateChange(boolean online) {
		new Handler(Looper.getMainLooper()).post(() ->
				Toast.makeText(MainActivity.this, "Service status is " + (online ? "on" : "off"), Toast.LENGTH_SHORT).show()
		);
	}
}
```


### 3.3 Hardware interface
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

### 3.4 Installation upgrade
#### 1. Local ota package upgrade
```
Function：public void installPackage(String path)
Description：Restart the ota package upgrade. Currently, it only supports to put in the built-in storage root directory, i.e./sdcard/
Parameter：path Absolute path to the ota package
Example：
mFireflyApi2.installPackage("/sdcard/update.zip");
```

### 3.5 Network
The network feature needs to add the following permissions:

```
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```
#### 3.5.1 Ethernet

Due to partial interface variations between `Android14` and `Android11/12`, the Ethernet APIs are explained separately below:

##### 3.5.1.1 Android11/Android12
##### 1. Whether it is a dual Ethernet port
```
Function：public boolean isDualEth()
Description：Get whether the device is a dual Ethernet port
Return：true  Dual Ethernet port
　　　　false Single Ethernet port
Example：
boolean isDual =  = mFireflyApi2.isDualEth();
```
##### 2. Get the MAC address of the device's  main Ethernet
```
Function：public  String getEthMacAddress()
Description：Get the MAC address of the device's  main Ethernet
Return：Returns null if failed
Example：
String ethMac  = mFireflyApi2.getEthMacAddress();
```
##### 3. Get device's main Ethernet information
```
Function：public EthernetInfo getEthConfiguration()
Description：Get device's main Ethernet information(including:ip,netmask,gateway,dns1,dns2)
Example：
IpConfig info  = mFireflyApi2.getEthConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 4. Set the IP address of the device's main Ethernet
```
Function：public boolean setEthConfiguration(boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
Description：Set the IP address of the device's main Ethernet
Parameters：isStatic When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
Return：Returns false if failed
Example：
boolean set_static_ip  = mFireflyApi2.setEthConfiguration(true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthConfiguration(false,null,null,null,null,null);
```
##### 5. Set main Ethernet on/off
```
Function：public void setEthEnable(boolean enabled)
Description：Set main Ethernet on/off
Parameters：enabled true(on)/false(off)
Example：
 mFireflyApi2.setEthEnable();
```
##### 6. Get the MAC address of the device's  secondary Ethernet
```
Function：public  String getEthAuxMacAddress()
Description：Get the MAC address of the device's  secondary Ethernet
Return：Returns null if failed
Example：
String auxEthMac  = mFireflyApi2.getEthAuxMacAddress();
```
##### 7. Get device's secondary Ethernet information
```
Function：public EthernetInfo getEthAuxConfiguration()
Description：Get device's main Ethernet information(including:ip,netmask,gateway,dns1,dns2)
Example：
IpConfig info  = mFireflyApi2.getEthAuxConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 8. Set the IP address of the device's secondary Ethernet
```
函数：public boolean setEthAuxConfiguration(boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
Description：Set the IP address of the device's secondary Ethernet
Parameters：isStatic When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
Return：Returns false if failed
Example：
boolean set_static_ip  = mFireflyApi2.setEthAuxConfiguration(true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthAuxConfiguration(false,null,null,null,null,null);
```

##### 3.5.1.2 Android14
##### 1. Get available Ethernet cards on the device
```
Function：public String[] getEthAvailableInterfaces()
Description：Get available Ethernet cards on the device
Return：String[],If null, there are no available Ethernet cards
Example：
String[] ifaces = mFireflyApi2.getEthAvailableInterfaces();
```
##### 2. Get the MAC address of the device's Ethernet
```
Function：public String getEthMacAddress(String iface)
Parameters：iface network card names such as eth0, eth1
Description：Get the MAC address of the device's Ethernet
Return：Returns null if failed
Example：
String ethMac  = mFireflyApi2.getEthMacAddress();
```
##### 3. Get device's Ethernet information
```
Function：public EthernetInfo getEthConfiguration(String iface)
Parameters：iface network card names such as eth0, eth1
Description：Get device's Ethernet information(including:ip,netmask,gateway,dns1,dns2)
Example：
IpConfig info  = mFireflyApi2.getEthConfiguration();
debug("IpAddress:"+info.getIpAddress()+"\n");
debug("Netmask:"+info.getNetmask()+"\n");
debug("Gateway:"+info.getGateway()+"\n");
debug("Dns1:"+info.getDns1()+"\n");
debug("Dns2:"+info.getDns2()+"\n");
```
##### 4. Set the IP address of the device's Ethernet
```
Function：public boolean setEthConfiguration(String iface, boolean isStatic,String ipaddressStr,String maskStr,String gatewayStr,String dns1Str,String dns2Str)
Description：Set the IP address of the device's Ethernet
Parameters：iface network card names such as eth0, eth1
	isStatic When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
Return：Returns false if failed
Example：
boolean set_static_ip  = mFireflyApi2.setEthConfiguration("eth0", true,"192.168.1.3",.....);
boolean set_dhcp_ip  = mFireflyApi2.setEthConfiguration("eth0, "false,null,null,null,null,null);
```
##### 5. Set Ethernet on/off
```
Function：public void setEthEnable(boolean enabled)
Description：Set main Ethernet on/off
Parameters：enabled true(on)/false(off)
Example：
 mFireflyApi2.setEthEnable();
```

#### 3.5.2 Wireless network
##### 1. Open device hotspot
```
Function：public void startTethering(ITetheringCallback callback)
Description：Open device hotspot
Parameter：callback  Event callbacks
Example：
mFireflyApi2.startTethering(new ITetheringCallback.Stub (){
    @Override
    public void onTetheringStarted() throws RemoteException {
        Log.v(TAG,"onTetheringStarted");
    }

    @Override
    public void onTetheringFailed() throws RemoteException {
        Log.v(TAG,"onTetheringFailed");
    }
});
```
##### 2. Close device hotspot
```
Function：public void stopTethering()
Description：Close device hotspot
Example：
mFireflyApi2.stopTethering();
```
##### 3. Is the device hotspot turned on
```
Function：public boolean isWifiHotspotEnabled()
Description：Is the device hotspot turned on
Return：true(on)/false(off)
Example：
boolean isEnabled  = mFireflyApi2.isWifiHotspotEnabled();
```
##### 4. Get device hotspot configuration information
```
Function：public SoftApConfiguration getWifiHotspotConfig()
Description：Get device hotspot configuration information
Return：SoftApConfiguration
Example：
SoftApConfiguration config = mFireflyApi2.getWifiHotspotConfig();
```
##### 5. Configure device hotspot information
```
Function：public void configWifiHotspot(String ssid, String password)
Description：Configure device hotspot information(Hotspot name, password)
Parameters：ssid Hotspot name
　　　　　　password password
Return：SoftApConfiguration
Example：
mFireflyApi2.configWifiHotspot("AndroidAP_Test","12345678");
```
##### 6. Automatically shutdown device hotspot
```
Function：public void setWifiHotspotAutoShutdownEnabled(boolean enabled)
Description：Automatically shutdown device hotspot(No device connection)
Parameters：enabled true(on)/false(off)
Example：
mFireflyApi2.setWifiHotspotAutoShutdownEnabled(true);
```
##### 7. whether the automatic shutdown function is enabled for the hotspot
```
Function：public boolean isWifiHotspotAutoShutdownEnabled()
Description：whether the automatic shutdown function is enabled for the hotspot
Return：true(on)/false(off)
Example：
boolean mAutoOff = mFireflyApi2.isWifiHotspotAutoShutdownEnabled();
```
##### 8. The device hotspot uses a fixed IP address
```
Function：public void setWifiHostspotUseFixedIfaceAddr(boolean use)
Description：The device hotspot uses a fixed IP address
Parameters：use true(on)/false(off)
Example：
mFireflyApi2.setWifiHostspotUseFixedIfaceAddr("true");//the default IP address is 192.168.43.1/24
```

##### 9. Set a fixed IP address for the device hotspot
```
Function：public void setWifiHostspotFixedIfaceAddr(String ifaceAddr)
Description：Set a fixed IP address for the device hotspot
Parameters：ifaceAddr IP
Example：
mFireflyApi2.setWifiHostspotFixedIfaceAddr("192.168.23.1/24");
```

##### 10. Obtain the IP address of the device's hotspot
```
Function：public String getWifiHostspotFixedIfaceAddr()
Description：Obtain the IP address of the device's hotspot
Example：
String address = mFireflyApi2.getWifiHostspotFixedIfaceAddr();// address = "192.168.23.1/24"
```

### 3.6 Scheduled task　　
#### 1. Set a scheduled startup
```
Function：public void setSchedulePowerOn(int id, boolean enabled, long alarm_time)
Description：Specify the UTC time, set the scheduled startup, only once without repetition
Parameters：id  Timed boot ID
　　　　　　enabled  true(on)/false(off)
　　　　　　alarm_time  Startup time (UTC time)
Example：
//Set start up in one minute, id is 11002
mFireflyApi2.setSchedulePowerOn(11002,true,System.currentTimeMillis()+60);
//Cancel operation with ID 11002
mFireflyApi2.setSchedulePowerOn(11002,false,0);
```
#### 2. Set a scheduled shutdown
```
Function：public void setSchedulePowerOff(int id, boolean enabled, long alarm_time)
Description：Specify the UTC time, set the scheduled shutdown, only once without repetition
Parameters：id Scheduled shutdown ID
　　　　　　enabled true(on)/false(off)
　　　　　　alarm_time Shutdown time (UTC time)
Example：
//Set shutdown after 1 minute, id is 11001
mFireflyApi2.setPowerOffAlarm(11001, true,System.currentTimeMillis()+60);
//Cancel operation with ID 11001
mFireflyApi2.setPowerOffAlarm(11001, false,0);
```
#### 3. Set a scheduled reboot
```
Function：public void setSchedulePowerReboot(int id, boolean enabled, long alarm_time)
Description：Specify the UTC time, set the scheduled reboot, only once without repetition
Parameters：id  Scheduled reboot ID
　　　　　　enabled  true(on)/false(off)
　　　　　　alarm_time  Reboot time (UTC time)
Example：
//Set reboot after 1 minute, id  is 11003
mFireflyApi2.setSchedulePowerReboot(11003, true,System.currentTimeMillis()+60);
//Cancel operation with ID 11003
mFireflyApi2.setSchedulePowerReboot(11003, false,0);
```
If the above functions do not meet your needs, you can use the interface to achieve the desired functions through the programming. This interface is only the most basic scheduled task function, it is controlled on/off by the incoming ID. **It is invalid after reboot and needs to be manually reset**.
