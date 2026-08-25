# Rockchip 编解码器
## MediaCodec
Android 的 [MediaCodec](https://developer.android.google.cn/reference/android/media/MediaCodec) 默认就会调用到 Rockchip 编解码器。
<br>
如果需要更底层的操作，可以使用 MPP。
## MPP
瑞芯微提供的媒体处理软件平台(Media Process Platform，简称 MPP)是适用于瑞芯微芯片系列的通用媒体处理软件平台。
该平台对应用软件屏蔽了芯片相关的复杂底层处理，其目的是为了屏蔽不同芯片的差异，为使用者提供统一的视频媒体处理接口(Media Process Interface，缩写 MPI)。
* 文档资料：`SDK/RKDocs/common/MPP/` 目录下
* MPP源码：[https://github.com/rockchip-linux/mpp](https://github.com/rockchip-linux/mpp)
