---
title: scala开发
date: 2020-03-06 21:40:33
categories: IT
tags:
    - IT,Web，
toc: true
thumbnail: 
---

​		后端开发新语言-scala

<!--more-->

## Scala

Scala 是一门多范式（multi-paradigm）的编程语言，设计初衷是要集成面向对象编程和函数式编程的各种特性。

Scala 运行在 Java 虚拟机上，并兼容现有的 Java 程序。

Scala 源代码被编译成 Java 字节码，所以它可以运行于 JVM 之上，并可以调用现有的 Java 类库。

## Hello world

```scala
object HelloWorld {
    def main(args: Array[String]): Unit = {
        println("Hello, world!")
    }
}
```

运行语言规范

```shell
$ scalac HelloWorld.scala  // 把源码编译为字节码
$ scala HelloWorld  // 把字节码放到虚拟机中解释运行
```

## conductor

微服务

https://github.com/Netflix/conductor



## 资源

https://static.runoob.com/download/Scala%E8%AF%AD%E8%A8%80%E8%A7%84%E8%8C%83.pdf

https://draveness.me/elixir-or-not/
