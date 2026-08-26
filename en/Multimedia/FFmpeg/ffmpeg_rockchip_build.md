# Building and Verifying

## Rockchip Platform

### 4. Building and Verifying

Check the dependencies:

```bash
pkg-config --modversion libdrm
pkg-config --modversion rockchip_mpp
pkg-config --modversion librga
```

Get and build the source code:

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

Confirm that the expected FFmpeg binary is being used:

```bash
command -v ffmpeg
ffmpeg -version
```

Check the Rockchip hardware features:

```bash
ffmpeg -hide_banner -hwaccels | grep -i rkmpp
ffmpeg -hide_banner -decoders | grep rkmpp
ffmpeg -hide_banner -encoders | grep rkmpp
ffmpeg -hide_banner -filters  | grep rkrga
```

If these entries are missing, the command may be using the distribution's standard FFmpeg binary, or MPP/RGA support may not have been enabled successfully during the build.
