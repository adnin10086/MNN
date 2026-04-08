# MNN LLM Windows 编译指南

> 本指南介绍了如何在 Windows 环境下使用 **Clang-cl/Clang** 和 **Ninja** 编译 MNN 库及其命令行工具（`mnncli`）。

---

## 一、环境准备

### 1. 安装 Visual Studio 2026

下载并安装 **[VS Community 2026](https://c2rsetup.officeapps.live.com/c2r/downloadVS.aspx?sku=community&channel=Stable&version=VS18)**，安装时请勾选以下组件：

- ✅ **适用于x64/x86的MSVC生成工具**（最新版）
- ✅ **适用于 Windows 的 C++ CMake 工具**
- ✅ **对 LLVM (clang-cl) 工具集的 MSBuild 支持**
- ✅ **适用于 Windows 的 C++ clang 编译器**（推荐版本 `20.1.8`）
- ✅ **Windows 11 SDK** (`10.0.26100.7705`)

### 2. 安装 Ninja 编译系统

1. 从 [Ninja GitHub](https://github.com/ninja-build/ninja/releases) 下载可执行文件
2. 将其放置在：`D:\Tools\ninja.exe`
3. 将 `D:\Tools` 添加到系统环境变量 **PATH** 中

### 3. 配置 vcpkg 依赖管理

```
# 克隆仓库
git clone https://github.com/microsoft/vcpkg.git D:\Tools\vcpkg

# 初始化 vcpkg(需要下载海外软件包)
cd D:\Tools\vcpkg
.\bootstrap-vcpkg.bat

# 安装 OpenSSL 静态库
.\vcpkg install openssl:x64-windows-static
```

## 二、 编译 MNN 主库

### 1. 启动编译环境

打开 **x64 Native Tools Command Prompt for VS 2026**，确保以下命令可正常调用：

```
cmake --version
rc /?
clang-cl --version
```

### 2. 执行编译

进入 MNN 源码目录并创建编译目录：

```
cd /d D:\MNN
mkdir build
cd build
```

请根据 CPU 硬件支持情况选择对应的编译命令：

#### 方案 A：开启 AVX512 指令集（高性能）

```
cmake -G Ninja .. ^
    -DCMAKE_BUILD_TYPE=Release ^
    -DCMAKE_C_COMPILER=clang-cl ^
    -DCMAKE_CXX_COMPILER=clang-cl ^
    -DCMAKE_LINKER=lld-link.exe ^
    -DCMAKE_ASM_COMPILER=ml64 ^
    -DCMAKE_C_FLAGS="-mavx2 -mssse3 -msse4.1" ^
    -DCMAKE_CXX_FLAGS="-mavx2 -mssse3 -msse4.1" ^
    -DMNN_AVX512=ON ^
    -DMNN_BUILD_LLM=ON ^
    -DMNN_BUILD_LLM_OMNI=ON ^
    -DMNN_BUILD_OPENCV=ON ^
    -DMNN_IMGCODECS=ON ^
    -DMNN_BUILD_AUDIO=ON ^
    -DMNN_LOW_MEMORY=ON ^
    -DMNN_CPU_WEIGHT_DEQUANT_GEMM=ON ^
    -DMNN_SUPPORT_TRANSFORMER_FUSE=ON ^
    -DMNN_SEP_BUILD=OFF

ninja -j4
```

#### 方案 B：普通模式（关闭 AVX512）

```
cmake -G Ninja .. ^
    -DCMAKE_BUILD_TYPE=Release ^
    -DCMAKE_C_COMPILER=clang-cl ^
    -DCMAKE_CXX_COMPILER=clang-cl ^
    -DMNN_AVX512=OFF ^
    -DMNN_BUILD_LLM=ON ^
    -DMNN_BUILD_LLM_OMNI=ON ^
    -DMNN_BUILD_OPENCV=ON ^
    -DMNN_IMGCODECS=ON ^
    -DMNN_BUILD_AUDIO=ON ^
    -DMNN_LOW_MEMORY=ON ^
    -DMNN_CPU_WEIGHT_DEQUANT_GEMM=ON ^
    -DMNN_SUPPORT_TRANSFORMER_FUSE=ON ^
    -DMNN_SEP_BUILD=OFF

ninja -j4
```

## 三、 编译 mnncli 工具

`mnncli` 需要依赖上一步编译好的主库以及 vcpkg 安装的 OpenSSL 静态库。

```
cd /d D:\MNN\apps\mnncli

# 配置工程
cmake -B build_mnncli . ^
    -DCMAKE_BUILD_TYPE=Release ^
    -DCMAKE_C_COMPILER=clang-cl ^
    -DCMAKE_CXX_COMPILER=clang-cl ^
    -DCMAKE_LINKER=lld-link.exe ^
    -DMNN_BUILD_DIR="D:/MNN/build" ^
    -DMNN_SOURCE_DIR="D:/MNN" ^
    -DCMAKE_TOOLCHAIN_FILE="D:/Tools/vcpkg/scripts/buildsystems/vcpkg.cmake" ^
    -DVCPKG_TARGET_TRIPLET=x64-windows-static ^
    -DOPENSSL_USE_STATIC_LIBS=TRUE

# 执行编译
cmake --build build_mnncli --config Release -j 4
```

## 编译产物位置

```
D:\MNN\build
├── libmnn.lib          # MNN 主库（静态）
├── libmnn.dll          # MNN 主库（动态，如启用）
└── ...                 # 其他中间文件

D:\MNN\apps\mnncli\build_mnncli
└── Release
└── mnncli.exe      # 命令行工具可执行文件
```

## 注意事项

| 提示项      | 说明                                                                                     |
| ------------- | ------------------------------------------------------------------------------------------ |
| 🔹 路径符号 | 在 CMake 参数中建议使用**正斜杠`/`**或**双反斜杠`\\`**以避免转义问题 |
| 🔹 清理构建 | 如果编译报错，建议删除`build`或`build_mnncli`文件夹后重新执行 CMake 流程         |
| 🔹 并行编译 | `-j4`/`-j8`可根据 CPU 核心数调整，加速编译过程                                   |
| 🔹 环境变量 | 确保`ninja.exe`、`clang-cl`、`cmake`已加入 PATH，避免命令找不到              |
| 🔹 静态链接 | 使用`x64-windows-static` 可避免运行时依赖问题                                 |
