# FFMedia General Usage

FFMedia provides a C++ API, Python bindings, and the `ffmedia` command-line tool. The recommended order is:

1. C++: for production applications and media workflows that require full control.
2. Python: for rapid development, functional validation, and script integration.
3. CLI: for rapid validation, debugging, general-purpose tasks, and script automation.

Regardless of the interface, the basic pipeline lifecycle is:

~~~text
Create modules -> set parameters -> initialize the input module -> connect downstream modules
-> initialize downstream modules -> start the input module -> run -> stop the pipeline
~~~

Initialize the producer before connecting it so that it can publish its output channels. `connectProducer()` matches the upstream and downstream modules using media type, codec, and image or audio parameters. Check the return value of every connection and initialization call before starting the pipeline.

## C++ Integration

C++ is FFMedia's primary application interface. It provides direct access to module APIs, the parameter system, channel matching, and `MediaBuffer`. Production applications normally use C++ for device management, business loops, error handling, and custom module integration.

### Project Configuration

Use the CMake target exported by the SDK:

~~~cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
~~~

Include the stable aggregate header:

~~~cpp
#include <ffmedia/ffmedia.hpp>
~~~

The headers, CMake configuration, and shared library must come from the same SDK package. Do not mix files from different packages.

### Create a Basic Pipeline

The following example reads a file, decodes it with MPP, and writes the decoded data to a file. In a real application, the output module can be replaced with `ModuleDrmDisplay`, `ModuleRendererVideo`, or a network output module.

~~~cpp
#include <cstdio>
#include <memory>

#include <ffmedia/ffmedia.hpp>

using namespace FFMedia;

int main()
{
    auto source = std::make_shared<ModuleFileReader>("input.mp4", false);
    int ret = source->init();
    if (ret < 0)
        return ret;

    auto decoder = std::make_shared<ModuleMppDec>();
    ret = decoder->connectProducer(source);
    if (ret < 0)
        return ret;
    ret = decoder->init();
    if (ret < 0)
        return ret;

    auto output = std::make_shared<ModuleFileWriter>("output.nv12");
    ret = output->connectProducer(decoder);
    if (ret < 0)
        return ret;
    ret = output->init();
    if (ret < 0)
        return ret;

    source->start();

    // A real application runs its business loop or waits for EOS here.
    std::getchar();

    source->stop();
    return 0;
}
~~~

The connection is:

~~~text
ModuleFileReader -> ModuleMppDec -> ModuleFileWriter
~~~

Calling `start()` on the source module starts its connected downstream modules. Calling `stop()` on the source stops the pipeline. To display and save at the same time, connect the same decoder to multiple output modules.

### Set Parameters

Parameters can be set directly through module interfaces or through parameter paths managed uniformly by `MediaParameter`. Check the return values before running the pipeline:

~~~cpp
auto encoder = std::make_shared<ModuleMppEnc>(MEDIA_CODEC_VIDEO_H264);

if (encoder->setParameter("encode/fps", 30) < 0)
    return -1;

if (encoder->setParameter("encode/bitrate", 4096) < 0)
    return -1;
~~~

Use the current module's `queryParameter()` or `params` output as the source of truth for parameter names, types, defaults, ranges, and writable states. See the [FFMedia parameter system](https://github.com/Firefly-rk-linux-utils/ffmedia_release/blob/master/docs/ffmedia_parameters.md) for complete rules.

## Python Integration

The Python binding module is named `ff_pymedia`. It exposes the main module, parameter, channel-connection, and lifecycle features. It is suitable for rapid development, automated validation, and script integration; use C++ when the application needs full control over threads, devices, or custom modules.

### Install and Import

Install a wheel that matches the Python interpreter and system architecture:

~~~bash
python3 -m pip install python/ff_pymedia-<wheel matching the current Python version>.whl
~~~

~~~python
import ff_pymedia as ff
~~~

The Python version, wheel architecture, and shared libraries must match. The exact wheel filename depends on the contents of the current package.

### Create a Python Pipeline

The following example reads a file, decodes it with MPP, and receives decoded buffers through a produce hook:

~~~python
import ff_pymedia as ff


def on_frame(name, value, buffer):
    # Keep an independent copy if the frame is needed after the callback.
    frame = buffer.clone()
    if frame is not None:
        print(name, frame.getActiveSize())


source = ff.ModuleFileReader("input.mp4", False)
if source.init() < 0:
    raise RuntimeError("failed to initialize source")

decoder = ff.ModuleMppDec()
if decoder.connectProducer(source) < 0:
    raise RuntimeError("failed to connect decoder")
if decoder.init() < 0:
    raise RuntimeError("failed to initialize decoder")
if not decoder.setMediaBufferProduceHooker(on_frame):
    raise RuntimeError("failed to set output hook")

source.start()
try:
    input("Press Enter to stop...\n")
finally:
    source.stop()
~~~

Use the current binding exports as the source of truth for Python constructors, parameter paths, and callback signatures. The current `ff_pymedia` binding does not export `ModuleAppSource`, `ModuleAppProcessor`, or application-processing callback types. Use the exported `ModuleMemReader` for application input, or use the C++ API. See the Python section of the [FFMedia API](https://github.com/Firefly-rk-linux-utils/ffmedia_release/blob/master/docs/ffmedia_api.md) for other binding differences.

## CLI Quick Validation

The `ffmedia` CLI is a generic pipeline validation tool. Use it before writing an application to check:

- Whether the current SDK provides the required modules.
- Whether parameter paths, types, and values are valid.
- Whether producer output channels match consumer input channels.
- Whether the pipeline connection graph is correct.

### Query Modules and Parameters

~~~bash
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia params rga
./bin/ffmedia run --help
~~~

### Describe a Pipeline with the CLI

The CLI uses three kinds of arguments:

| Argument | Purpose | Example |
| --- | --- | --- |
| `-m ID=TYPE` | Declare a module instance | `-m decoder=mpp-dec` |
| `-p ID:PARAMETERS` | Set module parameters | `-p 'decoder:output{format=NV12}'` |
| `-c PRODUCER[@CHANNELS]=CONSUMER` | Connect modules | `-c source@0=decoder` |

Example 1: Read a media file, decode it, and display it in a system window.

~~~bash
./bin/ffmedia run \
    -m source=ffmpeg-demux \
    -m decoder=mpp-dec \
    -m dis=renderer-video \
    -p 'source:source{uri=input.mp4;loop=0}' \
    -c source=decoder \
    -c decoder=dis \
    --sync dis=video
~~~

Example 2: Read four media files, decode them, compose them into a 2x2 layout on a 1080p canvas, and display the result in a 720p system window.

```bash
./bin/ffmedia run \
  -m src1=file-reader -m src2=file-reader -m src3=file-reader -m src4=file-reader \
  -m dec1=mpp-dec -m dec2=mpp-dec -m dec3=mpp-dec -m dec4=mpp-dec \
  -m stack=video-stack \
  -m vo=renderer-video \
  -p 'src1:source{path=/path/to/input1.mp4;loop=true}' \
  -p 'src2:source{path=/path/to/input2.mp4;loop=true}' \
  -p 'src3:source{path=/path/to/input3.mp4;loop=true}' \
  -p 'src4:source{path=/path/to/input4.mp4;loop=true}' \
  -p 'stack:output{width=1920;height=1080;format=NV12};frame-rate=30' \
  -p 'stack:input-layout{input-id=0;crop{x=0;y=0;width=956;height=536}}' \
  -p 'stack:input-layout{input-id=1;crop{x=964;y=0;width=956;height=536}}' \
  -p 'stack:input-layout{input-id=2;crop{x=0;y=544;width=956;height=536}}' \
  -p 'stack:input-layout{input-id=3;crop{x=964;y=544;width=956;height=536}}' \
  -p 'vo:window{x=100;y=100;width=1280;height=720}' \
  -c src1=dec1 \
  -c src2=dec2 \
  -c src3=dec3 \
  -c src4=dec4 \
  -c dec1=stack \
  -c dec2=stack \
  -c dec3=stack \
  -c dec4=stack \
  -c stack=vo

```

![](../../../img/FFMedia/multi_stack_display.png)

For the complete CLI syntax and options, see the upstream [`ffmedia` command-line guide](https://github.com/Firefly-rk-linux-utils/ffmedia_release/blob/master/examples/demo/ffmedia.md).

## Common Problems

| Symptom | First checks |
| --- | --- |
| Shared library not found | Check whether `LD_LIBRARY_PATH` contains the SDK's `lib/aarch64-linux-gnu` or `lib` directory |
| Module not found | Check whether `modules` lists the module and whether the required optional component is enabled |
| Parameter setting failed | Check the path, type, range, and current state against the `params` output |
| Connection failed | Check the channel ID, media type, and consumer input requirements |
| DRM or Camera initialization failed | Check device permissions, device formats, connector, and plane settings |
| Python import failed | Check the Python version, wheel architecture, and shared library path |
