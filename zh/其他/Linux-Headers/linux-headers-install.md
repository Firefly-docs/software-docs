# 安装 Linux-Headers

以下以 ROC-RK3568-PC 安装为例：

将得到的 deb 包放入设备中，然后安装，对于 headers，安装后还需要进行编译处理：

```shell
# kernel 5.10 or kernel 4.19 linux-headers
sudo apt install ./linux-headers-4.19.172_4.19.172-189_arm64.deb
sudo apt install -y build-essential python libssl-dev # 准备编译环境
cd /usr/src/linux-headers-4.19.172
make headers_check
make headers_install
make scripts # 可能会出错，如果出错在 tools，可以直接忽略，安装完成

# kernel 6.1 linux-headers
sudo apt install ./linux-headers-6.1-arm64_arm64.deb
cd /usr/src/linux-headers-6.1-arm64
./scripts/builddtb.sh
```
