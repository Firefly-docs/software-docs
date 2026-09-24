# 应用场景与模块选择

选择 FFMedia 模块时，可以先确定三件事：数据从哪里来、需要经过什么处理、结果要送到
哪里。常见组合如下：

| 目标 | 输入 | 处理 | 输出 |
| --- | --- | --- | --- |
| Camera 预览 | cam | rga（可选） | drm-display 或 renderer-video |
| 网络流播放 | rtsp-client 或 rtmp-client | mpp-dec | 显示、录制或再次编码 |
| 本地播放 | file-reader 或 ffmpeg-demux | mpp-dec、rga | 显示或音频播放 |
| 摄像头推流 | cam | mpp-enc | rtsp-server、rtmp-server 或网络 mux |
| 视频转码 | file-reader 或 ffmpeg-demux | mpp-dec、rga、mpp-enc | file-writer 或 ffmpeg-mux |
| 多路拼接 | 多路输入和解码器 | video-stack | 显示、录制或推流 |
| AI 视频分析 | 文件、网络流或 Camera | mpp-dec、inference | OSD、显示、录制或推流 |
| 应用自有数据 | ModuleAppSource 或 mem-reader | 自定义处理 | 任意 VO 输出模块 |

## 使用前确认

- 目标板是否提供所需的 MPP、RGA、DRM、Camera、ALSA 或 RKNN 运行环境。
- 输入格式、分辨率和帧率是否得到设备支持。
- 发布包架构、Python wheel 和目标系统是否匹配。
- 多路编解码是否需要调整文件描述符限制。
- 实际参数名称和模块能力是否与当前发布包的 modules、params 输出一致。
