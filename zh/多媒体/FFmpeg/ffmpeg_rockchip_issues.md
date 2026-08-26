# 常见问题

## Rockchip 平台

### 6. 常见问题

| 现象 | 检查方法 |
| --- | --- |
| `Unknown decoder/encoder` | 使用 `ffmpeg -version` 和 `ffmpeg -encoders/-decoders` 确认当前版本 |
| 无法打开 MPP/RGA 设备 | 检查设备节点、用户组、Kernel 驱动和 DTS |
| RGA 提示 `No hw context provided` | 解码时加入 `-hwaccel_output_format drm_prime` |
| RGA 提示格式或尺寸不支持 | 改用 `nv12` 等常用格式，并检查宽高和裁剪坐标对齐 |
| 硬件加速后 CPU 占用仍较高 | 检查是否使用了软件滤镜，导致 `hwdownload` 和 `hwupload` |
| 某个格式无法硬解 | 检查目标 SoC 是否支持该编码、位深、Profile 和分辨率 |

查看详细日志：

```bash
ffmpeg -loglevel verbose <other-options>
dmesg | grep -Ei 'mpp|rga|vpu|rkvdec|rkvenc'
```

参考资料：

- [ffmpeg-rockchip](https://github.com/nyanmisaka/ffmpeg-rockchip)
- [Rockchip MPP](https://github.com/rockchip-linux/mpp)
- [Rockchip RGA](https://github.com/airockchip/librga)
