# Buildroot 软件包编译

我们可以执行 `make <package>` 单独编译某个软件包。软件包的编译主要包括下载、解压、打补丁、配置、编译、安装等过程，具体可以查看 `package/pkg-generic.mk`。

* 下载

  Buildroot 会根据配置 `package/<package>/<package>.mk`，自动从网络获取对应的软件包，包括一些第三方库、插件、实用工具等，放在 `dl/` 目录。

* 解压

  软件包会解压在 `output/rockchip_rk3568/build/<package>-<version>` 目录下。

* 打补丁

  补丁集中放在 `package/<packgae>/` 目录，Buildroot 会在解压软件包后为其打上相应的补丁。如果要修改源码，可以通过打补丁的方式进行修改。

* 配置
* 编译
* 安装

  编译完成后，会将需要的编译生成文件拷贝到 `output/rockchip_rk3568/target/` 目录。

对于某个软件包，我们可以通过 `make <package>-<target>` 调用软件包构建中的某一步骤，如下：

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
