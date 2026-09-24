# FFMedia 下载及编译

## 获取 SDK

稳定部署建议从 [最新tags](https://github.com/Firefly-rk-linux-utils/ffmedia_release/tags) 下载发布包。发布包包含预编译 SDK、命令行工具、Python 绑定和示例工程。

需要查看源码或参与开发时，也可以获取仓库：

~~~bash
git clone --depth=1 https://github.com/Firefly-rk-linux-utils/ffmedia_release.git
cd ffmedia_release
~~~

主要目录如下：

| 路径 | 内容 |
| --- | --- |
| bin/ffmedia | 通用媒体管线命令行工具 |
| include/ffmedia/ | C++ 公共头文件 |
| lib/aarch64-linux-gnu/ | 动态库、CMake 配置和 pkg-config 文件 |
| python/ | Python 绑定 wheel |
| examples/demo/ | C++/Python Demo 和 CLI 源码 |
| examples/tests/ | CPU 测试和硬件手动测试 |
| examples/inference/ | RKNN 推理扩展 |

发布包根目录的 CMake 工程主要用于编译示例、测试和扩展；FFMedia 核心动态库由发布包
直接提供。

## 环境要求

基础环境：

- AArch64 Linux 目标机，或能够链接 AArch64 SDK 的交叉编译环境。
- CMake 3.10 及以上版本。
- 支持 C++17 的编译器和 pthread。
- 与 SDK 匹配的 libstdc++ ABI。

具体的编译器、GLIBC、GLIBCXX、CXXABI 和 Module ABI 要求，以 SDK_MANIFEST.txt、
ffmedia_abi.hpp 和当前 CMake 配置为准。

## 直接运行预编译工具

在发布包根目录设置动态库路径：

~~~bash
./bin/ffmedia --help
~~~

确认模块和参数：

~~~bash
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia params image-processor output
~~~

## 编译 Demo 和测试

基础构建：

~~~bash
cmake -S . -B build
cmake --build build -j$(nproc)
~~~

常用选项：

| 选项 | 作用 |
| --- | --- |
| DEMO_OPENCV | 编译 OpenCV Demo，需要 OpenCV 4 |
| ENABLE_TESTS | 编译 examples/tests |
| ENABLE_INFERENCE_EXAMPLES | 编译推理示例 |
| ENABLE_INFERENCE_EXTENSION | 编译独立 RKNN 推理扩展 |

例如编译 OpenCV Demo：

~~~bash
cmake -S . -B build -DDEMO_OPENCV=ON
cmake --build build -j$(nproc)
~~~

编译后可以运行 CLI：

~~~bash
./build/ffmedia modules
~~~

或运行简单demo：

~~~bash
./build/demo
~~~



## C++ 工程接入

推荐使用 SDK 导出的 CMake target：

~~~cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
~~~

如果 CMake 找不到 SDK，可以指定 CMake 配置目录：

~~~bash
cmake -S . -B build \
    -DFFMedia_DIR=/path/to/ffmedia_release/lib/aarch64-linux-gnu/cmake/FFMedia
~~~

代码中包含聚合头：

~~~cpp
#include <ffmedia/ffmedia.hpp>
~~~

头文件、CMake 配置和动态库必须来自同一个发布包，不能混用不同发布包中的文件。

## Python 接入

发布包提供与支持的 Python 解释器匹配的 AArch64 wheel。可以自动选择当前解释器对应的
wheel：

~~~bash
# 查看 Python 版本
python3 --version
# 安装与当前 Python 版本匹配的 wheel
ls python/ff_pymedia-*.whl
python3 -m pip install python/ff_pymedia-<匹配当前 Python 版本的 wheel>.whl
~~~

安装后运行 Python CLI：

~~~bash
python3 examples/demo/ffmedia.py modules
~~~

## 日志和校验

日志默认输出到标准输出，可以通过环境变量调整级别和输出文件：

~~~bash
export FFMEDIA_LOG_LEVEL=3
export FFMEDIA_LOG_OUTPUT=/tmp/ffmedia.log
./bin/ffmedia modules
~~~

SDK_MANIFEST.txt 和 SHA256SUMS 可用于核对当前发布包的 ABI、动态库、CLI 和 Python wheel。
