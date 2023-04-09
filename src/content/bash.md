---
title: linux bash脚本入门
date: 2020-03-02 21:40:33
categories: IT
tags:
    - 
toc: true
thumbnail: 
---

 bash脚本入门

<!--more-->

## 概念

bash 是一个为GNU计划编写的Unix shell。它的名字是一系列缩写：Bourne-Again SHell — 这是关于Bourne shell（sh）的一个双关语（Bourne again / born again）。Bourne shell是一个早期的重要shell，由史蒂夫·伯恩在1978年前后编写，并同Version 7 Unix一起发布。bash则在1987年由布莱恩·福克斯创造。

bash (GNU Bourne-Again Shell) 是许多**Linux发行版**的默认Shell 。事实上，还有许多传统UNIX上用的Shell，例如`tcsh`、`csh、ash`、`bsh`、`ksh`等等，Shell Script大致都类同。
由于历史原因，UNIX系统上有很多种Shell：

- `sh`（C Shell）：由Bill Joy开发，随BSD UNIX发布，它的流程控制语句很像C语言，支持很多Bourne Shell所不支持的功能：作业控制，命令历史，命令行编辑。
- `ksh`（Korn Shell）：由David Korn开发，向后兼容sh的功能，并且添加了csh引入的新功能，是目前很多UNIX系统标准配置的Shell，在这些系统上/bin/sh往往是指向/bin/ksh的符号链接。
- `tcsh`（TENEX C Shell）：是csh的增强版本，引入了命令补全等功能，在FreeBSD、Mac OS X等系统上替代了csh。
- `bash`（Bourne Again Shell）：由GNU开发的Shell，主要目标是与POSIX标准保持一致，同时兼顾对sh的兼容，bash从csh和ksh借鉴了很多功能，是各种Linux发行版标准配置的Shell，在Linux系统上/bin/sh往往是指向/bin/bash的符号链接。

可使用`cat /etc/shells` 指令查看自己系统可以使用的shell种类

在shell脚本的开头往往有一句话来定义使用哪种sh解释器来解释脚本。
目前常见的shell脚本中主要有以下两种方式：
(1) `#!/bin/sh`
(2)`#!/bin/bash`

每个脚本开头都使用"`#!`"，#!实际上是一个2字节魔法数字，这是**指定一个文件类型的特殊标记**，在这种情况下，指的就是一个可执行的脚本。在#!之后，接一个路径名，这个路径名指定了一个解释脚本命令的程序，这个程序可以是shell，程序语言或者任意一个通用程序。

sh是bash的一种特殊的模式，也就是`/bin/sh` 相当于`/bin/bash --posix`。说白了sh就是开启了POSIX标准的bash 。
在一般的linux系统当中（如redhat），使用sh调用执行脚本相当于打开了bash的POSIX标准模式
sh一般设成bash的软链

**在Mac OS上不是**，/bin/sh和/bin/bash是两个**不同**的文件，尽管它们的大小只相差100字节左右:

但鉴于 bash 过于复杂，有人把 bash 从 NetBSD 移植到 Linux 并更名为 dash（Debian Almquist Shell），并以获得更快的脚本执行速度。Debian Almquist shell，缩写为dash，一种 Unix shell。它比 Bash 小，只需要较少的磁盘空间，但是它的对话性功能也较少。它由 NetBSD版本的Almquist shell (ash)发展而来，于1997年，由赫伯特·许（Herbert Xu）移植到Linux上，于2002年改名为 dash。

## 基本命令

`echo`命令的作用是在屏幕输出一行文本，可以将该命令的参数原样输出。

如果想要输出的是多行文本，即包括换行符。这时需要把多行文本放在引号里面。

```bash
$ echo hello world

$ echo "<HTML>
    <HEAD>
          <TITLE>Page Title</TITLE>
    </HEAD>
    <BODY>
          Page body.
    </BODY>
</HTML>"
```

-n参数：默认情况下，`echo`输出的文本末尾会有一个回车符。`-n`参数可以取消末尾的回车符，使得下一个提示符紧跟在输出内容的后面。

`-e`参数会解释引号（双引号和单引号）里面的特殊字符（比如换行符`\n`）。如果不使用`-e`参数，即默认情况下，引号会让特殊字符变成普通字符，`echo`不解释它们，原样输出。



Bash 使用空格（或 Tab 键）区分不同的参数。

分号（`;`）是命令的结束符，使得一行可以放置多个命令，上一个命令执行结束后，再执行第二个命令。

除了分号，Bash 还提供两个命令组合符`&&`和`||`，允许更好地控制多个命令之间的继发关系。

&&表示`Command1`命令运行成功，则继续运行`Command2`命令。

｜｜表示`Command1`命令运行失败，则继续运行`Command2`命令。

Bash 本身内置了很多命令，同时也可以执行外部程序。`type`命令用来判断命令的来源。

```bash
$ type echo
echo is a shell builtin
$ type ls
ls is hashed (/bin/ls)
```

要查看一个命令的所有定义，可以使用`type`命令的`-a`参数。

`type`命令的`-t`参数，可以返回一个命令的类型：别名（alias），关键词（keyword），函数（function），内置命令（builtin）和文件（file）。



## 命令扩展

Shell 接收到用户输入的命令以后，会根据空格将用户的输入，拆分成一个个词元（token）。然后，Shell 会扩展词元里面的特殊字符，扩展完成后才会调用相应的命令。

这种特殊字符的扩展，称为模式扩展（globbing）。其中有些用到通配符，又称为通配符扩展（wildcard expansion）。Bash 一共提供八种扩展。

- 波浪线扩展
- `?` 字符扩展
- `*` 字符扩展
- 方括号扩展
- 大括号扩展
- 变量扩展
- 子命令扩展
- 算术扩展

模式扩展与正则表达式的关系是，模式扩展早于正则表达式出现，可以看作是原始的正则表达式。它的功能没有正则那么强大灵活，但是优点是简单和方便。

波浪线`~`会自动扩展成当前用户的主目录。

`~/dir`表示扩展成主目录的某个子目录，`dir`是主目录里面的一个子目录名。

`~user`表示扩展成用户`user`的主目录。

`?`字符代表文件路径里面的任意单个字符，不包括空字符。比如，`Data???`匹配所有`Data`后面跟着三个字符的文件名。

`*`字符代表文件路径里面的任意数量的任意字符，包括零个字符。

方括号扩展的形式是`[...]`，只有文件确实存在的前提下才会扩展。如果文件不存在，就会原样输出。括号之中的任意一个字符。比如，`[aeiou]`可以匹配五个元音字母中的任意一个。

方括号扩展有一个简写形式`[start-end]`，表示匹配一个连续的范围。比如，`[a-c]`等同于`[abc]`，`[0-9]`匹配`[0123456789]`。

下面是一些常用简写的例子。

- `[a-z]`：所有小写字母。
- `[a-zA-Z]`：所有小写字母与大写字母。
- `[a-zA-Z0-9]`：所有小写字母、大写字母与数字。
- `[abc]*`：所有以`a`、`b`、`c`字符之一开头的文件名。
- `program.[co]`：文件`program.c`与文件`program.o`。
- `BACKUP.[0-9][0-9][0-9]`：所有以`BACKUP.`开头，后面是三个数字的文件名。

大括号扩展`{...}`表示分别扩展成大括号里面的所有值，各个值之间使用逗号分隔。比如，`{1,2,3}`扩展成`1 2 3`。

大括号扩展有一个简写形式`{start..end}`，表示扩展成一个连续序列。比如，`{a..z}`可以扩展成26个小写英文字母。



## 行操作

光标移动

```bash
Ctrl + a：移到行首。
Ctrl + b：向行首移动一个字符，与左箭头作用相同。
Ctrl + e：移到行尾。
Ctrl + f：向行尾移动一个字符，与右箭头作用相同。
Alt + f：移动到当前单词的词尾。
Alt + b：移动到当前单词的词首。
```

编辑操作

```bash
Ctrl + d：删除光标位置的字符（delete）。
Ctrl + w：删除光标前面的单词。
Ctrl + t：光标位置的字符与它前面一位的字符交换位置（transpose）。
Alt + t：光标位置的词与它前面一位的词交换位置（transpose）。
Alt + l：将光标位置至词尾转为小写（lowercase）。
Alt + u：将光标位置至词尾转为大写（uppercase）。
```

剪切快捷键

```bash
Ctrl + k：剪切光标位置到行尾的文本。
Ctrl + u：剪切光标位置到行首的文本。
Alt + d：剪切光标位置到词尾的文本。
Alt + Backspace：剪切光标位置到词首的文本。
Ctrl + y：在光标位置粘贴文本。
```

自动补全

```bash
Tab：完成自动补全。
Alt + ?：列出可能的补全，与连按两次 Tab 键作用相同。
Alt + /：尝试文件路径补全。
Ctrl + x /：先按Ctrl + x，再按/，等同于Alt + ?，列出可能的文件路径补全。
Alt + !：命令补全。
Ctrl + x !：先按Ctrl + x，再按!，等同于Alt + !，命令补全。
Alt + ~：用户名补全。
Ctrl + x ~：先按Ctrl + x，再按~，等同于Alt + ~，用户名补全。
Alt + $：变量名补全。
Ctrl + x $：先按Ctrl + x，再按$，等同于Alt + $，变量名补全。
Alt + @：主机名补全。
Ctrl + x @：先按Ctrl + x，再按@，等同于Alt + @，主机名补全。
Alt + *：在命令行一次性插入所有可能的补全。
Alt + Tab：尝试用.bash_history里面以前执行命令，进行补全。
```





```bash
Ctrl + L：清除屏幕并将当前行移到页面顶部。
Ctrl + C：中止当前正在执行的命令。
Shift + PageUp：向上滚动。
Shift + PageDown：向下滚动。
Ctrl + U：从光标位置删除到行首。
Ctrl + K：从光标位置删除到行尾。
Ctrl + D：关闭 Shell 会话。
↑，↓：浏览已执行命令的历史记录。
```



## set命令

执行脚本时，如果遇到不存在的变量，Bash 默认忽略它。`set -u`就用来改变这种行为。脚本在头部加上它，遇到不存在的变量就会报错，并停止执行。

默认情况下，脚本执行后，只输出运行结果，没有其他内容。如果多个命令连续执行，它们的运行结果就会连续输出。有时会分不清，某一段内容是什么命令产生的。

`set -x`用来在运行结果之前，先输出执行的那一行命令。



## read命令

有时，脚本需要在执行过程中，由用户提供一部分数据，这时可以使用`read`命令。它将用户的输入存入一个变量，方便后面的代码使用。用户按下回车键，就表示输入结束。

read命令格式

```bash
read [-options] [variable...]
```

`options`是参数选项，`variable`是用来保存输入数值的一个或多个变量名。如果没有提供变量名，环境变量`REPLY`会包含用户输入的一整行数据。

例

```bash
#!/bin/bash

echo -n "输入一些文本 > "
read text
echo "你的输入：$text"
```

`read`命令的`-t`参数，设置了超时的秒数。如果超过了指定时间，用户仍然没有输入，脚本将放弃等待，继续向下执行。

```bash
#!/bin/bash

echo -n "输入一些文本 > "
if read -t 3 response; then
  echo "用户已经输入了"
else
  echo "用户没有输入"
fi
```

`-p`参数指定用户输入的提示信息。

```bash
read -p "Enter one or more values > "
echo "REPLY = '$REPLY'"
```

`-a`参数把用户的输入赋值给一个数组，从零号位置开始。

```bash
$ read -a people
alice duchess dodo
$ echo ${people[2]}
dodo
```

`-n`参数指定只读取若干个字符作为变量值，而不是整行读取。

```shell
$ read -n 3 letter
abcdefghij
$ echo $letter
abc
```

`-e`参数允许用户输入的时候，使用`readline`库提供的快捷键，比如自动补全。`read`命令的输入默认不支持`readline`库的功能。`-e`参数就可以允许用户使用自动补全。

```bash

```

IFS变量

`read`命令读取的值，默认是以空格分隔。可以通过自定义环境变量`IFS`（内部字段分隔符，Internal Field Separator 的缩写），修改分隔标志。

`IFS`的默认值是空格、Tab 符号、换行符号，通常取第一个（即空格）。

如果把`IFS`定义成冒号（`:`）或分号（`;`），就可以分隔以这两个符号分隔的值，这对读取文件很有用。



## cat



## awk

awk是一种处理文本文件的语言，是一个强大的文本分析工具

-v var=value 赋值一个用户定义变量

-f scriptfile 从脚本中读取awk命令

-F fs or --field-seprator fs 指定输入文件拆分字符串



## 传递参数

执行shell脚本时可以向脚本传递参数，脚本内获取参数的格式为$n，n是一个数字，$1表示执行脚本的第一个参数，$2为执行脚本的第二个参数，$0通常为执行的文件名，包含文件路径



## Shebang行







　
