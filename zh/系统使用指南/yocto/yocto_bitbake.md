# 更多 BitBake 选项

从根本上说，BitBake 是一个通用任务执行引擎，它允许 shell 和 Python 任务高效并行运行，同时在复杂的任务间依赖约束下工作。BitBake 的主要用户之一 OpenEmbedded 利用这个核心，并使用面向任务的方法构建嵌入式 Linux 软件堆栈。更多详细使用方法请查看 [BitBake 用户手册](https://docs.yoctoproject.org/bitbake/2.0/bitbake-user-manual/bitbake-user-manual-intro.html#)。

```bash
MACHINE=aio-1126bjd4 bitbake <target> <paramater>
# e.g
MACHINE=aio-1126bjd4 bitbake u-boot-rockchip -c clean
MACHINE=aio-1126bjd4 bitbake u-boot-rockchip
```

| BitBake 参数  | 描述 |
| :--------: | :-------: |
| -c fetch | 拉取目标所需要的代码 |
| -c clean | 清除目标的输出文件 |
| -c cleanall | 删除目标所有输出文件、共享高速缓存（shared state cache）和源代码 |
| -c compile -f | 使用此选项可在部署映像后强制重新编译，但不建议使用，除非 Yocto Project 不知道目标代码已经发生改变 |
| -c listtasks | 列出目标定义的所有任务 |
