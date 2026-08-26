# Buildroot Compilation

After configuring Buildroot, run `make` directly to compile.

## Compilation instructions

When you run `make` to compile, the following process will be executed:

1. Download the source code;
2. Configure, compile and install the cross-compilation toolchain;
3. Configure, compile and install the selected software package;
4. Generate the root filesystem according to the selected format;

More usages of `make` can be obtained through `make help`.
