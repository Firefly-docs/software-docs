# FireflyApi2 安装升级

#### 1. 本地ota包升级
```
函数：public void installPackage(String path)
描述：将重启进行ota包升级，目前仅支持放置在内置存储根目录，即/sdcard/下
参数：path ota包的绝对路径
范例：
mFireflyApi2.installPackage("/sdcard/update.zip");
```
<!--
1. 重启进入Recovery
	```
	函数：public void rebootRecovery(
	描述：重启进入Recovery
	范例：
	mFireflyApi.rebootRecovery();
	```
-->
