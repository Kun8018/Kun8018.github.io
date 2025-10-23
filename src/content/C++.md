---
title: C++
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，C++
toc: true
thumbnail: 
---

## 概述

​       先开坑吧，涉及到很多内容，慢慢补

<!--more-->

## 编辑器

emacs



clion　　



### clangd

clangd 是 llvm 项目推出的 C++ 语言服务器，通过 LSP (Language Server Protocal) 协议向编辑器如 vscode/vim/emacs 提供语法补全、错误检测、跳转、格式化等等功能。C++ 的 LSP 曾经是 cquery, ccls, clangd 三足鼎立。但是 clangd 支持 clang-tidy 实时检查的功能是另外两者不具备的，而且 cquery 和 ccls 都是单个开发者主导的项目，clangd 背后则是有 llvm 的背书

除了报错、跳转、代码补全这些大家都有的功能外，clangd 还提供一些非常好用的特色功能。

自动插入头文件

clangd 的代码补全可以搜索你的整个代码库，即使是没有被 include 进来的变量也会覆盖到。而且在补全时，它会自动帮你填上合适的 namespace 和头文件

clang-format

业界大型的 C++ 代码项目一般都会自带 clang-format 配置，作为 clang 家族的一员，clangd 可以提供自动的 clang-format 格式化

### ReSharper



## 编译器

### cmake

[CMake](https://zhida.zhihu.com/search?content_id=212732651&content_type=Article&match_order=1&q=CMake&zhida_source=entity)是一个开源、跨平台的工具系列，是用来构建、测试和打包软件。

CMake使用平台无关的配置文件来控制软件编译过程，并生成可在您选择的编译器环境中使用项目文件，比如可以生成[vs项目文件](https://zhida.zhihu.com/search?content_id=212732651&content_type=Article&match_order=1&q=vs项目文件&zhida_source=entity)或者[makefile](https://zhida.zhihu.com/search?content_id=212732651&content_type=Article&match_order=1&q=makefile&zhida_source=entity)。CMake工具套件由[Kitware](https://zhida.zhihu.com/search?content_id=212732651&content_type=Article&match_order=1&q=Kitware&zhida_source=entity)公司创建，以满足ITK和VTK等开源项目对跨平台构建环境的需求。Kitware是一家从事医疗计算，高性能的可视化和计算，数据和分析，计算机视觉的公司。该公司成立于1998年。

为什么用cmake，这里有两个问题，首先要问为什么我需要一个好的构建系统？，下面是主要的原因：

· 你想避免硬编码路径

· 您需要在多台计算机上构建一个包

· 你想使用 CI（[持续集成](https://zhida.zhihu.com/search?content_id=212732651&content_type=Article&match_order=1&q=持续集成&zhida_source=entity)）

· 你需要支持不同的操作系统

· 你想支持多个编译器

· 您想使用 IDE，但不是所有情况

· 你想描述你的程序的逻辑结构，而不是标志和命令

· 你想使用库

· 您想使用其他工具来帮助您编写代码 moc ProtoBuf

· 你想使用单元测试

**cmake特性**

· 自动搜索可能需要的程序、库和头文件的能力

· 独立的构建目录，可以安全清理

· 创建复杂的自定义命令，例如qt moc uic

· 配置时选择可选组件的能力

· 从简单的文本文件（CMakeLists.txt）自动生成工作区和项目的能力

· 在静态和共享构建之间轻松切换的能力

· 在大多数平台上自动生成文件依赖项并支持并行构建

**CMake配置源码 CMakeLists.txt**

```txt
# CMakeLists.txt 
cmake_minimum_required (VERSION 3.0) 
project (first_cmake) 
add_executable(first_cmake first_cmake.cpp) 
```

#### 使用

安装make的clang

```shell
brew install make

brew install clang

brew install clang++
```

解压cmake源码并编译

```shell
tar -xvf cmake-3.23.1.tar.gz

cd cmake-3.23.1

./configure

make -j32
```

安装编译好的cmake

```shell
sudo make install
```

查看cmake版本





## 动态库和静态库

静态库和动态库的区别

- 静态库的扩展名一般为“.a”或“.lib”；动态库的扩展名一般为“.so”或“.dll”。
- 静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行（如果程序编译成功，即使离开静态库，程序也是可以独立运行）。
- 动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行（如果程序编译成功，必须要有动态库的存在程序才可以运行，比如使用windows运行一些游戏程序时，会报缺少 .dll 文件的错误，导致程序无法正常运行，其实就是缺少动态库）。

生成动态库和静态库



使用外部动态库

使用外部静态库



　

## 

　　

## 

　　

## mold

GCC 12 编译器带来了一个小改动：支持使用 Mold 链接器（Mold linker）。作为高速链接器的 Mold 1.0 于上周发布，它可以提供比 GNU 旧的 Gold 链接器，甚至 LLVM 的 LLD 更好的性能

Mold 由 Rui Ueyama 设计，他最初是致力于研究 LLVM 的链接器。在 Mold 1.0 中，Rui 觉得 Mold 链接器已经可以投入生产了，并且 Mold 的测试结果提供了令人印象深刻的强大性能

https://github.com/rui314/mold



## ccache



https://github.com/ccache/ccache

### sccache

云端的ccache，编译缓存

安装

```shell
cargo install sccache
```

使用

```shell
sccache gcc -o foo.o -c foo.c
```



https://github.com/mozilla/sccache/

