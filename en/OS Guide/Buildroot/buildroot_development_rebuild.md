# Buildroot Rebuild

For specific instructions on rebuilding, you can check the document `buildroot/docs/manual/rebuilding-packages.txt`.

## Rebuild the package

During the development process, if the source code of a certain package is modified, Buildroot will not recompile the package. It can be operated as follows:

* Method one

```bash
make <package>-rebuild
```

* Method two

```bash
# Delete the compiled output directory of the package
rm -rf output/rockchip_rk3568/build/<package>-<version>
# Compile
make <package>
```

## Full Rebuild

Buildroot does not attempt to detect what parts of the system should be rebuilt when the system configuration is changed through `make menuconfig`, `make xconfig`, or one of the other configuration tools. In some cases, Buildroot should rebuild the entire system, in some cases, only a specific subset of packages. But detecting this in a completely reliable manner is very difficult, and therefore the Buildroot developers have decided to simply not attempt to do this.

### When a full rebuild is necessary

* When the target architecture configuration is changed, a complete rebuild is needed;
* When the toolchain configuration is changed, a complete rebuild generally is needed;
* When an additional package is added to the configuration, a full rebuild is not necessarily needed;
* When a package is removed from the configuration, buildroot does not do anything special. it does not remove the files installed by this package from the target root filesystem or from the toolchain _sysroot_. a full rebuild is needed to get rid of this package;
* When the sub-options of a package are changed, the package is not automatically rebuilt;
* When a change to the root filesystem skeleton is made, a full rebuild is needed;

Generally speaking, when you're facing a build error and you're unsure of the potential consequences of the configuration changes you've made, do a full rebuild. Specific instructions can be found in the document `rebuilding-packages.txt`.

### Full rebuild

* Method one

Directly delete the compilation output directory, and then re-configure and compile.

```bash
rm -rf output/
```

* Method two

Executing the following command will delete the compilation output and recompile.

```bash
make clean all
```
