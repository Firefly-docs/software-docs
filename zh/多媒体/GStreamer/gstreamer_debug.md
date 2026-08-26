# 通用调试和性能分析

## 5. 通用调试和性能分析

### 5.1 开启日志

GStreamer 日志等级如下：

| 等级 | 数值 |
| --- | --- |
| ERROR | `1` |
| WARNING | `2` |
| FIXME | `3` |
| INFO | `4` |
| DEBUG | `5` |
| LOG | `6` |
| TRACE | `7` |

设置全局日志等级：

```bash
GST_DEBUG=2 gst-play-1.0 test.mp4
```

为不同模块设置不同等级：

```bash
GST_DEBUG='2,decodebin:5,v4l2*:4' gst-play-1.0 test.mp4
```

### 5.2 查看帧率

`fpsdisplaysink` 可以统计视频帧率，并将画面转交给真正的 Video Sink：

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=autovideosink \
      signal-fps-measurements=true text-overlay=false
```

`sync=false` 可用于测试 Pipeline 的最大吞吐量，但会关闭时钟同步，不代表正常播放行为。

```bash
GST_DEBUG=fpsdisplaysink:7 \
gst-launch-1.0 videotestsrc \
  ! fpsdisplaysink video-sink=fakesink \
      signal-fps-measurements=true text-overlay=false sync=false
```

### 5.3 导出 Pipeline 图

```bash
export GST_DEBUG_DUMP_DOT_DIR=/tmp
gst-play-1.0 test.mp4
ls -l /tmp/*.dot
```

在安装了 Graphviz 的主机上转换为图片：

```bash
dot pipeline.dot -Tpng -o pipeline.png
```

一次运行可能生成多个 DOT 文件，分别对应 Pipeline 在不同状态下的结构。

### 5.4 通用排障流程

1. 用 `gst-inspect-1.0` 确认插件存在，并检查 Pad、Caps 和属性。
2. 用 `gst-discoverer-1.0` 或 `v4l2-ctl` 确认输入格式。
3. 为 `gst-launch-1.0` 增加 `-v`，查看协商后的 Caps。
4. 将复杂 Pipeline 简化为 `Source → Parser/Decoder → fakesink`。
5. 再逐步加入格式转换、编码、显示、音频和网络分支。
6. 在 `tee`、Demuxer 或动态 Pad 后增加 `queue`，排除分支阻塞。
7. 使用 `GST_DEBUG` 定位加载插件、状态切换和协商错误。
8. 使用 `fpsdisplaysink` 区分输入、编解码、处理和显示瓶颈。
9. 导出 DOT 图确认实际创建的 Element 和连接关系。
