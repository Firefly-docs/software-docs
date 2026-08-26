# 调整 Yocto 编译速度

文件 `firefly-kernel6.1-rv1126b.conf` 中的 BB_NUMBER_THREADS 与 PARALLEL_MAKE 默认以注释形式提供。可取消注释并根据编译主机的 CPU 核数和内存调整；线程数量过大会造成内存不足并导致编译失败。

```
BB_NUMBER_THREADS = "4"
PARALLEL_MAKE = "-j 4"
```

- [BB_NUMBER_THREADS](https://docs.yoctoproject.org/ref-manual/variables.html#term-BB_NUMBER_THREADS)：BitBake 同时执行的最大线程数。
- [BB_NUMBER_PARSE_THREADS](https://docs.yoctoproject.org/ref-manual/variables.html#term-BB_NUMBER_PARSE_THREADS)：BitBake 解析时使用的线程数。
- [PARALLEL_MAKE](https://docs.yoctoproject.org/ref-manual/variables.html#term-PARALLEL_MAKE)：传递给 `make` 命令的额外选项，用于指定 [do_compile](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-compile) 任务在本地编译主机上的并行编译数。
- [PARALLEL_MAKEINST](https://docs.yoctoproject.org/ref-manual/variables.html#term-PARALLEL_MAKEINST)：传递给 `make` 命令的额外选项，用于指定 [do_install](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-install) 任务在本地编译主机上的并行安装数。
