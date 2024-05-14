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

## 实现命令行



https://suibianxiedianer.github.io/rust-cli-book-zh_CN/tutorial/cli-args_zh.html



## Typeshare

支持在其他语言中无缝接入Rust中的类型, 支持语言包括typescript、kotlin、swift、scala、go

在Cargo.toml中加入依赖

```toml
typeshare = "1.0.0"
```

rust中的类型

```rust
// Rust type definitions

#[typeshare]
struct MyStruct {
    my_name: String,
    my_age: u32,
}

#[typeshare]
#[serde(tag = "type", content = "content")]
enum MyEnum {
    MyVariant(bool),
    MyOtherVariant,
    MyNumber(u32),
}
```

生成的typescript类型

```typescript
// Generated Typescript definitions

export interface MyStruct {
    my_name: string;
    my_age: number;
}

export type MyEnum = 
    | { type: "MyVariant", content: boolean }
    | { type: "MyOtherVariant", content: undefined }
    | { type: "MyNumber", content: number };
```

## webAssembly

### exitsm

webAssembly构建工具

https://github.com/extism/extism

## firecracker

https://github.com/firecracker-microvm/firecracker



## trustfall

查询

https://github.com/obi1kenobi/trustfall



## delta

diff高亮

https://github.com/dandavison/delta



## ast-grep

https://github.com/ast-grep/ast-grep

## 模版引擎

### lol-html

https://github.com/cloudflare/lol-html

使用

```rust
use lol_html::{element, HtmlRewriter, Settings};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut output = vec![];

    let mut rewriter = HtmlRewriter::new(
        Settings {
            element_content_handlers: vec![
                element!("a[href]", |el| {
                    let href = el
                        .get_attribute("href")
                        .expect("href was required")
                        .replace("http:", "https:");

                    el.set_attribute("href", &href)?;

                    Ok(())
                })
            ],
            ..Settings::default()
        },
        |c: &[u8]| output.extend_from_slice(c)
    );

    rewriter.write(b"<div><a href=")?;
    rewriter.write(b"http://example.com>")?;
    rewriter.write(b"</a></div>")?;
    rewriter.end()?;

    assert_eq!(
        String::from_utf8(output)?,
        r#"<div><a href="https://example.com"></a></div>"#
    );
    Ok(())
}
```

### maud

https://maud.lambda.xyz/getting-started.html
