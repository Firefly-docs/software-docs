# Buildroot Package Compilation

We can execute `make <package>` to compile a package separately. The compilation of the software package mainly includes the process of downloading, decompressing, patching, configuring, compiling, and installing. For details, please refer to `package/pkg-generic.mk`.

* Download

  Buildroot will automatically obtain the corresponding software packages from the Internet according to the configuration `package/<package>/<package>.mk`, including some third-party libraries, plug-ins, utilities, etc., and place them in the `dl/` directory.

* Unzip

  The package will be decompressed in the `output/rockchip_rk3568/build/<package>-<version>` directory.

* Patch

  Patches are placed in the `package/<packgae>/` directory, and Buildroot will apply the corresponding patches after decompressing the package. If you want to modify the source code, you can modify it by patching.

* Configure
* Compile
* Install

  After the compilation is completed, the required compilation files will be copied to the `output/rockchip_rk3568/target/` directory.

For a certain package, we can call a certain step in the package construction through `make <package>-<target>`, as follows:

```bash
Package-specific:
  <pkg>                  - Build and install <pkg> and all its dependencies
  <pkg>-source           - Only download the source files for <pkg>
  <pkg>-extract          - Extract <pkg> sources
  <pkg>-patch            - Apply patches to <pkg>
  <pkg>-depends          - Build <pkg>'s dependencies
  <pkg>-configure        - Build <pkg> up to the configure step
  <pkg>-build            - Build <pkg> up to the build step
  <pkg>-graph-depends    - Generate a graph of <pkg>'s dependencies
  <pkg>-dirclean         - Remove <pkg> build directory
  <pkg>-reconfigure      - Restart the build from the configure step
  <pkg>-rebuild          - Restart the build from the build step
```
