---
title: NodeJs开发（九） 
date: 2021-1-23 21:40:33
categories: IT
tags:
    - IT，Web
toc: true
thumbnail: http://cdn.kunkunzhang.top/nestjs.png
---

  Node的seveless服务

<!--more-->

## Fastify.js

高效的服务器意味着更低的基础设施成本、更好的负载响应能力和用户满意度。 在不牺牲安全验证和便捷开发的前提下，如何知道服务器正在处理尽可能多的请求，又如何有效地处理服务器资源？

Fastify 是一个 web 开发框架，其设计灵感来自 Hapi 和 Express，致力于以最少的开销和强大的插件结构提供最佳的开发体验。据我们所知，它是这个领域里速度最快的 web 框架之一。

Fastify 已经实现的主要功能及原理：

- **高性能：** 据我们所知，Fastify 是这一领域中最快的 web 框架之一，另外，取决于代码的复杂性，Fastify 最多可以处理每秒 3 万次的请求。
- **可扩展：** Fastify 通过其提供的钩子（hook）、插件和装饰器（decorator）提供完整的可扩展性。
- **基于 Schema：** 即使这不是强制性的，我们仍建议使用 [JSON Schema](http://json-schema.org/) 来做路由（route）验证及输出内容的序列化，Fastify 在内部将 schema 编译为高效的函数并执行。
- **日志：** 日志是非常重要且代价高昂的。我们选择了最好的日志记录程序来尽量消除这一成本，这就是 [Pino](https://github.com/pinojs/pino)!
- **对开发人员友好：** 框架的使用很友好，帮助开发人员处理日常工作，并且不牺牲性能和安全性。
- **支持 TypeScript：** 我们努力维护一个 [TypeScript](https://www.typescriptlang.org/) 类型声明文件，以便支持不断成长的 TypeScript 社区

安装

```shell
npm i fastify --save
```

创建服务

```javascript
// ESM
import Fastify from 'fastify'
const fastify = Fastify({
  logger: true
})
// CommonJs
const fastify = require('fastify')({
  logger: true
})

fastify.get('/', async (request, reply) => {
  return { hello: 'world' }
})

const start = async () => {
  try {
    await fastify.listen(3000)
  } catch (err) {
    fastify.log.error(err)
    process.exit(1)
  }
}
start()
```

### 序列化json

Fastify 对 JSON 提供了优异的支持，极大地优化了解析 JSON body 与序列化 JSON 输出的过程。
在 schema 的选项中设置 `response` 的值，能够加快 JSON 的序列化 (没错，这很慢！)

```javascript
const opts = {
  schema: {
    response: {
      200: {
        type: 'object',
        properties: {
          hello: { type: 'string' }
        }
      }
    }
  }
}

fastify.get('/', opts, async (request, reply) => {
  return { hello: 'world' }
})
```

一旦指明了 schema，序列化的速度就能达到原先的 2-3 倍。这么做同时也保护了潜在的敏感数据不被泄露，因为 Fastify 仅对 schema 里出现的数据进行序列化

### content-type

Fastify 原生只支持 `'application/json'` 和 `'text/plain'` content type。默认的字符集是 `utf-8`。如果你需要支持其他的 content type，你需要使用 `addContentTypeParser` API。*默认的 JSON 或者纯文本解析器也可以被更改或删除。*

*注：假如你决定用 `Content-Type` 自定义 content type，UTF-8 便不再是默认的字符集了。请确保如下包含该字符集：`text/html; charset=utf-8`。*

和其他的 API 一样，`addContentTypeParser` 被封装在定义它的作用域中了。这就意味着如果你定义在了根作用域中，那么就是全局可用，如果你定义在一个插件中，那么它只能在那个作用域和子作用域中可用



### 日志

日志默认关闭，你可以在创建 Fastify 实例时传入 `{ logger: true }` 或者 `{ logger: { level: 'info' } }` 选项来开启它。要注意的是，日志无法在运行时启用。为此，我们使用了 [abstract-logging](https://www.npmjs.com/package/abstract-logging)。

Fastify 专注于性能，因此使用了 [pino](https://github.com/pinojs/pino) 作为日志工具。默认的日志级别为 `'info'`

开启日志

```javascript
const fastify = require('fastify')({
  logger: true
})

fastify.get('/', options, function (request, reply) {
  request.log.info('Some info about the current request')
  reply.send({ hello: 'world' })
})
```

你还可以提供自定义的日志实例。将实例传入，取代配置选项即可。提供的示例必须实现 Pino 的接口，换句话说，便是拥有下列方法： `info`、`error`、`debug`、`fatal`、`warn`、`trace`、`child`

```javascript
const log = require('pino')({ level: 'info' })
const fastify = require('fastify')({ logger: log })

log.info('does not have request information')

fastify.get('/', function (request, reply) {
  request.log.info('includes request information, but is the same logger instance as `log`')
  reply.send({ hello: 'world' })
})
```

[Pino](https://getpino.io/) 支持低开销的日志修订，以隐藏特定内容。 举例来说，出于安全方面的考虑，我们也许想在 HTTP header 的日志中隐藏 `Authorization` 这一个 header

```javascript
const fastify = Fastify({
  logger: {
    stream: stream,
    redact: ['req.headers.authorization'],
    level: 'info',
    serializers: {
      req (request) {
        return {
          method: request.method,
          url: request.url,
          headers: request.headers,
          hostname: request.hostname,
          remoteAddress: request.ip,
          remotePort: request.socket.remotePort
        }
      }
    }
  }
})
```

### graphQL

#### mercurius

https://github.com/mercurius-js/mercurius



## thinkjs



## nitro

node Server 

```shell
yarn dlx giget@latest nitro nitro-app --install
```





## feathersjs

创建feathers项目

```shell
npm create feathers@pre feathers-chat
```

启动项目

```shell
npm run compile
npm run migrate
npm start
```

使用

```javascript
import type { Application, Id, NullableId, Params } from '@feathersjs/feathers'

class MyService {
  async find(params: Params) {}
  async get(id: Id, params: Params) {}
  async create(data: any, params: Params) {}
  async update(id: NullableId, data: any, params: Params) {}
  async patch(id: NullableId, data: any, params: Params) {}
  async remove(id: NullableId, params: Params) {}
  async setup(path: string, app: Application) {}
  async teardown(path: string, app: Application) {}
}
```



## firebase

[Firebase](https://firebase.google.com/)是Google Cloud Platform为应用开发者们（特别是全栈开发）推出的应用后台服务。借助Firebase，应用开发者们可以快速搭建应用后台，集中注意力在开发client上，并且可以享受到Google Cloud的稳定性和scalability。

Firebase为后台开发提供以下几个功能：

- 实时数据库（Realtime database）
- 用户认证（Authentication）
- 自定义API（Cloud function）
- 消息推送（Cloud messaging）
- 静态网页Hosting
- 云存储（Cloud storage）

实时数据库是Firebase提供的核心功能。通过为Android， iOS跟Web（JavaScript）提供SDK，前端开发者们可以轻松的读写Firebase的数据库（no-SQL，Json）

```javascript
// 更新用户信息（username， email）记录到/users表
// userId是为用户表的"主键"
firebase.database().ref('users/' + userId).set({
    username: name,
    email: email,
});
```

实时数据库中的另一个关键字是实时。开发者可以利用SDK在client中监听数据库的变化，并实时获得结果

```javascript
// /messages表发生变化执行function
firebase.database().ref("messages/").on('value', function(dataSnapshot) {
  ...
});
// /messages表添加纪录执行function
firebase.database().ref("messages/").on('child_added', function(childSnapshot, prevChildKey) {
  ...
});
...
```

实时数据库也有Authorization，可以在firebase console中通过一个Json文件轻松设置：

```shell
// /users表中每个用户只可以修改自己的信息 （uid来自Firebase另一个功能，下面会介绍）
{
  "rules": {
    "users": {
      "$uid": {
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

### 用户认证

Firebase提供了基于email的用户认证。通过client SDK，开发者可以轻松的实现账户注册，登陆登出，修改密码，忘记密码等常用功能。并且可以轻松集成3rd party Authentication方式（Google Signin，Facebook Login，Github，Twitter）以及任何customize的认证服务。支持手机号登陆，短信功能。

```javascript
// 注册新账户
firebase.auth().createUserWithEmailAndPassword(email, password).catch(function(error) {
});
// 账户登陆
firebase.auth().signInWithEmailAndPassword(email, password).catch(function(error) {
});
// 认证信息更新监听（认证成功，认证失效，密码更改）
firebase.auth().onAuthStateChanged(function(user) {
});
...
```

在Firebase server端，可以与Cloud function（下面会详细介绍）集成，实现例如新用户注册后发送verification邮件等功能

```javascript
// 账户创建监听
exports.sendWelcomeEmail = functions.auth.user().onCreate(event => {
});
// 账户删除监听
exports.sendByeEmail = functions.auth.user().onDelete(event => {
});
```

Cloud function相当于Firebase提供的一个node.js的server附加Firebase与其他功能的集成（例如上面与Authentication集成的案例），与Amazon AWS的[lambda](https://aws.amazon.com/lambda/)有异曲同工之妙。通过Cloud function，开发者可以自己定义cloud API，将原本client的部分功能迁移到server端，轻量化client。该功能在密集迭代中，相信未来会提供更多与Google产品的集成。

### 自定义API

Cloud function相当于Firebase提供的一个node.js的server附加Firebase与其他功能的集成（例如上面与Authentication集成的案例），与Amazon AWS的[lambda](https://aws.amazon.com/lambda/)有异曲同工之妙。通过Cloud function，开发者可以自己定义cloud API，将原本client的部分功能迁移到server端，轻量化client。该功能在密集迭代中，相信未来会提供更多与Google产品的集成

### 消息推送

Firebase提供了消息推送功能。通过client side SDK产生token注册至firebase服务器，并自动监听任何消息推送。开发者或者管理者可以在任何地方（服务器或者个人电脑）对任何一个device发送推送消息，提高engagement。

```javascript
// 初次请求用户允许推送消息
messaging.requestPermission().then(function() {

}).catch(function(err) {

});
// 获得token并自动注册至cloud messaging服务器
messaging.getToken().then(function(currentToken) {
});
// 定期刷新token
messaging.onTokenRefresh(function() {
});
```

### 静态网页Host

Firebase提供了最基本的web hosting功能。对于web应用开发者来说提供了极大的便利，client的代码不需要另外host，而与Firebase API server共同host在Google Cloud中，提供了效率，降低了成本。Firebase会为web hosting提供一个免费的hostname，允许开发者可以随意更换为任何自己拥有的hostname。

### 云存储

Firebase除了通过实时数据库对structured数据的支持，还通过云存储来提供上传下载大文件（blob file）。通过下面client代码案例来说明开发者如何操作云存储（上传为例）

```javascript
// 上传文件file到云存储的images/rivers.jpg位置
var uploadTask = storageRef.child('images/rivers.jpg').put(file);
// 监听上传任务的状态（进度，完成，异常等）
uploadTask.on('state_changed', function(snapshot){
})
// 暂停上传
uploadTask.pause();
// 继续上传
uploadTask.resume();
// 取消上传
uploadTask.cancel();
```

### 在web项目中引入firebase

安装

```shell
npm install firebase
```

使用

```javascript
import { initializeApp } from 'firebase/app';

// TODO: Replace the following with your app's Firebase project configuration
const firebaseConfig = {
  //...
};

const app = initializeApp(firebaseConfig);
```

Firebase 应用是一种类似于容器的对象，用于存储常见配置并在各种 Firebase 服务之间共享身份验证凭据。在代码中初始化 Firebase 应用对象后，可以添加并开始使用 Firebase 服务。

https://github.com/marketplace/actions/qiniu-upload

firebase 控制台：https://console.firebase.google.com/project/kun-extension/appcheck?hl=zh-cn

## supabase

**Supabase** 是一个开源的 **Firebase** 替代方案。官方表示，其正在使用企业级开源工具构建 Firebase 的功能。Supabase 可以：

- 监听数据库的变化。
- 查询你的表，包括过滤、分页和深度嵌套关系（如GraphQL）。
- 创建、更新和删除行。
- 管理你的用户和他们的权限。
- 使用一个简单的用户界面与你的数据库进行交互。

Supabase的美妙之处在于，在可能的情况下，他们选择了使用现有的库和数据库。他们没有利用最先进的技术，而是使用PostgreSQL来实现大量的功能，并使用其他库来实现其他功能。从锁定的角度来看，缺乏自定义的功能是一件好事。

https://zhuanlan.zhihu.com/p/408299698

https://chinese.freecodecamp.org/news/the-complete-guide-to-full-stack-development-with-supabas/

### 基本使用

安装

```shell
npm install --save @supabase/supabase-js
```

使用

```javascript
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = 'https://csmnbjrgnfzppgevieas.supabase.co'
const supabaseKey = process.env.SUPABASE_KEY
const supabase = createClient(supabaseUrl, supabaseKey)
```



### Next.js

https://supabase.com/docs/guides/with-nextjs



```react
import { useState, useEffect } from 'react'
import { supabase } from '../utils/supabaseClient'
import Auth from '../components/Auth'
import Account from '../components/Account'

export default function Home() {
  const [session, setSession] = useState(null)

  useEffect(() => {
    setSession(supabase.auth.session())

    supabase.auth.onAuthStateChange((_event, session) => {
      setSession(session)
    })
  }, [])

  return (
    <div className="container" style={{ padding: '50px 0 100px 0' }}>
      {!session ? <Auth /> : <Account key={session.user.id} session={session} />}
    </div>
  )
}
```



### 控制台

https://app.supabase.com/#

### 常用js Api

查询数据

```javascript
import { supabase } from '../path/to/api'

const { data, error } = await supabase
  .from('posts')
  .select()
```

过滤数据

```javascript
const { data, error } = await supabase
  .from('cities')
  .select('name, country_id')
  .eq('name', 'The Shire')    // Correct

const { data, error } = await supabase
  .from('cities')
  .eq('name', 'The Shire')    // Incorrect
  .select('name, country_id')
```

创建新条目

```javascript
const { data, error } = await supabase
  .from('posts')
  .insert([
    {
      title: "Hello World",
      content: "My first post",
      user_id: "some-user-id",
      user_email: "myemail@gmail.com"
    }
  ])
```

注册/登录

```javascript
const { user, session, error } = await supabase.auth.signUp({
  email: 'example@email.com',
  password: 'example-password',
})

const { user, session, error } = await supabase.auth.signIn({
  email: 'example@email.com',
  password: 'example-password',
})
```



## appwrite

Appwrite 是一个基于 Docker 的端到端开发者平台，其容器化的微服务库可应用于网页端，移动端，以及后端。Appwrite 通过视觉化界面简化了从零开始编写 API 的繁琐过程，在保证软件安全的前提下为开发者创造了一个高效的开发环境。

Appwrite 可以提供给开发者用户验证，外部授权，用户数据读写检索，文件储存，图像处理，云函数计算

Appwrite 的容器化服务器只需要一行指令就可以运行。您可以使用 docker-compose 在本地主机上运行 Appwrite，也可以在任何其他容器化工具（如 [Kubernetes](https://kubernetes.io/docs/home/)、[Docker Swarm](https://docs.docker.com/engine/swarm/) 或 [Rancher](https://rancher.com/docs/)）上运行 Appwrite。

启动 Appwrite 服务器的最简单方法是运行我们的 docker-compose 文件。在运行安装命令之前，请确保您的机器上安装了 [Docker](https://dockerdocs.cn/get-docker/index.html)：

https://appwrite.io/docs/quick-starts



## lagon

serverless 函数式部署平台

https://github.com/lagonapp/lagon

