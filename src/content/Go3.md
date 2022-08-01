---
title: Golang语言开发(三)
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - Web,IT,Go
toc: true
thumbnail: https://s1.ax1x.com/2020/04/20/J1Iu4O.th.jpg
---

　　高性能开发

<!--more-->

## 并发编程

### 协程

一个应用程序是运行在机器上的一个进程；进程是一个运行在自己内存地址空间里的独立执行体。一个进程由一个或多个操作系统线程组成，这些线程其实是共享同一个内存地址空间的一起工作的执行体。几乎所有'正式'的程序都是多线程的，以便让用户或计算机不必等待，或者能够同时服务多个请求（如 Web 服务器），或增加性能和吞吐量（例如，通过对不同的数据集并行执行代码）。一个并发程序可以在一个处理器或者内核上使用多个线程来执行任务，但是只有同一个程序在某个时间点同时运行在多核或者多处理器上才是真正的并行。

并行是一种通过使用多处理器以提高速度的能力。所以并发程序可以是并行的，也可以不是。

公认的，使用多线程的应用难以做到准确，最主要的问题是内存中的数据共享，它们会被多线程以无法预知的方式进行操作，导致一些无法重现或者随机的结果（称作 `竞态`）。

**不要使用全局变量或者共享内存，它们会给你的代码在并发运算的时候带来危险。**

解决之道在于同步不同的线程，对数据加锁，这样同时就只有一个线程可以变更数据。在 Go 的标准库 `sync` 中有一些工具用来在低级别的代码中实现加锁；我们在第 [9.3](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/09.3.md) 节中讨论过这个问题。不过过去的软件开发经验告诉我们这会带来更高的复杂度，更容易使代码出错以及更低的性能，所以这个经典的方法明显不再适合现代多核/多处理器编程：`thread-per-connection` 模型不够有效。

Go 更倾向于其他的方式，在诸多比较合适的范式中，有个被称作 `Communicating Sequential Processes（顺序通信处理）`（CSP, C. Hoare 发明的）还有一个叫做 `message passing-model（消息传递）`

在 Go 中，应用程序并发处理的部分被称作 `goroutines（协程）`，它可以进行更有效的并发运算。在协程和操作系统线程之间并无一对一的关系：协程是根据一个或多个线程的可用性，映射（多路复用，执行于）在他们之上的；协程调度器在 Go 运行时很好的完成了这个工作。

协程工作在相同的地址空间中，所以共享内存的方式一定是同步的；这个可以使用 `sync` 包来实现（参见第 [9.3](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/09.3.md) 节），不过我们很不鼓励这样做：Go 使用 `channels` 来同步协程

当系统调用（比如等待 I/O）阻塞协程时，其他协程会继续在其他线程上工作。协程的设计隐藏了许多线程创建和管理方面的复杂工作。

协程是轻量的，比线程更轻。它们痕迹非常不明显（使用少量的内存和资源）：使用 4K 的栈内存就可以在堆中创建它们。因为创建非常廉价，必要的时候可以轻松创建并运行大量的协程（在同一个地址空间中 100,000 个连续的协程）。并且它们对栈进行了分割，从而动态的增加（或缩减）内存的使用；栈的管理是自动的，但不是由垃圾回收器管理的，而是在协程退出后自动释放。

协程可以运行在多个操作系统线程之间，也可以运行在线程之内，让你可以很小的内存占用就可以处理大量的任务。由于操作系统线程上的协程时间片，你可以使用少量的操作系统线程就能拥有任意多个提供服务的协程，而且 Go 运行时可以聪明的意识到哪些协程被阻塞了，暂时搁置它们并处理其他协程。

存在两种并发方式：确定性的（明确定义排序）和非确定性的（加锁/互斥从而未定义排序）。Go 的协程和通道理所当然的支持确定性的并发方式（例如通道具有一个 sender 和一个 receiver）

协程是通过使用关键字 `go` 调用（执行）一个函数或者方法来实现的（也可以是匿名或者 lambda 函数）。这样会在当前的计算过程中开始一个同时进行的函数，在相同的地址空间中并且分配了独立的栈，比如：`go sum(bigArray)`，在后台计算总和。

协程的栈会根据需要进行伸缩，不出现栈溢出；开发者不需要关心栈的大小。当协程结束的时候，它会静默退出：用来启动这个协程的函数不会得到任何的返回值。

任何 Go 程序都必须有的 `main()` 函数也可以看做是一个协程，尽管它并没有通过 `go` 来启动。协程可以在程序初始化的过程中运行（在 `init()` 函数中）。

在一个协程中，比如它需要进行非常密集的运算，你可以在运算循环中周期的使用 `runtime.Gosched()`：这会让出处理器，允许运行其他协程；它并不会使当前协程挂起，所以它会自动恢复执行。使用 `Gosched()` 可以使计算均匀分布，使通信不至于迟迟得不到响应。



### 调度器

Go 语言在并发编程方面有强大的能力，这离不开语言层面对并发编程的支持。

多个线程可以属于同一个进程并共享内存空间。因为多线程不需要创建新的虚拟内存空间，所以它们也不需要内存管理单元处理上下文的切换，线程之间的通信也正是基于共享的内存进行的，与重量级的进程相比，线程显得比较轻量。

虽然线程比较轻量，但是在调度时也有比较大的额外开销。每个线程会都占用 1 兆以上的内存空间，在对线程进行切换时不止会消耗较多的内存，恢复寄存器中的内容还需要向操作系统申请或者销毁对应的资源，每一次线程上下文的切换都需要消耗 ~1us 左右的时间[1](https://draveness.me/golang/docs/part3-runtime/ch06-concurrency/golang-goroutine/#fn:1)，但是 Go 调度器对 Goroutine 的上下文切换约为 ~0.2us，减少了 80% 的额外开销

1. G — 表示 Goroutine，它是一个待执行的任务；
2. M — 表示操作系统的线程，它由操作系统的调度器调度和管理；
3. P — 表示处理器，它可以被看做运行在线程上的本地调度器；

Goroutine 就是 Go 语言调度器中待执行的任务，它在运行时调度器中的地位与线程在操作系统中差不多，但是它占用了更小的内存空间，也降低了上下文切换的开销。

Goroutine 只存在于 Go 语言的运行时，它是 Go 语言在用户态提供的线程，作为一种粒度更细的资源调度单元，如果使用得当能够在高并发的场景下更高效地利用机器的 CPU。

Goroutine 在 Go 语言运行时使用私有结构体 [`runtime.g`](https://github.com/golang/go/blob/753d56d3642eb83848aa39e65982a9fc77e722d7/src/runtime/runtime2.go#L395) 表示。这个私有结构体非常复杂，总共包含 40 多个用于表示各种状态的成员变量，



### 定时器



### 管道

Go 语言中最常见的、也是经常被人提及的设计模式就是 — 不要通过共享内存的方式进行通信，而是应该通过通信的方式共享内存。在很多主流的编程语言中，多个线程传递数据的方式一般都是共享内存，为了解决线程冲突的问题，我们需要限制同一时间能够读写这些变量的线程数量，这与 Go 语言鼓励的方式并不相同。



### 网络轮询器

大部分的服务都是 I/O 密集型的，应用程序会花费大量时间等待 I/O 操作执行完成。网络轮询器就是 Go 语言运行时用来处理 I/O 操作的关键组件，它使用了操作系统提供的 I/O 多路复用机制增强程序的并发处理能力。

网络轮询器不仅用于监控网络 I/O，还能用于监控文件的 I/O，它利用了操作系统提供的 I/O 多路复用模型来提升 I/O 设备的利用率以及程序的性能。

操作系统中包含阻塞 I/O、非阻塞 I/O、信号驱动 I/O 与异步 I/O 以及 I/O 多路复用五种 I/O 模型。我们在本节中会介绍上述五种模型中的三种：

- 阻塞 I/O 模型；
- 非阻塞 I/O 模型；
- I/O 多路复用模型；

阻塞I/O

阻塞 I/O 是最常见的 I/O 模型，对文件和网络的读写操作在默认情况下都是阻塞的。当我们通过 `read` 或者 `write` 等系统调用对文件进行读写时，应用程序就会被阻塞

非阻塞 I/O

当进程把一个文件描述符设置成非阻塞时，执行 `read` 和 `write` 等 I/O 操作就会立刻返回。在 C 语言中，我们可以使用如下所示的代码片段将一个文件描述符设置成非阻塞的



### 系统监控

很多系统中都有守护进程，它们能够在后台监控系统的运行状态，在出现意外情况时及时响应。系统监控是 Go 语言运行时的重要组成部分，它会每隔一段时间检查 Go 语言运行时，确保程序没有进入异常状态。

守护进程是很有效的设计，它在整个系统的生命周期中都会存在，会随着系统的启动而启动，系统的结束而结束。在操作系统和 Kubernetes 中，我们经常会将数据库服务、日志服务以及监控服务等进程作为守护进程运行。

Go 语言的系统监控也起到了很重要的作用，它在内部启动了一个不会中止的循环，在循环的内部会轮询网络、抢占长期运行或者处于系统调用的 Goroutine 以及触发垃圾回收，通过这些行为，它能够让系统的运行状态变得更健康。



## 内存管理

内存管理一般包含三个不同的组件，分别是用户程序（Mutator）、分配器（Allocator）和收集器（Collector）[1](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-memory-allocator/#fn:1)，当用户程序申请内存时，它会通过内存分配器申请新的内存，而分配器会负责从堆中初始化相应的内存区域。

### 分配方法

编程语言的内存分配器一般包含两种分配方法，一种是线性分配器（Sequential Allocator，Bump Allocator），另一种是空闲链表分配器（Free-List Allocator），这两种分配方法有着不同的实现机制和特性，本节会依次介绍它们的分配过程。

线性分配（Bump Allocator）是一种高效的内存分配方法，但是有较大的局限性。当我们在编程语言中使用线性分配器，我们只需要在内存中维护一个指向内存特定位置的指针，当用户程序申请内存时，分配器只需要检查剩余的空闲内存、返回分配的内存区域并修改指针在内存中的位置，即移动下图中的指针：

根据线性分配器的原理，我们可以推测它有较快的执行速度，以及较低的实现复杂度；但是线性分配器无法在内存被释放时重用内存。

正是因为线性分配器的这种特性，我们需要合适的垃圾回收算法配合使用。标记压缩（Mark-Compact）、复制回收（Copying GC）和分代回收（Generational GC）等算法可以通过拷贝的方式整理存活对象的碎片，将空闲内存定期合并，这样就能利用线性分配器的效率提升内存分配器的性能了。

不同的内存块以链表的方式连接，所以使用这种方式分配内存的分配器可以重新利用回收的资源，但是因为分配内存时需要遍历链表，所以它的时间复杂度就是 𝑂(𝑛)O(n)。空闲链表分配器可以选择不同的策略在链表中的内存块中进行选择，最常见的就是以下四种方式：

- 首次适应（First-Fit）— 从链表头开始遍历，选择第一个大小大于申请内存的内存块；
- 循环首次适应（Next-Fit）— 从上次遍历的结束位置开始遍历，选择第一个大小大于申请内存的内存块；
- 最优适应（Best-Fit）— 从链表头遍历整个链表，选择最合适的内存块；
- 隔离适应（Segregated-Fit）— 将内存分割成多个链表，每个链表中的内存块大小相同，申请内存时先找到满足条件的链表，再从链表中选择合适的内存块；

Go语言的内存分配策略与第四章策略相似，该策略会将内存分割成由 4、8、16、32 字节的内存块组成的链表，当我们向内存分配器申请 8 字节的内存时，我们会在上图中的第二个链表找到空闲的内存块并返回。隔离适应的分配策略减少了需要遍历的内存块数量，提高了内存分配的效率。

### 垃圾回收器

编程语言通常会使用手动和自动两种方式管理内存，C、C++ 以及 Rust 等编程语言使用手动的方式管理内存[2](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-garbage-collector/#fn:2)，工程师需要主动申请或者释放内存；而 Python、Ruby、Java 和 Go 等语言使用自动的内存管理系统，一般都是垃圾收集机制，



### 栈内存管理

栈区的内存一般由编译器自动进行分配和释放，其中存储着函数的入参以及局部变量，这些参数会随着函数的创建而创建，函数的返回而消亡，一般不会在程序中长期存在，这种线性的内存分配策略有着极高地效率，但是工程师也往往不能控制栈内存的分配，这部分工作基本都是由编译器自动完成的。

栈寄存器在是 CPU 寄存器中的一种，它的主要作用是跟踪函数的调用栈[2](https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-stack-management/#fn:2)，Go 语言的汇编代码中包含 BP 和 SP 两个栈寄存器，它们分别存储了栈的基址指针和栈顶的地址，栈内存与函数调用的关系非常紧密，我们在函数调用一节中曾经介绍过栈区，BP 和 SP 之间的内存就是当前函数的调用栈。

多数架构上默认栈大小都在 2 ~ 4 MB 左右，极少数架构会使用 32 MB 作为默认大小，用户程序可以在分配的栈上存储函数参数和局部变量。然而这个固定的栈大小在某些场景下可能不是一个合适的值，如果一个程序需要同时运行几百个甚至上千个线程，那么这些线程中的绝大部分都只会用到很少的栈空间，而如果函数的调用栈非常深，固定的栈大小也无法满足用户程序的需求。

Go 语言中的执行栈由 [`runtime.stack`](https://github.com/golang/go/blob/a38a917aee626a9b9d5ce2b93964f586bf759ea0/src/runtime/runtime2.go#L382) 结构体表示，该结构体中只包含两个字段，分别表示栈的顶部和栈的底部，每个栈结构体都表示范围 `[lo, hi)` 的内存空间




