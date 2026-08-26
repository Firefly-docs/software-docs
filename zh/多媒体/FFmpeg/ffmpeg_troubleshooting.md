# 通用问题排查

## FFmpeg 通用使用

### 7. 通用问题排查

| 现象 | 检查方向 |
| --- | --- |
| `Unknown encoder` | 使用 `ffmpeg -encoders` 检查编码器是否已编译 |
| `Invalid data found when processing input` | 检查文件是否完整、封装格式是否正确 |
| `Could not find codec parameters` | 增大 `-analyzeduration` 和 `-probesize`，或检查直播流是否包含完整头信息 |
| `Non-monotonous DTS` | 输入时间戳异常，检查拼接、转封装和采集时钟 |
| 输出无音频或字幕 | 使用 `ffprobe` 和 `-map` 显式选择码流 |
| 文件过大 | 调整 CRF、码率、分辨率或帧率 |
| 处理速度较慢 | 检查编码 `preset`、软件滤镜、I/O 和硬件加速状态 |

获取详细日志：

```bash
ffmpeg -loglevel verbose -i input.mp4 <output-options> output.mp4
```

保存可用于问题报告的完整日志：

```bash
ffmpeg -report -i input.mp4 <output-options> output.mp4
```
