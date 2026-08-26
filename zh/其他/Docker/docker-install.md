# 安装 Docker

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
