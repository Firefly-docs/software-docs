# Ubuntu/Debian 性能模式

需要让各模块尽量定频到最高频时，可切换到 root 用户后执行：

```bash
sudo su
echo performance | tee $(find /sys/ -name '*governor')
```

命令运行时，部分节点可能会提示 `Permission denied`、`Invalid argument` 等错误，这是因为这些 governor 节点不支持用户态写入或不支持 `performance` 参数，不影响其他节点生效，可忽略。

性能模式会让支持的 CPU、devfreq 等模块切换到 `performance` governor，但如果系统温度过高，thermal 机制仍然会强制降频，以保护硬件。
