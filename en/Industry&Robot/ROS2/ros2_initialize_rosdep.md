# Initialize rosdep

## 6. Initialize rosdep

```bash
sudo rosdep init
rosdep update --rosdistro humble
```

If `sudo rosdep init` reports that rosdep has already been initialized, ignore the message and continue with `rosdep update`.

If access to GitHub Raw times out, retry as follows:

```bash
curl -fL \
  --retry 8 \
  --retry-all-errors \
  --connect-timeout 20 \
  --max-time 300 \
  https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list \
  -o /tmp/20-default.list

sudo install -D -o root -g root -m 0644 \
  /tmp/20-default.list \
  /etc/ros/rosdep/sources.list.d/20-default.list

rm -f /tmp/20-default.list
rosdep update --rosdistro humble
```
