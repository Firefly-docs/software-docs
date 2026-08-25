# Log 
## Last Log
In the `/sys/fs/pstore` directory of the device: 
* `console-ramoops-0`: last_log The kernel log started last time, but only the logs with higher priority than the default log level are saved 
* `pmsg-ramoops-0`: Last user space log, android log 

## Log is automatically saved 
### Way one (<font color=#FF0000>recommended use</font>) 
* [Open "Developer Options"](developer_options.md)
* Enable automatic log saving 

	* Android7.1
		
    `Settings`-->`Developer options`-->`Android bug collector`
    
    * Android10/Android11/Android12
		
    `Settings`-->`System`-->`Developer options`-->`Android LogSave`
    
* reboot device 
* There are log related files in the `/data/logs` directory of the device (**Android 10 and above: **`/data/vendor/logs`) 

    * `android`: system logcat 
    * `kernel`: kernel kmsg 

### Way two 
* [Open "Developer Options"](developer_options.md)
* Enable `Enable logging to save` 

	`Settings`-->`Developer options`-->`Enable logging to save`
* reboot device 
* There are log related files in the `/data/media/0/.LOGSAVE` directory of the device 
    * `xxxx_kmsg.log`: kernel kmsg 
    * `xxxx_logcat.log`: system logcat 

**NOTE:** Only supports Android 7.1 

### Way three 
* [Open "Developer Options"](developer_options.md)
* Enable `Android bug collector` 

	`Settings`-->`Developer options`-->`Android bug collector`
* reboot device 
* There are log related files in the `/data/media/0/rk_logs` directory of the device 
	* `COPY-COMPLETEB` means log has been copied 
	* `anr` means ANR file 
	* `bugreport.log` means the bugreport file 
	* `logs` means logcat, kmsg after each boot 
	* `pstore` means last_log 
	* `tombstones` means tombstones file 

**NOTE:** Only supports Android 7.1 


