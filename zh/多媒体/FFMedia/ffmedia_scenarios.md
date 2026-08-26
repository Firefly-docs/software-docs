# 典型应用场景

## 低延迟视频预览

从摄像头或网络流获取视频后，使用 MPP 解码并通过 DRM 直接显示，适合本地预览、设备调试、远程画面回显等场景。

```text
Camera / RTSP -> MppDec -> DRM Display
```

## 实时转码推流

读取本地文件、摄像头或网络流，经过解码、缩放、旋转或重新编码后推送到 RTSP/RTMP 服务。

```text
File / Camera / RTSP -> MppDec -> RGA -> MppEnc -> RTSP / RTMP
```

## 多路视频处理

同时拉取多路 RTSP 视频流，进行解码、拼接、显示或重新编码推流。Video Stack 可用于多路视频拼接输出，硬件加速可以降低多路场景下的 CPU 压力。

```text
RTSP x N -> MppDec x N -> Video Stack -> DRM Display / MppEnc -> Network
```

## AI 视频分析

视频解码后接入 RKNN 推理模块，可实现目标检测、目标跟踪和多线程推理等业务。

```text
File / RTSP / Camera -> MppDec -> Inference -> OSD / Display / Encode
```
