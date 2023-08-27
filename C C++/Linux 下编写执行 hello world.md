# Linux 下编写执行 hello world

### 一、g++ 编译程序

**检查编译器：**

```bash
which gcc # c 语言编译器
which g++ # c++ 语言编译器
```

**vscode编写hello_world:**

```bash
# c 程序后缀 .c
# c++ 程序后缀 .cpp
```

**编译程序：**

```bash
gcc hello_world.c -o hello_world # c 程序编译
g++ hello_world.cpp -o hello_world # c++ 程序编译
```

**执行程序：**

```bash
./hello_world
```



- **g++ 编译多文件程序：**

```bash
g++ *.cpp -o hello_world # c++ 程序多文件程序编译
```



### 二、CMake-make编译

**单文件编译CMakeLists.txt文件：**

```bash
cmake_minimum_required(VERSION 3.1) # 最低版本 CMake
project(HelloWorld) # 生成项目名称
add_executable(HelloWorld hello_world.cpp) # 为项目添加可执行文件
```

**新建 build 目录编译：**

```bash
mkdir build 
cd build
cmake .. # CMake 构建项目，生成 Makeflie
make # 编译程序
./HelloWorld # 执行程序
```



- **多文件编译CMakeLists.txt文件：**

```bash
cmake_minimum_required(VERSION 3.1) 
project(HelloWorld VERSION 0.1)
# 生成库文件目标
add_library(MyLib MyLib.cpp MyLib.h)
# 生成可执行文件目标
add_executable(HelloWorld hello_world.cpp) 
# 将可执行文件目标 链接到 生成库文件目标
target_link_libraries(HelloWorld PRIVATE MyLib)
```





