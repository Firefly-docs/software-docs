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

### 开启需要的配置

从上面的检测结果中得知需要打开哪些配置后，先备份 .config: `cp .config .config.bk`，然后使用`make ARCH=arm64 menuconfig`进入菜单，搜索对应项目将其打开。请认真查看菜单中的操作说明，遇到不可选中的项目请注意依赖关系。

开启所有必要配置以及部分可选配置后，注意保存。将新的 .config 与旧的 .config.bk 进行对比，可以看到你本次修改了哪些内容，并将修改写入`arch/arm64/configs/`下实际的配置文件。

之后进行编译内核。

## 安装 Docker

烧录完新内核之后，可以开始在设备上安装 Docker (此安装方法同样适用于 PC)：

* 步骤1：快速安装
```bash
# 这里仅介绍直接使用脚本快速安装
apt-get update
wget -qO- https://get.docker.com/ | sh
```
等待安装成功之后应该会看见 Docker 版本信息

* 步骤2：检查 docker 存储位置（该步骤仅适用于 PC 安装 docker）

如果你在 Firefly 设备中安装 docker，请跳过步骤2
```bash
# 执行
docker info | grep -i dir
# 执行结果
 Docker Root Dir: /var/lib/docker
```
返回的信息显示了 docker 的默认存储位置，该位置在不同电脑上可能不一样

镜像和容器会占用大量空间，因此，**如果默认的位置空间不大，需要修改到空间充足的位置**

<font color=red>**再次强调，此步骤只用于 PC，Firefly 设备中，修改此位置会导致 docker 无法工作**</font>，请直接跳到下一步
```bash
# 先关闭 docker 服务
sudo systemctl stop docker

# 修改文件 /lib/systemd/system/docker.service
sudo vim /lib/systemd/system/docker.service

# 在这一行末尾添加想要修改的位置 --graph /home/firefly/docker/data
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --graph /home/firefly/docker/data

# 重启 docker 服务
sudo systemctl daemon-reload
sudo systemctl start docker

# 检查位置是否修改成功
docker info | grep -i dir
 Docker Root Dir: /home/firefly/docker/data
```

* 步骤3：将自己的用户添加到 docker 组
```bash
sudo usermod -a -G docker firefly
# 添加后重启
sudo reboot
```

* 步骤4：重启后运行 demo 测试是否正常：
```
firefly@firefly:~# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
93288797bd35: Pull complete
Digest: sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
