# Bin 和自动选路插件

## 2. 基本概念

### 2.6 Bin 和自动选路插件

GStreamer 提供多个用于自动构建播放链路的 Bin：

| Element | 作用 |
| --- | --- |
| `decodebin` / `decodebin3` | 自动选择 Parser 和 Decoder，并为不同轨道创建输出 Pad |
| `uridecodebin` / `uridecodebin3` | 根据 URI 自动选择 Source，并完成解封装和解码 |
| `playbin` / `playbin3` | 构建完整播放链路，并自动选择音视频 Sink |

快速验证时优先使用自动选路插件；需要固定硬件 Decoder、控制延迟或排查问题时，再显式构建 Pipeline。
