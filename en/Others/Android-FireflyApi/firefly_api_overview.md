# FireflyApi overview

## 1. Overview
FireflyApi provides some system interfaces and encapsulates the functional interface that users need, so that users can use the system's common interfaces in an easy and simple way.
This document is a brief introduction of the interfaces. The specific use cases are described below.

* Support all series models of rk3288 Android5.1 platform
* Support all series models of rk3399 Android7.1 platform
* Support all series models of rk3128 Android5.1 platform

## 2. Resources download and use

When using FireflyApi, please check whether the firmware of device is the latest version. Find the corresponding model in the page of [[Resource Download]] (http://www.t-firefly.com/doc/download/page/id/4.html), and check if your firmware version is up-to-date. At the same time, you can also synchronize the SDK to the latest submission. The specific steps are as follows: first, select the corresponding [[wiki]] (http://wiki.t-firefly.com/), then find the synchronized method in the Android Development-> Build the Android Firmware.

A complete set of Demo used by FireflyApi:

* [[RK3288 firefly_sdkapi_demo.zip]](https://drive.google.com/drive/folders/15g4mIQDLdLuMBvk6s8ss44ATBM4WW3gN?usp=sharing)
* [[RK3399 firefly_sdkapi_demo.zip]](https://drive.google.com/drive/folders/1Bez_YpDJyupHw3BWiJnAteo4MtlQMRgq?usp=sharing)
* [[RK3128 firefly_sdkapi_demo.zip]](https://drive.google.com/drive/folders/1w9y4axhZEq8QoAQfptNDCuJ3XyIH7WBC?usp=sharing)

Demo mainly includes the source codes of firefly_sdkapi_demo.apk, firefly-api.jar,libfirefly_api.so and firefly_sdkapi_demo, etc. About apk and
the use of the library are as follows:
```
├── apk
│   └── firefly_sdkapi_demo.apk
├── lib
│   ├── armeabi-v7a
│   │   └── libfirefly_api.so
│   └── firefly-api.jar
├── Readme_CN.txt
├── Readme_EN.txt
└── src
    └── firefly_sdkapi_demo
```

### 2.1 Brief introduction of firefly_sdkapi_demo apk
firefly_sdkapi_demo apk is a Demo program based on the FireflyApi interface. Users can verify their own programs or related interfaces through the implementation of functions in the Demo.
When using the latest version of firmware, there is a built-in firefly_sdkapi_demo app as shown in the figure

![](../../../img/Android-FireflyApi/apk_1.png)

* Click to enter the apk, there will be a corresponding interface implementation list, as shown below

![](../../../img/Android-FireflyApi/apk_2.png)

* System settings interface implementation
 
![](../../../img/Android-FireflyApi/apk_3.png)

* Time switch interface implementation

![](../../../img/Android-FireflyApi/apk_4.png)

* Hardware interface implementation
 
![](../../../img/Android-FireflyApi/apk_5.png)

### 2.2 How to use FireflyApi in eclipse
firefly_sdkapi_demo apk is to provide the user with corresponding functions of the interface, if the user needs to write his own application,
it is required to put firefly-api.jar in the libs directory, and libfirefly_api.so in the libs/armeabi-v7a directory.   
	

      　　　　　|-libfirefly_api.so
	

Adding method is as follows
