---
title: CMake 预定义变量
date: 2017-1-13 4:40:40 PM
categories:
- 术业专攻
tags: 
- cmake
---
本文主要总结 cmake 中预定义的变量及其说明。
<!-- more -->
# CMake 预定义变量
[官方文档][1]
文章内容均来自对官网文档的一些翻译，有删减，CPACK部分变量没有罗列其中。如有不当之处，欢迎批评指正。
## 提供信息的变量
- **CMAKE_ARGC**: 传递给 cmake 命令的变量数目
- **CMAKE_ARGV0**: 传递给 cmake 命令的第一个变量
- CMAKE_AR： 指定了创建归档或静态库的程序的名称
- **CMAKE_BINARY_DIR**: 当前编译目录的顶层目录， 如何在源码目录内编译，则与 `CMAKE_SOURCE_DIR` 相同
- **CMAKE_CACHEFILE_DIR**: `CMakeCache.txt`所在路径
- CMAKE_CACHE_MAJOR_VERSION： 用于生成 `CMakeCache.txt` 的 CMake 主版本
- CMAKE_CACHE_MINOR_VERSION： 用于生成 `CMakeCache.txt` 的 CMake 最小版本
- CMAKE_CACHE_PATCH_VERSION： `CMakeCache.txt`的补丁版本
- **CMAKE_COMMAND**： 可执行文件`cmake` 的路径，在linux下为 `/usr/bin/cmake`
- **CMAKE_CROSSCOMPILING**： 判定是否跨平台编译，即目标平台与编译平台是否不同
- CMAKE_CTEST_COMMAND： 可执行文件 `CTest` 的完整路径。
- **CMAKE_CURRENT_BINARY_DIR**： 当前正在处理的二进制文件的目录，即当前 build 的目录。
- **CMAKE_CURRENT_LIST_DIR**： 当前正在处理的文件的目录，随着当前处理的文件的变化二变化
- **CMAKE_CURRENT_LIST_FILE**： 当前正在处理的文件的路劲
- **CMAKE_CURRENT_LIST_LINE**： 当前正在处理的文件的行数
- **CMAKE_CURRENT_SOURCE_DIR**： 当前正在处理的源文件的目录
- CMAKE_DL_LIBS： 包含 `dlopen` 和 `dlclose` 的库名， 在 UNIX 中一般为 `-ldl`
- CMAKE_EDIT_COMMAND： `cmake-gui` 或 `ccmake` 的完整路径
- CMAKE_EXECUTABLE_SUFFIX： 此平台上可执行文件的后缀，Windows上为 `.exe`
- **CMAKE_EXTRA_GENERATOR**： 用于构建项目的生成器，用于生成对应的工程文件， 如Eclipse，CodeBlocks or KDevelop等工程文件
- CMAKE_EXTRA_SHARED_LIBRARY_SUFFIXES： 除`CMAKE_SHARED_LIBRARY_SUFFIX`指定的共享库后缀外，额外的共享库的后缀
- **CMAKE_GENERATOR**： 用于生成工程文件的生成器，如“Unix Makefiles”, “Visual Studio 6”等
- **CMAKE_GENERATOR_TOOLSET**： 由用户指定的本地编译系统工具集合， 使用`-T`选项指定。
- **CMAKE_HOME_DIRECTORY**： 源码的顶层目录路径
- CMAKE_IMPORT_LIBRARY_PREFIX： 导入库的前缀
- CMAKE_IMPORT_LIBRARY_SUFFIX： 导入库的后缀
- CMAKE_LINK_LIBRARY_SUFFIX： 链接库的前缀
- CMAKE_MAJOR_VERSION： cmake 主版本
- **CMAKE_MINIMUM_REQUIRED_VERSION**： 要求的 cmake 最低版本 
- **CMAKE_PARENT_LIST_FILE**： 包含当前cmake file 的完整路径。
- CMAKE_PATCH_VERSION：当前 cmake 组件的版本
- **CMAKE_PROJECT_NAME**： 当前 project 名
- **CMAKE_ROOT**： cmake 安装路径
- **CMAKE_SOURCE_DIR**： 源文件根路径
- **CMAKE_STANDARD_LIBRARIES**： 链接到所有目标（可执行，静态库，动态库）的库列表 
- **CMAKE_VERBOSE_MAKEFILE**： 启动 makefile 的详细输出
- **CMAKE_VERSION**： cmake 版本号，格式为 `<major>.<minor>.<patch>[-rc<n>]`
- **CMAKE_VS_DEVENV_COMMAND**： Visual Studio 7 及以上相关的变量
- **CMAKE_VS_MSBUILD_COMMAND**： visual Studio 10 及以上的相关变量
- **CMAKE_VS_MSDEV_COMMAND**：  visual Studio 6 相关变量
- **CMAKE_VS_PLATFORM_TOOLSET**： Visual studio 平台工具集 名
- **CMAKE_XCODE_PLATFORM_TOOLSET**：  XCODE 平台工具集名
- **PROJECT_BINARY_DIR**： project 的完整编译路径
- **<PROJECT-NAME>_BINARY_DIR**： `<PROJECT-NAME>`对应的二进制文件顶级目录
- **PROJECT_NAME**： project 名
- **<PROJECT-NAME>_SOURCE_DIR**： `<PROJECT-NAME>`对应的源文件顶级目录
- **PROJECT_SOURCE_DIR**： 当前 project 的源码文件顶级目录


## 影响行为的变量
- **BUILD_SHARED_LIBS**： 全局变量，设为true时，会编译 add_library 添加进来的所有动态库。
- **CMAKE_ABSOLUTE_DESTINATION_FILES**： 只读，列出所有 安装到 `ABSOLUTE DESTINATION` 路径的文件
- **CMAKE_APPBUNDLE_PATH**： 为 OS X 提供 cmake 中使用到的 `find_program()`,`find_package()` 的查找功能
- **CMAKE_AUTOMOC_RELAXED_MODE**： 改变对 QT project 的 automoc 模式支持
- **CMAKE_BUILD_TYPE**： 设置编译类型，默认为空，可选值为 `empty, Debug, Release, RelWithDebInfo and MinSizeRel`
- **CMAKE_COLOR_MAKEFILE**： 是否启用 makefile 生成器的彩色输出
- **CMAKE_CONFIGURATION_TYPES**： 为多配置的生成器指定编译类型（Debug,Release...）
- **CMAKE_DEBUG_TARGET_PROPERTIES**： 是否允许追踪目标属性的输出
- **CMAKE_DISABLE_FIND_PACKAGE_<PackageName>**： 是否禁止 find_package() 调用
- **CMAKE_FIND_LIBRARY_PREFIXES**： 查找库时，指定的前缀，一般为lib
- **CMAKE_FIND_LIBRARY_SUFFIXES**： 查找库时，指定的后缀，`.dll, .so, .lib, .a`等
- **CMAKE_FIND_PACKAGE_WARN_NO_MODULE**： 当没有指定module时，调用find_package发出警告
- **CMAKE_FIND_ROOT_PATH**： 文件系统的根目录列表  
- **CMAKE_INCLUDE_PATH**： `find_file()` `find path()` 的搜索路径
- **CMAKE_INSTALL_PREFIX**： `make install` 的安装路径，默认为 `/usr/local/` 或 `c:/ProgramFiles` 
- **CMAKE_LIBRARY_PATH**： `find_library()` 的搜索路径
- **CMAKE_MFC_FLAG**： 使用 MFC 或 dll
- **CMAKE_MODULE_PATH**：  cmake modules 搜索路径列表
- **CMAKE_PREFIX_PATH**： `find_xxx()`的前缀
- **CMAKE_PROGRAM_PATH**： `find_program()`的 路径
 
 ## 系统描述变量
- **APPLE** ： 在 Mac os X 上 为 true
- **BORLAND** ： 使用 Borland 编译器时为 true
- **CMAKE_CL_64** ： 使用 Microsoft 64位编译器 cl 时为 true
- **CMAKE_COMPILER_2005** ： 使用 Visual studio 2005 编译器时为 true
- **CMAKE_HOST_APPLE** ： 使用 Apple OS X 时为 true
- **CMAKE_HOST_SYSTEM_NAME** ： 当前操作系统的名字
- **CMAKE_HOST_SYSTEM_PROCESSOR** ： 当前 cpu 名
- **CMAKE_HOST_SYSTEM** ： 当前系统名
- **CMAKE_HOST_SYSTEM_VERSION** ： 当前系统版本
- **CMAKE_HOST_UNIX** ： 使用 UNIX 或者 类UNIX(apple,cygwin)时为true
- **CMAKE_HOST_WIN32** ： 使用 Windows(包含win64) 或者 cygwin 时为true
- **CMAKE_LIBRARY_ARCHITECTURE_REGEX** ： 正则匹配 可能的目标架构库的路径
- **CMAKE_LIBRARY_ARCHITECTURE** ： 目标结构库的路径
- **CMAKE_OBJECT_PATH_MAX** ： 当前编译工具允许的最大路径长度
- **CMAKE_SYSTEM_NAME** ：  目标系统名
- **CMAKE_SYSTEM_PROCESSOR** ： 目标系统CPU名
- **CMAKE_SYSTEM** ： 目标系统名，如果没有设置 cmake_system_version, 则与CMAKE_SYSTEM_NAME相同
- **CMAKE_SYSTEM_VERSION** ：目标系统版本
- **CYGWIN** ： 使用 cygwin 时为 true
- **ENV** ： 访问环境变量，`$ENV{var}` ,也可使用 set 名字设置环境变量
- **MSVC10** ： 使用 Microsoft Visual C 10.0 时为true
- **MSVC11** ： 使用 Microsoft Visual C 11.0 时为true
- **MSVC12** ： 使用 Microsoft Visual C 12.0 时为true
- **MSVC60** ： 使用 Microsoft Visual C 6.0 时为true
- **MSVC70** ： 使用 Microsoft Visual C 7.0 时为true
- **MSVC71** ： 使用 Microsoft Visual C 7.1 时为true
- **MSVC80** ： 使用 Microsoft Visual C 8.0 时为true
- **MSVC90** ： 使用 Microsoft Visual C 9.0 时为true
- **MSVC_IDE** ： 使用 Microsoft Visual C IDE 时为true
- **MSVC** ： 使用 Microsoft Visual C 时为true
- **MSVC_VERSION** ： 使用 Microsoft Visual C/C++ 的版本
- **UNIX** ： 使用 UNIX 或者 类UNIX(APPLE,CYGWIN)时为true 
- **WIN32** ： 使用Windows时为true，包含 64位windows
- **XCODE_VERSION** ： Xcode 版本号

## 控制编译的变量
- **CMAKE_ARCHIVE_OUTPUT_DIRECTORY** ： 指定 cmake 编译过程 归档目标的存储路径
- **CMAKE_AUTOMOC_MOC_OPTIONS** ： 使用 `CMAKE_AUTOMOC` 时指定额外的编译选项
- **CMAKE_AUTOMOC** ： 是否为 QT 工程处理 moc
- **CMAKE_AUTORCC** ： 是否为 QT 工程处理 rcc
- **CMAKE_AUTORCC_OPTIONS** ：  使用 `CMAKE_AUTORCC` 时指定额外的编译选项
- **CMAKE_AUTOUIC** ： 是否为 QT 工程处理 uic
- **CMAKE_AUTOUIC_OPTIONS** ： 使用 `CMAKE_AUTOUIC` 时指定额外的编译选项
- **CMAKE_EXE_LINKER_FLAGS** ： 创建 可执行文件时的link标记 
- **CMAKE_GNUtoMS** ： 将 GNU 导入的库 `.dll.a` 转换为 `.lib`
- **CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE** ： 自动添加 当前源码 以及编译路径 到 INTERFAC_INCLUDE_DIRECTORIES 中， 默认为false
- **CMAKE_INCLUDE_CURRENT_DIR** ： 自动添加 `${CMAKE_CURRENT_SOURCE_DIR}`，`${CMAKE_CURRENT_BINARY_DIR}`到当前include path， 不会添加子目录，默认为false
- **CMAKE_INSTALL_NAME_DIR** ： MAC OS X 上目标的安装路径 
- **CMAKE_LIBRARY_OUTPUT_DIRECTORY** ： 库目标的存储路径
- **CMAKE_LIBRARY_PATH_FLAG** ： 链接库的标记，一般为`-L`
- **CMAKE_LINK_DEF_FILE_FLAG** ： 在Windows 上定义，为 `dll` 目标创建一个`.def`文件
- **CMAKE_LINK_DEPENDS_NO_SHARED** ： 
- **CMAKE_LINK_INTERFACE_LIBRARIES** ： 
- **CMAKE_LINK_LIBRARY_FILE_FLAG** ： 
- **CMAKE_LINK_LIBRARY_FLAG** ： 创建可执行文件时，链接库的标记，一般为`-l`
- **CMAKE_MACOSX_BUNDLE** ：  MACOSX_BUNDLE的默认值
- **CMAKE_MODULE_LINKER_FLAGS** ： 创建模块时的链接标记 
- **CMAKE_OSX_ARCHITECTURES** ： OS X 上目标架构
- **CMAKE_OSX_SYSROOT** ： 指定 OSX 上 SDK 根目录
- **CMAKE_PDB_OUTPUT_DIRECTORY** ： MS DEBUG 符号文件 `.pdb` 文件的输出路径： 
- **CMAKE_RUNTIME_OUTPUT_DIRECTORY** ： 指定输入编译时的输出目录
- **CMAKE_SHARED_LINKER_FLAGS** ： 创建共享库的链接标记  
- **CMAKE_STATIC_LINKER_FLAGS** ： 创建静态库的链接标记 
- **CMAKE_VISIBILITY_INLINES_HIDDEN** ： 
- **CMAKE_WIN32_EXECUTABLE** ： win32 可执行程序的默认值
- **EXECUTABLE_OUTPUT_PATH** ： 可执行目标程序文件的存放路径，RUNTIME_OUTPUT_DIRECTORY 会取代该值
- **LIBRARY_OUTPUT_PATH** ： 目标库文件的存放路径，`ARCHIVE_OUTPUT_DIRECTORY`, `LIBRARY_OUTPUT_DIRECTORY`, `RUNTIME_OUTPUT_DIRECTOR` 会取代该值。


## 不同语言的变量
- **CMAKE_COMPILER_IS_GNU<LANG>** ： 编译器为 GNU 时为true，`<LANG>` 一般为 `CC，CXX，G77`，CC：C语言编译器，CXX： c++编译器， G77: fortran 编译器 
- **CMAKE_Fortran_MODDIR_DEFAULT** ：  fortran 默认模块叔叔路径
- **CMAKE_Fortran_MODDIR_FLAG** ： fortran 模块输出路径的标记
- **CMAKE_Fortran_MODOUT_FLAG** ： 是否允许 模块输出的Fortran 标记
- **CMAKE_<LANG>_COMPILER_ID** ： 编译器标识
> Absoft = Absoft Fortran (absoft.com)
ADSP = Analog VisualDSP++ (analog.com)
AppleClang = Apple Clang (apple.com)
Clang = LLVM Clang (clang.llvm.org)
Cray = Cray Compiler (cray.com)
Embarcadero, Borland = Embarcadero (embarcadero.com)
G95 = G95 Fortran (g95.org)
GNU = GNU Compiler Collection (gcc.gnu.org)
HP = Hewlett-Packard Compiler (hp.com)
Intel = Intel Compiler (intel.com)
MIPSpro = SGI MIPSpro (sgi.com)
MSVC = Microsoft Visual Studio (microsoft.com)
PGI = The Portland Group (pgroup.com)
PathScale = PathScale (pathscale.com)
SDCC = Small Device C Compiler (sdcc.sourceforge.net)
SunPro = Oracle Solaris Studio (oracle.com)
TI = Texas Instruments (ti.com)
TinyCC = Tiny C Compiler (tinycc.org)
Watcom = Open Watcom (openwatcom.org)
XL, VisualAge, zOS = IBM XL (ibm.com)

- **CMAKE_<LANG>_COMPILER_LOADED** ： 是否允许 `<LANG>` 加载
- **CMAKE_<LANG>_COMPILER** ： `<LANG>`编译器的完整路径
- **CMAKE_<LANG>_COMPILER_EXTERNAL_TOOLCHAIN** ： 跨平台编译工具链
- **CMAKE_<LANG>_COMPILER_TARGET** ： 跨平台编译的目标
- **CMAKE_<LANG>_COMPILER_VERSION** ：  编译版本号 ： 
- **CMAKE_<LANG>_IGNORE_EXTENSIONS** ： 编译时被忽略的扩展文件

  [1]: https://cmake.org/cmake/help/v3.0/manual/cmake-variables.7.html