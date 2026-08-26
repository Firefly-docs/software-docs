# Install Linux-Headers

The following takes the installation of ROC-RK3568-PC as an example:

Put deb packages in device to install, for headers, you have to compile them after installation
```shell
# Compile Kernel 5.10 or 4.19 Linux-Headers
sudo apt install ./linux-headers-4.19.172_4.19.172-189_arm64.deb
sudo apt install -y build-essential python libssl-dev # Prepare
cd /usr/src/linux-headers-4.19.172
make headers_check
make headers_install
make scripts # make scripts may go wrong but it doesn't matter

# Compile Kernel 6.1 Linux-Headers
sudo apt install ./linux-headers-6.1-arm64_arm64.deb
cd /usr/src/linux-headers-6.1-arm64
./scripts/builddtb.sh
```
