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





## 

　

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

