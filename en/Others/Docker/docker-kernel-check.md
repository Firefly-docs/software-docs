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
