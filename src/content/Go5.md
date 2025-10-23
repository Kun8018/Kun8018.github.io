---
title: Golang语言开发（四）
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - Web,IT,Go
toc: true
thumbnail: https://s1.ax1x.com/2020/04/20/J1Iu4O.th.jpg
---

　　本篇主要内容为go语言开发网页应用

<!--more-->

## 工具库

### lo

https://github.com/samber/lo

提供go语言的各种处理函数，类似于lodash对JavaScript

安装

```shell
go get github.com/samber/lo@v1
```

使用

```go
import (
    "github.com/samber/lo"
    lop "github.com/samber/lo/parallel"
)
```



### lancet

也是像lodash一样的工具库

安装

```shell
$ go get github.com/duke-git/lancet/v2
```

使用

```go
package main

import (
    "fmt"
    "github.com/duke-git/lancet/v2/strutil"
)

func main() {
    s := "hello"
    rs := strutil.ReverseStr(s)
    fmt.Println(rs) //olleh
}
```

https://github.com/duke-git/lancet



### gore

Go 语言的 REPL（read-eval-print-loop）工具

gore 是一个命令行工具，需要配合 Go Module 安装

安装

```shell
go install github.com/x-motemen/gore/cmd/gore@latest
```

go install 命令会将会在`$GOPATH/bin`目录生成的 gore 可执行文件。强烈推荐大家把`$GOPATH/bin`加入到系统的可执行文件搜索目录（即`$PATH`）中。

执行下面的命令即可进入 Go 的 REPL

```shell
gore
```

### expr

计算表达式的值

安装

```shell
go get -u /github.com/antonmedv/expr
```

使用

```go
package main

import (
	"fmt"
	"github.com/antonmedv/expr"
)

func main() {
	env := map[string]interface{}{
		"foo": 1,
		"bar": 2,
	}

	out, err := expr.Eval("foo + bar", env)

	if err != nil {
		panic(err)
	}
	fmt.Print(out)
}
```

https://czyt.tech/post/golang-expr-uncompleted-reference/

### go-funk

go的工具库函数

https://github.com/thoas/go-funk



### goph

go的ssh连接客户端

```shell
go get github.com/melbahja/goph
```

使用

```go
package main

import (
	"log"
	"fmt"
	"github.com/melbahja/goph"
)

func main() {

	// Start new ssh connection with private key.
	auth, err := goph.Key("/home/mohamed/.ssh/id_rsa", "")
	if err != nil {
		log.Fatal(err)
	}

	client, err := goph.New("root", "192.1.1.3", auth)
	if err != nil {
		log.Fatal(err)
	}

	// Defer closing the network connection.
	defer client.Close()

	// Execute your command.
	out, err := client.Run("ls /tmp/")

	if err != nil {
		log.Fatal(err)
	}

	// Get your output as []byte.
	fmt.Println(string(out))
}

// with private key
auth, err := goph.Key("/home/mohamed/.ssh/id_rsa", "you_passphrase_here")
if err != nil {
	// handle error
}

client, err := goph.New("root", "192.1.1.3", auth)
```

### go-git

go的git包

```go
// Clones the given repository in memory, creating the remote, the local
// branches and fetching the objects, exactly as:
Info("git clone https://github.com/go-git/go-billy")

r, err := git.Clone(memory.NewStorage(), nil, &git.CloneOptions{
    URL: "https://github.com/go-git/go-billy",
})

CheckIfError(err)

// Gets the HEAD history from HEAD, just like this command:
Info("git log")

// ... retrieves the branch pointed by HEAD
ref, err := r.Head()
CheckIfError(err)


// ... retrieves the commit history
cIter, err := r.Log(&git.LogOptions{From: ref.Hash()})
CheckIfError(err)

// ... just iterates over the commits, printing it
err = cIter.ForEach(func(c *object.Commit) error {
	fmt.Println(c)
	return nil
})
CheckIfError(err)
```

### envconfig

获取环境变量

https://github.com/kelseyhightower/envconfig



### got

比curl更快地下载，是go下载文件的工具包

https://github.com/melbahja/got

```go
package main

import "github.com/melbahja/got"

func main() {

	g := got.New()

	err := g.Download("http://localhost/file.ext", "/path/to/save")

	if err != nil {
		// ..
	}
}
```

### authboss

校验包

https://github.com/volatiletech/authboss?tab=readme-ov-file#getting-started



## gopsutil

获取系统内存、cpu

https://github.com/shirou/gopsutil

使用

```go
package main

import (
    "fmt"

    "github.com/shirou/gopsutil/v3/mem"
    // "github.com/shirou/gopsutil/mem"  // to use v2
)

func main() {
    v, _ := mem.VirtualMemory()

    // almost every return value is a struct
    fmt.Printf("Total: %v, Free:%v, UsedPercent:%f%%\n", v.Total, v.Free, v.UsedPercent)

    // convert to JSON. String() is also implemented
    fmt.Println(v)
}
```



### cli工具

https://github.com/urfave/cli

使用

```go
package main

import (
    "fmt"
    "log"
    "os"

    "github.com/urfave/cli/v2"
)

func main() {
    app := &cli.App{
        Name:  "greet",
        Usage: "fight the loneliness!",
        Action: func(*cli.Context) error {
            fmt.Println("Hello friend!")
            return nil
        },
    }

    if err := app.Run(os.Args); err != nil {
        log.Fatal(err)
    }
}
```



## go脚本

### yaegi



### tengo

安装

```shell
go get github.com/d5/tengo/v2
```

使用

```go
package main

import (
	"context"
	"fmt"

	"github.com/d5/tengo/v2"
)

func main() {
	// create a new Script instance
	script := tengo.NewScript([]byte(
`each := func(seq, fn) {
    for x in seq { fn(x) }
}

sum := 0
mul := 1
each([a, b, c, d], func(x) {
    sum += x
    mul *= x
})`))

	// set values
	_ = script.Add("a", 1)
	_ = script.Add("b", 9)
	_ = script.Add("c", 8)
	_ = script.Add("d", 4)

	// run the script
	compiled, err := script.RunContext(context.Background())
	if err != nil {
		panic(err)
	}

	// retrieve values
	sum := compiled.Get("sum")
	mul := compiled.Get("mul")
	fmt.Println(sum, mul) // "22 288"
}
```

其他脚本语言

https://github.com/d5/tengo 下有介绍



### goja





## 打包

### ko

ko可以轻松地打包go的程序为容器



### distroless

https://github.com/GoogleContainerTools/distroless
