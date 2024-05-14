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

## 网络

https://chai2010.cn/advanced-go-programming-book/ch1-basic/readme.html

Go 在编写 web 应用方面非常得力。因为目前它还没有GUI（Graphic User Interface 即图形化用户界面）的框架，通过文本或者模板展现的 html 页面是目前 Go 编写界面应用程序的唯一方式



### http

http 是比 tcp 更高层的协议，它描述了网页服务器如何与客户端浏览器进行通信。Go 提供了 `net/http` 包

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func HelloServer(w http.ResponseWriter, req *http.Request) {
	fmt.Println("Inside HelloServer handler")
	fmt.Fprintf(w, "Hello,"+req.URL.Path[1:])
}

func main() {
	http.HandleFunc("/", HelloServer)
	err := http.ListenAndServe("localhost:8080", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err.Error())
	}
}
```



### resty

提供go restful api

安装

```go
require github.com/go-resty/resty/v2 v2.11.0
```

使用

```go
import "github.com/go-resty/resty/v2"

// Create a Resty Client
client := resty.New()

resp, err := client.R().
      SetQueryParams(map[string]string{
          "page_no": "1",
          "limit": "20",
          "sort":"name",
          "order": "asc",
          "random":strconv.FormatInt(time.Now().Unix(), 10),
      }).
      SetHeader("Accept", "application/json").
      SetAuthToken("BC594900518B4F7EAC75BD37F019E08FBC594900518B4F7EAC75BD37F019E08F").
      Get("/search_result")


// Sample of using Request.SetQueryString method
resp, err := client.R().
      SetQueryString("productId=232&template=fresh-sample&cat=resty&source=google&kw=buy a lot more").
      SetHeader("Accept", "application/json").
      SetAuthToken("BC594900518B4F7EAC75BD37F019E08FBC594900518B4F7EAC75BD37F019E08F").
      Get("/show_product")


// If necessary, you can force response content type to tell Resty to parse a JSON response into your struct
resp, err := client.R().
      SetResult(result).
      ForceContentType("application/json").
      Get("v2/alpine/manifests/latest")
```



### websocket

安装go的websocket包

```shell
$ go get github.com/gorilla/websocket
```

使用

```go
var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
}

func handler(w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Println(err)
        return
    }
    ... Use conn to send and receive messages.
}
```

https://github.com/gorilla/websocket



https://github.com/nhooyr/websocket

### rpc

RPC (Remote Procedure Call，远程过程调用) 是一种计算机通信协议，允许调用不同进程空间的程序。RPC 的客户端和服务器可以在一台机器上，也可以在不同的机器上。程序员使用时，就像调用本地程序一样，无需关注内部的实现细节。

不同的应用程序之间的通信方式有很多，比如浏览器和服务器之间广泛使用的基于 HTTP 协议的 Restful API。与 RPC 相比，Restful API 有相对统一的标准，因而更通用，兼容性更好，支持不同的语言。HTTP 协议是基于文本的，一般具备更好的可读性。但是缺点也很明显：

- Restful 接口需要额外的定义，无论是客户端还是服务端，都需要额外的代码来处理，而 RPC 调用则更接近于直接调用。
- 基于 HTTP 协议的 Restful 报文冗余，承载了过多的无效信息，而 RPC 通常使用自定义的协议格式，减少冗余报文。
- RPC 可以采用更高效的序列化协议，将文本转为二进制传输，获得更高的性能。
- 因为 RPC 的灵活性，所以更容易扩展和集成诸如注册中心、负载均衡等功能。

Go 程序之间可以使用 `net/rpc` 包实现相互通信，这是另一种客户端-服务器应用场景。它提供了一种方便的途径，通过网络连接调用远程函数。当然，仅当程序运行在不同机器上时，这项技术才实用。`rpc` 包建立在 `gob` 包之上，实现了自动编码/解码传输的跨网络方法调用。

被调用的方法必须满足Go语言的RPC规则：方法只能有两个可序列化的参数，其中第二个参数是指针类型，并且返回一个error类型，同时必须是公开的方法。

```go
type HelloService struct {}

func (p *HelloService) Hello(request string, reply *string) error {
    *reply = "hello:" + request
    return nil
}

//注册rpc服务
func main() {
    rpc.RegisterName("HelloService", new(HelloService))

    listener, err := net.Listen("tcp", ":1234")
    if err != nil {
        log.Fatal("ListenTCP error:", err)
    }

    conn, err := listener.Accept()
    if err != nil {
        log.Fatal("Accept error:", err)
    }

    rpc.ServeConn(conn)
}

//调用rpc服务
func main() {
    client, err := rpc.Dial("tcp", "localhost:1234")
    if err != nil {
        log.Fatal("dialing:", err)
    }

    var reply string
    err = client.Call("HelloService.Hello", "hello", &reply)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println(reply)
}
```

首先是通过rpc.Dial拨号RPC服务，然后通过client.Call调用具体的RPC方法。在调用client.Call时，第一个参数是用点号链接的RPC服务名字和方法名字，第二和第三个参数分别我们定义RPC方法的两个参数。

在涉及RPC的应用中，作为开发人员一般至少有三种角色：首先是服务端实现RPC方法的开发人员，其次是客户端调用RPC方法的人员，最后也是最重要的是制定服务端和客户端RPC接口规范的设计人员。

#### protobuf

Protobuf是Protocol Buffers的简称，它是Google公司开发的一种数据描述语言，并于2008年对外开源。Protobuf刚开源时的定位类似于XML、JSON等数据描述语言，通过附带工具生成代码并实现将结构化数据序列化的功能。但是我们更关注的是Protobuf作为接口规范的描述语言，可以作为设计安全的跨语言PRC接口的基础工具。

我们尝试将Protobuf和RPC结合在一起使用，通过Protobuf来最终保证RPC的接口规范和安全。Protobuf中最基本的数据单元是message，是类似Go语言中结构体的存在。在message中可以嵌套message或其它的基础数据类型的成员。

首先创建hello.proto文件，其中包装HelloService服务中用到的字符串类型

```protobuf
syntax = "proto3";

package main;

message String {
    string value = 1;
}
```

开头的syntax语句表示采用proto3的语法。第三版的Protobuf对语言进行了提炼简化，所有成员均采用类似Go语言中的零值初始化（不再支持自定义默认值），因此消息成员也不再需要支持required特性。然后package指令指明当前是main包（这样可以和Go的包名保持一致，简化例子代码），当然用户也可以针对不同的语言定制对应的包路径和名称。最后message关键字定义一个新的String类型，在最终生成的Go语言代码中对应一个String结构体。String类型中只有一个字符串类型的value成员，该成员编码时用1编号代替名字。

在XML或JSON等数据描述语言中，一般通过成员的名字来绑定对应的数据。但是Protobuf编码却是通过成员的唯一编号来绑定对应的数据，因此Protobuf编码后数据的体积会比较小，但是也非常不便于人类查阅。我们目前并不关注Protobuf的编码技术，最终生成的Go结构体可以自由采用JSON或gob等编码格式，因此大家可以暂时忽略Protobuf的成员编码部分。

Protobuf核心的工具集是C++语言开发的，在官方的protoc编译器中并不支持Go语言。要想基于上面的hello.proto文件生成相应的Go代码，需要安装相应的插件。首先是安装官方的protoc工具，可以从 https://github.com/google/protobuf/releases 下载。然后是安装针对Go语言的代码生成插件，可以通过`go get github.com/golang/protobuf/protoc-gen-go`命令安装。

然后通过以下命令生成相应的Go代码：

```shell
$ protoc --go_out=. hello.proto
```

其中`go_out`参数告知protoc编译器去加载对应的protoc-gen-go工具，然后通过该工具生成代码，生成代码放到当前目录。最后是一系列要处理的protobuf文件的列表。

生成的结构体中还会包含一些以`XXX_`为名字前缀的成员，我们已经隐藏了这些成员。同时String类型还自动生成了一组方法，其中ProtoMessage方法表示这是一个实现了proto.Message接口的方法。此外Protobuf还为每个成员生成了一个Get方法，Get方法不仅可以处理空指针类型，而且可以和Protobuf第二版的方法保持一致（第二版的自定义默认值特性依赖这类方法）。



#### gRPC

gRPC是Google公司基于Protobuf开发的跨语言的开源RPC框架。gRPC基于HTTP/2协议设计，可以基于一个HTTP/2链接提供多个服务，对于移动设备更加友好。

最底层为TCP或Unix Socket协议，在此之上是HTTP/2协议的实现，然后在HTTP/2协议之上又构建了针对Go语言的gRPC核心库。应用程序通过gRPC插件生产的Stub代码和gRPC核心库通信，也可以直接和gRPC核心库通信。

gRPC建立在HTTP/2协议之上，对TLS提供了很好的支持。我们前面章节中gRPC的服务都没有提供证书支持，因此客户端在链接服务器中通过`grpc.WithInsecure()`选项跳过了对服务器证书的验证。没有启用证书的gRPC服务在和客户端进行的是明文通讯，信息面临被任何第三方监听的风险。为了保障gRPC通信不被第三方监听篡改或伪造，我们可以对服务器启动TLS加密特性。

使用以下命令为服务器和客户端分别生成私钥和证书

```shell
$ openssl genrsa -out server.key 2048
$ openssl req -new -x509 -days 3650 \
    -subj "/C=GB/L=China/O=grpc-server/CN=server.grpc.io" \
    -key server.key -out server.crt

$ openssl genrsa -out client.key 2048
$ openssl req -new -x509 -days 3650 \
    -subj "/C=GB/L=China/O=grpc-client/CN=client.grpc.io" \
    -key client.key -out client.crt
```

以上命令将生成server.key、server.crt、client.key和client.crt四个文件。其中以.key为后缀名的是私钥文件，需要妥善保管。以.crt为后缀名是证书文件，也可以简单理解为公钥文件，并不需要秘密保存。在subj参数中的`/CN=server.grpc.io`表示服务器的名字为`server.grpc.io`，在验证服务器的证书时需要用到该信息。

有了证书之后，我们就可以在启动gRPC服务时传入证书选项参数：

```go
func main() {
    creds, err := credentials.NewServerTLSFromFile("server.crt", "server.key")
    if err != nil {
        log.Fatal(err)
    }

    server := grpc.NewServer(grpc.Creds(creds))

    ...
}
```

其中credentials.NewServerTLSFromFile函数是从文件为服务器构造证书对象，然后通过grpc.Creds(creds)函数将证书包装为选项后作为参数传入grpc.NewServer函数。

以上这种方式，需要提前将服务器的证书告知客户端，这样客户端在链接服务器时才能进行对服务器证书认证。在复杂的网络环境中，服务器证书的传输本身也是一个非常危险的问题。如果在中间某个环节，服务器证书被监听或替换那么对服务器的认证也将不再可靠。

为了避免证书的传递过程中被篡改，可以通过一个安全可靠的根证书分别对服务器和客户端的证书进行签名。这样客户端或服务器在收到对方的证书后可以通过根证书进行验证证书的有效性。

基于证书的认证是针对每个gRPC链接的认证。gRPC还为每个gRPC方法调用提供了认证支持，这样就基于用户Token对不同的方法访问进行权限管理。

要实现对每个gRPC方法进行认证，需要实现grpc.PerRPCCredentials接口：

与Web服务共存

gRPC构建在HTTP/2协议之上，因此我们可以将gRPC服务和普通的Web服务架设在同一个端口之上。

对于没有启动TLS协议的服务则需要对HTTP2/2特性做适当的调整：

```go
func main() {
    mux := http.NewServeMux()

    h2Handler := h2c.NewHandler(mux, &http2.Server{})
    server = &http.Server{Addr: ":3999", Handler: h2Handler}
    server.ListenAndServe()
}
```

启动http服务和grpc服务

```go
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
        fmt.Fprintln(w, "hello")
    })

    http.ListenAndServeTLS(port, "server.crt", "server.key",
        http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            mux.ServeHTTP(w, r)
            return
        }),
    )
}

func main() {
    creds, err := credentials.NewServerTLSFromFile("server.crt", "server.key")
    if err != nil {
        log.Fatal(err)
    }

    grpcServer := grpc.NewServer(grpc.Creds(creds))

    ...
}
```

首先gRPC是建立在HTTP/2版本之上，如果HTTP不是HTTP/2协议则必然无法提供gRPC支持。同时，每个gRPC调用请求的Content-Type类型会被标注为"application/grpc"类型。

这样我们就可以在gRPC端口上同时提供Web服务了。

## 服务流量限制

真实环境的程序要复杂得多，有些程序偏网络IO瓶颈，例如一些CDN服务、Proxy服务；有些程序偏CPU/GPU瓶颈，例如登陆校验服务、图像处理服务；有些程序瓶颈偏磁盘，例如专门的存储系统，数据库。不同的程序瓶颈会体现在不同的地方，这里提到的这些功能单一的服务相对来说还算容易分析。如果碰到业务逻辑复杂代码量巨大的模块，其瓶颈并不是三下五除二可以推测出来的，还是需要从压力测试中得到更为精确的结论。

对于IO/Network瓶颈类的程序，其表现是网卡/磁盘IO会先于CPU打满，这种情况即使优化CPU的使用也不能提高整个系统的吞吐量，只能提高磁盘的读写速度，增加内存大小，提升网卡的带宽来提升整体性能。而CPU瓶颈类的程序，则是在存储和网卡未打满之前CPU占用率先到达100%，CPU忙于各种计算任务，IO设备相对则较闲。

无论哪种类型的服务，在资源使用到极限的时候都会导致请求堆积，超时，系统hang死，最终伤害到终端用户。对于分布式的Web服务来说，瓶颈还不一定总在系统内部，也有可能在外部。非计算密集型的系统往往会在关系型数据库环节失守，而这时候Web模块本身还远远未达到瓶颈。

不管我们的服务瓶颈在哪里，最终要做的事情都是一样的，那就是流量限制。

流量限制的手段有很多，最常见的：漏桶、令牌桶两种：

1. 漏桶是指我们有一个一直装满了水的桶，每过固定的一段时间即向外漏一滴水。如果你接到了这滴水，那么你就可以继续服务请求，如果没有接到，那么就需要等待下一滴水。
2. 令牌桶则是指匀速向桶中添加令牌，服务请求时需要从桶中获取令牌，令牌的数目可以按照需要消耗的资源进行相应的调整。如果没有令牌，可以选择等待，或者放弃。

这两种方法看起来很像，不过还是有区别的。漏桶流出的速率固定，而令牌桶只要在桶中有令牌，那就可以拿。也就是说令牌桶是允许一定程度的并发的，比如同一个时刻，有100个用户请求，只要令牌桶中有100个令牌，那么这100个请求全都会放过去。令牌桶在桶中没有令牌的情况下也会退化为漏桶模型。

实际应用中令牌桶应用较为广泛，开源界流行的限流器大多数都是基于令牌桶思想的。并且在此基础上进行了一定程度的扩充，比如`github.com/juju/ratelimit`提供了几种不同特色的令牌桶填充方式



## 数据库

数据库几乎是所有 Web 服务不可或缺的一部分，在所有类型的数据库中，关系型数据库是我们在想要持久存储数据时的首要选择，不过因为关系型数据库的种类繁多，所以 Go 语言的标准库 [`database/sql`](https://golang.org/pkg/database/sql/) 就为访问关系型数据提供了通用的接口，这样不同数据库只要实现标准库中的接口，应用程序就可以通过标准库中的方法访问。

结构化查询语言（Structured Query Language、SQL）是在关系型数据库系统中使用的领域特定语言（Domain-Specific Language、DSL），它主要用于处理结构化的数据[1](https://draveness.me/golang/docs/part4-advanced/ch09-stdlib/golang-database-sql/#fn:1)。作为一门领域特定语言，它由更加强大的表达能力，与传统的命令式 API 相比，它能够提供两个优点：

1. 可以使用单个命令在数据库中访问多条数据；
2. 不需要在查询中指定获取数据的方法；

所有的关系型数据库都会提供 SQL 作为查询语言，应用程序可以使用相同的 SQL 查询在不同数据库中查询数据，

Go官方提供了`database/sql`包来给用户进行和数据库打交道的工作，`database/sql`库实际只提供了一套操作数据库的接口和规范，例如抽象好的SQL预处理（prepare），连接池管理，数据绑定，事务，错误处理等等。官方并没有提供具体某种数据库实现的协议支持。

和具体的数据库，例如MySQL打交道，还需要再引入MySQL的驱动，

```go
package main

import (
    "database/sql"
    _ "github.com/go-sql-driver/mysql"
)

func main() {
    // db 是一个 sql.DB 类型的对象
    // 该对象线程安全，且内部已包含了一个连接池
    // 连接池的选项可以在 sql.DB 的方法中设置，这里为了简单省略了
    db, err := sql.Open("mysql",
        "user:password@tcp(127.0.0.1:3306)/hello")
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    var (
        id int
        name string
    )
    rows, err := db.Query("select id, name from users where id = ?", 1)
    if err != nil {
        log.Fatal(err)
    }

    defer rows.Close()

    // 必须要把 rows 里的内容读完，或者显式调用 Close() 方法，
    // 否则在 defer 的 rows.Close() 执行之前，连接永远不会释放
    for rows.Next() {
        err := rows.Scan(&id, &name)
        if err != nil {
            log.Fatal(err)
        }
        log.Println(id, name)
    }

    err = rows.Err()
    if err != nil {
        log.Fatal(err)
    }
}
```

官方的`db`库提供的功能这么简单，我们每次去数据库里读取内容岂不是都要去写这么一套差不多的代码？或者如果我们的对象是结构体，把`sql.Rows`绑定到对象的工作就会变得更加得重复而无聊。所以Go社区才会有各种各样的SQL Builder和ORM

ORM, 对象关系映射（英语：Object Relational Mapping，简称ORM，或O/RM，或O/R mapping），
是一种程序设计技术，用于实现面向对象编程语言里不同类型系统的数据之间的转换。
从效果上说，它其实是创建了一个可在编程语言里使用的“虚拟对象数据库”。

ORM的目的就是屏蔽掉DB层，很多语言的ORM只要把你的类或结构体定义好，再用特定的语法将结构体之间的一对一或者一对多关系表达出来。那么任务就完成了。然后你就可以对这些映射好了数据库表的对象进行各种操作，例如save，create，retrieve，delete。至于ORM在背地里做了什么阴险的勾当，你是不一定清楚的。使用ORM的时候，我们往往比较容易有一种忘记了数据库的直观感受。

相比ORM来说，SQL Builder在SQL和项目可维护性之间取得了比较好的平衡。首先sql builder不像ORM那样屏蔽了过多的细节，其次从开发的角度来讲，SQL Builder进行简单封装后也可以非常高效地完成开发

写SQL Builder的相关代码，或者读懂都不费劲。把这些代码脑内转换为sql也不会太费劲。所以通过代码就可以对这个查询是否命中数据库索引，是否走了覆盖索引，是否能够用上联合索引进行分析了。

说白了SQL Builder是sql在代码里的一种特殊方言，如果你们没有DBA但研发有自己分析和优化sql的能力，或者你们公司的DBA对于学习这样一些sql的方言没有异议。那么使用SQL Builder是一个比较好的选择，不会导致什么问题。

### bun

golang的ORM https://github.com/uptrace/bun/

安装

```shell
go get github.com/uptrace/bun@latest
```

使用

```go
import (
    "database/sql"
    "github.com/uptrace/bun/driver/sqliteshim"
)

sqldb, err := sql.Open(sqliteshim.ShimName, "file::memory:?cache=shared")
if err != nil {
	panic(err)
}
```

创建/删除表

```go
// Create users table.
res, err := db.NewCreateTable().Model((*User)(nil)).Exec(ctx)

// Drop users table.
res, err := db.NewDropTable().Model((*User)(nil)).Exec(ctx)

// Drop and create tables.
err := db.ResetModel(ctx, (*User)(nil))
```

创建/更新/删除行

```go
// Insert a single user.
user := &User{Name: "admin"}
res, err := db.NewInsert().Model(user).Exec(ctx)

// Insert multiple users (bulk-insert).
users := []User{user1, user2}
res, err := db.NewInsert().Model(&users).Exec(ctx)

user := &User{ID: 1, Name: "admin"}
res, err := db.NewUpdate().Model(user).Column("name").WherePK().Exec(ctx)

user := &User{ID: 1}
res, err := db.NewDelete().Model(user).WherePK().Exec(ctx)
```

查询

```go
// Select a user by a primary key.
user := new(User)
err := db.NewSelect().Model(user).Where("id = ?", 1).Scan(ctx)

// Select first 10 users.
var users []User
err := db.NewSelect().Model(&users).OrderExpr("id ASC").Limit(10).Scan(ctx)
```

自动时间戳

```go
type User struct {
	CreatedAt time.Time `bun:",nullzero,notnull,default:current_timestamp"`
	UpdatedAt time.Time `bun:",nullzero,notnull,default:current_timestamp"`
}

type User struct {
	CreatedAt time.Time `bun:",nullzero,notnull,default:current_timestamp"`
	UpdatedAt bun.NullTime
}
```

### gorm

https://github.com/go-gorm/gorm

安装

```shell
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
```

查询

```go
// 获取第一条记录（主键升序）
db.First(&user)
// SELECT * FROM users ORDER BY id LIMIT 1;

// 获取一条记录，没有指定排序字段
db.Take(&user)
// SELECT * FROM users LIMIT 1;

// 获取最后一条记录（主键降序）
db.Last(&user)
// SELECT * FROM users ORDER BY id DESC LIMIT 1;

result := db.First(&user)
result.RowsAffected // 返回找到的记录数
result.Error        // returns error or nil

// 检查 ErrRecordNotFound 错误
errors.Is(result.Error, gorm.ErrRecordNotFound)
```

关联表(一对一)

```go
// User 有一张 CreditCard，UserID 是外键
type User struct {
  gorm.Model
  CreditCard CreditCard
}

type CreditCard struct {
  gorm.Model
  Number string
  UserID uint
}
```

关联表(一对多)

```go
// User 有多张 CreditCard，UserID 是外键
type User struct {
  gorm.Model
  CreditCards []CreditCard
}

type CreditCard struct {
  gorm.Model
  Number string
  UserID uint
}
```

关联表(多对多)

```go
// User 拥有并属于多种 language，`user_languages` 是连接表
type User struct {
  gorm.Model
  Languages []*Language `gorm:"many2many:user_languages;"`
}

type Language struct {
  gorm.Model
  Name string
  Users []*User `gorm:"many2many:user_languages;"`
}
```



使用示例

```go
package main

import (
  "gorm.io/gorm"
  "gorm.io/driver/sqlite"
)

type Product struct {
  gorm.Model
  Code  string
  Price uint
}

func main() {
  db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
  if err != nil {
    panic("failed to connect database")
  }

  // Migrate the schema
  db.AutoMigrate(&Product{})

  // Create
  db.Create(&Product{Code: "D42", Price: 100})

  // Read
  var product Product
  db.First(&product, 1) // find product with integer primary key
  db.First(&product, "code = ?", "D42") // find product with code D42

  // Update - update product's price to 200
  db.Model(&product).Update("Price", 200)
  // Update - update multiple fields
  db.Model(&product).Updates(Product{Price: 200, Code: "F42"}) // non-zero fields
  db.Model(&product).Updates(map[string]interface{}{"Price": 200, "Code": "F42"})

  // Delete - delete product
  db.Delete(&product, 1)
}
```



### xorm



### bbolt

go内嵌的key-value数据库

https://github.com/etcd-io/bbolt



## Webrtc



https://github.com/pion/webrtc



## webhook

https://github.com/adnanh/webhook

https://www.kandaoni.com/news/14421.html

## Graphql

https://github.com/graphql-go/graphql

安装

```shell
go get github.com/graphql-go/graphql
```

使用

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"

	"github.com/graphql-go/graphql"
)

func main() {
	// Schema
	fields := graphql.Fields{
		"hello": &graphql.Field{
			Type: graphql.String,
			Resolve: func(p graphql.ResolveParams) (interface{}, error) {
				return "world", nil
			},
		},
	}
	rootQuery := graphql.ObjectConfig{Name: "RootQuery", Fields: fields}
	schemaConfig := graphql.SchemaConfig{Query: graphql.NewObject(rootQuery)}
	schema, err := graphql.NewSchema(schemaConfig)
	if err != nil {
		log.Fatalf("failed to create new schema, error: %v", err)
	}

	// Query
	query := `
		{
			hello
		}
	`
	params := graphql.Params{Schema: schema, RequestString: query}
	r := graphql.Do(params)
	if len(r.Errors) > 0 {
		log.Fatalf("failed to execute graphql operation, errors: %+v", r.Errors)
	}
	rJSON, _ := json.Marshal(r)
	fmt.Printf("%s \n", rJSON) // {"data":{"hello":"world"}}
}
```

### genqlient

https://github.com/Khan/genqlient

## conc

更好地并发编程工具，基于标准库stdlib进行封装

安装

```shell
go get github.com/sourcegraph/conc
```

更好地处理panic错误

```go
// 标准库
type caughtPanicError struct {
    val   any
    stack []byte
}

func (e *caughtPanicError) Error() string {
    return fmt.Sprintf(
        "panic: %q\n%s",
        e.val,
        string(e.stack)
    )
}

func main() {
    done := make(chan error)
    go func() {
        defer func() {
            if v := recover(); v != nil {
                done <- &caughtPanicError{
                    val: v,
                    stack: debug.Stack()
                }
            } else {
                done <- nil
            }
        }()
        doSomethingThatMightPanic()
    }()
    err := <-done
    if err != nil {
        panic(err)
    }
}
// conc编程
func main() {
    var wg conc.WaitGroup
    wg.Go(doSomethingThatMightPanic)
    // panics with a nice stacktrace
    wg.Wait()
}
```

https://github.com/sourcegraph/conc



## 热更新

### fresh

https://github.com/gravityblast/fresh



## 路由

### gocraft/web

https://github.com/gocraft/web

go的路由和中间件



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
