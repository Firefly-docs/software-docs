# FFMedia

FFMedia is a modular audio/video framework for Linux multimedia applications and supports rapid application development. It is primarily tailored to Rockchip hardware capabilities and encapsulates complex chip-specific low-level processing into reusable functional modules according to their functions. Camera, file, network stream, decoding, encoding, image processing, inference, display, recording, and streaming are packaged as functional modules, making it easy to quickly compose and develop multimedia applications.

## Get the SDK

For stable deployment, download a release package from the [latest tags](https://github.com/Firefly-rk-linux-utils/ffmedia_release/tags). The release package includes the prebuilt SDK, command-line tool, Python bindings, and example projects.

To inspect the source code or participate in development, you can also clone the repository:

~~~bash
git clone --depth=1 https://github.com/Firefly-rk-linux-utils/ffmedia_release.git
cd ffmedia_release
~~~

The main directories are as follows:

| Path | Contents |
| --- | --- |
| bin/ffmedia | Generic media pipeline command-line tool |
| include/ffmedia/ | Public C++ headers |
| lib/aarch64-linux-gnu/ | Shared libraries, CMake configuration, and pkg-config files |
| python/ | Python binding wheels |
| examples/demo/ | C++/Python demos and CLI source code |
| examples/tests/ | CPU tests and manual hardware tests |
| examples/inference/ | RKNN inference extensions |

The CMake project at the root of the release package is primarily used to build examples, tests, and extensions. The FFMedia core shared library is provided directly by the release package.

## Run the Prebuilt Tool

From the root of the release package, set the shared library path:

~~~bash
./bin/ffmedia --help
~~~

Check the supported modules and parameters:

~~~bash
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia params image-processor output
~~~

For more detailed usage, read the `examples/demo/ffmedia.md` documentation and the `examples/demo/ffmedia.cpp` source code in the SDK.

## Build Demos and Tests

Basic build:

~~~bash
cmake -S . -B build
cmake --build build -j$(nproc)
~~~

Common options:

| Option | Description |
| --- | --- |
| DEMO_OPENCV | Build the OpenCV demo; requires OpenCV 4 |
| ENABLE_TESTS | Build `examples/tests` |
| ENABLE_INFERENCE_EXAMPLES | Build inference examples |
| ENABLE_INFERENCE_EXTENSION | Build the standalone RKNN inference extension |

For example, build the OpenCV demo:

~~~bash
cmake -S . -B build -DDEMO_OPENCV=ON
cmake --build build -j$(nproc)
~~~

After building, run the demo to view its help information:

~~~bash
./build/demo
Firefly FFMedia: v2.6.2
INFO: ff_media: usage: Usage: ./demo <Input source> [Options]

Options:
-i, --input                  Input image size
-o, --output                 Output image size, default same as input
-a, --inputfmt               Input image format, default MJPEG
-b, --outputfmt              Output image format, default NV12
--use_ffmpeg_demux           Use ffmpeg demux. e.g. --use_ffmpeg_demux or --use_ffmpeg_demux=kmsgrab
--use_ffmpeg_mux             Use ffmpeg demux. e.g. --use_ffmpeg_mux or --use_ffmpeg_mux=rtsp
-c, --count                  Instance count, default 1
-d, --drmdisplay             Drm display, set display plane, set 0 to auto find plane, default disabled
    --connector              Set drm display connector, default 0 to auto find connector
-x, --x11                    X11 window displays, render the video using opengl. default disabled
-z, --zpos                   Drm display plane zpos, default auto select
-e, --encodetype             Encode encode, set encode type, default disabled
-f, --file                   Enable save source output data to file, set filename, default disabled
--port                       Enable push stream, default rtsp stream, set push port, depend on encode enabled, default disabled
--push_type                  Set push stream type, default rtsp. e.g. --push_type rtmp
--push_path                  Set push stream path, default /live/0. e.g. --push_path /live/test
--rtmp_url                   Set the rtmp client push address. e.g. --rtmp_url rtmp://xxx
--rtsp_transport             Set the rtsp transport type, default udp.
                               e.g. --rtsp_transport tcp | --rtsp_transport multicast
-m, --enmux                  Enable save encode data to file. Enable package as mp4, mkv, flv, ts, ps or raw stream files, muxer type depends on the filename suffix.
                               default disabled. e.g. -m out.mp4 | -m out.mkv | -m out.yuv
-s, --sync                   Enable synchronization module, default disabled. Enable the default audio.
                               e.g. -s | --sync=video | --sync=abs
--audio                      Enable audio, default disabled.
--aplay                      Enable play audio, default disabled. e.g. --aplay plughw:3,0
--arecord                    Enable record audio, default disabled. e.g. --arecord plughw:3,0
-l, --loop                   Loop reads the media file.
--gb28181_user_id            Enable gb28181 client, default disabled. set user id
--gb28181_server_id          Set the server id of gb28181 client
--gb28181_server_ip          Set the server ip of gb28181 client
--gb28181_server_port        Set the server port of gb28181 client
--dec_disabled               Disabled decoder
-r, --rotate                 Image rotation degree, default 0
                               0:   none
                               1:   vertical mirror
                               2:   horizontal mirror
                               90:  90 degree
                               180: 180 degree
                               270: 270 degree

~~~

Example: The input is an RTSP camera with a resolution of 1080p. The decoded image is scaled to 720p, rotated by 90 degrees, and displayed on the screen.

```
./demo rtsp://admin:firefly123@172.16.2.96 -o 1280x720 -r 90 -d 0 -s
```

For more usage details, read the `examples/demo/Readme.md` documentation in the SDK.

~~~
