# Buildroot 交叉编译工具

Buildroot 编译完成后，会在 `output/rockchip_rk3568/host/` 目录下生成交叉编译工具，我们可以用来编译目标程序。

* 交叉编译工具目录

`output/rockchip_rk3568/host/bin/`

* 编译示例 hello.c

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
        printf("Hello World!\n");
        return 0;
}
```

* 编译

```bash
.../host/bin/arm-buildroot-linux-gnueabihf-gcc hello.c -o hello
```

* 运行

将可执行程序 `hello` 拷贝到设备，运行 `./hello`，则会看到打印信息 `Hello World!`。
