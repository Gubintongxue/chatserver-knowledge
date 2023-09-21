**笔记10-13内容**

## CMake使用：

使用简单方便，可以跨平台，构建项目编译环境。在构建大型工程编译时需要写大量的文件依赖关系，故可使用简单的 CMake 来生成复杂的 Makefile 文件。

### 查看cmake的版本：

```cmake
cmake --version
```

### cmake的使用简介：

项目中，有需要编译的可执行文件的**子目录**下，`CMakeLists.txt`的内容。

```cmake
# 要求cmake最低的版本号
cmake_minimum_required (VERSION 2.8) 

# 定义当前工程名字
project (project_name) 

# 设置debug模式，如果没有这一行将不能调试设断点
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} -g)

# 添加头文件搜索路径，相当于makefile里面的-I
include_directories(${PROJECT_SOURCE_DIR}/include)
# 库文件的搜索路径
link_directories(${PROJECT_SOURCE_DIR}/lib)

# 生成库，动态库是SHARED，静态库是STATIC
add_library(*** SHARED ***.cpp)
# 修改库的名字
set_target_properties(*** PROPERTIES OUTPUT_NAME "lib***")

# 定义变量，存储当前目录下的所有源文件
aux_source_directory(. SRC_LIST)  # 存在的弊端：会把指定目录下的所有源文件都加进来，可能会加入一些我们不需要的文件。
# 可通过set命令，新建变量来存放需要的源文件：set(SRC_LIST ./***.cpp ./***.cpp)

# 设置可执行文件，最终存储的路径
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 表示生成可执行文件 main，由 SRC_LIST 变量所定义的源文件编译而来
add_executable(main ${SRC_LIST})

# 表示生成可执行文件 main，需要的库文件library_1、library_2、library_3、...
target_link_libraries(main library_1 library_2 library_3)
```

项目中，**主目录**下，`CMakeLists.txt`的内容。

```cmake
# 要求cmake最低的版本号
cmake_minimum_required (VERSION 2.8) 

# 定义当前工程名字
project (project_name) 

# 设置debug模式，如果没有这一行将不能调试设断点
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} -g)  # 配置编译选项

# 指定搜索的子目录：
# 进入子目录下执行其中的 CMakeLists.txt 文件，这里的 tests1 和 tests2 里面都有可编译的代码文件
add_subdirectory(tests1)
add_subdirectory(tests2)
```

### 静态库和动态库的编译控制：

```cmake
// 有待进一步学习。。。
```

### 一个正式的工程构建：

1.  bin 存放最终的可执行文件
2.  build 存放编译中间文件
3.  lib 存放编译生成的库文件
4.  include 头文件
5.  src 源代码文件
6.  main.cpp
7.  CMakeLists.txt

### CMake常用的预定义变量：

```cmake
PROJECT_NAME # 通过 project() 指定项目名称

PROJECT_SOURCE_DIR # 工程的根目录

PROJECT_BINARY_DIR # 执行 cmake 命令的目录

CMAKE_CURRENT_SOURCE_DIR # 当前 CMakeList.txt 文件所在的目录

CMAKE_CURRENT_BINARY_DIR # 编译目录，可使用 add subdirectory 来修改

EXECUTABLE_OUTPUT_PATH # 二进制可执行文件输出位置

LIBRARY_OUTPUT_PATH # 库文件输出位置

BUILD_SHARED_LIBS # 默认的库编译方式 ( shared 或 static ) ，默认为 static

CMAKE_C_FLAGS # 设置 C 编译选项

CMAKE_CXX_FLAGS # 设置 C++ 编译选项

CMAKE_CXX_FLAGS_DEBUG # 设置编译类型 Debug 时的编译选项

CMAKE_CXX_FLAGS_RELEASE # 设置编译类型 Release 时的编译选项

CMAKE_GENERATOR # 编译器名称

CMAKE_COMMAND # CMake 可执行文件本身的全路径

CMAKE_BUILD_TYPE # 工程编译生成的版本， Debug / Release
```
