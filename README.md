# AOCL Build-It-Yourself

[![Build and Release AOCL Libraries](https://github.com/aelobdog/AOCL_BIY/actions/workflows/create_build_artifacts.yml/badge.svg)](https://github.com/aelobdog/AOCL_BIY/actions/workflows/create_build_artifacts.yml)

AOCL now offers the capability to compile individual libraries and
consolidate them into a unified binary. With the Build-It-Yourself
feature, you can choose one or more AOCL libraries and merge them into a
single library by configuring the appropriate CMake options. This
unified binary is assigned a default name: `libaocl.so`/ `libaocl.a` for
Linux or `aocl.dll`/ `aocl.lib` for Windows. This approach simplifies
integration by eliminating dependencies on library linking order and
preventing API duplication, ensuring smooth and efficient incorporation
of multiple AOCL libraries.

**Note**

Currently, Build-It-Yourself supports selection of AOCL-BLAS,
AOCL-Utils, AOCL-LAPACK, AOCL-Sparse, AOCL-LibM, AOCL-Compression, and
AOCL-Cryptography libraries only.

## Table of Contents

- [AOCL Build-It-Yourself](#aocl-build-it-yourself)
  - [Table of Contents](#table-of-contents)
  - [Project structure](#project-structure)
  - [Configure Build-It-Yourself](#configure-build-it-yourself)
    - [Linux Prerequisites](#linux-prerequisites)
    - [Windows Prerequisites](#windows-prerequisites)
    - [Clone the Repository](#clone-the-repository)
    - [Configure the Build Options](#configure-the-build-options)
    - [Build the Unified Binary](#build-the-unified-binary)
  - [Examples of Configuration and Build Commands using CMake Presets](#examples-of-configuration-and-build-commands-using-cmake-presets)
    - [Introduction](#introduction)
    - [On Linux](#on-linux)
      - [Single-Thread AOCL](#single-thread-aocl)
      - [Multi-Thread AOCL](#multi-thread-aocl)
    - [On Windows](#on-windows)
  - [Verifying AOCL Installation](#verifying-aocl-installation)
  - [CMake Variables Reference](#cmake-variables-reference)
    - [CMake Options to Select Libraries](#cmake-options-to-select-libraries)
    - [CMake Options to Set Library Source Path](#cMake-options-to-set-library-source-path)
    - [CMake Options to Set GIT Repository and Tag/Branch](#cmake-options-to-set-git-repository-and-tagbranch)

## Project Structure

The project is structured as follows:

- `aocl_blis_build.cmake`: CMake script for building AOCL-BLAS.
- `aocl_compression_build.cmake`: CMake script for building AOCL-COMPRESSION.
- `aocl_crypto_build.cmake`: CMake script for building AOCL-CRYPTO.
- `aocl_libflame_build.cmake`: CMake script for building AOCL-LAPACK.
- `aocl_libm_build.cmake`: CMake script for building AOCL-LIBM.
- `aocl_sparse_build.cmake`: CMake script for building AOCL-SPARSE.
- `aocl_utils_build.cmake`: CMake script for building AOCL-UTILS.
- `CMakeLists.txt`: Main CMake script for the AOCL project.
- `CMakePresets.json`: CMake presets for different build configurations.
- `README.md`: This README file.
- `presets/`: Directory containing preset configurations for different platforms.

## Configure Build-It-Yourself

This section explains how to configure the CMake options and the
procedure to build the unified AOCL binary on both Linux and Windows
platforms. Note that the procedure to configure is the same for both OSs
but the only difference is in the prerequisites for each OS.

The following sub-sections describe the process:

1.  Meeting the prerequisites
2.  Cloning the repository
3.  Configuring the build options
4.  Building the unified binary

### Linux Prerequisites

The following dependencies must be met for installing AOCL on Linux:

-   Target CPU with support for FMA, AVX2 or higher

-   Git

-   Python

-   CMake

-   GCC, g++, and Gfortran

-   AOCC

-   OpenSSL for AOCL-Cryptography:

    -   Define the environment variable `OPENSSL_INSTALL_DIR` to point
        to OpenSSL installation:

    ``` bash
    $ export OPENSSL_INSTALL_DIR=/home/user/openssl
    ```

**Note**

To build the AOCL-Cryptography library, the `libcrypto.so` and
`libssl.so` libraries are required. Set the `OPENSSL_INSTALL_DIR`
environment variable to the path where OpenSSL is installed. Ensure this
directory includes the `include` folder and either `lib` or `lib64`.
Within the `lib` or `lib64` folder, verify that the `libcrypto.so` and
`libssl.so` libraries are present.

### Windows Prerequisites

The following dependencies must be met for building AOCL on Windows:

-   Target CPU with support for FMA, AVX2 or higher

-   LLVM

-   CMake

-   Microsoft Visual Studio IDE

-   Microsoft Visual Studio tools:

    -   Python development
    -   Desktop development with C++: C++ Clang-Cl for v142 build
        tool(x64/x86)

-   OpenSSL for AOCL-Cryptography:

    -   Define the environment variable `OPENSSL_INSTALL_DIR` to point
        to OpenSSL installation:

    ``` console
    $ set OPENSSL_INSTALL_DIR=C:/Program Files/OpenSSL-Win64
    ```

**Note**

To build the AOCL-Cryptography library, the `libcrypto.lib` and
`libssl.lib` libraries are required. Set the `OPENSSL_INSTALL_DIR`
environment variable to the directory where OpenSSL is installed. Make
sure this directory includes the `include` and `lib` folders. Within the
`lib` folder, ensure that the `libcrypto.lib` and `libssl.lib` libraries
are present.

For more information on validated versions of compiler/LLVM, CMake and
Python, and OpenSSL libraries refer to `Validation Matrix` chapter in 
AOCL userguide document.

To set up and use Build-It-Yourself, you must clone the repository,
configure the build options, and build the unified binary.

### Clone the Repository

First, clone the AOCL repository from GitHub:

``` console
$ git clone https://github.com/amd/aocl.git
$ cd aocl
```

### Configure the Build Options

There are multiple CMake options you can configure. The following
sections explain the CMake options to:

1.  Include or exclude individual AOCL libraries (see
    [CMake Options to Select Libraries](#cmake-options-to-select-libraries)).
2.  Provide the source code for the selected libraries by using one of
    the following options:
    1.  Setting the path of the AOCL libraries source code (see
        [CMake Options to Set Library Source Path](#cMake-options-to-set-library-source-path))
    2.  Setting the GIT repository and tag or branch name (see
        [CMake Options to Set GIT Repository and Tag/Branch](#cmake-options-to-set-git-repository-and-tagbranch))
3.  Static or Shared Library:
    1.  Static Library `-DBUILD_SHARED_LIBS=OFF`
    2.  Shared Library `-DBUILD_SHARED_LIBS=ON` (default)
4.  Select Data Type (LP64 or ILP64):
    1.  LP64 `-DENABLE_ILP64=OFF` (default)
    2.  ILP64 `-DENABLE_ILP64=ON`
5.  Enable or disable threading:
    1.  Multithreading `-DENABLE_MULTITHREADING=ON`
    2.  Single threading `-DENABLE_MULTITHREADING=OFF` (default)
6.  Link Desired OpenMP library using
    `-DOpenMP_libomp_LIBRARY=<path to OpenMP library>` when,
    `-DENABLE_MULTITHREADING=ON`.

Here is an example of a configuration command:

**Linux**

``` console
$ cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=OFF
-DENABLE_ILP64=OFF -DENABLE_AOCL_BLAS=ON -DENABLE_AOCL_UTILS=ON 
-DENABLE_AOCL_LAPACK=ON -DENABLE_MULTITHREADING=ON -DOpenMP_libomp_LIBRARY=""
-DCMAKE_INSTALL_PREFIX=$PWD/install_package
```

**Windows**

``` console
$ cmake -S . -B build -G "Visual Studio 17 2022" -DCMAKE_BUILD_TYPE=Release
-DBUILD_SHARED_LIBS=OFF -DENABLE_ILP64=OFF -DENABLE_AOCL_BLAS=ON 
-DENABLE_AOCL_UTILS=ON -DENABLE_AOCL_LAPACK=ON -DENABLE_MULTITHREADING=ON -TClangCl
-DCMAKE_INSTALL_PREFIX=%CD%/install_package -DCMAKE_CONFIGURATION_TYPES=Release
```

### Build the Unified Binary

Use the following command to build the unified binary:

``` console
$ cmake --build build --config release --target install
```

## Examples of Configuration and Build Commands using CMake Presets

### Introduction

The AOCL project provides a set of CMake presets to simplify the
configuration and build process for different platforms and compilers.
Some of these presets are:

1.   **aocl-linux-make-lp-ga-gcc-config**: Linux with GNU Make and GCC
2.   **aocl-linux-make-ilp-ga-gcc-config**: Linux with GNU Make and GCC
    (ILP64)
3.   **aocl-linux-make-lp-ga-aocc-config**: Linux with GNU Make and AOCC
4.   **aocl-linux-make-ilp-ga-aocc-config**: Linux with GNU Make and AOCC
    (ILP64)
5.   **aocl-win-msvc-lp-ga-config**: Windows with Visual Studio and
    Clang/LLVM
6.   **aocl-win-msvc-ilp-ga-config**: Windows with Visual Studio and
    Clang/LLVM (ILP64)
7.   **aocl-win-ninja-lp-ga-config**: Windows with Ninja and Clang/LLVM
8.   **aocl-win-ninja-ilp-ga-config**: Windows with Ninja and Clang/LLVM
    (ILP64)

Details of **aocl-linux-make-lp-ga-gcc-config** are given here.

The `aocl-linux-make-lp-ga-gcc-config` preset is a convenient and
efficient way to build AOCL on Linux platforms with GCC. It provides a
stable, production-ready configuration while allowing flexibility for
customization based on specific requirements.

-   How is the Name Derived?

    -   **aocl**: Refers to AMD Optimized Libraries.
    -   **linux**: Indicates that the preset is for Linux platforms.
    -   **make**: Specifies the use of the GNU Make build system.
    -   **lp**: Refers to the LP64 data model, which is the default for
        most Linux systems.
    -   **ga**: Stands for General Availability Release, indicating that
        this preset is stable and production-ready.
    -   **gcc**: Specifies the use of the GCC compiler.

-   Default Configuration: By default, this preset builds a shared
    multithreaded library. It can be customized to build static or
    single-threaded libraries by modifying the relevant CMake variables.

    -   **CMAKE_BUILD_TYPE**: `Release` Specifies that the build should
        be optimized for performance.
    -   **BUILD_SHARED_LIBS**: `ON` Indicates that shared libraries are
        built by default.
    -   **ENABLE_ILP64**: `OFF` Configures the build to use the LP64
        data model.
    -   **ENABLE_MULTITHREADING**: `ON` Enables multithreading support
        by default.
    -   **CMAKE_C_COMPILER**: `gcc` Specifies GCC as the C compiler.
    -   **CMAKE_CXX_COMPILER**: `g++` Specifies G++ as the C++ compiler.
    -   **CMAKE_Fortran_COMPILER**: `gfortran` Specifies GFortran as the
        Fortran compiler.

-   Required Environment Variables: Before running this preset, ensure
    the following environment variables are set:

    1.  **OPENSSL_INSTALL_DIR**: Points to the directory where OpenSSL
        is installed. This is required for building the
        AOCL-Cryptography library. Ensure this directory contains the
        `include` folder and either the `lib` or `lib64` folder with
        `libcrypto.so` and `libssl.so` libraries.

    2.  **ONEAPI_ROOT / oneAPI_ROOT**: Specifies the root directory of
        the Intel oneAPI toolkit. Use **ONEAPI_ROOT** for Linux and
        **oneAPI_ROOT** for Windows. This is **mandatory for Windows**
        and **optional for Linux**. It is required if you want to use
        the Intel OpenMP runtime library.

        -   **Linux**: The library is typically located at:
            `$env{ONEAPI_ROOT}/compiler/latest/lib/libiomp5.so`.
        -   **Windows**: The library is typically located at:
            `$env{oneAPI_ROOT}/compiler/latest/lib/libiomp5md.lib`.

        Ensure that the appropriate environment variable (`ONEAPI_ROOT`
        or `oneAPI_ROOT`) is set to the correct path where the oneAPI
        toolkit is installed.

-   Example Command to Use This Preset: To configure the build using
    this preset, run the following command:

    ``` bash
    $ cmake --preset aocl-linux-make-lp-ga-gcc-config --fresh
    ```

    This command will set up the build environment with the predefined
    configuration for this preset.

-   Customization Options: You can customize the build by overriding the
    default CMake variables. For example:

    -   To build a static library, set `-DBUILD_SHARED_LIBS=OFF`.
    -   To disable multithreading, set `-DENABLE_MULTITHREADING=OFF`.

    For more customization options, refer to the CMake variables
    `configure-the-build-options`{.interpreted-text role="ref"}.

### On Linux

The following sections provide examples of configuration and build
commands on Linux using the CMake build system.

**Note**

Use the [-DOpenMP_libomp_LIBRARY]{.title-ref} option to link the desired
OpenMP library.

#### Single-Thread AOCL

Complete the following steps to build and install a single-thread AOCL:

1.  Clone the AOCL from Git repository
    (<https://github.com/amd/aocl.git>).

    ``` bash
    $ git clone https://github.com/amd/aocl.git
    $ cd aocl
    ```

2.  Configure the library as required:

    ``` bash
    # CMake commands

    # GCC (Default) and LP64 
    $ cmake --preset aocl-linux-make-lp-ga-gcc-config -DENABLE_MULTITHREADING=OFF --fresh 

    # GCC and ILP64
    $ cmake --preset aocl-linux-make-ilp-ga-gcc-config -DENABLE_MULTITHREADING=OFF --fresh 

    # AOCC and LP64 
    $ cmake --preset aocl-linux-make-lp-ga-aocc-config -DENABLE_MULTITHREADING=OFF --fresh 

    # AOCC and ILP64
    $ cmake --preset aocl-linux-make-ilp-ga-aocc-config -DENABLE_MULTITHREADING=OFF --fresh 
    ```

3.  Build the unified binary and install using the command:

    ``` bash
    $ cmake --build build --config release -j --target install
    ```

#### Multi-Thread AOCL

Complete the following steps to install a multi-thread AOCL:

1.  Clone the AOCL from Git repository
    (<https://github.com/amd/aocl.git>).

    ``` bash
    $ git clone https://github.com/amd/aocl.git
    $ cd aocl
    ```

2.  Configure the library as required:

    ``` bash
    # CMake commands

    # GCC (Default) and LP64 
    $ cmake --preset aocl-linux-make-lp-ga-gcc-config --fresh 

    # GCC and ILP64
    $ cmake --preset aocl-linux-make-ilp-ga-gcc-config --fresh 

    # AOCC and LP64 
    $ cmake --preset aocl-linux-make-lp-ga-aocc-config --fresh 

    # AOCC and ILP64
    $ cmake --preset aocl-linux-make-ilp-ga-aocc-config --fresh 

    # GCC (Default) and LP64 with Desired OpenMP library Path
    $ cmake --preset aocl-linux-make-lp-ga-gcc-config --fresh -DOpenMP_libomp_LIBRARY=<path to OpenMP library>
    ```

3.  Build the unified binary and install using the command:

    ``` bash
    $ cmake --build build --config Release -j --target install
    ```

### On Windows

**Configure the Project in Command Prompt**

``` console
# CMake commands using Visual Studio 17 2022 Generator
"C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\VC\\Auxiliary\\Build\\vcvarsall.bat"  x64

# Clang/LLVM (Default) and LP64 
$ cmake --preset aocl-win-msvc-lp-ga-config --fresh 

# Clang/LLVM and ILP64
$ cmake --preset aocl-win-msvc-ilp-ga-config --fresh 

# Clang/LLVM (Default) and LP64 with Desired OpenMP library Path
$ cmake --preset aocl-win-msvc-lp-ga-config --fresh -DOpenMP_libomp_LIBRARY=<path to OpenMP library>
```

``` console
# CMake commands using Ninja Generator

$ "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\VC\\Auxiliary\\Build\\vcvarsall.bat"  x64
$ set PATH="C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\Common7\\IDE\\CommonExtensions\\Microsoft\\CMake\\Ninja";%PATH%

# Clang/LLVM (Default) and LP64 
$ cmake --preset aocl-win-ninja-lp-ga-config --fresh 

# Clang/LLVM and ILP64
$ cmake --preset aocl-win-ninja-ilp-ga-config --fresh 

# Clang/LLVM (Default) and LP64 with Desired OpenMP library Path
$ cmake --preset aocl-win-ninja-lp-ga-config --fresh -DOpenMP_libomp_LIBRARY=<path to OpenMP library>
```

**Build the Project in Command Prompt**

``` console
$ cmake --build build --config Release --target install
```

## Verifying AOCL Installation

The AOCL package will be installed in the `install_package` directory
which is created inside the AOCL source directory.

There are two subfolders within the `install_package` folder: `lib` and
`include`.

-   The `include` folder contains the header files required for using
    the AOCL libraries in applications.
-   The `lib` folder contains the compiled binaries:
    -   On Linux: `libaocl.so` and `libaocl.a`.
    -   On Windows: `aocl.dll` and `aocl.lib`.

## CMake Variables Reference

This section provides a detailed reference for the CMake variables used
to configure the Build-It-Yourself AOCL project. These variables allow
customization of the build process, including selecting libraries and
specifying source paths. Use these options to tailor the unified AOCL
binary to specific requirements.

### CMake Options to Select Libraries

The following table lists the CMake variables used to include or exclude
individual AOCL libraries.

| CMake Variable or Option  | Usage |
|---------------------------|---------------------------------------------------------------|
| **ENABLE_AOCL_UTILS**     | `-DENABLE_AOCL_UTILS=ON` (default) or `-DENABLE_AOCL_UTILS=OFF` to exclude from the library. |
| **ENABLE_AOCL_BLAS**      | `-DENABLE_AOCL_BLAS=OFF` (default) or `-DENABLE_AOCL_BLAS=ON` to include in the library. |
| **ENABLE_AOCL_LAPACK**    | `-DENABLE_AOCL_LAPACK=OFF` (default) or `-DENABLE_AOCL_LAPACK=ON` to include in the library. |
| **ENABLE_AOCL_SPARSE**    | `-DENABLE_AOCL_SPARSE=OFF` (default) or `-DENABLE_AOCL_SPARSE=ON` to include in the library. |
| **ENABLE_AOCL_CRYPTO**    | `-DENABLE_AOCL_CRYPTO=OFF` (default) or `-DENABLE_AOCL_CRYPTO=ON` to include in the library. |
| **ENABLE_AOCL_LIBM**      | `-DENABLE_AOCL_LIBM=OFF` (default) or `-DENABLE_AOCL_LIBM=ON` to include in the library. |
| **ENABLE_AOCL_COMPRESSION** | `-DENABLE_AOCL_COMPRESSION=OFF` (default) or `-DENABLE_AOCL_COMPRESSION=ON` to include in the library. |


### CMake Options to Set Library Source Path

The following table lists CMake variables to specify the path of AOCL
library sources. These variables are useful when local copies of the
repositories are available, particularly in environments without
internet access.

| CMake Variable or Option  | Usage |
|---------------------------|---------------------------------------------------------------|
| **UTILS_PATH**           | `-DUTILS_PATH=<Directory Path where AOCL-Utils is present>`. |
| **BLAS_PATH**            | `-DBLAS_PATH=<Directory Path where AOCL-BLAS is present>`. |
| **LAPACK_PATH**          | `-DLAPACK_PATH=<Directory Path where AOCL-LAPACK is present>`. |
| **SPARSE_PATH**          | `-DSPARSE_PATH=<Directory Path where AOCL-Sparse is present>`. |
| **CRYPTO_PATH**          | `-DCRYPTO_PATH=<Directory Path where AOCL-Cryptography is present>`. |
| **LIBM_PATH**            | `-DLIBM_PATH=<Directory Path where AOCL-LibM is present>`. |
| **COMPRESSION_PATH**     | `-DCOMPRESSION_PATH=<Directory Path where AOCL-Compression is present>`. |


### CMake Options to Set GIT Repository and Tag/Branch

The following table lists CMake variables to specify the GIT repository
and tag or branch name for cloning individual AOCL libraries. If the
source code path is not provided, CMake uses the specified GIT
repository and tag or branch. This is useful for building source code
from the `dev` branch of individual libraries. If neither the source
code path nor the GIT repository and tag are provided, CMake defaults to
the repository and branch/tag for the AOCL stable public release.

| CMake Variable or Option    | Default Value                                      | Usage |
|-----------------------------|----------------------------------------------------|-----------------------------------------------------------|
| **UTILS_GIT_REPOSITORY**    | <https://github.com/amd/aocl-utils.git>            | `-DUTILS_GIT_REPOSITORY=<AOCL-Utils Repository URL>` |
| **UTILS_GIT_TAG**           | `main`                                             | `-DUTILS_GIT_TAG=<AOCL-Utils Git Tag or Branch Name>` |
| **BLAS_GIT_REPOSITORY**     | <https://github.com/amd/blis.git>                  | `-DBLAS_GIT_REPOSITORY=<AOCL-BLAS Repository URL>` |
| **BLAS_GIT_TAG**            | `master`                                           | `-DBLAS_GIT_TAG=<AOCL-BLAS Git Tag or Branch Name>` |
| **LAPACK_GIT_REPOSITORY**   | <https://github.com/amd/libflame.git>              | `-DLAPACK_GIT_REPOSITORY=<AOCL-LAPACK Repository URL>` |
| **LAPACK_GIT_TAG**          | `master`                                           | `-DLAPACK_GIT_TAG=<AOCL-LAPACK Git Tag or Branch Name>` |
| **SPARSE_GIT_REPOSITORY**   | <https://github.com/amd/aocl-sparse.git>           | `-DSPARSE_GIT_REPOSITORY=<AOCL-Sparse Repository URL>` |
| **SPARSE_GIT_TAG**          | `master`                                           | `-DSPARSE_GIT_TAG=<AOCL-Sparse Git Tag or Branch Name>` |
| **CRYPTO_GIT_REPOSITORY**   | <https://github.com/amd/aocl-crypto.git>           | `-DCRYPTO_GIT_REPOSITORY=<AOCL-Cryptography Repository URL>` |
| **CRYPTO_GIT_TAG**          | `main`                                             | `-DCRYPTO_GIT_TAG=<AOCL-Cryptography Git Tag or Branch Name>` |
| **LIBM_GIT_REPOSITORY**     | <https://github.com/amd/aocl-libm-ose.git>         | `-DLIBM_GIT_REPOSITORY=<AOCL-LibM Repository URL>` |
| **LIBM_GIT_TAG**            | `master`                                           | `-DLIBM_GIT_TAG=<AOCL-LibM Git Tag or Branch Name>` |
| **COMPRESSION_GIT_REPOSITORY** | <https://github.com/amd/aocl-compression.git>   | `-DCOMPRESSION_GIT_REPOSITORY=<AOCL-Compression Repository URL>` |
| **COMPRESSION_GIT_TAG**     | `amd-main`                                         | `-DCOMPRESSION_GIT_TAG=<AOCL-Compression Git Tag or Branch Name>` |

