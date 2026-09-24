# FFMedia Download and Build

## Get the SDK

For stable deployment, download a package from the [latest tags](https://github.com/Firefly-rk-linux-utils/ffmedia_release/tags). The package includes the prebuilt SDK, command-line tool, Python bindings, and example projects.

To inspect the source or participate in development, clone the repository:

~~~bash
git clone --depth=1 https://github.com/Firefly-rk-linux-utils/ffmedia_release.git
cd ffmedia_release
~~~

The main directories are:

| Path | Contents |
| --- | --- |
| `bin/ffmedia` | Generic media pipeline command-line tool |
| `include/ffmedia/` | Public C++ headers |
| `lib/aarch64-linux-gnu/` | Shared libraries, CMake configuration, and pkg-config files |
| `python/` | Python binding wheels |
| `examples/demo/` | C++/Python demos and CLI source |
| `examples/tests/` | CPU tests and manual hardware tests |
| `examples/inference/` | RKNN inference extensions |

The CMake project at the package root is mainly used to build examples, tests, and extensions. The FFMedia core shared library is provided by the package.

## Requirements

Basic requirements:

- An AArch64 Linux target, or a cross-compilation environment that can link against the AArch64 SDK.
- CMake 3.10 or later.
- A C++17-compatible compiler and pthread.
- A libstdc++ ABI compatible with the SDK.

The exact compiler, GLIBC, GLIBCXX, CXXABI, and Module ABI requirements are defined by `SDK_MANIFEST.txt`, `ffmedia_abi.hpp`, and the current CMake configuration.

## Run the Prebuilt Tool

From the root of the package, run:

~~~bash
./bin/ffmedia --help
~~~

Check the available modules and parameters:

~~~bash
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia params image-processor output
~~~

## Build Demos and Tests

Basic build:

~~~bash
cmake -S . -B build
cmake --build build -j$(nproc)
~~~

Common options:

| Option | Description |
| --- | --- |
| `DEMO_OPENCV` | Build the OpenCV demo; requires OpenCV 4 |
| `ENABLE_TESTS` | Build `examples/tests` |
| `ENABLE_INFERENCE_EXAMPLES` | Build inference examples |
| `ENABLE_INFERENCE_EXTENSION` | Build the standalone RKNN inference extension |

For example, build the OpenCV demo:

~~~bash
cmake -S . -B build -DDEMO_OPENCV=ON
cmake --build build -j$(nproc)
~~~

After building, run the CLI:

~~~bash
./build/ffmedia modules
~~~

Or run the simple demo:

~~~bash
./build/demo
~~~

## C++ Integration

The recommended method is to use the CMake target exported by the SDK:

~~~cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
~~~

If CMake cannot find the SDK, specify its CMake configuration directory:

~~~bash
cmake -S . -B build \
    -DFFMedia_DIR=/path/to/ffmedia_release/lib/aarch64-linux-gnu/cmake/FFMedia
~~~

Include the aggregate header in the source code:

~~~cpp
#include <ffmedia/ffmedia.hpp>
~~~

The headers, CMake configuration, and shared library must come from the same package. Do not mix files from different SDK packages.

## Python Integration

The package provides an AArch64 wheel for each supported Python interpreter. Select the wheel that matches the current interpreter:

~~~bash
# Check the Python version
python3 --version
# Install the wheel matching the current Python version
ls python/ff_pymedia-*.whl
python3 -m pip install python/ff_pymedia-<wheel matching the current Python version>.whl
~~~

After installation, run the Python CLI:

~~~bash
python3 examples/demo/ffmedia.py modules
~~~

## Logging and Verification

Logs are written to standard output by default. You can configure the log level and output file with environment variables:

~~~bash
export FFMEDIA_LOG_LEVEL=3
export FFMEDIA_LOG_OUTPUT=/tmp/ffmedia.log
./bin/ffmedia modules
~~~

Use `SDK_MANIFEST.txt` and `SHA256SUMS` to verify the ABI, shared libraries, CLI, and Python wheel in the current package.
