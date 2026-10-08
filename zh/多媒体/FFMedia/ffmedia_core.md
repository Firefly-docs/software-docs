# FFMedia

FFMedia 是面向 Linux 多媒体应用的模块化音视频框架，可支持应用软件快速开发 。它重点适配了Rockchip 硬件能力，将芯片相关的复杂的底层处理，根据其功能封装成对应的可复用的功能模块，它将 Camera、文件、网络流、解码、编码、图像处理、推理、显示、录制和推流封装为功能模块，方便用户快速组合开发自己的多媒体应用。

## 获取 SDK

稳定部署建议从 [最新tags](https://github.com/Firefly-rk-linux-utils/ffmedia_release/tags) 下载发布包。发布包包含预编译 SDK、命令行工具、Python 绑定和示例工程。

需要查看源码或参与开发时，也可以获取仓库：

~~~bash
git clone --depth=1 https://github.com/Firefly-rk-linux-utils/ffmedia_release.git
cd ffmedia_release
~~~

主要目录如下：

| 路径 | 内容 |
| --- | --- |
| bin/ffmedia | 通用媒体管线命令行工具 |
| include/ffmedia/ | C++ 公共头文件 |
| lib/aarch64-linux-gnu/ | 动态库、CMake 配置和 pkg-config 文件 |
| python/ | Python 绑定 wheel |
| examples/demo/ | C++/Python Demo 和 CLI 源码 |
| examples/tests/ | CPU 测试和硬件手动测试 |
| examples/inference/ | RKNN 推理扩展 |

发布包根目录的 CMake 工程主要用于编译示例、测试和扩展；FFMedia 核心动态库由发布包
直接提供。

## 运行预编译工具

在发布包根目录设置动态库路径：

~~~bash
./bin/ffmedia --help
~~~

参考支持模块和参数：

~~~bash
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia params image-processor output
~~~

更多详细使用可阅读sdk下的examples/demo/ffmedia.md文档和examples/demo/ffmedia.cpp源码。

## 编译 Demo 和测试

基础构建：

~~~bash
cmake -S . -B build
cmake --build build -j$(nproc)
~~~

常用选项：

| 选项 | 作用 |
| --- | --- |
| DEMO_OPENCV | 编译 OpenCV Demo，需要 OpenCV 4 |
| ENABLE_TESTS | 编译 examples/tests |
| ENABLE_INFERENCE_EXAMPLES | 编译推理示例 |
| ENABLE_INFERENCE_EXTENSION | 编译独立 RKNN 推理扩展 |

例如编译 OpenCV Demo：

~~~bash
cmake -S . -B build -DDEMO_OPENCV=ON
cmake --build build -j$(nproc)
~~~

运行 demo 可以查看帮助信息

~~~bash
./build/demo
Firefly FFMedia: v2.6.2
INFO: ff_media: usage: Usage: ./demo <Input source> [Options]

Options:
-i, --input                  Input image size
-o, --output                 Output image size, default same as input
-a, --inputfmt               Input image format, default MJPEG
-b, --outputfmt              Output image format, default NV12
--use_ffmpeg_demux           Use ffmpeg demux. e.g. --use_ffmpeg_demux or --use_ffmpeg_demux=kmsgrab
--use_ffmpeg_mux             Use ffmpeg demux. e.g. --use_ffmpeg_mux or --use_ffmpeg_mux=rtsp
-c, --count                  Instance count, default 1
-d, --drmdisplay             Drm display, set display plane, set 0 to auto find plane, default disabled
    --connector              Set drm display connector, default 0 to auto find connector
-x, --x11                    X11 window displays, render the video using opengl. default disabled
-z, --zpos                   Drm display plane zpos, default auto select
-e, --encodetype             Encode encode, set encode type, default disabled
-f, --file                   Enable save source output data to file, set filename, default disabled
--port                       Enable push stream, default rtsp stream, set push port, depend on encode enabled, default disabled
--push_type                  Set push stream type, default rtsp. e.g. --push_type rtmp
--push_path                  Set push stream path, default /live/0. e.g. --push_path /live/test
--rtmp_url                   Set the rtmp client push address. e.g. --rtmp_url rtmp://xxx
--rtsp_transport             Set the rtsp transport type, default udp.
                               e.g. --rtsp_transport tcp | --rtsp_transport multicast
-m, --enmux                  Enable save encode data to file. Enable package as mp4, mkv, flv, ts, ps or raw stream files, muxer type depends on the filename suffix.
                               default disabled. e.g. -m out.mp4 | -m out.mkv | -m out.yuv
-s, --sync                   Enable synchronization module, default disabled. Enable the default audio.
                               e.g. -s | --sync=video | --sync=abs
--audio                      Enable audio, default disabled.
--aplay                      Enable play audio, default disabled. e.g. --aplay plughw:3,0
--arecord                    Enable record audio, default disabled. e.g. --arecord plughw:3,0
-l, --loop                   Loop reads the media file.
--gb28181_user_id            Enable gb28181 client, default disabled. set user id
--gb28181_server_id          Set the server id of gb28181 client
--gb28181_server_ip          Set the server ip of gb28181 client
--gb28181_server_port        Set the server port of gb28181 client
--dec_disabled               Disabled decoder
-r, --rotate                 Image rotation degree, default 0
                               0:   none
                               1:   vertical mirror
                               2:   horizontal mirror
                               90:  90 degree
                               180: 180 degree
                               270: 270 degree

~~~

示范：输入是分辨率为 1080p 的 rtsp 摄像头，把解码图像缩放为 720p 并且旋转 90 度，输出到显示器上。

```
./demo rtsp://admin:firefly123@172.16.2.96 -o 1280x720 -r 90 -d 0 -s
```

更多使用可阅读sdk下的examples/demo/Readme.md文档。
