# OpenCL

OpenCL 用于 GPU 通用计算，常见于图像处理、并行计算、预处理、后处理和部分 AI 辅助任务。OpenCL 能力取决于芯片 GPU、驱动和用户态运行库。

安装并查看 OpenCL 信息：

```bash
sudo apt update
sudo apt install clinfo
clinfo
```

重点检查：

- 是否能识别到 OpenCL Platform。
- 是否能识别到 GPU Device。
- `Device Type` 是否包含 `GPU`。
- OpenCL C 版本、最大工作组大小、全局内存大小是否符合应用需求。

如果 `clinfo` 找不到平台或设备，通常需要检查：

- OpenCL 用户态库是否安装。
- ICD 配置是否存在并指向正确驱动库。
- 当前固件是否包含对应 GPU 的 OpenCL 支持。
- 是否混用了不匹配的 Mali DDK、Mesa、内核驱动或根文件系统。
