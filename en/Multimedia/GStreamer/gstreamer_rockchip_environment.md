# Environment Variables

## 7. Rockchip Platform Support

### 7.12 Environment Variables

The Rockchip SDK usually configures the relevant environment variables centrally in `/etc/profile.d/gst.sh`.

| Environment Variable | Purpose |
| --- | --- |
| `GST_MPP_DEC_DISABLE_NV12_10=1` | Disable direct 10-bit NV12 output and attempt to convert it to NV12 |
| `GST_MPP_DEC_DISABLE_NV16_10=1` | Disable direct 10-bit NV16 output and attempt to convert it to NV12 |
| `GST_MPP_VIDEODEC_DEFAULT_FORMAT=NV12` | Make the MPP decoder output NV12 by default; this may invoke RGA |
| `GST_MPP_VIDEODEC_DEFAULT_ARM_AFBC=1` | Enable AFBC output from the MPP decoder by default |
| `GST_V4L2_PREFERRED_FOURCC=...` | Set the preferred order of V4L2 pixel formats |
| `GST_VIDEO_CONVERT_PREFERRED_FORMAT=...` | Set the preferred order of `videoconvert` output formats |
| `GST_V4L2SRC_MAX_RESOLUTION=3840x2160` | Limit the maximum resolution that `v4l2src` can negotiate |
| `GST_V4L2_MIN_BUFS=64` | Set the minimum number of V4L2 driver buffers |
| `GST_VIDEO_CONVERT_USE_RGA=1` | Enable the RGA path for format conversion and scaling |
| `GST_VIDEO_FLIP_USE_RGA=1` | Enable the RGA path for rotation and flipping |

Set only the variables required by the application, and record the environment when documenting an issue:

```bash
env | grep '^GST_' | sort
```
