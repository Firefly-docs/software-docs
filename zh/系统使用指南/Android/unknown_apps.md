# 打开或关闭未知应用安装功能
## 代码修改的方式
### Android 7.1
在`frameworks/base/packages/SettingsProvider/res/values/defaults.xml`文件里，修改`def_install_non_market_apps`的值。
* false : 关闭
* true : 打开

### Android 10及以上
在Android10中，已经删除了"允许未知来源"安装应用程序的永久授权选项，从系统设置当中已经找不到该开关，谷歌将永久授权修改为每次的单独授权，当用户每次安装第三方来源的Android软件时需要对软件权限进行手动确认。

