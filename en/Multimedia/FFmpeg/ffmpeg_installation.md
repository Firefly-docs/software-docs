# Installation and Feature Checks

## General FFmpeg Usage

### 2. Installation and Feature Checks

On Debian or Ubuntu, install the standard FFmpeg package provided by the distribution:

```bash
sudo apt update
sudo apt install ffmpeg
```

Display the version and build configuration:

```bash
ffmpeg -version
ffmpeg -buildconf
```

Display the capabilities supported by the current build:

```bash
ffmpeg -formats
ffmpeg -codecs
ffmpeg -encoders
ffmpeg -decoders
ffmpeg -filters
ffmpeg -protocols
ffmpeg -hwaccels
```

Build options differ between distributions. Before using `libx264`, `libx265`, SRT, or a particular network protocol, confirm that the current FFmpeg build includes the corresponding encoder or external library.

> Standard FFmpeg packages supplied by distributions normally do not include the `rkmpp` codecs and `rkrga` filters described below. Rockchip hardware acceleration requires a suitable `ffmpeg-rockchip` build.
