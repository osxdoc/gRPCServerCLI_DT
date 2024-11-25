Here’s a revised and fully separated version for macOS and Linux, formatted for direct copy-pasting into GitHub:

---

# Setting Up `gRPCServerCLI` for the Draw Things App

This guide provides step-by-step instructions to set up and run `gRPCServerCLI` for the [Draw Things](https://github.com/drawthingsai/draw-things-community) app. Separate instructions are provided for macOS and Linux systems.

---

## Table of Contents

1. [Setting Up on macOS](#setting-up-on-macos)
   - [Installation Steps (macOS)](#installation-steps-macos)
   - [Running gRPCServerCLI on macOS](#running-grpcservercli-on-macos)
2. [Setting Up on Linux](#setting-up-on-linux)
   - [Installation Steps (Linux)](#installation-steps-linux)
   - [Running gRPCServerCLI on Linux](#running-grpcservercli-on-linux)
3. [Troubleshooting Tips](#troubleshooting-tips)
4. [Additional Notes](#additional-notes)

---

## 1. Setting Up on macOS

### Installation Steps (macOS)

1. **Clone the Repository**  
   Open a terminal and clone the repository:
   ```bash
   git clone https://github.com/drawthingsai/draw-things-community.git
   cd draw-things-community
   ```

2. **Install Bazelisk**  
   Download and make Bazelisk executable:
   ```bash
   curl -Lo bazel https://github.com/bazelbuild/bazelisk/releases/download/v1.23.0/bazelisk-darwin-arm64
   chmod +x bazel
   ```

3. **Set Up the Repository**  
   Run the provided installation script:
   ```bash
   ./Scripts/install.sh
   ```

4. **Specify Bazel Version**  
   Set the required Bazel version:
   ```bash
   echo 7.3.1 > .bazelversion
   ```

---

### Running gRPCServerCLI on macOS

1. **Compile and Run with Bazel**  
   Use Bazel to compile and run the app:
   ```bash
   ./bazel run Apps:gRPCServerCLI --compilation_mode=opt --keep_going -- ~/Library/Containers/com.liuliu.draw-things/Data/Documents/Models
   ```

2. **View Available Options**  
   To see all available options, use:
   ```bash
   ./bazel run Apps:gRPCServerCLI -- -h
   ```

3. **Copy the Compiled Binary**  
   After compiling, copy the binary to your desired location:
   ```bash
   cp draw-things-community/bazel-bin/Apps/gRPCServerCLI /your/desired/path
   ```

4. **Run the Application**  
   Navigate to the location where you copied the binary and execute it:
   ```bash
   ./gRPCServerCLI ~/Library/Containers/com.liuliu.draw-things/Data/Documents/Models
   ```

---

## 2. Setting Up on Linux

### Installation Steps (Linux)

1. **Install System Dependencies**  
   Update your package manager and install the required dependencies:
   ```bash
   sudo apt update
   sudo apt install -y git libpng-dev libjpeg-dev libatlas-base-dev libblas-dev libgsl-dev clang libomp-dev llvm
   sudo ln -s /usr/bin/llvm-config /usr/local/bin/llvm-config 
   ```

2. **Clone the Repository**  
   Clone the repository and navigate into it:
   ```bash
   git clone https://github.com/drawthingsai/draw-things-community.git
   cd draw-things-community
   ```

3. **Install CUDA Toolkit**  
   Install CUDA Toolkit 12.6 (ensure compatibility with your system):
   (???"sudo apt install nvidia-cuda-toolkit")

   ```bash
   wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-ubuntu2404.pin
   sudo mv cuda-ubuntu2404.pin /etc/apt/preferences.d/cuda-repository-pin-600
   sudo apt update
   sudo apt -y install cuda-toolkit-12-6
   ```

5. **Install cuDNN**  
   Install cuDNN (ensure compatibility with your CUDA version):
   ```bash
   wget https://developer.download.nvidia.com/compute/cudnn/9.5.1/local_installers/cudnn-local-repo-ubuntu2404-9.5.1_1.0-1_amd64.deb
   sudo dpkg -i cudnn-local-repo-ubuntu2404-9.5.1_1.0-1_amd64.deb
   sudo apt update
   sudo apt -y install cudnn
   ```

6. **Install Swift**  
   Download and install Swift:
   ```bash
   wget https://download.swift.org/swift-6.0.2-release/ubuntu2004/swift-6.0.2-RELEASE/swift-6.0.2-RELEASE-ubuntu20.04.tar.gz
   tar xzf swift-6.0.2-RELEASE-ubuntu20.04.tar.gz
   sudo mv swift-6.0.2-RELEASE-ubuntu20.04 /usr/share/swift
   echo 'export PATH=/usr/share/swift/usr/bin:$PATH' >> ~/.bashrc
   source ~/.bashrc
   ```

7. **Install Bazelisk**  
   Download and set up Bazelisk:
   ```bash
   wget https://github.com/bazelbuild/bazelisk/releases/download/v1.23.0/bazelisk-linux-amd64
   chmod +x bazelisk-linux-amd64
   mv bazelisk-linux-amd64 bazel
   ```

8. **Set Up the Repository**  
   Run the setup script:
   ```bash
   ./Scripts/install.sh
   ```

9. **Specify Bazel Version**  
   Set the required Bazel version:
   ```bash
   echo 6.4.0 > .bazelversion
   ```

10. **Configure Clang**  
   Generate the `clang.bazelrc` file:
   ```bash
   ./Scripts/setup_clang.sh
   ```

11. **Configure .bazelrc.local**
    ```bash
    nano .bazelrc.local

    build --action_env TF_NEED_CUDA="1"
    build --action_env TF_NEED_OPENCL="1"
    build --action_env TF_CUDA_CLANG="0"
    build --action_env HOST_CXX_COMPILER="/usr/local/bin/clang"
    build --action_env HOST_C_COMPILER="/usr/local/bin/clang"
    build --action_env CLANG_CUDA_COMPILER_PATH="/usr/local/bin/clang"
    build --action_env GCC_HOST_COMPILER_PATH="/usr/local/bin/clang"

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
---

### Running gRPCServerCLI on Linux

1. **Compile and Run with Bazel**  
   Use Bazel to compile and run the app:
   ```bash
   ./bazel run Apps:gRPCServerCLI --compilation_mode=opt -- ~/Library/Containers/com.liuliu.draw-things/Data/Documents/Models
   ```

2. **View Available Options**  
   To see all available options, use:
   ```bash
   ./bazel run Apps:gRPCServerCLI -- -h
   ```

3. **Copy the Compiled Binary**  
   After compiling, copy the binary to your desired location:
   ```bash
   cp draw-things-community/bazel-bin/Apps/gRPCServerCLI /your/desired/path
   ```

4. **Run the Application**  
   Navigate to the location where you copied the binary and execute it:
   ```bash
   ./gRPCServerCLI ~/Library/Containers/com.liuliu.draw-things/Data/Documents/Models
   ```

---

## 3. Troubleshooting Tips

- **CUDA Compatibility**: Ensure your CUDA version matches your GPU.
- **Environment Variables**: Double-check paths and versions in `.bazelrc.local`.
- **Compilation Errors**: Retry with the `--keep_going` flag if encountering `nvcc` issues.

---

## 4. Additional Notes

- For a release build, use:
  ```bash
  ./bazel build Apps:gRPCServerCLI --compilation_mode=opt
  ```

---

By following these instructions, you should be able to set up and run `gRPCServerCLI` for the Draw Things app on macOS or Linux. For additional assistance, please consult the community or open an issue on the [GitHub repository](https://github.com/drawthingsai/draw-things-community).

--- 
