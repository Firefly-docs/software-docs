# Qt 交叉编译环境支持

Firefly 发布了两个 Qt 交叉编译工具链，适用于以下环境，请根据需求选择:

- Qt: 5.12.2
- Host:  x86-64 / Ubuntu 18.04
- Target:  Firefly RK3568 RK3566 RK3399 RK3328 PX30 / Ubuntu 18.04 Minimal&Desktop

和

- Qt: 5.15
- Host:  x86-64 / Ubuntu 20.04
- Target:  Firefly RK3588 RK3568 RK3566 / Ubuntu 20.04 Desktop

工具链完整支持 wenEngine, 支持 EGLFS LinuxFB XCB 等 backend。

* 下载地址

点击[下载链接](https://pan.baidu.com/s/1sFzjS0nelCyw0xT9se1KMQ)(提取码：FFQT)

* 部署

**详情参见工具链中的 Qt5.1x.x_Release.md 文件**

**注意，文档中所有路径的名称不可更改，否则会导致编译或者运行出错。**

* 编译

在host端，进入 Qt 工程目录，`qmake && make` 即可.

* 运行

工具链中含有例程，用户在部署完成后，可以在 host 端 build demo，在 tartget 端运行 demo 以测试部署是否成功。

确定了使用哪个后端，就可以修改设备中 /etc/profile.d/target_qtEnv.sh 文件，去除对应平台环境变量前面的`#`使其一直生效
```bash
# 例如，使用 XCB ，则将文件内 XCB 部分前面的 # 删除

#XCB
export QT_QPA_PLATFORM=XCB
export QT_QPA_EGLFS_INTEGRATION=XCB_EGL
```
