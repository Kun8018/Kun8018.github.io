---
title: Python
date: 2020-03-011 21:40:33
categories: IT
tags:
    - IT，Web,数据库
toc: true
thumbnail: 
---

　　python语法简单，应用广泛，好好学习。

<!--more-->

https://github.com/jackfrued/Python-100-Days

## Python库

### mamba

包管理器

```shell
brew install micromamba
```

### conda



### miniforge

https://github.com/conda-forge/miniforge

Miniforge 是一个轻量级、社区驱动的包和环境管理器，它是 **Conda** 的一个精简发行版，旨在提供一种比 Anaconda 更小的安装包，并使用 **[conda-forge](https://www.google.com/search?q=conda-forge&oq=miniforge是什么&gs_lcrp=EgZjaHJvbWUyCQgAEEUYORiABDIKCAEQABiABBiiBDIKCAIQABiABBiiBDIKCAMQABiABBiiBDIKCAQQABiABBiiBNIBCDE0MzJqMGo3qAIAsAIA&sourceid=chrome&ie=UTF-8&mstk=AUtExfC-o0aXLarOYSkYN-o6-mIuUEEt4TZ7WMuFqV8T7SE_p8VUzslA0FW9a80S8S7Q2j9otgn389ninGJcHL0fGAJM5nLzfLu6ypkvkKjuYos3goW2DzXEdRFqvufGGUW4zUbgVreyBcRQz5dJVUI_Apt9NDR0ZJgEbaA9zU3OvIF8v3YXVKbr1S123RcCcR8pmFXIZH83fM8GWRg3bDqiHFIfJw03qXBoOywfsoK-le7JL86IqC54seQXtMZ39-Z1FRtiDoBTadSsHBQuAVGMkEOU&csui=3&ved=2ahUKEwj1qfqn3KKRAxVHdvUHHfH6DEIQgK4QegQIARAB)** 作为默认的包源。它的主要优点是灵活性和效率高，特别适合那些不需要预装大量库的用户，例如数据科学家或开发者

主要特点

- **轻量级**: Miniforge 不包含 Anaconda 的预装包集合，只提供核心的 `conda` 包管理器，用户可以根据自己的需求安装所需的库和工具，从而节省磁盘空间和安装时间。
- **社区驱动**: 由 conda-forge 社区维护，默认使用 conda-forge 作为软件源，提供了一个庞大且活跃的开源软件包集合。
- **跨平台和多架构**: 支持多种平台，如 Windows、macOS 和 Linux，并对多种 CPU 架构有良好支持，特别是对 Apple Silicon (M1/M2/M3) 等 ARM 架构设备。
- **灵活性**: 用户可以自由选择安装的工具，例如可以选择基于 PyPy 的版本（Miniforge-pypy3）或集成 Mamba（Mambaforge）以获得更快的包管理速度。 

与 Anaconda 和 Miniconda 的区别

- **Miniforge** 和 **Miniconda** 都是 Anaconda 的轻量级版本。
- **Miniforge** 最大的不同在于它默认使用 `conda-forge` 渠道，而 **Miniconda** 则使用 `defaults` 渠道（由 Anaconda 维护）。
- 对于希望避开 Anaconda 庞大安装包的用户来说，Miniforge 是一种轻量级的替代方案



### paramiko

https://github.com/paramiko/paramiko



### gevent

python 程序实现的一种单线程下的多任务执行调度器，简单来说在一个线程里，先后执行 AB 两个任务，但是当 A 遇到耗时操作（网络等待、文件读写等），这个时候 gevent 会让 A 继续执行，但是同时也会开始执行 B 任务，如果 B 在遇到耗时操作同时 A 又执行完了耗时操作，gevent 又继续执行 A



### asyncio

**event_loop** 事件循环：理解为一个循环的池，里面存放一些 async 关键词定义的协程函数，只有放到循环池里才能执行

**coroutine** 协程：协程对象，指一个使用 async 关键字定义的函数，它的调用不会立即执行函数，而是会返回一个协程对象。协程对象需要注册到事件循环，由事件循环调用。

**task** 任务：一个协程对象就是一个原生可以挂起的函数，任务则是对协程进一步封装，其中包含任务的各种状态。

**future**：代表将来执行或没有执行的任务的结果。它和 task 上没有本质的区别

**async/await** 关键字：python3.5 用于定义协程的关键字，async 定义一个协程，await 用于挂起阻塞的异步调用接口。



### hiredis



### grequests



### celery

python分布式队列库

https://docs.celeryq.dev/en/stable/index.html



### uv

python包管理工具

https://github.com/astral-sh/uv



### ruff

python linter

https://astral.sh/ruff



### psutil

获取系统内存、cpu、磁盘等信息

https://github.com/giampaolo/psutil



### rich

Python终端脚本展示优化

安装

```shell
python -m pip install rich
```

使用

```python
from rich import print

print("Hello, [bold magenta]World[/bold magenta]!", ":vampire:", locals())
```



### markItdown

把pdf、doc等文档转换为markdown

https://github.com/microsoft/markitdown



### logging

日志模块

```python
import logging

# 配置日志
logging.basicConfig(level=logging.INFO)  # 设置最低日志级别
logging.debug('这是调试信息')    # 不会显示（默认级别为 INFO）
logging.info('这是普通信息')     # INFO 级别
logging.warning('这是警告信息')  # WARNING 级别
logging.error('这是错误信息')    # ERROR 级别
logging.critical('这是严重错误') # CRITICAL 级别
```



### nodezator

https://github.com/IndieSmiths/nodezator?tab=readme-ov-file



### pickle

pickle 名词是**泡菜**，动词是**腌渍**的意思。可以理解为把东西腌起来保存成文件，要用的时候读出来洗洗再用。

python的pickle模块实现了基本的**数据序列化和反序列化**

序列化对象可以在磁盘上保存对象，并在需要的时候读取出来。任何对象都可以执行序列化操作。

**pickling**是将Python对象层次结构转换为字节流的过程。

**unpickling**是反向操作，从而将字节流（来自二进制文件或类似字节的对象）转换回对象层次结构

优点：

pickle提供了一个简单的**持久化**功能。可以将对象以文件的形式**存放在磁盘上**。

通过pickle模块的序列化操作我们能够将程序中运行的对象信息保存到文件中去，**永久存储**。

python中**几乎所有的数据类型**（列表，字典，集合，类等）都可以用pickle来序列化。

**没有外部标准强加的限制**，例如JSON或XDR（不能代表指针共享）。

默认情况下，pickle数据格式使用**相对紧凑的二进制**表示。如果需要最佳尺寸特征，则可以有效地压缩数据。

pickle可以**表示极其庞大的Python类型**（其中许多是自动的，通过巧妙地使用Python的内省工具；复杂的案例可以通过实现特定的对象API来解决）。

通过pickle模块的反序列化操作，我们能够从文件中创建上一次程序保存的对象。

缺点：

pickle模块只能在python中使用，**仅限于传输的两端都是python的情况**，且需要尽量保持两端的版本一致。
非Python程序可能无法重建pickled Python对象。

pickle序列化后的数据，**可读性差**，人一般无法识别

与JSON的区别

1.JSON是一种文本序列化格式（它输出unicode文本，虽然大部分时间它被编码utf-8），而pickle是二进制序列化格式。

2、JSON是人类可读的，而pickle则不是。

3、JSON是可互操作的，并且在Python生态系统之外广泛使用，而pickle是特定于Python的。

默认情况下，JSON只能表示Python内置类型的子集，而**不能表示自定义类**

主要特点：

- **二进制格式**：保存Python对象的内存状态
- **Python专用**：主要用于Python程序间数据交换
- **保持对象结构**：保存完整的类实例、函数等复杂对象

常见用途：

1. **保存机器学习模型**（scikit-learn、TensorFlow/PyTorch的检查点）
2. **存储预处理数据**（特征工程结果、数据集）
3. **缓存计算结果**（避免重复计算）
4. **保存程序状态**

### npz文件

文件是NumPy库创建的一种压缩存档文件，用于**存储一个或多个NumPy数组**，它本质上是一个压缩的ZIP文件，方便高效地打包和传输大量数值数据，主要在Python科学计算、数据分析和机器学习中应用，可以用`numpy.load()`函数轻松加载

**主要特点**

- **多数组存储**：可以把多个相关的数组保存在一个`.npz`文件中.
- **压缩**：使用`np.savez_compressed()`可以生成压缩的`.npz`文件，节省存储空间.
- **便捷**：方便了数据集的分享、备份和管理



## Django入门

### Django版本与python版本对应关系

| Django版本 | Python版本                                |
| ---------- | ----------------------------------------- |
| 1.8        | 2.7、3.2、3.3、3.4、3.5                   |
| 1.9、1.10  | 2.7、3.4、3.5                             |
| 1.11       | 2.7、3.4、3.5、3.6、3.7（Django 1.11.17） |
| 2.0        | 3.4、3.5、3.6、3.7                        |
| 2.1        | 3.5、3.6、3.7                             |
| 2.2        | 3.5、3.6、3.7、3.8（Django 2.2.8）        |
| 3.0        | 3.6、3.7、3.8                             |

### 创建应用

执行命令

```shell
python manage.py startapp first
```



### DRF

在Django项目中，如果要实现REST架构，即将网站的资源发布成REST风格的API接口，可以使用著名的三方库`djangorestframework` ，我们通常将其简称为DRF。

安装DRF

```shell
pip install djangorestframework
```

配置DRF

```python
INSTALLED_APPS = [
    'rest_framework',
]

# 下面的配置根据项目需要进行设置
REST_FRAMEWORK = {
    # 配置默认页面大小
    # 'PAGE_SIZE': 10,
    # 配置默认的分页类
    # 'DEFAULT_PAGINATION_CLASS': '...',
    # 配置异常处理器
    # 'EXCEPTION_HANDLER': '...',
    # 配置默认解析器
    # 'DEFAULT_PARSER_CLASSES': (
    #     'rest_framework.parsers.JSONParser',
    #     'rest_framework.parsers.FormParser',
    #     'rest_framework.parsers.MultiPartParser',
    # ),
    # 配置默认限流类
    # 'DEFAULT_THROTTLE_CLASSES': (
    #     '...'
    # ),
    # 配置默认授权类
    # 'DEFAULT_PERMISSION_CLASSES': (
    #     '...',
    # ),
    # 配置默认认证类
    # 'DEFAULT_AUTHENTICATION_CLASSES': (
    #     '...',
    # ),
}
```



### JWT

用三方库`PyJWT`生成和验证JWT，下面是安装`PyJWT`的命令。

```shell
pip install pyjwt
```

生成令牌

```shell
payload = {
    'exp': datetime.datetime.utcnow() + datetime.timedelta(days=1),
    'userid': 10001
}
token = jwt.encode(payload, settings.SECRET_KEY).decode()
```

验证令牌

```python
try:
    token = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1OTQ4NzIzOTEsInVzZXJpZCI6MTAwMDF9.FM-bNxemWLqQQBIsRVvc4gq71y42I9m2zt5nlFxNHUo'
    payload = jwt.decode(token, settings.SECRET_KEY)
except InvalidTokenError:
    raise AuthenticationFailed('无效的令牌或令牌已经过期')
```



### 中间件



### session与cookie

在创建Django项目时，默认的配置文件`settings.py`文件中已经激活了一个名为`SessionMiddleware`的中间件（关于中间件的知识我们在后面的章节做详细讲解，这里只需要知道它的存在即可），因为这个中间件的存在，我们可以直接通过请求对象的`session`属性来操作会话对象。前面我们说过，`session`属性是一个像字典一样可以读写数据的容器对象，因此我们可以使用“键值对”的方式来保留用户数据。与此同时，`SessionMiddleware`中间件还封装了对cookie的操作，在cookie中保存了sessionid，这一点我们在上面已经提到过了。

在默认情况下，Django将session的数据序列化后保存在关系型数据库中，在Django 1.6以后的版本中，默认的序列化数据的方式是JSON序列化，而在此之前一直使用Pickle序列化。JSON序列化和Pickle序列化的差别在于前者将对象序列化为字符串（字符形式），而后者将对象序列化为字节串（二进制形式），因为安全方面的原因，JSON序列化成为了目前Django框架默认序列化数据的方式，这就要求在我们保存在session中的数据必须是能够JSON序列化的，否则就会引发异常。还有一点需要说明的是，使用关系型数据库保存session中的数据在大多数时候并不是最好的选择，因为数据库可能会承受巨大的压力而成为系统性能的瓶颈，在后面的章节中我们会告诉大家如何将session保存到缓存服务中以提升系统的性能。

## Robyn

https://github.com/sparckles/robyn

Robyn 是一款令人兴奋的异步 Python Web 框架，其核心运行时基于 Rust 构建，这使得它在性能方面拥有显著优势

Robyn 的核心竞争力在于其 Rust 运行时。Rust 作为一门注重内存安全和性能的系统编程语言，为 Robyn 提供了坚实的底层支撑。这使得 Robyn 能够处理大量的并发请求，并保持极低的延迟，在 TechEmpower 基准测试中表现出色。这种架构选择，让 Robyn 能够充分利用多核处理器的优势，实现真正的多核扩展性，在高负载场景下依然保持高效稳定的运行

Robyn 提供了简洁直观的 API，即使是 Python 新手也能快速上手。只需几行代码，你就可以创建一个简单的 Web 服务器

```python
from robyn import Robyn

app = Robyn(__file__)

@app.get("/")
async def h(request):
    return "Hello, world!"

app.start(port=8080)
```

Robyn 并非只是一个简单的 Web 服务器，它还提供了许多高级特性，例如：

- • **异步功能支持:** 充分利用 Python 的异步特性，提升服务器的并发处理能力。
- • **动态 URL 路由:** 灵活地定义 URL 路径和相应的处理函数。
- • **OpenAPI 自动生成:** 方便地生成 API 文档，便于开发者理解和使用 API。
- • **中间件支持:** 方便地扩展服务器功能，例如日志记录、身份验证等。
- • **内置表单数据处理:** 简化表单数据的处理过程。
- • **依赖注入:** 方便地管理和注入依赖项，提高代码的可维护性和可测试性。
- • **热重载:** 在开发过程中，修改代码后无需重启服务器，加快开发速度。
- • **WebSocket 支持:** 支持实时双向通信，构建实时应用。
- • **直接 Rust 集成:** 允许开发者直接使用 Rust 代码扩展 Robyn 的功能



## Falcon

Falcon是一个极简主义的ASGI/WSGI框架，专为构建高可靠性、高性能的REST API和微服务而设计。它注重性能、正确性和可扩展性，在处理高并发请求方面表现出色。不同于其他Python Web框架，Falcon摒弃了繁琐的依赖和不必要的抽象，提供了一个简洁、高效的开发体验。

Falcon的设计哲学是“大道至简”，力求在保持高效率的同时，尽量减少不必要的组件和功能。这使得它具有以下几个关键优势：

- **闪电般的速度:** Falcon以其卓越的性能而闻名。它在基准测试中常常超越Django和Flask等流行框架，尤其是在结合PyPy解释器时，性能提升更为显著。Falcon的快速响应能力使其成为构建高并发API的理想选择。
- **极致的可靠性:** Falcon注重稳定性和向后兼容性。框架本身经过严格测试，并尽可能避免引入破坏性变更。其对标准库的依赖性最小化，降低了安全风险，并减少了因依赖冲突导致的问题。
- **灵活的定制性:** Falcon赋予开发者极高的自由度，允许对API的各个方面进行精细化的控制。它不强制使用特定的架构模式或开发流程，开发者可以根据自身需求选择合适的工具和方法。
- **简洁易用的API:** Falcon的API设计简洁明了，易于学习和使用。其清晰的文档和大量的示例代码，让开发者能够快速上手，并专注于业务逻辑的实现。
- **支持 ASGI 和 WSGI:** Falcon 同时支持 ASGI 和 WSGI 规范，能够与各种现代化的异步服务器和传统的同步服务器兼容，扩展性极强。

使用

```python
import falcon

class ThingsResource:
    def on_get(self, req, resp):
        resp.text = "Hello, Falcon!"

app = falcon.App()
app.add_route('/things', ThingsResource())
```

Falcon 提供了强大的中间件机制，允许开发者在请求处理过程中插入自定义逻辑，例如身份验证、日志记录和数据转换等。 它还提供了一套灵活的错误处理机制，方便开发者处理各种异常情况



## Flet

客户端全栈项目

https://github.com/flet-dev/flet

```python
import flet
from flet import IconButton, Page, Row, TextField, icons

def main(page: Page):
    page.title = "Flet counter example"
    page.vertical_alignment = "center"

    txt_number = TextField(value="0", text_align="right", width=100)

    def minus_click(e):
        txt_number.value = str(int(txt_number.value) - 1)
        page.update()

    def plus_click(e):
        txt_number.value = str(int(txt_number.value) + 1)
        page.update()

    page.add(
        Row(
            [
                IconButton(icons.REMOVE, on_click=minus_click),
                txt_number,
                IconButton(icons.ADD, on_click=plus_click),
            ],
            alignment="center",
        )
    )

flet.app(target=main)
```





## Redis

通常情况下，Web应用的性能瓶颈都会出现在关系型数据库上，当并发访问量较大时，如果所有的请求都需要通过关系型数据库完成数据持久化操作，那么数据库一定会不堪重负。优化Web应用性能最为重要的一点就是使用缓存，就是把哪些数据体量不大但访问频率非常高的数据提前加载到缓存服务器中，这又是典型的空间换时间的方法。通常缓存服务器都是直接将数据置于内存中而且使用了非常高效的数据存取策略（例如：键值对方式），在读写性能上是远远优于传统的关系型数据库的，因此我们可以让Web应用接入缓存服务器来优化其性能，一个非常好的选择就是使用Redis。



## 数据库



### sqlalchemy

https://github.com/sqlalchemy/sqlalchemy



### sqlmodel

https://github.com/tiangolo/sqlmodel

### asyncpg

https://github.com/MagicStack/asyncpg



### psycopg2

https://github.com/psycopg/psycopg2



### psycopg

https://github.com/psycopg/psycopg



## OCR

### Surya

安装

```shell
pip install surya-ocr
```

https://github.com/VikParuchuri/surya



## GUI

### textual

```python
from textual.app import App, ComposeResult
from textual.widgets import Footer, Header


class StopwatchApp(App):
    """A Textual app to manage stopwatches."""

    BINDINGS = [("d", "toggle_dark", "Toggle dark mode")]

    def compose(self) -> ComposeResult:
        """Create child widgets for the app."""
        yield Header()
        yield Footer()

    def action_toggle_dark(self) -> None:
        """An action to toggle dark mode."""
        self.theme = (
            "textual-dark" if self.theme == "textual-light" else "textual-light"
        )


if __name__ == "__main__":
    app = StopwatchApp()
    app.run()
```



## 大数据

https://blog.csdn.net/wally21st/article/details/77688755?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param



## 新语言Mojo

https://github.com/modularml/mojo



## 新语言Jax

https://github.com/google/jax



## 新性能python编译器codon

https://github.com/exaloop/codon



## 瘦脸算法

自动瘦脸与眼睛放大可以算作图像局部扭曲算法的一个应用，

大概流程如下：

1.使用dlib检测出人脸关键点

2.使用Interactive Image Warping 局部平移算法实现瘦脸

```python
import dlib
import cv2
import numpy as np
import math
predictor_path='shape_predictor_68_face_landmarks.dat'
 
#使用dlib自带的frontal_face_detector作为我们的特征提取器
detector = dlib.get_frontal_face_detector()
predictor = dlib.shape_predictor(predictor_path)

def landmark_dec_dlib_fun(img_src):
    img_gray = cv2.cvtColor(img_src,cv2.COLOR_BGR2GRAY)
 
    land_marks = []
 
    rects = detector(img_gray,0)
 
    for i in range(len(rects)):
        land_marks_node = np.matrix([[p.x,p.y] for p in predictor(img_gray,rects[i]).parts()])
        # for idx,point in enumerate(land_marks_node):
        #     # 68点坐标
        #     pos = (point[0,0],point[0,1])
        #     print(idx,pos)
        #     # 利用cv2.circle给每个特征点画一个圈，共68个
        #     cv2.circle(img_src, pos, 5, color=(0, 255, 0))
        #     # 利用cv2.putText输出1-68
        #     font = cv2.FONT_HERSHEY_SIMPLEX
        #     cv2.putText(img_src, str(idx + 1), pos, font, 0.8, (0, 0, 255), 1, cv2.LINE_AA)
        land_marks.append(land_marks_node)
 
    return land_marks
'''
方法： Interactive Image Warping 局部平移算法
'''
 
 
def localTranslationWarp(srcImg,startX,startY,endX,endY,radius):
 
    ddradius = float(radius * radius)
    copyImg = np.zeros(srcImg.shape, np.uint8)
    copyImg = srcImg.copy()
 
    # 计算公式中的|m-c|^2
    ddmc = (endX - startX) * (endX - startX) + (endY - startY) * (endY - startY)
    H, W, C = srcImg.shape
    for i in range(W):
        for j in range(H):
            #计算该点是否在形变圆的范围之内
            #优化，第一步，直接判断是会在（startX,startY)的矩阵框中
            if math.fabs(i-startX)>radius and math.fabs(j-startY)>radius:
                continue
 
            distance = ( i - startX ) * ( i - startX) + ( j - startY ) * ( j - startY )
 
            if(distance < ddradius):
                #计算出（i,j）坐标的原坐标
                #计算公式中右边平方号里的部分
                ratio=(  ddradius-distance ) / ( ddradius - distance + ddmc)
                ratio = ratio * ratio
 
                #映射原位置
                UX = i - ratio  * ( endX - startX )
                UY = j - ratio  * ( endY - startY )
 
                #根据双线性插值法得到UX，UY的值
                value = BilinearInsert(srcImg,UX,UY)
                #改变当前 i ，j的值
                copyImg[j,i] =value
 
    return copyImg

#双线性插值法
def BilinearInsert(src,ux,uy):
    w,h,c = src.shape
    if c == 3:
        x1=int(ux)
        x2=x1+1
        y1=int(uy)
        y2=y1+1
 
        part1=src[y1,x1].astype(np.float)*(float(x2)-ux)*(float(y2)-uy)
        part2=src[y1,x2].astype(np.float)*(ux-float(x1))*(float(y2)-uy)
        part3=src[y2,x1].astype(np.float) * (float(x2) - ux)*(uy-float(y1))
        part4 = src[y2,x2].astype(np.float) * (ux-float(x1)) * (uy - float(y1))
 
        insertValue=part1+part2+part3+part4
        return insertValue.astype(np.int8)

def face_thin_auto(src):
 
    landmarks = landmark_dec_dlib_fun(src)
 
    #如果未检测到人脸关键点，就不进行瘦脸
    if len(landmarks) == 0:
        return
 
    for landmarks_node in landmarks:
        left_landmark= landmarks_node[3]
        left_landmark_down=landmarks_node[5]
 
        right_landmark = landmarks_node[13]
        right_landmark_down = landmarks_node[15]
 
        endPt = landmarks_node[30]
 
 
        #计算第4个点到第6个点的距离作为瘦脸距离
        r_left=math.sqrt((left_landmark[0,0]-left_landmark_down[0,0])*(left_landmark[0,0]-left_landmark_down[0,0])+
                         (left_landmark[0,1] - left_landmark_down[0,1]) * (left_landmark[0,1] - left_landmark_down[0, 1]))
 
        # 计算第14个点到第16个点的距离作为瘦脸距离
        r_right=math.sqrt((right_landmark[0,0]-right_landmark_down[0,0])*(right_landmark[0,0]-right_landmark_down[0,0])+
                         (right_landmark[0,1] -right_landmark_down[0,1]) * (right_landmark[0,1] -right_landmark_down[0, 1]))
 
        #瘦左边脸
        thin_image = localTranslationWarp(src,left_landmark[0,0],left_landmark[0,1],endPt[0,0],endPt[0,1],r_left)
        #瘦右边脸
        thin_image = localTranslationWarp(thin_image, right_landmark[0,0], right_landmark[0,1], endPt[0,0],endPt[0,1], r_right)
 
    #显示
    cv2.imshow('thin',thin_image)
    cv2.imwrite('thin.jpg',thin_image)

def main():
    src = cv2.imread('timg4.jpg')
    cv2.imshow('src', src)
    face_thin_auto(src)
    cv2.waitKey(0)
 
if __name__ == '__main__':
    main()
```



人脸老化

- 制作皱纹、斑点纹理图,并且手动标定纹理图的关键点
- 头发分割，根据年纪改变发色
- 检测人脸关键点，通过关键点对纹理图进行形变
- 纹理图与原图进行叠加、融合

https://blog.csdn.net/danffer1985/article/details/54602585?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param

