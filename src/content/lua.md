---
title: lua
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，lua
toc: true
thumbnail: 
---

​       先开坑吧，涉及到很多内容，慢慢补

<!--more-->

## 介绍

Lua (LOO-ah) 是一种可嵌入、轻量、快速、功能强大的脚本语言。它支持过程式编程、 面向对象编程、函数式编程、数据驱动编程和数据描述（data description）。

Lua 将简洁的过程式语法和基于关联数组、可扩展语义的数据描述语法结构结合了起来。 Lua 是动态类型的语言，它使用基于寄存器的虚拟机解释和运行字节码（bytecode），并 使用增量垃级回收（incremental garbage collection）机制自动管理内存。这些特点使 得 Lua 很适合用于配置、脚本化和快速构造原型的场景

Lua 是第一个由第三世界国家（巴西）开发者开发的流行度很高的语言（and the leading scripting language in games）。

Lua 解释器只有 2w+ 多行 ANSI C/C++ 代码， 可执行文件 200+ KB 大小

下面是几个嵌入了 Lua 解释器，可以使用 Lua 扩展功能的知名应用程序：

- World of Warcraft
- Angry Birds
- Redis
- Wireshark
- Wrk
- Nmap
- MySQL Workbench
- VLC

Lua 是一种轻量小巧的脚本语言，用标准 C`ANSI C` 编写并以源代码形式开放。其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制的功能。

Lua 和传统的脚本语言不同，它是一种**易整合语言（glue language）**。一般的脚本语言用于控制执行重复的任务，而易整合语言可以让使用者把其他语言开发的功能整合在一起。这样就让脚本程序员有了更大的发挥空间，而不仅仅局限于执行命令。程序员可以使用这种脚本在底层语言开发的功能模块基础上储行间新的命令

Lua 本身是一种简单且强大的编程语言，可以让脚本程序员完成大量的处理。这种语言拥有很强大的字符处理和数学运算能力、灵活的数据结构，以及定义函数的功能。但是如果没有整合其他环境的组件的魔力，这些基础的特性也就丧失了。

**Lua 非常适合作为更强大的底层编程语言的搭档**，如 C++。Lua 能让游戏开发者快速建立游戏原型甚至是完整的游戏。游戏开发者可以在没有程序员帮助下，构建整个图形界面。它还可以用来管理游戏进度文件的保存和载入，而且很容易阅读和调试。在游戏开发领域，Lua 能帮助开发者构建一个高效且方便验证游戏想法的环境。

按 Lua 开发团队的描述，Lua 是一个可集成在应用程序中的 “**语言引擎**”。它本身是一种编程语言，提供很多可以和应用程序交换数据的 API。另外，Lua 还能够通过整合 C++ 的模块来进行功能的扩展。和程序开发语言如 C++ 配合使用时，Lua 也可以用来作为特定项目的框架语言。这种易扩展性使得 Lua 非常适合作为游戏开发的环境

作为独立的编程语言，Lua 功能有限，只能用作教学工具。Lua 只有集成在其他语言中才能发挥它的价值。它的实现非常简单。通过 LuaClue 函数就可以和底层语言通信，在用户自定义 LuaGlue 函数的基础上，还可以进一步被扩展，甚至成为一种新的编程语言。

Lua 应用场景

- 游戏开发
- 独立应用脚本
-  Web 应用脚本
- 扩展和数据库插件
- 安全系统

## 数据类型

数据类型

| 数据名称 | 用法描述                                                     |
| :------- | :----------------------------------------------------------- |
| nil      | 表示一个无效值 (在条件表达式中相当于 false)                  |
| boolean  | 布尔值，包含两个值：false 和 true                            |
| number   | 实数，表示双精度类型的实浮点数，也可以是整数                 |
| string   | 字符串，由一对单引号或双引号来表示                           |
| table    | Lua 的一种数据结构，可用来创建不同的数据类型，如：数组、字典等 |
| function | 由 C 或 Lua 编写的函数                                       |
| userdata | 表示任意存储在变量中的 C 数据结构                            |
| thread   | 表示执行的独立线路，用于执行协同程序                         |

代码示例

```lua
-- 字符串
string1 = "Lua"
print("\"字符串 1 是\"",string1)
string2 = 'https://www.mini1.cn/'
print("字符串 2 是",string2)
string3 = [["Lua 教程"]]
print("字符串 3 是",string3)
```

可以使用整数索引来访问数组元素，如果知道的索引没有值则返回 nil。 在 Lua 索引值是以 1 为起始，但你也可以指定 0 开始。
除此外我们还可以以负数为数组索引值

```lua
array = {}

for i= -2, 2 do
   array[i] = i *2
end

for i = -2,2 do
   print(array[i])
end

--以上代码执行输出结果为：
-- -4
-- -2
-- 0
-- 2
-- 4
```

表

构造器是创建和初始化表的表达式。表是 Lua 特有的功能强大的东西。最简单的构造函数是 {}，用来创建一个空表。可以直接初始化数组

```lua
-- 初始化表
mytable = {}

-- 指定值
mytable[1]= "Lua"

-- 移除引用
mytable = nil
-- lua 垃圾回收会释放内存
```





## 变量

1. 变量在使用前，必须在代码中进行声明，即创建该变量。
2. 编译程序执行代码之前编译器需要知道如何给语句变量开辟存储区，用于存储变量的值。
3. Lua 变量有三种类型：全局变量、局部变量、表中的域。
4. Lua 中的变量全是全局变量，那怕是语句块或是函数里，除非用 local 显式声明为局部变量。
5. 局部变量的作用域为从声明位置开始到所在语句块结束。
6. 变量的默认值均为 nil

## 运算符

下表列出了 Lua 语言中的常用算数运算符，设定 A 的值为 10，B 的值为 20：

| 操作符 | 描述 | 实例               |
| :----- | :--: | :----------------- |
| +      | 加法 | A + B 输出结果 30  |
| -      | 减法 | A - B 输出结果 -10 |
| *      | 乘法 | A * B 输出结果 200 |
| /      | 除法 | B / A w 输出结果 2 |
| %      | 取余 | B % A 输出结果 0   |
| ^      | 乘幂 | A^2 输出结果 100   |
| -      | 负号 | -A 输出结果 -10    |

下表列出了 Lua 语言中的常用关系运算符，设定 A 的值为 10，B 的值为 20

| 操作符 |                             描述                             | 实例                 |
| :----- | :----------------------------------------------------------: | :------------------- |
| ==     |   等于，检测两个值是否相等，相等返回 true，否则返回 false    | (A == B) 为 false。  |
| ~=     |  不等于，检测两个值是否相等，相等返回 false，否则返回 true   | (A ~= B) 为 true。   |
| >      |  大于，如果左边的值大于右边的值，返回 true，否则返回 false   | (A> B) 为 false。    |
| <      |  小于，如果左边的值大于右边的值，返回 false，否则返回 true   | (A < B) 为 true。    |
| >=     | 大于等于，如果左边的值大于等于右边的值，返回 true，否则返回 false | (A>= B) 返回 false。 |
| <=     | 小于等于， 如果左边的值小于等于右边的值，返回 true，否则返回 false | (A <= B) 返回 true。 |

逻辑运算符

| 操作符 |                             描述                             | 实例                    |
| :----- | :----------------------------------------------------------: | :---------------------- |
| and    |     逻辑与操作符。 若 A 为 false，则返回 A，否则返回 B。     | (A and B) 为 false。    |
| or     |     逻辑或操作符。 若 A 为 true，则返回 A，否则返回 B。      | (A or B) 为 true。      |
| not    | 逻辑非操作符。与逻辑运算结果相反，如果条件为 true，逻辑非为 false。 | not (A and B) 为 true。 |

其他运算符

| 操作符 |                描述                | 实例                                                         |
| :----- | :--------------------------------: | :----------------------------------------------------------- |
| ..     |           连接两个字符串           | a..b ，其中 a 为 "Hello" ， b 为 "World", 输出结果为 "Hello World"。 |
| #      | 一元运算符，返回字符串或表的长度。 | #"Hello" 返回 5                                              |

## 语句

### 循环语句

repeat... until

```lua
--实例：
--以下代码在聊天框输出a的值
a = 0
--[ 执行循环 --]
repeat
	Chat:sendSystemMsg( a, 0)
   a = a + 1
until( a > 5 )  --循环直到a>5
--输出结果为：
-- 0
-- 1
-- 2
-- 3
-- 4
-- 5
```

for循环

```lua
--案例：
--简单的for循环，可以在聊天框输出i的值：
for i = 1 , 5 , 1  do 
	Chat:sendSystemMsg( i, 0)
end 
--输出结果为：
-- 1
-- 2
-- 3
-- 4
-- 5
```

while循环

```lua
--案例：
--以下代码实例在聊天框循环输出a的值
a = 0
while( a < 5 )  --当a<5的时候才会执行do里面的内容
do
   Chat:sendSystemMsg( a, 0)  --输出a的值到聊天框
   a = a+1  --每次循环a都会自身+1
end
--输出结果为：
-- 0 
-- 1
-- 2
-- 3
-- 4
```

break语句

```lua
--以下实例执行 while 循环，在变量 a 小于 20 时输出 a 的值，并在 a 大于 15 时终止执行循环：

--[ 定义变量 --]
a = 10
--[ while 循环 --]
while( a < 20 )
do
	Chat:sendSystemMsg( a, 0)
   a=a+1
   if( a > 15)
   then
      --[ 使用 break 语句终止循环 --]
      break
   end
end
--输出结果为：
-- 10
-- 11
-- 12
-- 13
-- 14 
-- 15
```

goto语句

Lua 语言中的 goto 语句允许将控制流程无条件地转到被标记的语句处。

```lua
local a = 1
::start:: Chat:sendSystemMsg("goto的位置",0)

a = a+1
if a < 3 then
    goto start   -- a 小于 3 的时候跳转到标签 start
end
--输出的结果：
-- goto的位置
-- goto的位置
```

### 条件语句

Lua 编程语言流程控制语句通过程序设定一个或多个条件语句来设定。在条件为 true 时执行指定程序代码，在条件为 false 时执行其他指定代码。

控制结构的条件表达式结果可以是任何值，Lua 认为 false 和 nil 为假，true 和非 nil 为真。

要注意的是 Lua 中 0 为 true

```lua
--以下实例用于判断变量 a 的值是否小于 20：
--[ 定义变量 --]
a = 10;

--[ 使用 if 语句 --]
if( a < 20 )
then
   --[ if 条件为 true 时打印以下信息 --]
   Chat:sendSystemMsg("a 小于 20" , 0);
end

Chat:sendSystemMsg( "a的值为："..a.."", 0);

--输出的结果为：
--a小于20
--a的值为：10
```

If-else语句

```lua
--以下实例用于判断变量 a 的值：
a = 100;
--[ 检查条件 --]
if( a < 20 )
then
   --[ if 条件为 true 时执行该语句块 --]
   Chat:sendSystemMsg("a 小于 20",0 )
else
   --[ if 条件为 false 时执行该语句块 --]
   Chat:sendSystemMsg("a 大于 20",0 )
end
Chat:sendSystemMsg("a的值为 :"..a.."", 0)

--输出的结果为：
--a 大于 20
--a的值为：100
```

## 函数

在 Lua 中，函数是对语句和表达式进行抽象的主要方法。既可以用来处理一些特殊的工作，也可以用来计算一些值。

Lua 提供了许多的内建函数，你可以很方便的在程序中调用它们，如 print () 函数可以将传入的参数打印在控制台上。

Lua 函数主要有两种用途：

- 完成指定的任务，这种情况下函数作为调用语句使用；
- 计算并返回值，这种情况下函数作为赋值语句的表达式使用。

```lua
--[[ 函数返回两个值的最大值 --]]
function max(num1, num2)

   if (num1 > num2) then
      result = num1;
   else
      result = num2;
   end

   return result; 
end
-- 调用函数
Chat:sendSystemMsg("两值比较最大值为:"..max(10,4).."",0)
Chat:sendSystemMsg("两值比较最大值为:"..max(5,6).."",0)
```

Lua 中我们可以将函数作为参数传递给函数

```lua
myprint = function(param)
   Chat:sendSystemMsg("param的值：" ..param.. "",0)

end

function add(num1,num2,functionPrint)
   result = num1 + num2
   -- 调用传递的函数参数
   functionPrint(result)
end
myprint(10)
-- myprint 函数作为参数传递
add(2,5,myprint)
```

Lua 函数可以返回多个结果值，比如 string.find，其返回匹配串 "开始和结束的下标"（如果不存在匹配串返回 nil）

Lua 函数中，在 return 后列出要返回的值的列表即可返回多值

```lua
function maximum (a)
    local mi = 1             -- 最大值索引
    local m = a[mi]          -- 最大值
    for i,val in ipairs(a) do
       if val > m then
           mi = i
           m = val
       end
    end
    return m, mi
end
print(maximum({8,10,23,12,5}))
--由于Chat:sendSystemMsg输出机制只能输出一个值，只能用print打印来看输出的返回值
```

## 模块

从 Lua 5.1 开始，Lua 加入了标准的模块管理机制， Lua 的就是一个 table

给模块添加变量

```lua
module = {} -- 定义一个module

module.ct = "this is a constant"

-- 共有函数， 没有 pub 之类的关键偶
function module.fn()
    -- your fn body
end
-- 私有函数
local function module.fnp()
    -- your private fn body
end
-- 在共有函数中，调用私有函数
function module.func3()\
    func2()\
end

return module -- 返回一个 module
```

 

## 面向对象

重复一遍面向对象的特征

- 1） 封装：指能够把一个实体的信息、功能、响应都装入一个单独的对象中的特性。
- 2） 继承：继承的方法允许在不改动原程序的基础上对其进行扩充，这样使得原功能得以保存，而新功能也得以扩展。这有利于减少重复编码，提高软件的开发效率。
- 3） 多态：同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果。在运行时，可以通过指向基类的指针，来调用实现派生类中的方法。
- 4）抽象：抽象 (Abstraction) 是简化复杂的现实问题的途径，它可以为具体问题找到最恰当的类定义，并且可以在最恰当的继承级别解释问题。

对象由属性和方法组成。LUA 中最基本的结构是 table，所以需要用 table 来描述对象的属性。lua 中的 function 可以用来表示方法。那么 LUA 中的类可以通过 table + function 模拟出来。至于继承，可以通过 metetable 模拟出来（不推荐用，只模拟最基本的对象大部分实现够用了）。

Lua 中的表不仅在某种意义上是一种对象。像对象一样，表也有状态（成员变量）；也有与对象的值独立的本性，特别是拥有两个不同值的对象（table）代表两个不同的对象；一个对象在不同的时候也可以有不同的值，但他始终是一个对象；与对象类似，表的生命周期与其由什么创建、在哪创建没有关系。对象有他们的成员函数，表也有

以下简单的类包含了三个属性： area, length 和 breadth，printArea 方法用于打印计算结果

```lua
-- 元类
Rectangle = {area = 0, length = 0, breadth = 0}

-- 派生类的方法 new
function Rectangle:new (o,length,breadth)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  self.length = length or 0
  self.breadth = breadth or 0
  self.area = length*breadth;
  return o
end

-- 派生类的方法 printArea
function Rectangle:printArea ()
  print("矩形面积为",self.area)
end
```

创建对象

可以使用点号 (.) 来访问类的属性, 使用冒号 **:** 来访问类的成员函数

```lua
r = Rectangle:new(nil,10,20)

print(r.length)

r:printArea()
```

完整实例

```lua
-- Meta class
Shape = {area = 0}
-- 基础类方法 new
function Shape:new (o,side)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  side = side or 0
  self.area = side*side;
  return o
end
-- 基础类方法 printArea
function Shape:printArea ()
  print("面积为",self.area)
end

-- 创建对象
myshape = Shape:new(nil,10)
myshape:printArea()

Square = Shape:new()
-- 派生类方法 new
function Square:new (o,side)
  o = o or Shape:new(o,side)
  setmetatable(o, self)
  self.__index = self
  return o
end

-- 派生类方法 printArea
function Square:printArea ()
  print("正方形面积为",self.area)
end

-- 创建对象
mysquare = Square:new(nil,10)
mysquare:printArea()

Rectangle = Shape:new()
-- 派生类方法 new
function Rectangle:new (o,length,breadth)
  o = o or Shape:new(o)
  setmetatable(o, self)
  self.__index = self
  self.area = length * breadth
  return o
end

-- 派生类方法 printArea
function Rectangle:printArea ()
  print("矩形面积为",self.area)
end

-- 创建对象
myrectangle = Rectangle:new(nil,10,20)
myrectangle:printArea()
```



## coroutine

Lua 协同程序 (coroutine) 与线程比较类似：拥有独立的堆栈，独立的局部变量，独立的指令指针，同时又与其它协同程序共享全局变量和其它大部分东西。

协同是非常强大的功能，但是用起来也很复杂

线程与协同程序的主要区别在于，一个具有多个线程的程序可以同时运行几个线程，而协同程序却需要彼此协作的运行。

在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只有在明确的被要求挂起的时候才会被挂起。

协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同

语法

| 方法                | 描述                                                         |
| :------------------ | :----------------------------------------------------------- |
| coroutine.create()  | 创建 coroutine，返回 coroutine， 参数是一个函数，当和 resume 配合使用的时候就唤醒函数调用 |
| coroutine.resume()  | 重启 coroutine，和 create 配合使用                           |
| coroutine.yield()   | 挂起 coroutine，将 coroutine 设置为挂起状态，这个和 resume 配合使用能有很多有用的效果 |
| coroutine.status()  | 查看 coroutine 的状态 注：coroutine 的状态有三种：dead，suspended，running，具体什么时候有这样的状态请参考下面的程序 |
| coroutine.wrap（）  | 创建 coroutine，返回一个函数，一旦你调用这个函数，就进入 coroutine，和 create 功能重复 |
| coroutine.running() | 返回正在跑的 coroutine，一个 coroutine 就是一个线程，当使用 running 的时候，就是返回一个 coroutine 的线程号 |





## 转换

### gopher-lua

用go编写的lua的编译器，可以在go中运行lua脚本

安装

```shell
go get github.com/yuin/gopher-lua
```

运行脚本

```go
import (
    "github.com/yuin/gopher-lua"
)

L := lua.NewState()
defer L.Close()
if err := L.DoString(`print("hello")`); err != nil {
    panic(err)
}
```

我们使用 NewState 得到一个独立的 Lua 解释器实例，后续的所有操作都是基于这个实例内部进行的，全局状态限于 L 对象内部，没有进程级别的全局状态。如果要得到多个解释器实例，使用 NewState 多创建几个就行。

也许你会想到 golang 有如此多的 goroutine，难道要每个 goroutine 都开一个 lua 解释器实例么，如果这样，内存肯定是要被撑爆的。

GopherLua 考虑到了这点，它使用解释器实例池解决了这个问题。当用户想要使用 Lua 解释器时，从池中取出一个，用完了再还回去。因为同一个解释器可能要被多个协程使用，虽然不是同一时间被多个协程使用，要注意全局状态不要相互干扰

```go
// fib.lua
function fib(n)
	if n < 2 then
		return 1
	end
	return fib(n-1) + fib(n-2)
end

// main.go
package main

import (
	"fmt"

	lua "github.com/yuin/gopher-lua"
)

func main() {
	L := lua.NewState()
	defer L.Close()
	// 加载fib.lua
	if err := L.DoFile("fib.lua"); err != nil {
		panic(err)
	}
	// 调用fib(n)
	err := L.CallByParam(lua.P{
		Fn:      L.GetGlobal("fib"), // 获取fib函数引用
		NRet:    1,                  // 指定返回值数量
		Protect: true,               // 如果出现异常，是panic还是返回err
	}, lua.LNumber(10)) // 传递输入参数n=10
	if err != nil {
		panic(err)
	}
	// 获取返回结果
	ret := L.Get(-1)
	// 从堆栈中扔掉返回结果
	L.Pop()
	// 打印结果
	res, ok := ret.(lua.LNumber)
	if ok {
		fmt.Println(int(res))
	} else {
		fmt.Println("unexpected result")
	}
}
```

GopherLua 除了可以满足基本的 lua 需要，还将 Go 语言特有的高级设计直接移植到 lua 环境中，使得内嵌的脚本也具备了一些高级的特性

1. 可以使用 context.WithTimeout 对执行的 lua 脚本进行超时控制
2. 可以使用 context.WithCancel 打断正在执行的 lua 脚本
3. 多个 lua 解释器实例之间还可以通过 channel 共享数据
4. 支持多路复用选择器 select

https://zhuanlan.zhihu.com/p/33471484

### TypeScriptToLua

讲typescript编译为lua

```shell
npm install -D typescript-to-lua
```

https://github.com/TypeScriptToLua/TypeScriptToLua



## LuaSQL

LuaSQL 可以使用 [LuaRocks](https://luarocks.org/) 来安装可以根据需要安装你需要的数据库驱动。

```shell
luarocks install luasql-sqlite3
luarocks install luasql-postgres
luarocks install luasql-mysql
luarocks install luasql-sqlite
luarocks install luasql-odbc
```





## 学习资源

https://developers.mini1.cn/wiki/questions.html
