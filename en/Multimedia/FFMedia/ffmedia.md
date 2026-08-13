# FFMedia

[FFMedia](https://github.com/Firefly-rk-linux-utils/ffmedia_release) is a modular audio and video processing framework for Linux multimedia applications, with a focus on support for Rockchip platform capabilities such as V4L2, MPP, RGA, DRM/KMS, and EGL/GLES.

The framework abstracts capture, demultiplexing, encoding and decoding, image processing, inference, display, recording, and streaming from cameras, files, network streams, and application memory into composable modules. It is suitable for real-time media pipelines and embedded multimedia applications.

## Core Features

### Modular Pipelines

FFMedia encapsulates capabilities such as cameras, files, RTSP/RTMP, codecs, RGA image processing, GPU image processing, DRM display, streaming, and recording into unified modules. Modules are connected through a producer/consumer model, so users only need to focus on the data flow and a small number of parameter settings.

Common pipelines can be understood as follows:

```text
Input source VI -> Processing module VP -> Output module VO
```

For example:

```text
RTSP stream pulling -> MPP hardware decoding -> DRM low-latency display
Camera capture -> MPP hardware encoding -> RTSP/RTMP streaming
File reading -> Demuxing/decoding -> RGA/GPU scaling and rotation -> Display or re-encoding and saving
Multi-channel video -> Video Stack stitching -> Display or encoding and streaming
```

The project also provides C++ examples and Python bindings, making it convenient to quickly verify functions, integrate services, or perform secondary development.

### Hardware Acceleration

FFMedia is optimized for Rockchip platform hardware. Video encoding and decoding use MPP, while image scaling, cropping, format conversion, and composition use RGA or the GPU. Compared with pure CPU processing, a hardware-accelerated pipeline can significantly reduce CPU usage, making it more suitable for multi-channel video, long-term operation, and edge device deployment.

In typical applications, FFMedia can be used for:

- Multi-channel RTSP stream pulling, decoding, and display.
- Real-time encoding and streaming after camera capture.
- Video scaling, rotation, format conversion, and image composition.
- Multi-channel video stitching output.
- Video transcoding, container format conversion, and local recording.
- Connecting decoded video to RKNN inference for AI video analysis such as detection and tracking.

### Unified Parameter Interface and Extensibility

- Module configurations are described by the parameter system. The same parameter names, types, and hierarchy can be queried and configured from C++, Python, and the command line.
- Applications can directly compose existing modules or inherit from `ModuleMedia` to implement custom data sources, processors, or output modules.

### High Real-Time Performance

FFMedia's low-latency capability comes from hardware codecs, modular pipelines, buffer queue control, and display path optimization. According to the low-latency display tests in the project README:

- In an HDMI input forwarding display scenario, the average latency from HDMI input to forwarding display is about `29ms`.
- In a camera capture forwarding display scenario, the average latency from camera capture to forwarding display is about `19ms`.
- By adjusting display timing and the screen refresh rate, the display latency fluctuation range can be optimized from about `1ms~17.6ms` to about `1ms~7ms`.

![FFMedia low-latency display test](../../../img/FFMedia/low_delay_demo.png)

These features make FFMedia suitable for latency-sensitive scenarios such as video surveillance, remote control, low-latency preview, industrial vision, edge AI video analysis, and multimedia forwarding.

## Architecture Overview

FFMedia uses a Producer/Consumer model, and all units are abstracted as the `ModuleMedia` class. One producer can connect to multiple consumers, and one consumer can also connect to multiple producers. Input source modules have no upstream producers, while processing and output modules are connected to the pipeline through unified interfaces.

![FFMedia software framework](../../../img/FFMedia/p1.png)

## Typical Application Scenarios

### Low-Latency Video Preview

After obtaining video from a camera or network stream, MPP is used for decoding and DRM is used for direct display. This is suitable for local preview, device debugging, remote image echo, and similar scenarios.

```text
Camera / RTSP -> MppDec -> DRM Display
```

### Real-Time Transcoding and Streaming

Read a local file, camera, or network stream, then push it to an RTSP/RTMP service after decoding, scaling, rotation, or re-encoding.

```text
File / Camera / RTSP -> MppDec -> RGA -> MppEnc -> RTSP / RTMP
```

### Multi-Channel Video Processing

Pull multiple RTSP video streams at the same time, then decode, stitch, display, or re-encode and stream them. Video Stack can be used for multi-channel video stitching output, and hardware acceleration can reduce CPU pressure in multi-channel scenarios.

```text
RTSP x N -> MppDec x N -> Video Stack -> DRM Display / MppEnc -> Network
```

### AI Video Analysis

After video decoding, the RKNN inference module can be connected to implement object detection, object tracking, multi-threaded inference, and related services.

```text
File / RTSP / Camera -> MppDec -> Inference -> OSD / Display / Encode
```

## Integration Method

The current SDK provides precompiled shared libraries, public header files, CMake configuration, the `ffmedia` command-line tool, C++ examples, and Python wheels.

### Command-Line Tool

The `ffmedia` tool composes media pipelines using modules, parameters, and connections:

```bash
export LD_LIBRARY_PATH="$PWD/lib:$LD_LIBRARY_PATH"
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia run --help
```

### C++ Integration

The recommended integration method is to use the imported CMake target:

```cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
```

Use the unified include file in your source code:

```cpp
#include <ffmedia/ffmedia.hpp>
```

The typical workflow is to create modules, set parameters, initialize them, call `connectProducer()` to connect upstream and downstream modules, and then start and stop the source modules.

### Python Integration

The release package provides Python 3.10 and Python 3.11 wheels for AArch64:

```bash
python3 -m pip install python/ff_pymedia-2.6.0-cp310-cp310-linux_aarch64.whl
# Use the cp311 wheel with Python 3.11
```

### Build the Examples

```bash
cmake -S . -B build
cmake --build build -j$(nproc)
```

## Services Suitable for FFMedia

FFMedia is suitable for real-time audio and video services deployed on Rockchip platforms, especially in the following areas:

- Real-time camera preview and recording.
- RTSP/RTMP stream pulling, forwarding, and streaming.
- Multi-channel video decoding, stitching, display, and transcoding.
- Low-latency HDMI/Camera capture forwarding display.
- Video format conversion, scaling, cropping, rotation, and composition.
- Edge AI video analysis, object detection, and object tracking.
- Embedded audio and video systems that require low CPU usage and long-term stable operation.
