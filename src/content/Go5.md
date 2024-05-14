---
title: Golang语言开发（五）
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - Web,IT,Go
toc: true
thumbnail: https://s1.ax1x.com/2020/04/20/J1Iu4O.th.jpg
---

　　本篇主要内容为go语言开发网页应用

<!--more-->

## Fiber

fiber是受express启发，致力于最快的http框架

安装

```shell
go get -u github.com/gofiber/fiber/v2
```

使用

```go
func main() {
    app := fiber.New()

    // GET /api/register
    app.Get("/api/*", func(c *fiber.Ctx) error {
        msg := fmt.Sprintf("✋ %s", c.Params("*"))
        return c.SendString(msg) // => ✋ register
    })

    // GET /flights/LAX-SFO
    app.Get("/flights/:from-:to", func(c *fiber.Ctx) error {
        msg := fmt.Sprintf("💸 From: %s, To: %s", c.Params("from"), c.Params("to"))
        return c.SendString(msg) // => 💸 From: LAX, To: SFO
    })

    // GET /dictionary.txt
    app.Get("/:file.:ext", func(c *fiber.Ctx) error {
        msg := fmt.Sprintf("📃 %s.%s", c.Params("file"), c.Params("ext"))
        return c.SendString(msg) // => 📃 dictionary.txt
    })

    // GET /john/75
    app.Get("/:name/:age/:gender?", func(c *fiber.Ctx) error {
        msg := fmt.Sprintf("👴 %s is %s years old", c.Params("name"), c.Params("age"))
        return c.SendString(msg) // => 👴 john is 75 years old
    })

    // GET /john
    app.Get("/:name", func(c *fiber.Ctx) error {
        msg := fmt.Sprintf("Hello, %s 👋!", c.Params("name"))
        return c.SendString(msg) // => Hello john 👋!
    })

    log.Fatal(app.Listen(":3000"))
}
```

静态文件

```go
func main() {
    app := fiber.New()

    app.Static("/", "./public")
    // => http://localhost:3000/js/script.js
    // => http://localhost:3000/css/style.css

    app.Static("/prefix", "./public")
    // => http://localhost:3000/prefix/js/script.js
    // => http://localhost:3000/prefix/css/style.css

    app.Static("*", "./public/index.html")
    // => http://localhost:3000/any/path/shows/index/html

    log.Fatal(app.Listen(":3000"))
}
```

中间件

```go
func main() {
    app := fiber.New()

    // Match any route
    app.Use(func(c *fiber.Ctx) error {
        fmt.Println("🥇 First handler")
        return c.Next()
    })

    // Match all routes starting with /api
    app.Use("/api", func(c *fiber.Ctx) error {
        fmt.Println("🥈 Second handler")
        return c.Next()
    })

    // GET /api/list
    app.Get("/api/list", func(c *fiber.Ctx) error {
        fmt.Println("🥉 Last handler")
        return c.SendString("Hello, World 👋!")
    })

    log.Fatal(app.Listen(":3000"))
}
```

代理

```go
import (
    "github.com/gofiber/fiber/v2"
    "github.com/gofiber/fiber/v2/middleware/recover"
)

func main() {
    app := fiber.New(fiber.Config{
        EnableTrustedProxyCheck: true,
        TrustedProxies: []string{"0.0.0.0", "1.1.1.1/30"}, // IP address or IP address range
        ProxyHeader: fiber.HeaderXForwardedFor},
    })

    // ...

    log.Fatal(app.Listen(":3000"))
}
```

websocket支持

```go
app.Get("/ws", websocket.New(func(c *websocket.Conn) {
  // Websocket logic
  for {
    mtype, msg, err := c.ReadMessage()
    if err != nil {
      break
    }
    log.Printf("Read: %s", msg)

    err = c.WriteMessage(mtype, msg)
    if err != nil {
      break
    }
  }
  log.Println("Error:", err)
}))
```



## Iris

安装

```shell
go get github.com/kataras/iris/v12@master # or @v12.2.0-beta2
```

使用

```go
package main

import "github.com/kataras/iris/v12"

func main() {
	app := iris.New()
	app.Use(iris.Compression)

	app.Get("/", func(ctx iris.Context) {
		ctx.HTML("Hello <strong>%s</strong>!", "World")
	})

	app.Listen(":8080")
}
```



## Gin

Gin 特性

- **快速**：路由不使用反射，基于 Radix 树，内存占用少。
- **中间件**：HTTP 请求，可先经过一系列中间件处理，例如：Logger，Authorization，GZIP 等。这个特性和 NodeJs 的 `Koa` 框架很像。中间件机制也极大地提高了框架的可扩展性。
- **异常处理**：服务始终可用，不会宕机。Gin 可以捕获 panic，并恢复。而且有极为便利的机制处理 HTTP 请求过程中发生的错误。
- **JSON**：Gin 可以解析并验证请求的 JSON。这个特性对 `Restful API` 的开发尤其有用。
- **路由分组**：例如将需要授权和不需要授权的 API 分组，不同版本的 API 分组。而且分组可嵌套，且性能不受影响。
- **渲染内置**：原生支持 JSON，XML 和 HTML 的渲染。

安装

```go
go get -u github.com/gin-gonic/gin
```

新建gin.go文件

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main(){
  g := gin.Default()
  
  g.GET("/hello",func(c *gin.Context){
    c.JSON(200,gin.H{
      "message":"hello world",
    })
  })
  
  g.Run();
}
```

依赖导入，执行命令

```go
go mod init git-demo
go mod tidy
```

启动命令

```go
go run gin.go
```

Gin 框架不会自动为 GET 请求生成 HEAD 请求的处理方法。因此，如果要支持 HEAD 请求，你需要为对应的请求路径显式地编写一个处理 HEAD 请求的函数

```go
import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()

    r.GET("/example", func(c *gin.Context) {
        // 这里是 GET 请求的业务逻辑
        c.String(http.StatusOK, "Hello, GET request!")
    })

    r.HEAD("/example", func(c *gin.Context) {
        // 这里是 HEAD 请求的业务逻辑
        c.Status(http.StatusOK)
    })

    r.Run(":8080")
}
```





## GoFrame



```go
go get -u -v github.com/gogf/gf
```





## echo

http服务框架 http://echo.topgoer.com/%E4%B8%AD%E9%97%B4%E4%BB%B6/%E4%BC%9A%E8%AF%9D.html

安装

```shell
go get github.com/labstack/echo/v4
```

使用

```go
package main

import (
  "github.com/labstack/echo/v4"
  "github.com/labstack/echo/v4/middleware"
  "net/http"
)

func main() {
  // Echo instance
  e := echo.New()

  // Middleware
  e.Use(middleware.Logger())
  e.Use(middleware.Recover())

  // Routes
  e.GET("/", hello)

  // Start server
  e.Logger.Fatal(e.Start(":1323"))
}

// Handler
func hello(c echo.Context) error {
  return c.String(http.StatusOK, "Hello, World!")
}
```

### 路由

基于 [radix tree](http://en.wikipedia.org/wiki/Radix_tree) ，Echo 的路由查询速度非常快。路由使用 [sync pool](https://golang.org/pkg/sync/#Pool) 来重用内存，实现无 GC 开销下的零动态内存分配。

通过特定的 HTTP 方法，url 路径和一个匹配的处理程序 (handler) 可以注册一个路由。例如，下面的代码则展示了一个注册路由的例子：它包括 `Get` 的访问方式， `/hello` 的访问路径，以及发送 `Hello World` HTTP 响应的处理程序

```go
// 业务处理
func hello(c echo.Context) error {
      return c.String(http.StatusOK, "Hello, World!")
}

// 路由
e.GET("/hello", hello)
```

你可以用 `Echo.Any(path string, h Handler)` 来为所有的 HTTP 方法发送注册 处理程序(handler)；如果仅需要为某个特定的方法注册路由，可使用 `Echo.Match(methods []string, path string, h Handler)`。

Echo 通过 `func(echo.Context) error` 定义 handler 方法，其中 `echo.Context` 已经内嵌了 HTTP 请求和响应接口

路由匹配的路径会按照固定路径 -参数路径-匹配所有的顺序

```go
e.GET("/users/:id", func(c echo.Context) error {
    return c.String(http.StatusOK, "/users/:id")
})

e.GET("/users/new", func(c echo.Context) error {
    return c.String(http.StatusOK, "/users/new")
})

e.GET("/users/1/files/*", func(c echo.Context) error {
    return c.String(http.StatusOK, "/users/1/files/*")
})
```

上面定义的路由将按下面的优先级顺序匹配:

- `/users/new`
- `/users/:id`
- `/users/1/files/*`

### 中间件

中间件是一个函数，嵌入在HTTP 的请求和响应之间。它可以获得 `Echo#Context` 对象用来进行一些特殊的操作， 比如记录每个请求或者统计请求数。

Action的处理在所有的中间件运行完成之后

root level

Before router

`Echo#Pre()` 用于注册一个在路由执行之前运行的中间件，可以用来修改请求的一些属性。比如在请求路径结尾添加或者删除一个'/'来使之能与路由匹配

下面的这几个内建中间件被注册在这一级别：

- AddTrailingSlash
- RemoveTrailingSlash
- MethodOverride

*注意*: 由于在这个级别路由还没有执行，所以这个级别的中间件不能调用任何 `echo.Context` 的 API

After route

大部分时间你将用到 `Echo#Use()` 在这个级别注册中间件。 这个级别的中间件运行在路由处理完请求之后，可以调用所有的 `echo.Context` API。

下面的这几个内建中间件应该被注册在这一级别：

- BodyLimit
- Logger
- Gzip
- Recover
- BasicAuth
- JWTAuth
- Secure
- CORS
- Static

Group Level

当在路由中创建一个组的时候，可以为这个组注册一个中间件。例如，给 admin 这个组注册一个 BasicAuth 中间件

Route level

当你创建了一个新的路由，可以选择性的给这个路由注册一个中间件。

代理中间件

Proxy 提供 HTTP / WebSocket 反向代理中间件。它使用已配置的负载平衡技术将请求转发到上游服务器。

```go
url1, err := url.Parse("http://localhost:8081")
if err != nil {
  e.Logger.Fatal(err)
}
url2, err := url.Parse("http://localhost:8082")
if err != nil {
  e.Logger.Fatal(err)
}
e.Use(middleware.Proxy(&middleware.RoundRobinBalancer{
  Targets: []*middleware.ProxyTarget{
    {
      URL: url1,
    },
    {
      URL: url2,
    },
  },
}))
```

#### 常用中间件

auth中间件

```go
e.Use(middleware.BasicAuth(func(username, password string, c echo.Context) (bool, error) {
    if username == "joe" && password == "secret" {
        return true, nil
    }
    return false, nil
}))
```



重定向中间件



日志

Logger 中间件记录有关每个 HTTP 请求的信息。

```
e.Use(middleware.Logger())
```

Casbin Auth中间件

[Casbin](https://github.com/casbin/casbin) 是 Go 下的强大而高效的开源访问控制库，它为基于各种模型的授权提供支持。到目前为止，Casbin 支持的访问控制模型如下：

- ACL (访问控制列表)
- 超级用户下的ACL
- 没有用户的 ACL： 对于没有身份验证或用户登录的系统尤其有用。
- 没有资源的ACL：过使用 write-article , read-log 等权限，某些方案可以应对一类资源而不是单个资源，它不控制对特定文章或日志的访问。
- RBAC (基于角色的访问控制)
- 具有资源角色的 RBAC： 用户和资源可以同时具有角色 (或组)。
- 具有域 / 租户 (tenants) 的 RBAC ：用户可以针对不同的域 / 租户 (tenants) 具有不同的角色集。
- ABAC (基于属性的访问控制)
- RESTful
- Deny-override：支持允许和拒绝授权，否认覆盖允许。





### 错误处理

Echo 提倡通过中间件或处理程序 (handler) 返回 HTTP 错误集中处理。集中式错误处理程序允许我们从统一位置将错误记录到外部服务，并向客户端发送自定义 HTTP 响应。

你可以返回一个标准的 `error` 或者 `echo.*HTTPError`



## hertz

安装

```shell
go install github.com/cloudwego/hertz/cmd/hz@latest
```

新建main.go文件

```go
package main

import (
    "context"

    "github.com/cloudwego/hertz/pkg/app"
    "github.com/cloudwego/hertz/pkg/app/server"
    "github.com/cloudwego/hertz/pkg/common/utils"
    "github.com/cloudwego/hertz/pkg/protocol/consts"
)

func main() {
    h := server.Default()

    h.GET("/ping", func(c context.Context, ctx *app.RequestContext) {
            ctx.JSON(consts.StatusOK, utils.H{"message": "pong"})
    })

    h.Spin()
}
```

更多方法

```go
package main

import (
	"context"
	"github.com/cloudwego/hertz/pkg/app"
	"github.com/cloudwego/hertz/pkg/app/server"
	"github.com/cloudwego/hertz/pkg/protocol/consts"
)

func main(){
	h := server.Default(server.WithHostPorts("127.0.0.1:8080"))
	
	h.StaticFS("/", &app.FS{Root: "./", GenerateIndexPages: true})

	h.GET("/get", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "get")
	})
	h.POST("/post", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "post")
	})
	h.PUT("/put", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "put")
	})
	h.DELETE("/delete", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "delete")
	})
	h.PATCH("/patch", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "patch")
	})
	h.HEAD("/head", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "head")
	})
	h.OPTIONS("/options", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "options")
	})
	h.Any("/ping_any", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "any")
	})
	h.Handle("LOAD","/load", func(ctx context.Context, c *app.RequestContext) {
		c.String(consts.StatusOK, "load")
	})
	h.Spin()
}
```

### 中间件

路由级别的中间件

```go
package main

import (
	"context"
	"fmt"
	"net/http"

	"github.com/cloudwego/hertz/pkg/app"
	"github.com/cloudwego/hertz/pkg/app/server"
)

func PathMiddleware() []app.HandlerFunc {
	return []app.HandlerFunc{func(ctx context.Context, c *app.RequestContext) {
		fmt.Println("path middleware")
		c.Next(ctx)
	}}
}

func main() {
	h := server.Default(server.WithHostPorts("127.0.0.1:8888"))

	h.GET("/path", append(PathMiddleware(),
		func(ctx context.Context, c *app.RequestContext) {
			c.String(http.StatusOK, "path")
		})...)

	h.Spin()
}

```

分组级别的中间件

```go
h := server.Default()
group := h.Group("/group")
group.Use(GroupMiddleware())
// or

package main

import (
	"context"
	"fmt"

	"github.com/cloudwego/hertz/pkg/app"
	"github.com/cloudwego/hertz/pkg/app/server"
)

func GroupMiddleware() []app.HandlerFunc {
	return []app.HandlerFunc{func(ctx context.Context, c *app.RequestContext) {
		fmt.Println("group middleware")
		c.Next(ctx)
	}}
}

func main() {
	h := server.Default(server.WithHostPorts("127.0.0.1:8888"))

	group := h.Group("/group", append(GroupMiddleware(),
        func(ctx context.Context, c *app.RequestContext) {
            fmt.Println("group middleware 2")
            c.Next(ctx)
        })...)
	// ...
	h.Spin()
}
```

服务级别的中间件

```go
h := server.Default()
h.Use(MyMiddleware())
```

## Martini

Martini 是一个强大为了编写模块化 Web 应用而生的 GO 语言框架.

server.go

```go
package main

import "github.com/go-martini/martini"

func main() {
  m := martini.Classic()
  m.Get("/", func() string {
    return "Hello world!"
  })
  m.Run()
}
```

### 路由

在 Martini 中，路由是一个 HTTP 方法配对一个 URL 匹配模型。每一个路由可以对应一个或多个处理器方法

```go
m.Get("/", func() {
  // 显示
})

m.Patch("/", func() {
  // 更新
})

m.Post("/", func() {
  // 创建
})

m.Put("/", func() {
  // 替换
})

m.Delete("/", func() {
  // 删除
})

m.Options("/", func() {
  // http 选项
})

m.NotFound(func() {
  // 处理 404
})
```

路由匹配的顺序是按照他们被定义的顺序执行的。最先被定义的路由将会首先被用户请求匹配并调用.

路由模型可能包含参数列表，可以通过 [martini.Params](http://godoc.org/github.com/go-martini/martini#Params) 服务来获取:

路由匹配可以通过正则表达式或者 glob 的形式

```go
m.Get("/hello/:name", func(params martini.Params) string {
  return "Hello " + params["name"]
})

m.Get("/hello/**", func(params martini.Params) string {
  return "Hello " + params["_1"]
})

m.Get("/hello/(?P<name>[a-zA-Z]+)", func(params martini.Params) string {
  return fmt.Sprintf ("Hello %s", params["name"])
})
```

也可以通过Group方法将route编成一组，然后增加midelware中间件

```go
m.Group("/books", func(r martini.Router) {
    r.Get("/:id", GetBooks)
    r.Post("/new", NewBook)
    r.Put("/update/:id", UpdateBook)
    r.Delete("/delete/:id", DeleteBook)
}, MyMiddleware1, MyMiddleware2)
```



## 测试框架

### ginkgo

使用

```go
import (
    . "github.com/onsi/ginkgo/v2"
    . "github.com/onsi/gomega"
    ...
)

Describe("Checking books out of the library", Label("library"), func() {
    var library *libraries.Library
    var book *books.Book
    var valjean *users.User
    BeforeEach(func() {
        library = libraries.NewClient()
        book = &books.Book{
            Title: "Les Miserables",
            Author: "Victor Hugo",
        }
        valjean = users.NewUser("Jean Valjean")
    })

    When("the library has the book in question", func() {
        BeforeEach(func(ctx SpecContext) {
            Expect(library.Store(ctx, book)).To(Succeed())
        })

        Context("and the book is available", func() {
            It("lends it to the reader", func(ctx SpecContext) {
                Expect(valjean.Checkout(ctx, library, "Les Miserables")).To(Succeed())
                Expect(valjean.Books()).To(ContainElement(book))
                Expect(library.UserWithBook(ctx, book)).To(Equal(valjean))
            }, SpecTimeout(time.Second * 5))
        })

        Context("but the book has already been checked out", func() {
            var javert *users.User
            BeforeEach(func(ctx SpecContext) {
                javert = users.NewUser("Javert")
                Expect(javert.Checkout(ctx, library, "Les Miserables")).To(Succeed())
            })

            It("tells the user", func(ctx SpecContext) {
                err := valjean.Checkout(ctx, library, "Les Miserables")
                Expect(error).To(MatchError("Les Miserables is currently checked out"))
            }, SpecTimeout(time.Second * 5))

            It("lets the user place a hold and get notified later", func(ctx SpecContext) {
                Expect(valjean.Hold(ctx, library, "Les Miserables")).To(Succeed())
                Expect(valjean.Holds(ctx)).To(ContainElement(book))

                By("when Javert returns the book")
                Expect(javert.Return(ctx, library, book)).To(Succeed())

                By("it eventually informs Valjean")
                notification := "Les Miserables is ready for pick up"
                Eventually(ctx, valjean.Notifications).Should(ContainElement(notification))

                Expect(valjean.Checkout(ctx, library, "Les Miserables")).To(Succeed())
                Expect(valjean.Books(ctx)).To(ContainElement(book))
                Expect(valjean.Holds(ctx)).To(BeEmpty())
            }, SpecTimeout(time.Second * 10))
        })  
    })

    When("the library does not have the book in question", func() {
        It("tells the reader the book is unavailable", func(ctx SpecContext) {
            err := valjean.Checkout(ctx, library, "Les Miserables")
            Expect(error).To(MatchError("Les Miserables is not in the library catalog"))
        }, SpecTimeout(time.Second * 5))
    })
})
```



## 微服务

### eagle

https://github.com/go-eagle/eagle



### go-micro

go微服务

```go
import "go-micro.dev/v4"

// create a new service
service := micro.NewService(
    micro.Name("helloworld"),
    micro.Handle(new(Helloworld)),
)

// initialise flags
service.Init()

// start the service
service.Run()
```



### cadence

https://github.com/uber/cadence



## Monorepo

go的monorepo使用replace替换

```go
module github.com/earthly/earthly/examples/go-monorepo/services/one

go 1.17

require (
  github.com/earthly/earthly/examples/go-monorepo/libs/hello v0.0.0
  github.com/labstack/echo/v4 v4.6.3
)

replace github.com/earthly/earthly/examples/go-monorepo/libs/hello v0.0.0 => ../../libs/hello
```

构建工具

earthly

安装

```shell
brew install earthly/earthly/earthly && earthly bootstrap
```

https://earthly.dev/

## go-clean-arch



https://github.com/bxcodec/go-clean-arch



## Node、Go、Python对比

Go的语法简洁，是强语言类型，效率高，可直接被编译为机器码，





