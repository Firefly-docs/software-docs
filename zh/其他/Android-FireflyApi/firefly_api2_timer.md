# FireflyApi2 定时开关机

#### 1. 设置一次定时开机
```
函数：public void setSchedulePowerOn(int id, boolean enabled, long alarm_time)
描述：指定UTC时间，设置定时开机，仅一次不会重复
参数：id 定时开机id
　　　enabled true(开启)/false(关闭)
　　　alarm_time 开机时间(UTC时间)
范例：
//设置１分钟后开机，id为11002
mFireflyApi2.setSchedulePowerOn(11002,true,System.currentTimeMillis()+60);
//取消id为11002的操作
mFireflyApi2.setSchedulePowerOn(11002,false,0);
```
#### 2. 设置一次定时关机
```
函数：public void setSchedulePowerOff(int id, boolean enabled, long alarm_time)
描述：指定UTC时间，设置定时关机，仅一次不会重复
参数：id 定时关机id
　　　enabled true(开启)/false(关闭)
　　　alarm_time 关机时间(UTC时间)
范例：
//设置１分钟后关机，id为11001
mFireflyApi2.setPowerOffAlarm(11001, true,System.currentTimeMillis()+60);
//取消id为11001的操作
mFireflyApi2.setPowerOffAlarm(11001, false,0);
```
#### 3. 设置一次定时重启
```
函数：public void setSchedulePowerReboot(int id, boolean enabled, long alarm_time)
描述：指定UTC时间，设置定时重启，仅一次不会重复
参数：id 定时重启id
　　　enabled true(开启)/false(关闭)
　　　alarm_time 重启时间(UTC时间)
范例：
//设置１分钟后重启，id 为11003
mFireflyApi2.setSchedulePowerReboot(11003, true,System.currentTimeMillis()+60);
//取消id 为11003的操作
mFireflyApi2.setSchedulePowerReboot(11003, false,0);
```
<!--
2. 设置重复定时开机
	```
	函数：setPowerOnAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	描述：以周为单位，设置循环的定时开机的时间
    参数：enabled 开启/关闭
          hour　开机时间的小时
          minutes　开机时间的分钟
          daysOfWeek 以周为单位重复，设置定时开机的日期
         　　　　　　　SUNDAY -> MONDAY
	 	               0b1111111
	范例：
	mFireflyApi.setPowerOnAlarmRepeat(true,10,30,0b1000011);
    //设置周一、二、日10点30分开机
	```

3. 获取定时开机状态
	```
	函数：public Alarm getPowerOnAlarm()
	描述：获取定时开机状态
	范例：
	Alarm　powerOnAlarm =mFireflyApi.getPowerOnAlarm();
	```
4. 设置重复定时关机
	```
	函数：setPowerOffAlarmRepeat( boolean enabled,int hour, int minutes,DaysOfWeek daysofweek)
	描述：以周为单位，设置循环的定时开机的时间
    参数：enabled    开启/关闭
         hour　开机时间的小时
         minutes　开机时间的分钟
         daysOfWeek　以周为单位重复，设置定时开机的日期
         　　　　　　　SUNDAY -> MONDAY
	 	            0b1111111
	范例：
	mFireflyApi.setPowerOffAlarmRepeat(true,10,30,0b1000011);
    //设置周一、二、日10点30分关机
	```
6. 获取定时关机状态
	```
	函数：public Alarm getPowerOffAlarm()
	描述：获取定时开机状态
	范例：
	Alarm　powerOffAlarm =mFireflyApi.getPowerOffAlarm();
	```
-->
如果以上的功能无法满足您的需求，可以使用一下接口，自己做逻辑实现功能。此接口只是最基本的定时开关机功能，由传入的id控制开启/关闭，**重启后失效需要手动再次设置**。

<!--
#### 3.6.2 自定义开关机接口

1. 设置定时开机
	```
	函数：public void setSchedulePowerOn(int id,boolean enabled,long alarm_time)
	描述：设置定时开机,id由用户定义，用于开启和关闭定时开机时使用，重启后失效需要重新设置
    参数：id 定时开机id
    	  enabled 开启/关闭　　
          alarm_time 开机时间(UTC时间)
    备注: 通过多组定时开机id,可以实现多组定时开机功能
	范例：
	mFireflyApi.setSchedulePowerOn("12",true,System.currentTimeMillis()+60);
    //设置１分钟后开机，id为12
    mFireflyApi.setSchedulePowerOn("12",true,0);
    //取消id为"12"操作
	```

2. 设置定时关机
	```
	函数：public void setSchedulePowerOff(int id,boolean enabled,long alarm_time)
	描述：设置定时关机,id由用户定义，用于开启和关闭定时关机时使用，重启后失效需要重新设置
    参数：id 定时关机id
    	  enabled 开启/关闭　　
          alarm_time 关机时间(UTC时间)
    备注: 通过多组定时关机id,可以实现多组定时关机功能
	范例：
	mFireflyApi.setSchedulePowerOff("12",true,System.currentTimeMillis()+60);
    //设置１分钟后关机，id为12
    mFireflyApi.setSchedulePowerOff("12",true,0);
    //取消id为"12"操作
	```
-->
