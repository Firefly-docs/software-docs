# 接入方式

当前 SDK 提供预编译动态库、公共头文件、CMake 配置、`ffmedia` 命令行工具、C++ 示例和 Python wheel。

## 命令行工具

`ffmedia` 可通过模块、参数和连接关系组合媒体管线：

```bash
export LD_LIBRARY_PATH="$PWD/lib:$LD_LIBRARY_PATH"
./bin/ffmedia modules
./bin/ffmedia params mpp-dec
./bin/ffmedia run --help
```

## C++ 接入

推荐通过 CMake imported target 接入：

```cmake
find_package(FFMedia REQUIRED CONFIG COMPONENTS core)
target_link_libraries(my_app PRIVATE FFMedia::FFMedia)
```

代码中可统一包含：

```cpp
#include <ffmedia/ffmedia.hpp>
```

典型流程为创建模块、设置参数、初始化、调用 `connectProducer()` 连接上下游，然后启动和停止源模块。

## Python 接入

发布包提供适用于 AArch64 的 Python 3.10 和 Python 3.11 wheel：

```bash
python3 -m pip install python/ff_pymedia-2.6.0-cp310-cp310-linux_aarch64.whl
# Python 3.11 使用 cp311 wheel
```

## 示例编译

```bash
cmake -S . -B build
cmake --build build -j$(nproc)
```
