# FireflyApi2

## 1.Overview

FireflyApi2 provides some system interfaces and encapsulates the functional interface that users need, so that users can use the system's common interfaces in an easy and simple way. This document is only a brief description of the interface. For specific use, please refer to Source code of FireflyApi2Demo.

* Support all series models of rk3568/rk3566 Android11 platform.
* Support all series models of rk3588 Android12 platform.
* Support all series models of rk3588/rk3576 Android14 platform.

## 2.Resources download and usage

<font color="red">When using FireflyApi2, first check whether the firmware of the machine is the latest version. The latest version of the firmware has built-in FireflyApi2Demo application based on FireflyApi2. You can find the corresponding model on the [Resource Download](https://community.t-firefly.com/en/doc/download) page to view and download the latest firmware. At the same time, you can also synchronize the SDK to the latest submission. The specific steps are as follows: first, select the corresponding [wiki](https://en.t-firefly.com/wiki), then find the synchronized method in the Android `Development`-> `Build the Android Firmware`.</font>

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

![](../../../img/Android-FireflyApi/fireflyapi2demo_en.png)

* After clicking to enter the application, there will be a corresponding interface implementation list, as shown in the figure:

![](../../../img/Android-FireflyApi/fireflyapi2demo_ui_en.png)

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

