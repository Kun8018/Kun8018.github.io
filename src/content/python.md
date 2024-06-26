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

## 基础函数、标准包

os:提供与操作系统关联的函数

sys：通常用于命令行参数

math：数学运算

re:正则匹配

datetime：处理日期时间

### 内置函数

divmod(x,y):返回一个包含商和余数的元组

```python
divmod(7,2) //(3,1)
```



## 基础语法

### 条件语句

```python
if b > a:
  print("b is greater than a")
elif a == b:
  print("a and b are equal")
else:
  print("a is greater than b")
```

### 循环语句

for循环可以直接遍历数组和字符串

```python
fruits = ["apple", "banana", "cherry"]
for x in fruits:
  print(x)
```





### 并行赋值

python具有并行赋值的特性，可以一次性赋值多个

```python
a,b = 2,0
```

对于右边有变量的，先求出右边的表达式，再一起赋值给左边

``` python
a,b = 2,0
a,b,a = 1,a,4
>>>a,b(4,2)
```

a,b的值分别为2,0，执行并行语句时，先求出右边的右边的表达式为1,2,4，再依次赋值给左边，a先被赋值为1，最后被赋值为4

应用：反转链表的题目：

```python
def reverseList(head):
    prev = None
    while head:
        cur = head
        head = head.next
        cur.next = prev
        prev = cur
    return prev
```

并行赋值写法简化

```python
def reverseList(head):
    prev = None
    while head:
        head.next,prev,head = prev,head,head.next
    return prev
```







python中加语句

```python
if __name__ == '__main__':
```

表示该语句之后的代码只作为脚本执行，被其他软件引用时不执行此部分代码，称为主程序的入口



## 变量类型

### 字符串

join方法

join方法可以将一个字符串列表（一个列表中所有元素均为字符串）连接起来，并用指定的分割符隔开。





### 列表（list）

列表是一种有序的集合，可以存储任意类型，list里面可以是数字、字典、对象、列表等。

list方法

```python
L = range(1,5)#创建连续list，即[1,2,3,4],间隔默认为1，不包含最后一个元素
L = range(1,10,2)#创建间隔为2的连续list，即[1,3,5,7,9],同样不包含最后一个元素
L.append(var) #追加元素
L.insert(index,var)#在指定位置追加元素
L.pop(var) #返回最后一个元素，并在list中删除
L.remove(var) #删除第一次出现的该元素
L.count(var) #该元素在列表中出现的个数
L.remove(var) #删除第一次出现的该元素
L.extend(list) #追加list，即合并list到L上
L.sort() #排序
L.reverse() #倒序
len(L)#获取列表长度
del L[1] #删除指定下标的元素
del L[1:3] #删除指定下标范围的元素
```

列表允许使用列表推导式组成新的列表



### 元组（tuple）

元组与列表相似，区别在于元组的元素不能修改

列表用方括号，元组用小括号，



### 数组（array）

array 是只能够保存一种类型, 初始化的时候就决定了可以保存什么样的类型。

array 可以紧凑地表示一个基本值的数组：字符，整数，浮点数。

[1:] :去掉列表中第一个元素（下标为0），去后面的元素进行操作

[::-1]：python的slice notation的特殊用法。

b = a[i:j] 表示复制a[i]到a[j-1]，以生成新的list对象

当i缺省时，默认为0，即 a[:3]相当于 a[0:3]
当j缺省时，默认为len(alist), 即a[1:]相当于a[1:10]

当i,j都缺省时，a[:]就相当于完整复制一份a了

b = a[i:j:s]的表示：i,j与上面的一样，但s表示步进，缺省为1.
所以a[i:j:1]相当于a[i:j]
当s<0时：i缺省时，默认为-1； j缺省时，默认为-len(a)-1
所以a[::-1]相当于 a[-1:-len(a)-1:-1]，也就是从最后一个元素到第一个元素复制一遍。

数组与列表可以使用很多方法，如append、insert、pop、extend、index等

二维数组

使用*号

```python
m = n = 3
test = [[0] * m] * n
print("test =", test)

test[0][0] = 233
print("test =", test)

//输出
test = [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
test = [[233, 0, 0], [233, 0, 0], [233, 0, 0]]
```

matrix = [array] * 3操作中，只是创建3个指向array的引用，所以一旦array改变，matrix中3个list也会随之改变。

正确的方法：

列表生成式法：

```python
test = [[0 for i in range(m)] for j in range(n)]
```



### 链表





### 字典

字典是一个容器类，用来存储数据。

字典存储数据结构为key：value键值对类型，

key 必须是不可变的，一般使用字符串作为字典中的key，也可以使用数字、元组等不可变类型的值

key是唯一的 如果有多个相同的key的情况，保留key最后一次对应的值

字典中存储的数据没有顺序

定义字典

```python
dict_1={
  'name':'zhangsan',
  'age':22,
  'phone':110,
  'sex':'男'
}
dict2=dict.copy() #克隆字典，即另一个拷贝。
```

根据key取出字典中的值

```python
name=dict_1['name']
```

添加/修改数据

```python
//如果key不存在则直接添加数据，如果key存在则修改数据
dict_1['name']='lisi'
dict_1['sss']='4s'
```

删除字典元素

```python
del dict['Name']  # 删除键是'Name'的条目
dict.clear()      # 清空字典所有条目
del dict 
```

获取所有key

```python
keys=dict_1.keys()
```

获取所有value

```python
values=dict_1.values()
#for循环 取出所有的value
for value in values:
    print(value)
```

获取所有键值对

```python
items=dict_1.items()
#for循环遍历items
for item in items:
    # print(item)
    #从元组中 根据索引取出数据
    key=item[0]
    value=item[1]
    print('键：%s值：%s'%(key,value))
```

判断字典中是否有某个key

```python
#python2中 有has_key函数，可以直接调用
dict_1.has_key(key)
if 'phone' in dict_1.keys():
    print('有这个key')
else:
    print('没有这个key')
```



其他方法

比较两个字典元素：cmp(dict1,dict2)

计算字典元素个数：len(dict)

输出字典可打印的字符串表示: str(dict)

返回输入的变量类型，如果变量是字典就返回字典类型:type(variable)

## self参数

python规定不管是构造方法还是实例方法最少要包含一个参数，于是约定俗成self，self指向类自身，对象可以通过self调用类里的方法

函数调用self也表示调用自身



## 进程与线程

Python既支持多进程又支持多线程，因此使用Python实现并发编程主要有3种方式：多进程、多线程、多进程+多线程。

Unix和Linux操作系统上提供了`fork()`系统调用来创建进程，调用`fork()`函数的是父进程，创建出的是子进程，子进程是父进程的一个拷贝，但是子进程拥有自己的PID。`fork()`函数非常特殊它会返回两次，父进程中可以通过`fork()`函数的返回值得到子进程的PID，而子进程中的返回值永远都是0。Python的os模块提供了`fork()`函数。由于Windows系统没有`fork()`调用，因此要实现跨平台的多进程编程，可以使用multiprocessing模块的`Process`类来创建子进程，而且该模块还提供了更高级的封装，例如批量启动进程的进程池（`Pool`）、用于进程间通信的队列（`Queue`）和管道（`Pipe`）等。

```python
import threading

```



## 多继承



## 网络

### request库

requests是一个基于HTTP协议来使用网络的第三库，其[官方网站](http://cn.python-requests.org/zh_CN/latest/)有这样的一句介绍它的话：“Requests是唯一的一个**非转基因**的Python HTTP库，人类可以安全享用。”简单的说，使用requests库可以非常方便的使用HTTP，避免安全缺陷、冗余代码以及“重复发明轮子”（行业黑话，通常用在软件工程领域表示重新创造一个已有的或是早已被优化過的基本方法）。前面的文章中我们已经使用过这个库，下面我们还是通过requests来实现一个访问网络数据接口并从中获取美女图片下载链接然后下载美女图片到本地的例子程序，程序中使用了[天行数据](https://www.tianapi.com/)提供的网络API。

我们可以先通过pip安装requests及其依赖库。

```powershell
pip install requests
```



### 发送短信与邮件

在即时通信软件如此发达的今天，电子邮件仍然是互联网上使用最为广泛的应用之一，公司向应聘者发出录用通知、网站向用户发送一个激活账号的链接、银行向客户推广它们的理财产品等几乎都是通过电子邮件来完成的，而这些任务应该都是由程序自动完成的。

就像我们可以用HTTP（超文本传输协议）来访问一个网站一样，发送邮件要使用SMTP（简单邮件传输协议），SMTP也是一个建立在TCP（传输控制协议）提供的可靠数据传输服务的基础上的应用级协议，它规定了邮件的发送者如何跟发送邮件的服务器进行通信的细节，而Python中的smtplib模块将这些操作简化成了几个简单的函数。

```python
from smtplib import SMTP
from email.header import Header
from email.mime.text import MIMEText


def main():
    # 请自行修改下面的邮件发送者和接收者
    sender = 'abcdefg@126.com'
    receivers = ['uvwxyz@qq.com', 'uvwxyz@126.com']
    message = MIMEText('用Python发送邮件的示例代码.', 'plain', 'utf-8')
    message['From'] = Header('王大锤', 'utf-8')
    message['To'] = Header('骆昊', 'utf-8')
    message['Subject'] = Header('示例代码实验邮件', 'utf-8')
    smtper = SMTP('smtp.126.com')
    # 请自行修改下面的登录口令
    smtper.login(sender, 'secretpass')
    smtper.sendmail(sender, receivers, message.as_string())
    print('邮件发送完成!')


if __name__ == '__main__':
    main()
```

对于有附件的邮件，

```python
from smtplib import SMTP
from email.header import Header
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from email.mime.multipart import MIMEMultipart

import urllib


def main():
    # 创建一个带附件的邮件消息对象
    message = MIMEMultipart()
    
    # 创建文本内容
    text_content = MIMEText('附件中有本月数据请查收', 'plain', 'utf-8')
    message['Subject'] = Header('本月数据', 'utf-8')
    # 将文本内容添加到邮件消息对象中
    message.attach(text_content)

    # 读取文件并将文件作为附件添加到邮件消息对象中
    with open('/Users/Hao/Desktop/hello.txt', 'rb') as f:
        txt = MIMEText(f.read(), 'base64', 'utf-8')
        txt['Content-Type'] = 'text/plain'
        txt['Content-Disposition'] = 'attachment; filename=hello.txt'
        message.attach(txt)
    # 读取文件并将文件作为附件添加到邮件消息对象中
    with open('/Users/Hao/Desktop/汇总数据.xlsx', 'rb') as f:
        xls = MIMEText(f.read(), 'base64', 'utf-8')
        xls['Content-Type'] = 'application/vnd.ms-excel'
        xls['Content-Disposition'] = 'attachment; filename=month-data.xlsx'
        message.attach(xls)
    
    # 创建SMTP对象
    smtper = SMTP('smtp.126.com')
    # 开启安全连接
    # smtper.starttls()
    sender = 'abcdefg@126.com'
    receivers = ['uvwxyz@qq.com']
    # 登录到SMTP服务器
    # 请注意此处不是使用密码而是邮件客户端授权码进行登录
    # 对此有疑问的读者可以联系自己使用的邮件服务器客服
    smtper.login(sender, 'secretpass')
    # 发送邮件
    smtper.sendmail(sender, receivers, message.as_string())
    # 与邮件服务器断开连接
    smtper.quit()
    print('发送完成!')


if __name__ == '__main__':
    main()
```

发送短信也是项目中常见的功能，网站的注册码、验证码、营销信息基本上都是通过短信来发送给用户的。在下面的代码中我们使用了[互亿无线](http://www.ihuyi.com/)短信平台（该平台为注册用户提供了50条免费短信以及常用开发语言发送短信的demo，可以登录该网站并在用户自服务页面中对短信进行配置）提供的API接口实现了发送短信的服务，当然国内的短信平台很多，读者可以根据自己的需要进行选择（通常会考虑费用预算、短信达到率、使用的难易程度等指标），如果需要在商业项目中使用短信服务建议购买短信平台提供的套餐服务。

```python
import urllib.parse
import http.client
import json


def main():
    host  = "106.ihuyi.com"
    sms_send_uri = "/webservice/sms.php?method=Submit"
    # 下面的参数需要填入自己注册的账号和对应的密码
    params = urllib.parse.urlencode({'account': '你自己的账号', 'password' : '你自己的密码', 'content': '您的验证码是：147258。请不要把验证码泄露给其他人。', 'mobile': '接收者的手机号', 'format':'json' })
    print(params)
    headers = {'Content-type': 'application/x-www-form-urlencoded', 'Accept': 'text/plain'}
    conn = http.client.HTTPConnection(host, port=80, timeout=30)
    conn.request('POST', sms_send_uri, params, headers)
    response = conn.getresponse()
    response_str = response.read()
    jsonstr = response_str.decode('utf-8')
    print(json.loads(jsonstr))
    conn.close()


if __name__ == '__main__':
    main()
```

## 读写word和excel



## Lint工具

安装

```shell
pip install ruff
```

运行单个lint命令格式化文件、文件夹或者监听文件

```shell
ruff path/to/code/to/lint.py  # Run Ruff over `lint.py`
ruff path/to/code/            # Run Ruff over all files in `/path/to/code` (and any subdirectories)
ruff path/to/code/*.py        # Run Ruff over all `.py` files in `/path/to/code`

ruff path/to/code/ --watch
```

在pre-commit hooks中配置

```toml
- repo: https://github.com/charliermarsh/ruff-pre-commit
  # Ruff version.
  rev: 'v0.0.221'
  hooks:
    - id: ruff
      # Respect `exclude` and `extend-exclude` settings.
      args: ["--force-exclude"]
```

配置

```toml
[tool.ruff]
line-length = 88

# Enable Pyflakes `E` and `F` codes by default.
select = ["E", "F"]
ignore = []

# Exclude a variety of commonly ignored directories.
exclude = [
    ".bzr",
    ".direnv",
    ".eggs",
    ".git",
    ".hg",
    ".mypy_cache",
    ".nox",
    ".pants.d",
    ".ruff_cache",
    ".svn",
    ".tox",
    ".venv",
    "__pypackages__",
    "_build",
    "buck-out",
    "build",
    "dist",
    "node_modules",
    "venv",
]
per-file-ignores = {}

# Allow unused variables when underscore-prefixed.
dummy-variable-rgx = "^(_+|(_+[a-zA-Z0-9_]*[a-zA-Z0-9]+?))$"

# Assume Python 3.10.
target-version = "py310"

[tool.ruff.mccabe]
# Unlike Flake8, default to a complexity level of 10.
max-complexity = 10
```



https://github.com/charliermarsh/ruff#installation-and-usage

## git hooks 工具

安装

```shell
pip install pre-commit
```



https://pre-commit.com/



## 定义链表

https://blog.csdn.net/Tonywu2018/article/details/88853533

定义单链表类

```python
class Node(object)
        def _init_(sef,data,next = None):
               self.data = data
               self.next = next
```



实例化节点对象

```python

```



## Poetry

Poetry是一个Python虚拟环境和依赖管理工具，另外它还提供了包管理功能，比如打包和发布。
可以用来管理python库和python程序

安装

```shell
pip3 install poetry
```

验证安装成功

```shell
poetry --version
```

使用命令行创建一个pyproject.toml

```shell
poetry init
```

pyproject.toml: 使用此文件管理依赖列表和项目的各种meta信息，用来替代 Pipfile、requirements.txt、setup.py、setup.cfg、MANIFEST.in 等等各种配置文件。

然后项目下安装虚拟环境

```shell
poetry install
```

这个命令会读取pyproject.toml中的所有依赖并安装（包括开发依赖），如果不想安装开发依赖可以附加：--no-dev 选项。如果项目根目录有 poetry.lock 文件，会安装这个文件中列出的锁定版本的依赖。如果执行 add/remove 命令的时候没有检测到虚拟环境，也会为当前目录自动创建虚拟

其他命令

```shell
## 激活虚拟环境
poetry shell

## 查看python版本
poetry run python -v

## 执行脚本
poetry run python app.py

## 安装包
poetry add flask

## 追踪 & 更新包
poetry show
poetry show --tree

## 更新所有锁定版本的依赖
poetry update

## 卸载包
poetry remove dep_name
```

其他相关命令行工具：pyenv  pipenv





