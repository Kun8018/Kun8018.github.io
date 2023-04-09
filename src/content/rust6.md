---
title: rust（六）
date: 2020-03-11 21:40:33
categories: IT
tags:
    - IT,Rust,Web
toc: true
thumbnail: 
---

   rust第六篇，用rust写的通用工具

<!--more-->

## just

`just` 为您提供一种保存和运行项目特有命令的便捷方式。

命令，在此也称为配方，存储在一个名为 `justfile` 的文件中，其语法受 `make` 启发：

然后你可以用 `just RECIPE` 运行它们

```shell
$ just test-all
cc *.c -o main
./test --all
Yay, all your tests passed!
```

`just` 有很多很棒的特性，而且相比 `make` 有很多改进：

- `just` 是一个命令运行器，而不是一个构建系统，所以它避免了许多 [`make` 的复杂性和特异性](https://github.com/casey/just/blob/master/README.中文.md#just-避免了-make-的哪些特异性)。不需要 `.PHONY` 配方!
- 支持 Linux、MacOS 和 Windows，而且无需额外的依赖。(尽管如果你的系统没有 `sh`，你需要 [选择一个不同的 Shell](https://github.com/casey/just/blob/master/README.中文.md#shell))。
- 错误具体且富有参考价值，语法错误将会与产生它们的上下文一起被报告。
- 配方可以接受 [命令行参数](https://github.com/casey/just/blob/master/README.中文.md#配方参数)。
- 错误会尽可能被静态地解决。未知的配方和循环依赖关系会在运行之前被报告。
- `just` 可以 [加载`.env`文件](https://github.com/casey/just/blob/master/README.中文.md#env-集成)，简化环境变量注入。
- 配方可以在 [命令行中列出](https://github.com/casey/just/blob/master/README.中文.md#列出可用的配方)。
- 命令行自动补全脚本 [支持大多数流行的 Shell](https://github.com/casey/just/blob/master/README.中文.md#shell-自动补全脚本)。
- 配方可以用 [任意语言](https://github.com/casey/just/blob/master/README.中文.md#用其他语言书写配方) 编写，如 Python 或 NodeJS。
- `just` 可以从任何子目录中调用，而不仅仅是包含 `justfile` 的目录。



https://github.com/casey/just
