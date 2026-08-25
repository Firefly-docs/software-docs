# Overview
## Scope of application 
* RK3399 Android7.1 Industry
* RK3399 Android10
* RK356X Android11
* RK3588 Android12

For other Android versions, it is available for reference.
<br>
<br>

## Modification method 
For Android system customization, there are generally **Temporary modification methods** and **Code modification methods**.
* Temporary modification method：Generally, the device can be operated through the debug serial port or ADB.
* Code modification method：After modifying the code, you need to perform <font color=#FF0000> SDK compilation</font> and <font color=#FF0000> firmware flashing</font>. 

Among them, *SDK compilation* or *firmware flashing* [refer to WiKi tutorial](https://wiki.t-firefly.com/en/)。

## Glossary
* `CPU_TYPE`：Indicates the main control chip, such as rk3288, rk3399, rk3399pro, rk356x, rk3588
* `PRODUCT_TYPE`：Indicates the product type, such as rk3399_firefly, rk3399_firefly_aio, rk3399_firefly_aioc_ai 

## Custom description 
For the customized content of the system, there will be some differences in Android12, Android11, Android10.0 and Android7.1 Industry. This part of the content will be distinguished, and the undifferentiated content indicates compatibility. 

