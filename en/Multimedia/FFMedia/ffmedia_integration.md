# Integration Method

The current SDK provides precompiled shared libraries, public header files, CMake configuration, the `ffmedia` command-line tool, C++ examples, and Python wheels.

## Command-Line Tool

The `ffmedia` tool composes media pipelines using modules, parameters, and connections:

```bash
export LD_LIBRARY_PATH="$PWD/lib:$LD_LIBRARY_PATH"
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia run --help
```

## C++ Integration

The recommended integration method is to use the imported CMake target:

```cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
```

Use the unified include file in your source code:

```cpp
#include <ffmedia/ffmedia.hpp>
```

The typical workflow is to create modules, set parameters, initialize them, call `connectProducer()` to connect upstream and downstream modules, and then start and stop the source modules.

## Python Integration

The release package provides Python 3.10 and Python 3.11 wheels for AArch64:

```bash
python3 -m pip install python/ff_pymedia-2.6.0-cp310-cp310-linux_aarch64.whl
# Use the cp311 wheel with Python 3.11
```

## Build the Examples

```bash
cmake -S . -B build
cmake --build build -j$(nproc)
```
