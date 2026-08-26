# 命令基本结构

## FFmpeg 通用使用

### 3. 命令基本结构

FFmpeg 命令的基本结构为：

```bash
ffmpeg [全局选项] [输入选项] -i <输入> [输出选项] <输出>
```

例如：

```bash
ffmpeg -hide_banner -i input.mp4 -c:v libx264 -c:a aac output.mp4
```

选项的位置很重要：

- 放在 `-i` 前的选项通常属于后面的输入。
- 放在输出文件前的选项通常属于该输出。
- 多个输入和输出可以分别使用不同选项。

常用全局选项：

| 选项 | 说明 |
| --- | --- |
| `-hide_banner` | 隐藏版本 Banner，精简日志 |
| `-y` | 直接覆盖已存在的输出文件 |
| `-n` | 输出文件已存在时终止 |
| `-loglevel <level>` | 设置日志级别，例如 `error`、`info`、`verbose`、`debug` |
| `-stats` | 显示实时处理进度 |
| `-benchmark` | 输出处理时间和资源统计 |
