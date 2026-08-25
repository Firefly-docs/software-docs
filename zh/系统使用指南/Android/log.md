# Log 日志
## Last Log
在设备的`/sys/fs/pstore`目录下：
* console-ramoops-0 : last_log 上次启动的 kernel log，但只保存里优先级比默认 log level 高的 log
* pmsg-ramoops-0 : 上次用户空间的log，android 的 log

## Log 自动保存
### 方式一（<font color=#FF0000>推荐使用</font>）
* [打开“开发者选项”](developer_options.md)
* 使能 Log 自动保存

	* Android7.1
		
    `Settings`-->`Developer options`-->`Android bug collector`
    
    * Android10/Android11/Android12
		
    `Settings`-->`System`-->`Developer options`-->`Android LogSave`
    
* 重启设备
* 在设备的`/data/logs`目录下有 log 相关的文件(**Android 10及以上为:**`/data/vendor/logs`)

    * `android`: 系统 logcat
    * `kernel`: 内核 kmsg

### 方式二
* [打开“开发者选项”](developer_options.md)
* 使能`Enable logging to save`

	`Settings`-->`Developer options`-->`Enable logging to save`
* 重启设备
* 在设备的`/data/media/0/.LOGSAVE`目录下有 log 相关的文件
    * xxxx_kmsg.log: 内核 kmsg
    * xxxx_logcat.log: 系统 logcat

**NOTE:** 仅支持Android 7.1

### 方式三
* [打开“开发者选项”](developer_options.md)
* 使能`Android bug collector`

	`Settings`-->`Developer options`-->`Android bug collector`
* 重启设备
* 在设备的`/data/media/0/rk_logs`目录下有 log 相关的文件
	* `COPY-COMPLETEB`表示 log 已经拷贝完成
	* `anr`表示ANR文件
	* `bugreport.log`表示 bugreport 文件
	* `logs`表示每次开机之后的 logcat，kmsg
	* `pstore`表示 last_log 
	* `tombstones`表示 tombstones 文件

**NOTE:** 仅支持Android 7.1


