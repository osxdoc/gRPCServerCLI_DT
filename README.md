# Setting Up `gRPCServerCLI` for Draw Things App

This guide provides step-by-step instructions to set up and run `gRPCServerCLI` for the [Draw Things](https://github.com/drawthingsai/draw-things-community) app on both macOS and Linux systems.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Setting Up on macOS](#setting-up-on-macos)
  - [Installation Steps](#installation-steps-macos)
- [Setting Up on Linux](#setting-up-on-linux)
  - [Installation Steps](#installation-steps-linux)
- [Additional Notes](#additional-notes)
- [Troubleshooting Tips](#troubleshooting-tips)

---

## Prerequisites

### Common Requirements

- **Git**: Ensure Git is installed.
  ```bash
  git --version
  ```
- **Bazelisk**: A launcher for Bazel.

### macOS Specific

- **Xcode**: Install the latest version from the [Mac App Store](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

### Linux Specific

- **NVIDIA GPU**: Compatible with `sm80` architecture (e.g., RTX 30XX, 40XX series). Check compatibility [here](https://arnon.dk/matching-sm-architectures-arch-and-gencode-for-various-nvidia-cards/).
- **CUDA Toolkit**: Version 12.4 or compatible. Download from [NVIDIA CUDA Downloads](https://developer.nvidia.com/cuda-downloads).
- **cuDNN**: Version 8.9.7 or compatible. Download from [NVIDIA cuDNN Downloads](https://developer.nvidia.com/cudnn).
- **Clang**: Required for compilation.

---

## Setting Up on macOS

### Installation Steps (macOS)

1. **Clone the Repository**

   ```bash
   git clone https://github.com/drawthingsai/draw-things-community.git
   cd draw-things-community
   ```

2. **Install Bazelisk**

   Download Bazelisk and place it in `/usr/local/bin/`:

   ```bash
   curl -Lo /usr/local/bin/bazel https://github.com/bazelbuild/bazelisk/releases/download/v1.23.0/bazelisk-darwin-amd64
   chmod +x /usr/local/bin/bazel
   ```

3. **Set Up the Repository**

   ```bash
   ./Scripts/install.sh
   ```

4. **Specify Bazel Version**

   ```bash
   echo 7.3.1 > .bazelversion
   ```

5. **Run `gRPCServerCLI`**

   Replace `[username]` with your macOS username:

   ```bash
   bazel run Apps:gRPCServerCLI -- /Users/[username]/Library/Containers/com.liuliu.draw-things/Data/Documents/Models
   ```

   **Note**: To view available options, run:

   ```bash
   bazel run Apps:gRPCServerCLI -- -h
   ```

---

## Setting Up on Linux

### Installation Steps (Linux)

1. **Clone the Repository**

   ```bash
   git clone https://github.com/drawthingsai/draw-things-community.git
   cd draw-things-community
   ```

2. **Install System Dependencies**

   ```bash
   sudo apt update
   sudo apt install -y libpng-dev libjpeg-dev libatlas-base-dev libblas-dev libgsl-dev clang libomp-dev llvm
   ```

3. **Install CUDA Toolkit**

   Download and install from [NVIDIA CUDA Downloads](https://developer.nvidia.com/cuda-downloads).
   ```bash
   wget https://developer.download.nvidia.com/compute/cuda/12.6.2/local_installers/cuda_12.6.2_560.35.03_linux.run
   sudo sh cuda_12.6.2_560.35.03_linux.run
   ```

5. **Install cuDNN**

   Download and install from [NVIDIA cuDNN Downloads](https://developer.nvidia.com/cudnn).

6. **Install Swift**

   Download and install from https://www.swift.org/download/

7. **Install Bazelisk**

   Download Bazelisk and place it in `~/draw-things-community`:

   ```bash
   wget https://github.com/bazelbuild/bazelisk/releases/download/v1.23.0/bazelisk-linux-amd64 
   chmod +x bazelisk-linux-amd64
   mv bazelisk-linux-amd64 bazel
   ```

8. **Set Up the Repository**

   ```bash
   ./Scripts/install.sh
   ```

9. **Specify Bazel Version**
   ```bash
   echo 6.3.2 > .bazelversion
   ```
10. **Configure Clang**

   ```bash
   ./Scripts/setup_clang.sh /usr
   ```

   This command generates a `clang.bazelrc` file.

11. **Create `.bazelrc.local` File**

   In the root directory of the repository, create and edit the `.bazelrc.local` file:

   ```bash
   nano .bazelrc.local
   ```

   **Content** (Update CUDA and cuDNN versions if necessary):

   ```bash
   build --action_env TF_NEED_CUDA="1"
   build --action_env TF_NEED_OPENCL="1"
   build --action_env TF_CUDA_CLANG="0"
   build --action_env HOST_CXX_COMPILER="/usr/bin/clang"
   build --action_env HOST_C_COMPILER="/usr/bin/clang"
   build --action_env CLANG_CUDA_COMPILER_PATH="/usr/bin/clang"
   build --action_env GCC_HOST_COMPILER_PATH="/usr/bin/clang"

   build --action_env CUDA_TOOLKIT_PATH="/usr/local/cuda"
   build --action_env TF_CUDA_VERSION="12.4"
   build --action_env TF_CUDA_COMPUTE_CAPABILITIES="8.0"
   build --action_env COMPUTECPP_TOOLKIT_PATH="/usr/local/computecpp"
   build --action_env TMP="/tmp"
   build --action_env TF_CUDNN_VERSION="8"
   build --action_env CUDNN_INSTALL_PATH="/usr"
   build --action_env TF_NCCL_VERSION="2"
   build --action_env NCCL_INSTALL_PATH="/usr"

   build --config=clang
   build --config=cuda

   build --linkopt="-z nostart-stop-gc"
   build --host_linkopt="-z nostart-stop-gc"

   build --define=enable_sm80=true
   ```

11. **Build and Run `gRPCServerCLI`**

    ```bash
    ./bazel run Apps:gRPCServerCLI --keep_going --compilation_mode=opt
    ```

    **Note**: The `--keep_going` flag allows the build to continue despite errors. If you encounter nvcc compilation issues with flash attention, retrying a few times may resolve them.

---

## Additional Notes

- **Optimized Build**: For a release build, use the `--compilation_mode=opt` flag.

  ```bash
  ./bazel build Apps:gRPCServerCLI --compilation_mode=opt
  ```

- **Standalone Binary**: After building, you can copy the binary to any location and run it independently without additional dependencies.

---

## Troubleshooting Tips

- **CUDA Compatibility**: Ensure the CUDA version matches your GPU. The compute capability (`TF_CUDA_COMPUTE_CAPABILITIES`) should correspond to your GPU's architecture.

- **Environment Variables**: Double-check the paths and versions in `.bazelrc.local` to match your system's configuration.

- **Permissions**: If you encounter permission issues, you may need to run commands with `sudo` or adjust file permissions accordingly.

- **Compilation Errors**: If you face compilation errors related to `nvcc` or flash attention:

  - Ensure all dependencies are correctly installed.
  - Retry the build command with the `--keep_going` flag.
  - Verify that the CUDA and cuDNN versions are compatible.

---

By following these instructions, you should be able to set up and run `gRPCServerCLI` for the Draw Things app on your macOS or Linux system. If you encounter any issues, please refer to the [Troubleshooting Tips](#troubleshooting-tips) section or consult the community for support.

---

**Contributing**

If you have suggestions or improvements for this guide, feel free to submit a pull request or open an issue on the [GitHub repository](https://github.com/drawthingsai/draw-things-community).
