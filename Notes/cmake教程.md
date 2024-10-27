# cmake 编程指北

bilibili:【CMake 保姆级教程【C/C++】】https://www.bilibili.com/video/BV14s4y1g7Zj

## cmake概述

1. 安装cmake
2. 编写CMakeLists.txt

## cmake使用

### 注释

- \#
- \#[[   ]]

### 只有一个源文件目录

CMakeLists.txt:

- `cmake_minimum_required` 指定cmake版本，检测本地环境
- `project` 指定项目名称
- `add_executable(程序名 源文件名称1 源文件名称2...)` 生成一个可执行程序

编译命令

- `cmake CMakeLists文件所在的目录`  编译文件 一般创建一个build目录，在build目录下执行改命令。执行后会在build文件夹下生成许多文件，其中包含makefile
- `make` 生成可执行文件

### 其他设置

- `set()` 设置值，默认是字符串类型
- `${ }` 对变量进行取值
- `set(CMAKE_CXX_STANDARD 11)` 设置c++11标准
- `set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin)`设置生成路径，路径不存在就自动创建


搜索文件

- `aux_source_directory(搜索路径 变量名)`
  - `PROJECT_SOURCE_DIR` 为`cmake`命令后面跟着的目录	
  - `CMAKE_CURRENT_SOURCE_DIR` 当前cmake文件所在的目录
- `file(GLOB/GLOB_RECURSE 变量名 文件路径匹配)`
  - 当前目录 或者当前目录及其子目录 
- `include_directories(${PROJECT_SOURCE_DIR}/include)` 指定源文件要搜索的头文件路径

### cmake生成库文件

- `add_library(库名字中间部分 STATIC/SHARED 源文件)` 生成静态/动态库
- `set(LIBRARY_OUTPUT_PATH ...)` 指定库的**生成位置**
- `link_libraries(掐头去尾的库名)` 链接一个或多个**静态**库，必须被依赖的放后面
- `target_link_libraries(<target> <item>)` 链接动态库
  - 这条命令一般在**最后**	
  - 也能进行静态库的链接
- `link_directories()` 指定静态/动态库的**路径**  若系统路径中已经设置，则不需要指定路径

### 日志

- `message([<level>] "...")`

### 变量操作

- `set(target [<item>.....])` 字符串拼接
- `list(APPEND <list> [<item>....])` 字符串追加
- `list(REMOVE_ITEM <list> <item> [<item>...])` 字符串移除
-  `list()`函数还有许多其他作用 

### 宏定义

- `add_definitions(-D宏名称)` 添加宏定义 
  - 通过宏的定义能够处理程序中的日志输出，相当于一个开关 

### 嵌套cmake

- 头文件的目录不用写CMakeLists.txt
- 包含源文件的要写

- `add_subdirectory(子节点目录)` 添加子目录
- 父节点的变量可以被子节点使用
- 父节点可以用来设置全局变量，添加子节点
- 子节点可以用来实现相对独立的任务，类似于单源文件夹情况

- 静态库可以链接静态库，与可执行程序的链接是一样的
- 动态库可以链接静态库，同上
- 静态库可以链接动态库
- 动态库可以链接动态库

- 可以在根节点设置 include目录/ 加入子节点
- 每个子节点各司其职，生成动态或者静态库
- 包含main函数的节点（根节点或者子节点）生成可执行程序