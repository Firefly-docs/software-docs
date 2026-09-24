# FFMedia Introduction

FFMedia is a modular audio and video framework for Linux multimedia applications. It is designed to help application software use Rockchip hardware capabilities without dealing directly with every platform-specific detail.

FFMedia packages camera, file, network stream, decoding, encoding, image processing, inference, display, recording, and streaming capabilities as reusable modules. Applications can combine these modules to build their own multimedia pipelines.

## Core Features

### Composable Media Pipelines

Modules can run independently or be combined into a pipeline. A pipeline usually contains input, processing, and output modules, but these stages are not mandatory. Modules can be replaced, branched, or merged, and the same data can be sent to multiple downstream modules.

### Hardware Acceleration

FFMedia packages common platform capabilities as modules: MPP provides hardware video encoding and decoding, RGA/GPU handles image preprocessing such as cropping, scaling, rotation, and composition, DRM provides direct display, and RKNN provides NPU inference.

### Unified Configuration and Lifecycle

C++, Python, and the `ffmedia` command-line tool use the same parameter model. Modules follow a common configuration, connection, initialization, start, and stop lifecycle. Developers can first inspect modules and parameters with the command line, then move the configuration into an application.

### Shared Buffers and Extensibility

Media data is passed between modules through `MediaBuffer` without unnecessary copying. When supported by the device and module, DMA-BUF can share the underlying memory and reduce copies across hardware components.

C++ applications can compose existing modules directly, use `ModuleAppSource` and `ModuleAppProcessor`, or inherit from `ModuleMedia` to connect custom data sources and processing logic.
