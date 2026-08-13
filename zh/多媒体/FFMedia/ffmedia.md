# FFMedia

[FFMedia](https://github.com/Firefly-rk-linux-utils/ffmedia_release) 是面向 Linux 多媒体应用的模块化音视频处理框架，重点适配 Rockchip 平台的 V4L2、MPP、RGA、DRM/KMS、EGL/GLES 等能力。

框架将摄像头、文件、网络流和应用内存的采集、解复用、编解码、图像处理、推理、显示、录制和推流抽象为可组合模块，适用于实时媒体管线和嵌入式多媒体应用。

## 核心特点

### 模块化管线

FFMedia 将摄像头、文件、RTSP/RTMP、编解码器、RGA 图像处理、 GPU图像处理、DRM 显示、推流和录制等能力封装为统一模块。模块之间通过生产者/消费者模型连接，使用者只需要关注数据流向和少量参数配置。

常见链路可以直接按以下方式理解：

```text
输入源 VI -> 处理模块 VP -> 输出模块 VO
```

例如：

```text
RTSP 拉流 -> MPP 硬件解码 -> DRM 低延迟显示
Camera 采集 -> MPP 硬件编码 -> RTSP/RTMP 推流
文件读取 -> 解封装/解码 -> RGA/GPU 缩放旋转 -> 显示或重新编码保存
多路视频 -> Video Stack 拼接 -> 显示或编码推流
```

项目同时提供 C++ 示例和 Python 绑定，便于快速验证功能、接入业务或进行二次开发。

### 硬件加速

FFMedia 重点适配 了Rockchip 平台硬件，视频编解码使用 MPP，图像缩放、裁剪、格式转换和合成使用 RGA或GPU。相比纯 CPU 处理，硬件加速链路可以显著降低 CPU 占用，更适合多路视频、长时间运行和边缘设备部署。

在典型业务中，FFMedia 可用于：

- 多路 RTSP 拉流、解码和显示。
- 摄像头采集后实时编码推流。
- 视频缩放、旋转、格式转换和画面合成。
- 多路视频拼接输出。
- 视频转码、封装转换和本地录制。
- 解码后接入 RKNN 推理，实现检测、跟踪等 AI 视频分析。

### 统一参数接口与扩展性

- 模块配置由参数系统描述，可在 C++、Python 和命令行中使用相同的参数名称、类型和层级进行查询与设置。
- 应用可以直接组合现有模块，也可以继承 ModuleMedia 实现自定义数据源、处理器或输出模块。

### 高实时性

FFMedia 的低延迟能力来自硬件编解码、模块化管线、缓冲队列控制和显示链路优化。根据项目 README 中的低延迟显示测试：

- HDMI 输入转发显示场景中，从 HDMI 输入到转发显示的平均延迟约为 `29ms`。
- Camera 采集转发显示场景中，从 Camera 采集到转发显示的平均延迟约为 `19ms`。
- 通过调整显示时序和屏幕刷新率，可将显示延迟波动范围从约 `1ms~17.6ms` 优化到约 `1ms~7ms`。

![FFMedia 低延迟显示测试](../../../img/FFMedia/low_delay_demo.png)

这些特性使 FFMedia 适合视频监控、远程操控、低延迟预览、工业视觉、边缘 AI 视频分析和多媒体转发等对实时性敏感的场景。

## 架构概览

FFMedia 采用 Producer/Consumer 模型，所有单元都抽象为 `ModuleMedia` 类。一个生产者可以连接多个消费者，一个消费者也可以连接多个生产者。输入源模块没有上游生产者，处理和输出模块通过统一接口接入管线。

![FFMedia 软件框架](../../../img/FFMedia/p1.png)



## 典型应用场景

### 低延迟视频预览

从摄像头或网络流获取视频后，使用 MPP 解码并通过 DRM 直接显示，适合本地预览、设备调试、远程画面回显等场景。

```text
Camera / RTSP -> MppDec -> DRM Display
```

### 实时转码推流

读取本地文件、摄像头或网络流，经过解码、缩放、旋转或重新编码后推送到 RTSP/RTMP 服务。

```text
File / Camera / RTSP -> MppDec -> RGA -> MppEnc -> RTSP / RTMP
```

### 多路视频处理

同时拉取多路 RTSP 视频流，进行解码、拼接、显示或重新编码推流。Video Stack 可用于多路视频拼接输出，硬件加速可以降低多路场景下的 CPU 压力。

```text
RTSP x N -> MppDec x N -> Video Stack -> DRM Display / MppEnc -> Network
```

### AI 视频分析

视频解码后接入 RKNN 推理模块，可实现目标检测、目标跟踪和多线程推理等业务。

```text
File / RTSP / Camera -> MppDec -> Inference -> OSD / Display / Encode
```

## 接入方式

当前 SDK 提供预编译动态库、公共头文件、CMake 配置、`ffmedia` 命令行工具、C++ 示例和 Python wheel。

### 命令行工具

`ffmedia` 可通过模块、参数和连接关系组合媒体管线：

```bash
export LD_LIBRARY_PATH="$PWD/lib:$LD_LIBRARY_PATH"
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia run --help
```

### C++ 接入

推荐通过 CMake imported target 接入：

```cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
```

代码中可统一包含：

```cpp
#include <ffmedia/ffmedia.hpp>
```

典型流程为创建模块、设置参数、初始化、调用 `connectProducer()` 连接上下游，然后启动和停止源模块。

### Python 接入

发布包提供适用于 AArch64 的 Python 3.10 和 Python 3.11 wheel：

```bash
python3 -m pip install python/ff_pymedia-2.6.0-cp310-cp310-linux_aarch64.whl
# Python 3.11 使用 cp311 wheel
```

### 示例编译

```bash
cmake -S . -B build
cmake --build build -j$(nproc)
```

## 适合 FFMedia 的业务

FFMedia 适合部署在 Rockchip 平台上的实时音视频业务，尤其适合以下方向：

- 摄像头实时预览和录制。
- RTSP/RTMP 拉流、转发和推流。
- 多路视频解码、拼接、显示和转码。
- 低延迟 HDMI/Camera 采集转发显示。
- 视频格式转换、缩放、裁剪、旋转和合成。
- 边缘 AI 视频分析、目标检测和目标跟踪。
- 需要低 CPU 占用、长时间稳定运行的嵌入式音视频系统。
