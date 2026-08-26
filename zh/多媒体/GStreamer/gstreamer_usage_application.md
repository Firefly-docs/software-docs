# 应用程序接入

## 4. GStreamer 通用使用方法

### 4.6 应用程序接入

`appsrc` 和 `appsink` 用于应用程序与 Pipeline 交换数据：

- `appsrc`：应用向 Pipeline 输入数据，例如输入解密后的私有码流。
- `appsink`：应用从 Pipeline 获取数据，例如取得解码帧并交给算法处理。

典型应用结构：

```text
自定义输入 → appsrc → GStreamer Pipeline → appsink → 图像算法
                                            │
                                            ▼
                         appsrc → 后处理/编码/显示
```

命令行主要用于验证，正式使用通常需要通过 GStreamer API 处理 Buffer、时间戳、EOS 和状态切换。
