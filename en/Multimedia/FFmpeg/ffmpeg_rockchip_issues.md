# Common Issues

## Rockchip Platform

### 6. Common Issues

| Symptom | What to check |
| --- | --- |
| `Unknown decoder/encoder` | Use `ffmpeg -version` and `ffmpeg -encoders/-decoders` to verify the active build |
| MPP/RGA device cannot be opened | Check device nodes, user groups, kernel drivers, and the DTS |
| RGA reports `No hw context provided` | Add `-hwaccel_output_format drm_prime` when decoding |
| RGA rejects a format or size | Try a common format such as `nv12`, and check dimension and crop-coordinate alignment |
| CPU usage remains high with hardware acceleration | Check whether software filters are causing `hwdownload` and `hwupload` operations |
| A format cannot be hardware-decoded | Check whether the target SoC supports the codec, bit depth, profile, and resolution |

Display detailed logs:

```bash
ffmpeg -loglevel verbose <other-options>
dmesg | grep -Ei 'mpp|rga|vpu|rkvdec|rkvenc'
```

References:

- [ffmpeg-rockchip](https://github.com/nyanmisaka/ffmpeg-rockchip)
- [Rockchip MPP](https://github.com/rockchip-linux/mpp)
- [Rockchip RGA](https://github.com/airockchip/librga)
