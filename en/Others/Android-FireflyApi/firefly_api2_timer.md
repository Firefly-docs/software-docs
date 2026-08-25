# FireflyApi2 scheduled task

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
