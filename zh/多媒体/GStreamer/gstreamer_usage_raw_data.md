# 保存原始数据

## 4. GStreamer 通用使用方法

### 4.5 保存原始数据

`filesrc` 从文件读取数据，`filesink` 将数据保存为文件：

```bash
gst-launch-1.0 filesrc location=input.bin \
  ! filesink location=output.bin
```

处理 YUV 裸流时，每个 Buffer 通常应对应一帧。以 640×320 NV12 为例，一帧大小为 `640 × 320 × 1.5 = 307200` 字节：

```bash
gst-launch-1.0 filesrc location=input_nv12.yuv blocksize=307200 \
  ! 'video/x-raw,format=NV12,width=640,height=320,framerate=30/1' \
  ! fakesink
```
