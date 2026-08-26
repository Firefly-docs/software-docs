# Buildroot Add Local Source Package

During the development process, the built-in software package of Buildroot may not meet our needs sometimes, so we need to add a custom software package. Buildroot supports packages in a variety of formats, including generic-package, cmake-package, autotools-package, etc. We take generic-package as an example.

* Create a project directory

```bash
cd path/to/Firefly_Linux_SDK/
mkdir buildroot/package/rockchip/firefly_demo/
```

* Create Config.in

Add Config.in under `firefly_demo/`:

```Kconfig
config BR2_PACKAGE_FIREFLY_DEMO
        bool "Simple Firefly Demo"
```

* Create firefly_demo.mk

Add firefly_demo.mk under `firefly_demo/`:

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

* Create source code directory

The source code directory `external/firefly_demo/src` has been specified in the above Makefile.

```bash
cd path/to/Firefly_Linux_SDK/
mkdir external/firefly_demo/src
```

* Write source code firefly_demo.c

Add firefly_demo.c under `firefly_demo/src/`:

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
        printf("Hello World!\n");
        return 0;
}
```

* Write Makefile

Add Makefile under `firefly_demo/src/`:

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

* Modify the upper Config.in

Add a line at the end of `buildroot/package/rockchip/Config.in`:

```Kconfig
source "package/rockchip/firefly_demo/Config.in"
```

* Select package

Open the configuration menu `make menuconfig`, find firefly_demo and select the configuration.

* Compile

```bash
# Compile firefly_demo
make firefly_demo
# Package into the root filesystem
make
# If you modify the source code, recompile the package
make firefly_demo-rebuild
```
