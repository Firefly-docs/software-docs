# 初始化 rosdep

## 6. 初始化 rosdep

```bash
sudo rosdep init
rosdep update --rosdistro humble
```

如果 `sudo rosdep init` 提示已经初始化，可以忽略并继续执行 `rosdep update`。

如果访问 GitHub Raw 超时，使用下面的方式重试：

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
