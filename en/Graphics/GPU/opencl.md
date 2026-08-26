# OpenCL

OpenCL is used for general-purpose GPU computing, including image processing, parallel computing, preprocessing, postprocessing, and some AI-related helper workloads. OpenCL availability depends on the chip GPU, driver, and userspace runtime.

Install and check OpenCL information:

```bash
sudo apt update
sudo apt install clinfo
clinfo
```

Key checks:

- Whether an OpenCL Platform is detected.
- Whether a GPU Device is detected.
- Whether `Device Type` includes `GPU`.
- Whether the OpenCL C version, maximum work-group size, and global memory size meet the application requirements.

If `clinfo` cannot find a platform or device, check:

- Whether the OpenCL userspace library is installed.
- Whether the ICD configuration exists and points to the correct driver library.
- Whether the current firmware includes OpenCL support for the GPU.
- Whether mismatched Mali DDK, Mesa, kernel driver, or root filesystem components are mixed together.
