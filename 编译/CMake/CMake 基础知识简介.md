# CMake 基础知识简介

### 1、简介

#### 最低版本要求

每个 CMakeLists.txt 都必须包含的第一行

```cmake
cmake_minimum_required(VERSION 3.5)
```

代码规范：

​	命令 cmake_minimum_required 不区分大小写，常用的做法是使用小写，`VERSION` 和它后面的版本号是这个函数的特殊关键字

推荐使用 

```cmake
# 只会设置为最低 3.1 版本，3.21 是用于告知用户在这一范围的版本上测试成功过
cmake_minimum_required(VERSION 3.1...3.21)
```

注释：

​	行注释：#

​	块注释：[[ ... ]]

#### 设置一个项目

每一个顶层 CMakeLists 文件都应该有下面一行

```cmake
project(MyProject VERSION 1.0
				  DESCRIPTION "Very nice project"
				  LANGUAGES CXX)
```

VERSION 变量可选

​	VERSION、MyProject_VERSION、PROJECT_VERSION

默认语言

​	C CXX

​	支持：C`,`CXX`,`Fortran`,`ASM`,`CUDA`(CMake 3.8+),`CSharp`(3.8+),`SWIFT等

项目名称没有什么特别的用处，这里没有添加任何的目标

#### 生成一个可执行文件

```cmake
add_executable(one tow.cpp three.h)
```

参数说明：

​	one：既是生成的可执行文件的名称，也是创建的 CMake 目标的名称

​	two.cpp：源文件列表

​	three.h：头文件，大多数情况会被忽略

#### 生成一个库

```cmake
add_library(one STATIC two.cpp three.h)
```

库的类型：STATIC、SHARED、MODULE，若不指定，则 CMake 会通过 BUILD_SHARED_LIBS 的值构建 STATIC 或 SHARED 库

#### 目标时常陪伴着你

现在我们已经指定了一个目标，那我们如何添加关于它的信息呢？例如，它可能需要包含一个目录：

```cmake
target_include_directories(one PUBLIC include)
```

[`target_include_directories`](https://cmake.org/cmake/help/latest/command/target_include_directories.html) 为目标添加了一个目录。 `PUBLIC` 对于一个二进制目标没有什么含义；但对于库来说，它让 CMake 知道，任何链接到这个目标的目标也必须包含这个目录。其他选项还有 `PRIVATE`（只影响当前目标，不影响依赖），以及 `INTERFACE`（只影响依赖）。

接下来我们可以将目标之间链接起来：

```cmake
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

[`target_link_libraries`](https://cmake.org/cmake/help/latest/command/target_link_libraries.html) 可能是 CMake 中最有用也最令人迷惑的命令。它指定一个目标，并且在给出目标的情况下添加一个依赖关系。如果不存在名称为 `one` 的目标，那他会添加一个链接到你路径中 `one` 库（这也是命令叫 `target_link_libraries` 的原因）。或者你可以给定一个库的完整路径，或者是链接器标志。最后再说一个有些迷惑性的知识：），经典的 CMake 允许你省略 `PUBLIC` 关键字，但是你在目标链中省略与不省略混用，那么 CMake 会报出错误。

只要记得在任何使用目标的地方都指定关键字，那么就不会有问题。

目标可以有包含的目录、链接库（或链接目标）、编译选项、编译定义、编译特性（见C++11 章节）等等。正如你将在之后的两个项目章节中看到的，你经常可以得到目标（并且经常是指定目标）来代表所有你使用的库。甚至有些不是真正的库，像 `OpenMP`，就可以用目标来表示。这也是为什么现代 CMake 如此的棒！

### 2、变量与缓存

#### 本地变量

```cmake
# 声明一个本地变量
set(MY_VARIABLE "value")
```

变量名通常全部用大写，变量值跟在其后，可以通过 ${} 来解析一个变量

```cmake
# 列表变量
set(MY_LIST "one" "two")
# 或 ";" 划分
set(MY_LIST "one;two")
```

#### 缓存变量

缓存变量可以让你只能在命令行中设置这些变量。CMake 中已经有一些预置的变量，像 `CMAKE_BUILD_TYPE` 。如果一个变量还没有被定义，你可以这样声明并设置它。

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")
```

这么写**不会覆盖**已定义的值。这是为了让你只能在命令行中设置这些变量，而不会在 CMake 文件执行的时候被重新覆盖。如果你想把这些变量作为一个临时的全局变量，你可以这样做：

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "" FORCE)
mark_as_advanced(MY_CACHE_VARIABLE)
```

第一行将会强制设置该变量的值，第二行将使得用户运行 `cmake -L ..` 或使用 GUI 界面的时候不会列出该变量。此外，你也可以通过 `INTERNAL` 这个类型来达到同样的目的（尽管在技术上他会强制使用 STRING 类型，这不会产生任何的影响）：

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE INTERNAL "")
```

设定布尔变量

```cmake
option(MY_OPTION "This is settable from the command line" OFF)
```

#### 环境变量

```cmake
# 设置环境变量
set(ENV{variable_name} value)
# 获取环境变量
$ENV{valiable_name}
```

一般来说，最好避免这么使用

#### 缓存

缓存实际上是一个文件，CMakeCache.txt，当你运行 CMake 构建目录时会创建它

#### 属性

CMake 也可以通过属性来存储信息；许多目标属性都是被以 `CMAKE_` 为前缀的变量来初始化的。例如你设置 `CMAKE_CXX_STANDARD` 这个变量，这意味着你之后创建的所有目标的 `CXX_STANDARD` 都将被设为`CMAKE_CXX_STANDARD` 变量的值。

你可以这样来设置属性：

```cmake
set_property(TARGET TargetName
             PROPERTY CXX_STANDARD 11)

set_target_properties(TargetName PROPERTIES
                      CXX_STANDARD 11)
```

第一种方式更加通用 ( general ) ，它可以一次性设置多个目标、文件、或测试，并且有一些非常有用的选项。第二种方式是为一个目标设置多个属性的快捷方式。此外，你可以通过类似于下面的方式来获得属性：

```cmake
get_property(ResultVariable TARGET TargetName PROPERTY CXX_STANDARD)
```

### 3、用 CMake 进行编程

#### 控制流程

if-endif 语句

```cmake
if(variable)
    # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
else()
    # If variable is `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`, or ends in `-NOTFOUND`
endif()
# If variable does not expand to one of the above, CMake will expand it then try again
```

关键字

- 一元的: `NOT`, `TARGET`, `EXISTS` (文件), `DEFINED`, 等。
- 二元的: `STREQUAL`, `AND`, `OR`, `MATCHES` ( 正则表达式 ), `VERSION_LESS`, `VERSION_LESS_EQUAL` ( CMake 3.7+ ), 等。
- 括号可以用来分组

generator-expressions 强大，但有点奇怪和高级

#### 宏定义与函数

下面十一个简单的函数的例子：

```cmake
function(SIMPLE REQUIRED_ARG)
    message(STATUS "Simple arguments: ${REQUIRED_ARG}, followed by ${ARGN}")
    set(${REQUIRED_ARG} "From SIMPLE" PARENT_SCOPE)
endfunction()

simple(This Foo Bar)
message("Output: ${This}")
```

输出如下：

```cmake
-- Simple arguments: This, followed by Foo;Bar
Output: From SIMPLE
```

如果你想让函数中定义的变量对外部可见，你需要使用 `PARENT_SCOPE` 来改变其作用域

#### 参数控制

可以通过 cmake_parse_arguments 函数来对变量进行命名与解析



### 4、与你的代码交互

#### 通过 CMake 配置文件

CMake 允许在代码中使用 configure_file 来访问 CMake 变量，

该命令将一个文件的内容复制到另一个文件（一般 .in 结尾）中，并替换其中它找到的所有 CMake 变量

这个功能在 CMake 中使用的相当频繁



### 5、如何组织你的项目

```bash
- project
  - .gitignore
  - README.md
  - LICENCE.md
  - CMakeLists.txt
  - cmake
    - FindSomeLib.cmake
    - something_else.cmake
  - include
    - project
      - lib.hpp
  - src
    - CMakeLists.txt
    - lib.cpp
  - apps
    - CMakeLists.txt
    - app.cpp
  - tests
    - CMakeLists.txt
    - testlib.cpp
  - docs
    - CMakeLists.txt
  - extern
    - googletest
  - scripts
    - helper.py
```

 `CMakeLists.txt` 文件被分割到除了 `include` 目录外的所有源代码目录下。

这是为了能够将 `include` 目录下的所有文件拷贝到 `/usr/include` 目录或其他类似的目录下。

顶层 `CMakeLists.txt` 中应使用 `add_subdirectory` 命令来添加一个包含 `CMakeLists.txt` 的子目录。

























































