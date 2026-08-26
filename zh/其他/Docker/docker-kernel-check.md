# Docker 支持

Firefly 发布的普通固件一般不满足 Docker 的运行要求，如果有需求，可以使用 SDK 打开内核的相关配置，重新编译烧录内核以支持 Docker。

**（RK356X v1.2.4a 及以后版本 、RK3399/RK3588 默认支持 Docker，可直跳到 安装 Docker 步骤）**

以下案例是基于 Firefly Ubuntu 20.04，内核配置部分是通用的！

## 检查 Kernel 配置

首先需要通过工具检查当前设备的内核缺少了哪些 Docker 需要的配置。检测脚本`check-config.sh`可以前往[GitHub](https://github.com/moby/moby/blob/master/contrib/check-config.sh)获取。

获取到脚本之后，开始进行检测：
```bash
#将脚本拷贝到SDK的kernel目录下
cp check-config.sh PathToSDK/kernel/
cd PathToSDK/kernel
chmod +x check-config.sh

#检测
#你需要事先编译一遍内核以获取正确的 .config 文件
./check-config.sh .config
```
执行后的结果如下，主要是两部分：
```
Generally Necessary:
- cgroup hierarchy: properly mounted [/sys/fs/cgroup]
- apparmor: enabled and tools installed
- CONFIG_NAMESPACES: enabled
- CONFIG_NET_NS: enabled
- CONFIG_PID_NS: enabled
- CONFIG_IPC_NS: enabled
- CONFIG_UTS_NS: enabled
- CONFIG_CGROUPS: enabled
......

Optional Features:
- CONFIG_USER_NS: enabled
- CONFIG_SECCOMP: enabled
- CONFIG_SECCOMP_FILTER: enabled
- CONFIG_CGROUP_PIDS: enabled
- CONFIG_MEMCG_SWAP: enabled
......
```
Generally Necessary: 表示必要的配置，如果有显示 missing 的地方，就需要在内核配置中打开它。

Optional Features: 是可选配置，根据需要打开。
