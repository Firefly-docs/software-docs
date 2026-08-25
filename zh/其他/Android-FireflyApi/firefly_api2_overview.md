# FireflyApi2

## 1.概述
FireflyApi2 提供了部分系统接口以及封装了部分用户需要的功能接口，主要是为了让用户容易和简单的使用系统常用接口，
此文档只是对接口进行简单的说明，具体的使用参考
 FireflyApi2Demo 的源码。

* 支持 rk3568/rk3566 Android11 平台所有系列机型
* 支持 rk3588 Android12 平台所有系列机型
* 支持 rk3588/rk3576 Android14 平台所有系列机型 

## 2.资源下载和使用

<font color="red">使用 FireflyApi2 时先检查一下机器的固件是否是最新版本，最新版本的固件内置了基于 FireflyApi2 实现的 FireflyApi2Demo 应用，可以在[[资源下载]](https://community.t-firefly.com/doc/download)
页面找到对应的机型查看并下载最新固件，同时也可以同步SDK到最新提交，具体先选择对应机型的[[wiki]](http://wiki.t-firefly.com/)，然后在`Android开发`->`编译Android固件`里面找到同步的方式。</font>

FireflyApi2Demo 源码下载：
* [[FireflyApi2Demo]](https://pan.baidu.com/s/1p52gBTCN9xkN2nlqhNSNiQ?pwd=1234 )(提取码:1234)

Demo 主要包含 fireflyapi2.aar 和 FireFlyApi2Demo 的源码，其结构目录如下：
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

### 2.1 FireflyApi2Demo 应用简述
FireflyApi2Demo 是基于 FireflyApi2 的接口实现的 Demo 程序，用户可参考 Demo 的源码实现自己所需的功能。
当使用最新版本的固件时，里面会内置 FireflyApi2Demo 的应用。
* 应用如图

![](../../../img/Android-FireflyApi/fireflyapi2demo.png)

* 点击进入应用后会有相应的接口实现列表如图

![](../../../img/Android-FireflyApi/fireflyapi2demo_ui.png)


### 2.2 FireflyApi2的使用
#### 2.2.1 Android Studio
Android Studio 是谷歌推出的一个 Android 集成开发工具，基于IntelliJ IDEA，类似 Eclipse ADT，Android Studio 提供了集成的 Android 开发工具用于开发和调试。

#### 2.2.2  项目中导入fireflyapi2.aar
FireflyApi2Demo 是给用户呈现出接口相应的功能，用户如需要编写自己的应用程序，可将 fireflyapi2.aar 放在 libs 目录下：
```
app/
├── build.gradle
├── libs
│   └── fireflyapi2.aar
```
然后配置 build.gradle 文件，即可使用 FireflyApi2 定制应用程序。
```diff
dependencies {
    ......
+   implementation files('libs/fireflyapi2.aar')
}
```
## 3.FireflyApi2 接口说明

要使用 FireflyApi2 的接口需要先导入依赖并获取实例化对象：
```
import com.firefly.api2.FireflyApi2;

public class *** {
	......
	private static FireflyApi2 mFireflyApi2 = FireflyApi2.getInstance();
	......
}
```
