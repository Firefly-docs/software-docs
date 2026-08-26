# Install the Matching libudev Package

## 3. Install the Matching Firefly libudev Development Package

Firefly Ubuntu images usually include a customized version of `libudev1`. ROS 2 Desktop requires the `libudev-dev` package with exactly the same version.

Temporarily enable the official Firefly repository:

```bash
printf '%s\n' \
  'deb https://wiki.t-firefly.com/firefly-ubuntu-repo jammy main' \
  | sudo tee /etc/apt/sources.list.d/firefly-ros2-install.list >/dev/null

sudo apt-get update
```

Install the version that exactly matches the current `libudev1` package:

```bash
libudev_version=$(dpkg-query -W -f='${Version}' libudev1)
echo "libudev version: $libudev_version"

sudo apt-get install -y --no-remove --no-upgrade \
  "libudev-dev=$libudev_version"
```

After installation, remove the temporary Firefly repository:

```bash
sudo rm -f /etc/apt/sources.list.d/firefly-ros2-install.list
sudo apt-get update
```
