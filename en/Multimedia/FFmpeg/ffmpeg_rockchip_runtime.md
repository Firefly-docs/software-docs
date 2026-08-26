# Runtime Requirements

## Rockchip Platform

### 3. Runtime Requirements

`ffmpeg-rockchip` requires a Rockchip BSP/vendor kernel. The project has mainly been tested with Linux kernels 5.10 and 6.1.

Build and runtime requirements include:

- Rockchip MPP 1.3.9 or later.
- A librga version matching the BSP.
- libdrm with DRM_PRIME/DMA-BUF support.
- Permission to access the MPP, RGA, and DRM device nodes.

Common device nodes include:

```text
/dev/mpp_service
/dev/rga
/dev/dri/
/dev/dma_heap/
```

Check the device nodes:

```bash
ls -l /dev/mpp_service /dev/rga /dev/dri /dev/dma_heap 2>/dev/null
```

The FFmpeg user normally needs to belong to the `video` or `render` group associated with these device nodes:

```bash
id
sudo usermod -aG video,render "$USER"
```

The group change takes effect after logging in again. Production systems should configure permissions with udev rules instead of permanently using `chmod 777`.
