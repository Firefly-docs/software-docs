# FireflyApi network
### 3.5 Network 
The network feature needs to add the following permissions:


1. Get the MAC address of the device's Ethernet
	```
	Function：public  String getEthMacAddress() 
	Description：Get the MAC address of the device's Ethernet
    Return：Returns null if failed 
	Example：  

2. Get the IP address of the device's Ethernet
	```
	Function：public String getEthIpAddress() 
	Description：Get the IP address of the device's Ethernet
	Example：  

3. Get device's Ethernet information
	```
	Function：public EthernetInfo getEthInfo()
	Description：Get device's Ethernet information (including: ip, netmask, gateway, dns1, dns2)
	Example：  

4. Set the IP address of the device's Ethernet
	```
	Function：public boolean setEthIPAddress(boolean use_static_ip,String mIpaddr, String mMask, String mGw, String mDns1,String mDns2)
	Description：Set the IP address of the device's Ethernet
    Parameters：use_static_ip. When true is set to static ip, other parameters are valid, otherwise, when false is set to dynamic ip, it will automatically obtain ip address
    Return：Returns false if failed
	Example：  

5. Whether Ethernet is currently connected
	```
	Function：public boolean isEthConnect()
	Description：Whether Ethernet is currently connected
	Example：  

6. Set Ethernet on/off
	```
	Function：public boolean setEthernetEnabled(boolean enabled)
	Description：Set Ethernet on/off
    Parameters：enabled true(open)/false(close) 
	Example：  

7. Get the type of current network connection
	```
	Function：public String getCurNetworkType()
	Description：Get the type of current network connection
    Return：UNKNOWN/WIFI/ETHERNET/MOBILE
    Remark：Use BroadcatReceiver to monitor the Ethernet ConnectState changes through the monitoring of ETHERNET_STATE_CHANGED_ACTION = "android.net.ethernet.ETHERNET_STATE_CHANGED".
    　　　int connectState=intent.getIntExtra("ethernet_state", -1)
	Example：  
