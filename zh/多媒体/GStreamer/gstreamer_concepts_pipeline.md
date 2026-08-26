# Pipeline

## 2. 基本概念

### 2.2 Pipeline

命令行中使用 `!` 连接 Element：

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

该 Pipeline 使用 `videotestsrc` 生成测试画面，再由 `autovideosink` 自动选择显示插件。

生产程序通常通过 GStreamer API 创建和控制 Pipeline；`gst-launch-1.0` 主要用于验证方案、复现问题和性能测试。
