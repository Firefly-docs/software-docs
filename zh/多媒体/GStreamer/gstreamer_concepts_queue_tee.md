# Queue、Tee 和多线程

## 2. 基本概念

### 2.5 Queue、Tee 和多线程

`tee` 将一路数据复制为多路，`queue` 为不同分支建立独立线程和缓存。

```bash
gst-launch-1.0 videotestsrc \
  ! tee name=t \
  t. ! queue ! autovideosink \
  t. ! queue ! fakesink
```

分支后通常都应增加 `queue`。否则任意一路阻塞时，可能导致整个 Pipeline 停止。
