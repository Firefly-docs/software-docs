# FireflyApi timer
### 3.7 Timer switch　　
The timer switch has built-in a time switch apk in the system, and realizes the functions of different timers through interaction

Note: The rk3399 platform does not support the preset timer switch function.
#### 3.7.1 Preset timer switch function
1. Set power-on alarm
	```
	Function：public void setPowerOnAlarm(boolean enabled,long alarm_time)
	Description：Specify the UTC time, set the power-on alarm with no repetition
    Parameters：enabled    enable/disenable
         alarm_time startup time (UTC time)
	Example：  
	mFireflyApi.setPowerOnAlarm(true,System.currentTimeMillis()+60);
    //set to boot up after 1 minute
	```
2. Set the repeat timer to boot up
	```
	Function：setPowerOnAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	Description：Set a repeat cycle timer to boot up on a weekly basis
    Parameters：enabled    enable/disenable
         hour　 hour of boot time
         minutes　 minutes of boot time
         daysOfWeek　 Set a repeat cycle date to boot up on a weekly basis
         　　　　　　　SUNDAY -> MONDAY
	 	            0b1111111
	Example：  
	mFireflyApi.setPowerOnAlarmRepeat(true,10,30,0b1000011);
    // Set to boot up at 10:30 on Monday, Tuesday and Sunday
	```
3. Get the state of scheduled boot-up
	```
	Function：public Alarm getPowerOnAlarm()
	Description：Get the state of scheduled boot-up
	Example：  
	Alarm　powerOnAlarm =mFireflyApi.getPowerOnAlarm();
	```
4. Set a timed shutdown
	```
	Function：public void setPowerOffAlarm(boolean enabled,long alarm_time)
	Description：Specify the UTC time, set the power-off alarm with no repetition
    Parameters：enabled    enable/disenable
         alarm_time shutdown time (UTC time)
	Example：  
	mFireflyApi.setPowerOffAlarm(true,System.currentTimeMillis()+60);
    // set to boot up after 1 minute
	```
5. Set a repeat timer for automatic shutdown
	```
	Function：setPowerOffAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	Description：Set a repeat cycle timer to boot up on a weekly basis
    Parameters：enabled    enable/disenable
         hour　 hour of boot time
         minutes　 minutes of boot time
         daysOfWeek　 Set a repeat cycle date to boot up on a weekly basis
         　　　　　　　SUNDAY -> MONDAY
	 	            0b1111111
	Example：  
	mFireflyApi.setPowerOffAlarmRepeat(true,10,30,0b1000011);
    // Set to shut down device at 10:30 on Monday, Tuesday and Sunday
	```
6. Get the state of scheduled shutdown
	```
	Function：public Alarm getPowerOffAlarm()
	Description：Get the state of scheduled shutdown
	Example：  
	Alarm　powerOffAlarm =mFireflyApi.getPowerOffAlarm();
	```
    
If the above functions do not meet your needs, you can use the interface to achieve the desired functions through the programming. This interface is the most basic function to boot up/shut down device at a fixed time. It is turned on/off by the incoming id control. It needs to be manually set again after restart.

#### 3.7.2 Custom the interface to boot up/shut down device

1. Set a scheduled boot-up
	```
	Function：public void setSchedulePowerOn(int id,boolean enabled,long alarm_time)
	Description：Set a scheduled boot-up, id is defined by the user, which is used to turn on and off the scheduled boot-up. It needs to reset after restart
    Parameters：id    startup timer id
    	 enabled    enable/disenable　　
         alarm_time startup time (UTC time)
    Remark: Multiple scheduled boot-ups can be realized by multiple sets of startup timer id
	Example：  
	mFireflyApi.setSchedulePowerOn("12",true,System.currentTimeMillis()+60);
    // Set to start up device after 1 minute, id is 12
    mFireflyApi.setSchedulePowerOn("12",true,0);
    // Cancel the id that is "12"
	```
    
2. Set a scheduled shutdown
	```
	Function：public void setSchedulePowerOff(int id,boolean enabled,long alarm_time)
	Description：Set a scheduled shutdown, id is defined by the user, which is used to turn on and off the scheduled shutdown. It needs to reset after restart
    Parameters：id    shutdown timer id
    	 enabled    enable/disenable　　
         alarm_time shutdown time (UTC time)
    Remark: Multiple scheduled shutdowns can be realized by multiple sets of shutdown timer id
	Example：  
	mFireflyApi.setSchedulePowerOff("12",true,System.currentTimeMillis()+60);
    // Set to shut down device after 1 minute, id is 12
    mFireflyApi.setSchedulePowerOff("12",true,0);
    // Cancel the id that is "12"
	```
