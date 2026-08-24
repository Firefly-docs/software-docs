# Docker support

Firefly normal firmware generally does not meet Docker operation requirements. If there is a need, you can use SDK to enable related kernel configs and rebuild the kernel to support Docker. 

**(RK356X v1.2.4a and later version, RK3399/RK3588 support Docker by default, you can skip straight to the installation step)**

The following case is based on Firefly Ubuntu 20.04, and the kernel configuration part is generic!

## Check Kernel Configuration

First use script to see which configuration is needed by Docker but missing in current kernel. You can get the check script from [GitHub](https://github.com/moby/moby/blob/master/contrib/check-config.sh).

After get the script, begin to check:
```bash
#Copy the script to SDK/kernel/
cp check-config.sh PathToSDK/kernel/
cd PathToSDK/kernel
chmod +x check-config.sh

#Check
#You need to build kernel first to get correct .config
./check-config.sh .config
```
The result looks like this, mainly two parts:
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
Generally Necessary: All features in this part need to be enabled in kernel.

Optional Features: Enable them as you needed.

## Enable Needed Features

From the result you can find out which features are need to be enabled, back up the current .config: `cp .config .config.bk`, then use `make ARCH=arm64 menuconfig` to enter config menu, search and enable them. Please read the instructions in the config menu, if some features can't be selected, check the dependency.

Enabled all the necessary features and some optional features, remember to save. Compare the new .config and previous .config.bk to show the modifications you have made, then write them into the actual config file under `arch/arm64/configs/`

Then re-compile the kernel.

## Install Docker

After downloading new kernel to device, install Docker on device (This method works on PC as well)

* Step1：Quick install
```bash
# Here only introduces the quick-install using script
apt-get update
wget -qO- https://get.docker.com/ | sh
```
You should see the Docker version information after the installation is successful.

* Step2：Check docker root dir (This step is only for PC docker installing)

If you are installing docker in Firefly device, please skip this step.
```bash
# execute
docker info | grep -i dir
# result
 Docker Root Dir: /var/lib/docker
```
The result shows docker root dir, this location may be different on different PC.

Images and containers need large disk space, **if the default location does not have enough space, you need to change the default location to one with sufficient space.**

<font color=red>**Emphasize again：This step is only for PC, in Firefly device, change this dir will cause docker error**</font>, please go to next step.
```bash
# stop docker service first
sudo systemctl stop docker

# modify /lib/systemd/system/docker.service
sudo vim /lib/systemd/system/docker.service

# add your destination dir at the end of ExecStart line: --graph /home/firefly/docker/data
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --graph /home/firefly/docker/data

# start docker service
sudo systemctl daemon-reload
sudo systemctl start docker

# check if the location is updated
docker info | grep -i dir
 Docker Root Dir: /home/firefly/docker/data
```

* Step3：add your user to docker group
```bash
sudo usermod -a -G docker firefly
# reboot
sudo reboot
```

* Step4：After reboot, run demo to test docker:
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
