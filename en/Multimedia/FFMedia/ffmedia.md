# FFMedia

FFMedia is an audio and video codec and processing framework developed based on Rockchip MPP/RGA. It is designed for scenarios such as camera capture, network stream pulling, hardware codec processing, image processing, multi-channel video stitching, real-time display, transcoding and streaming, file recording, and AI inference.

The framework divides complex audio and video pipelines into three types of modules: input, processing, and output. Developers can combine modules like building blocks to quickly complete a full workflow from capture, decoding, scaling, and encoding to display or streaming. FFMedia makes full use of the hardware capabilities of Rockchip platforms to reduce CPU load while maintaining high real-time performance and low end-to-end latency.

## Core Features

### Easy to Use

FFMedia encapsulates capabilities such as cameras, files, RTSP/RTMP, codecs, RGA image processing, DRM display, streaming, and recording into unified modules. Modules are connected through a producer/consumer model, so users only need to focus on the data flow and a small number of parameter settings.

Common pipelines can be understood as follows:

```text
Input source VI -> Processing module VP -> Output module VO
```

For example:

```text
RTSP stream pulling -> MPP hardware decoding -> DRM low-latency display
Camera capture -> MPP hardware encoding -> RTSP/RTMP streaming
File reading -> Demuxing/decoding -> RGA scaling and rotation -> Display or re-encoding and saving
Multi-channel video -> Video Stack stitching -> Display or encoding and streaming
```

The project also provides C++ examples and Python bindings, making it convenient to quickly verify functions, integrate services, or perform secondary development.

### Low Load

FFMedia is designed for the hardware capabilities of Rockchip platforms. Video encoding and decoding use MPP, while image scaling, cropping, format conversion, and composition use RGA. Compared with pure CPU processing, a hardware-accelerated pipeline can significantly reduce CPU usage, making it more suitable for multi-channel video, long-term operation, and edge device deployment.

In typical applications, FFMedia can be used for:

- Multi-channel RTSP stream pulling, decoding, and display.
- Real-time encoding and streaming after camera capture.
- Video scaling, rotation, format conversion, and image composition.
- Multi-channel video stitching output.
- Video transcoding, container format conversion, and local recording.
- Connecting decoded video to RKNN inference for AI video analysis such as detection and tracking.

### High Real-Time Performance and Low Latency

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

The key to integrating FFMedia is not memorizing many commands, but selecting modules according to service goals and connecting them into a pipeline. Developers usually only need to complete three steps:

1. Select an input or aggregation module: camera, file, memory data, RTSP/RTMP network stream, FFmpeg Demux, or Video Stack multi-channel stitching module.
2. Select processing capabilities: hardware decoding, hardware encoding, RGA image processing, AAC audio codec processing, or RKNN inference.
3. Select an output target: DRM/X11 display, file saving, RTSP/RTMP streaming, audio playback, or FFmpeg Mux.

### C++ Integration

The C++ interface is suitable for services with high requirements for performance, stability, and lifecycle control. Modules uniformly inherit from `ModuleMedia`. A typical workflow is to create modules, set parameters, initialize them, establish upstream and downstream relationships, start running, and then stop and release resources.

This mode is suitable for embedded products, edge gateways, multi-channel video processing services, and long-running audio/video background processes.

### Python Integration

The Python bindings are based on pybind11, and the interfaces basically correspond to the C++ modules. They are suitable for quickly verifying pipelines, debugging algorithms, integrating AI inference workflows, or reusing FFMedia capture, codec, and display capabilities in existing Python services.

### Deployment Environment

FFMedia is mainly designed for Rockchip platforms. Common dependencies include the compilation toolchain, CMake, libdrm, ALSA, GLES, X11, and JPEG-related libraries. When AI inference is involved, the corresponding RKNN runtime library is required.

In actual products, it is recommended to solidify dependency library paths, chip platform settings, display devices, audio devices, and network protocol parameters into the deployment configuration to reduce debugging costs caused by runtime environment differences.

## Services Suitable for FFMedia

FFMedia is suitable for real-time audio and video services deployed on Rockchip platforms, especially in the following areas:

- Real-time camera preview and recording.
- RTSP/RTMP stream pulling, forwarding, and streaming.
- Multi-channel video decoding, stitching, display, and transcoding.
- Low-latency HDMI/Camera capture forwarding display.
- Video format conversion, scaling, cropping, rotation, and composition.
- Edge AI video analysis, object detection, and object tracking.
- Embedded audio and video systems that require low CPU usage and long-term stable operation.
