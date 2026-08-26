# 处理操作：文件合并与网络推流

## FFmpeg 通用使用

### 5. 常用处理操作

#### 5.8 合并多个文件

当多个文件的封装、编码、时间基和码流参数一致时，可使用 concat demuxer。创建 `list.txt`：

```text
file 'part-01.mp4'
file 'part-02.mp4'
file 'part-03.mp4'
```

执行合并：

```bash
ffmpeg -f concat -safe 0 -i list.txt -c copy output.mp4
```

如果各文件参数不一致，需要先转码为相同格式，或使用 concat filter 重新编码。

#### 5.9 网络推流

将文件按实时速度推送到 RTMP 服务器：

```bash
ffmpeg \
  -re \
  -i input.mp4 \
  -c:v libx264 \
  -preset veryfast \
  -b:v 4M \
  -maxrate 4M \
  -bufsize 8M \
  -g 60 \
  -c:a aac \
  -b:a 128k \
  -f flv \
  rtmp://server.example.com/live/stream
```

发布到 RTSP 服务器：

```bash
ffmpeg \
  -re \
  -i input.mp4 \
  -c:v libx264 \
  -preset veryfast \
  -c:a aac \
  -f rtsp \
  rtsp://server.example.com/live/stream
```

`-re` 使文件输入按原始时间速度读取，适合模拟实时源，不应用于摄像头、声卡等本身就是实时的输入。
