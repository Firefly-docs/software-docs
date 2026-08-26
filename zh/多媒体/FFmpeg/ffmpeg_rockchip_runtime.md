# 运行要求

## Rockchip 平台

### 3. 运行要求

`ffmpeg-rockchip` 需要 Rockchip BSP/vendor Kernel。项目主要测试了 Kernel 5.10 和 6.1。

编译和运行需要：

- Rockchip MPP 1.3.9 或更高版本。
- 与 BSP 匹配的 librga。
- libdrm 和 DRM_PRIME/DMA-BUF 支持。
- MPP、RGA 和 DRM 设备节点的访问权限。

常见设备节点：

```text
/dev/mpp_service
/dev/rga
/dev/dri/
/dev/dma_heap/
```

检查设备节点：

```bash
ls -l /dev/mpp_service /dev/rga /dev/dri /dev/dma_heap 2>/dev/null
```

运行 FFmpeg 的用户通常需要属于设备节点对应的 `video` 或 `render` 用户组：

```bash
id
sudo usermod -aG video,render "$USER"
```

重新登录后用户组配置才会生效。量产系统建议使用 udev 规则配置权限，不要长期使用 `chmod 777`。
