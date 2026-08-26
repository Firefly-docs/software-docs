# Buildroot 新增本地源码包

开发过程中，Buildroot 自带的软件包有时可能无法满足需求，为此需要添加自定义软件包。Buildroot 支持多种格式的软件包，包括 generic-package、cmake-package、autotools-package 等，下面以 generic-package 为例说明。

* 创建工程目录

```bash
cd path/to/Firefly_Linux_SDK/
mkdir buildroot/package/rockchip/firefly_demo/
```

* 新建 Config.in

在 `firefly_demo/` 下添加 Config.in：

```Kconfig
config BR2_PACKAGE_FIREFLY_DEMO
        bool "Simple Firefly Demo"
```

* 新建 firefly_demo.mk

在 `firefly_demo/` 下添加 firefly_demo.mk：

```makefile
##################################################
###########
#
### firefly_demo
#
##################################################
###########
ifeq ($(BR2_PACKAGE_FIREFLY_DEMO), y)

        FIREFLY_DEMO_VERSION:=1.0.0
        FIREFLY_DEMO_SITE=$(TOPDIR)/../external/firefly_demo/src
        FIREFLY_DEMO_SITE_METHOD=local

define FIREFLY_DEMO_BUILD_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) CC=$(TARGET_CC) CXX=$(TARGET_CXX) -C $(@D)
endef

define FIREFLY_DEMO_CLEAN_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) -C $(@D) clean
endef

define FIREFLY_DEMO_INSTALL_TARGET_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) -C $(@D) install
endef

define FIREFLY_DEMO_UNINSTALL_TARGET_CMDS
        $(TARGET_MAKE_ENV) $(MAKE) -C $(@D) uninstall
endef

$(eval $(generic-package))
endif
```

* 创建源码目录

上文的 Makefile 文件里已经指定了源码目录 `external/firefly_demo/src`。

```bash
cd path/to/Firefly_Linux_SDK/
mkdir external/firefly_demo/src
```

* 编写源码 firefly_demo.c

在 `firefly_demo/src/` 下添加 firefly_demo.c：

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
        printf("Hello World!\n");
        return 0;
}
```

* 编写 Makefile

在 `firefly_demo/src/` 下添加 Makefile：

```makefile
DEPS =
OBJ = firefly_demo.o
CFLAGS =
%.o: %.c $(DEPS)
        $(CC) -c -o $@ $< $(CFLAGS)

firefly_demo: $(OBJ)
        $(CXX) -o $@ $^ $(CFLAGS)

.PHONY: clean
clean:
        rm -f *.o *~ firefly_demo

.PHONY: install
install:
        cp -f firefly_demo $(TARGET_DIR)/usr/bin/

.PHONY: uninstall
uninstall:
        rm -f $(TARGET_DIR)/usr/bin/firefly_demo
```

* 修改上一级 Config.in

在 `buildroot/package/rockchip/Config.in` 末尾添加一行：

```Kconfig
source "package/rockchip/firefly_demo/Config.in"
```

* 配置软件包

打开配置菜单 `make menuconfig`，找到 firefly_demo 并选中配置。

* 编译

```bash
# 编译 firefly_demo
make firefly_demo
# 打包进根文件系统
make
# 若修改源码，重新编译软件包
make firefly_demo-rebuild
```
