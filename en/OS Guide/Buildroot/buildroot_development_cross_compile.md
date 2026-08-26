# Buildroot Cross-compilation Tool

After Buildroot is compiled, it will generate a cross-compilation tool in the `output/rockchip_rk3568/host/` directory, which we can use to compile the target program.

* Cross-compilation tool directory

`output/rockchip_rk3568/host/bin/`

* Compile example hello.c

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
         printf("Hello World!\n");
         return 0;
}
```

* Compile

```bash
.../host/bin/arm-buildroot-linux-gnueabihf-gcc hello.c -o hello
```

* Run

Copy the executable program `hello` to the device, run `./hello`, you will see the printed message `Hello World!`.
