# 为 CMake 项目添加特性

### 1、C++11 及后续版本

CMake 中支持 C++11，但是这是针对于 CMake 2.8 及以后的版本来说的。这是为什么？很容易可以猜到， C++11 在 2009年——CMake 2.0 发布的时候还不存在。只要你使用 CMake 的是 CMake 3.1 或者更新的版本，你将会得到 C++11 的完美支持，不过这里有两种不同的方式来启用支持。 并且你将看到，在 CMake 3.8+ 中对 C++11 有着更好的支持。我将会在 CMake 3.8+ 的基础上讲解，因为这才叫做 Modern CMake。

### 2、一些小而常用的需求

想要链接到数学库没这么简单。如果你需要明确地链接到它，你可以使用 `target_link_libraries(MyTarget PUBLIC m)`，但是使用 CMake 通用的 [`find_library`](https://cmake.org/cmake/help/latest/command/find_library.html) 可能更好，如下是一个例子：

```cmake
find_library(MATH_LIBRARY m)
if(MATH_LIBRARY)
    target_link_libraries(MyTarget PUBLIC ${MATH_LIBRARY})
endif()
```

### 3、一些实用的工具



### 4、一些有用的模组



### 5、CMake 对 IDE 的支持



### 6、调试

#### 打印变量

使用 message 打印语句

```cmake
message(STATUS "MY_VARIABLE=${MY_VARIABLE}")
```

然而，通过一个内置的模组 `CMakePrintHelpoers` 可以更方便的打印变量：

```cmake
include(CMakePrintHelpers)
cmake_print_variables(MY_VARIABLE)
```

如何你只是想要打印一个变量，那么上述方法已经很好用了！如何你想要打印一些关于某些目标 (或者是其他拥有变量的项目，比如 `SOURCES`、`DIRECTORIES`、`TESTS` , 或 `CACHE_ENTRIES` - 全局变量好像因为某些原因缺失了) 的变量，与其一个一个打印它们，你可以简单的列举并打印它们：

```cmake
cmake_print_properties(
    TARGETS my_target
    PROPERTIES POSITION_INDEPENDENT_CODE
)
```

#### 跟踪运行

如何你只是想要打印一个变量，那么上述方法已经很好用了！如何你想要打印一些关于某些目标 (或者是其他拥有变量的项目，比如 `SOURCES`、`DIRECTORIES`、`TESTS` , 或 `CACHE_ENTRIES` - 全局变量好像因为某些原因缺失了) 的变量，与其一个一个打印它们，你可以简单的列举并打印它们：

```cmake
cmake_print_properties(
    TARGETS my_target
    PROPERTIES POSITION_INDEPENDENT_CODE
)
```

如果你添加了 --trace-expand 选项，变量会直接展开成他们的值。

### 以 debug 模式构建

对于单一构建模式的生成器 (single-configuration generators)，你可以使用参数 `-DCMAKE_BUILD_TYPE=Debug` 来构建项目，以获得调试标志 (debugging flags)。对于支持多个构建模式的生成器 (multi-configuration generators)，像是多数IDE，你可以在 IDE 里打开调试模式。这种模式有不同的标志（变量以 `_DEBUG` 结尾，而不是 `_RELEASE` 结尾），以及生成器表达式的值 `CONFIG:Debug` 或 `CONFIG:Release`。

如果你使用了 debug 模式构建，你就可以在上面运行调试器了，比如 gdb 或 lldb。





















