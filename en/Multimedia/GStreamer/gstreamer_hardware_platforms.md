# Hardware Platform Support

## 6. Hardware Platform Support

GStreamer's high-level interfaces and pipeline model are platform-independent, but hardware encoding and decoding, zero-copy paths, memory types, display control, and performance optimizations are generally implemented by platform-specific plugins.

Each platform support section should consistently include the following information:

1. Platform architecture and plugin source.
2. Supported operating systems and GStreamer versions.
3. Hardware decoders and encoders.
4. Camera, display, and zero-copy paths.
5. Platform-specific environment variables.
6. Build and deployment methods.
7. Platform limitations and common issues.

Current platform status in this document:

| Platform | Support Status | Main Plugins/Interfaces |
| --- | --- | --- |
| Rockchip | Documented | MPP, RGA, DRM/KMS, `gstreamer-rockchip` |
| Other platforms | To be added |  |
