# 编译与检查

## Rockchip 平台

### 4. 编译与检查

检查依赖：

```bash
pkg-config --modversion libdrm
pkg-config --modversion rockchip_mpp
pkg-config --modversion librga
```

获取并编译源码：

```bash
git clone https://github.com/nyanmisaka/ffmpeg-rockchip.git
cd ffmpeg-rockchip

./configure \
  --prefix=/usr/local \
  --enable-version3 \
  --enable-libdrm \
  --enable-rkmpp \
  --enable-rkrga \
  --enable-shared \
  --disable-static

make -j$(nproc)
sudo make install
sudo ldconfig
```

确认当前调用的是正确的 FFmpeg：

```bash
command -v ffmpeg
ffmpeg -version
```

检查 Rockchip 硬件功能：

```bash
ffmpeg -hide_banner -hwaccels | grep -i rkmpp
ffmpeg -hide_banner -decoders | grep rkmpp
ffmpeg -hide_banner -encoders | grep rkmpp
ffmpeg -hide_banner -filters  | grep rkrga
```

如果没有对应条目，通常是当前运行了系统自带的标准 FFmpeg，或者编译时未成功启用 MPP/RGA。
