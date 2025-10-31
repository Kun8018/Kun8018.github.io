---
title: NodeJs开发（八） 
date: 2021-1-23 21:40:33
categories: IT
tags:
    - IT，Web
toc: true
thumbnail: http://cdn.kunkunzhang.top/nestjs.png
---

万万万万万万没想到会来到第十一篇，第十一篇写Nest和Nodejs游戏框架

<!--more-->

## Nestjs

https://segmentfault.com/a/1190000018153359

nest之于javascript就像spring boot之于java，nest可以使用typescrip或者JavaScript开发，默认使用express作为底层服务框架

nest基于typescript编写并且结合了OOP(面向对象编程)、FP(函数式编程)、FRP(函数式响应编程)

熟悉spring或者angular的同学可以快速上手Nestjs，因为它大量借鉴了Spring和Angular中的思想和概念。nest 的核心思想是提供一个层与层之间直接耦合度极小、抽象化较高的架构体系。

安装nest

```js
npm i -g @nestjs/cli
```

检查安装是否成功

```js
nest -h
```

创建nest项目

```js
nest  new nest-demo
```

进入项目，npm run start

```js
//nest常用指令
nest new []//创建项目
nest -h//帮助
nest g co [名称]//创建控制器
nest g s [名称]//创建服务
nest g mi [名称]//创建中间件
nest g pi [名称]//创建管道
nest g mo [名称]//创建模块
nest g gu [名称]//创建守卫
```

### 依赖注入

依赖注入是面向对象中控制反转最常见的实现方式，主要降低代码的耦合度，

实例

```js
import { Engine } from './engine'
import { Tire } from './tire'

class Container {
    private constructorPool;

    constructor() {
        this.constructorPool = new Map();
    }
    
    register(name,constructor) {
        this.constructorPool.set(name,constructor);
    }
    
    get(name){
       const target = this.constructorPool.get(name);
       return new target();
    }
}

const container = new Container();
container.bind('engine',Engine);
container.bind('tire',Tire);

class Car {
    private engine;
    private tire;
    
    constructor() {
        this.engine = container.get('engine');
        this.tire = container.get('tire');
    }
}
```

在nestjs中，通过@injectable装饰器向IoC容器注册

```js
import { Injectable } from '@nestjs/common';

```



### 控制器

控制器负责处理传入的请求和向客户端返回响应。每个控制器有多个路由，每个路由能执行不同的操作

通过命令行创建控制器

```shell
$ nest g co cats
```

实例

```js
import { Controller ,Get，Post, Req} from '@nestjs/common'

@Controller('cats')
export class CatsController {
    @Post
    create(): string {
        return 'this is a cat';
    }
    @Get
    findAll(@Req() request: Request): string {
        return 'this return all cats';
    }
}

```

当向该端点发起 GET 请求时，Nest 会将请求路由到用户定义的 `findAll()` 方法。请注意，此处选择的方法名称完全是任意的。虽然我们必须声明一个方法来绑定路由，但 Nest 不会对方法名称赋予任何特定含义。

Nest还提供其他端点装饰器@Put()、@Delete()、@Patch()、



#### 状态码





#### 自定义响应头

可以使用 `@header()` 修饰器或类库特有的响应对象,

```ts
@Post()
@Header('Cache-Control', 'none')
create() {
  return 'This action adds a new cat';
}
```

#### 重定向

可以使用 `@Redirect()`装饰器或特定于库的响应对象(并直接调用 `res.redirect()`)。

`@Redirect()` 带有必需的 `url`参数和可选的 `statusCode`参数。 如果省略，则 `statusCode` 默认为 `302`。

```ts
@Redirect('https://nestjs.com', 301)
```

#### 动态路由

当您需要接受**动态数据**作为请求的一部分时，

### 提供者

Providers 是 `Nest` 的一个基本概念。许多基本的 `Nest` 类可能被视为 provider - `service`,`repository`, `factory`, `helper` 等等。

定义一个service为provider

```typescript
export interface Cat {
 name: string;
 age: number;
 breed: string;
}

import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
 private readonly cats: Cat[] = [];

 create(cat: Cat) {
   this.cats.push(cat);
 }

 findAll(): Cat[] {
   return this.cats;
 }
}

// 在controller中消费提供者
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
 constructor(private catsService: CatsService) {}

 @Post()
 async create(@Body() createCatDto: CreateCatDto) {
   this.catsService.create(createCatDto);
 }

 @Get()
 async findAll(): Promise<Cat[]> {
   return this.catsService.findAll();
 }
}
```



### 模块化

nest把controller、service、pipe等打包成内部的功能块，每个模块聚焦一个特性区域、业务领域、工作流等。

在nest中通过@Module装饰器声明一个模块，每个nest程序至少有一个模块，即根模块，根模块是Nest开始安排应用程序树的地方

@module()装饰器接受一个描述模块属性的对象

| 属性        |                                                              |
| ----------- | ------------------------------------------------------------ |
| providers   | 将由 Nest 注入器实例化,且至少可在本模块内共享的提供者        |
| controllers | 本模块中定义的需要实例化的控制器集合                         |
| imports     | 导入模块的列表，这些模块导出了本模块所需的提供者             |
| exports     | 本模块提供的 providers 子集，这些提供者应可供导入本模块的其他模块使用。可以使用提供者本身或其令牌（provide 值） |

把模块到处就能在其他任意模块中重复使用，模块导出时可以导出他们的内部提供者，也可以再导出自己导入的模块

```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
 controllers: [CatsController],
 providers: [CatsService],
})
export class CatsModule {}
```



#### 全局模块

当你在很多地方需要导入同一模块时，可以将模块定义为全局模块。一旦定义，他们到处可用。

@Global装饰器使模块注册为全局模块。全局模块只注册一次，最好在根模块或者核心模块注册

实例

```js
import { Module,Global } from '@nestjs/common'
import { CatsController } from './cats.controller'
import { CatsService } from './cats.service'

@Global()
@Module({
   controllers: [CatsController],
   provider: [CatsService],
   exports: [CatsService],
})
export class CatModule {}
```



### 生命周期事件

生命周期事件发生在应用程序启动和关闭过程中。Nest 会在以下每个生命周期事件中调用模块、提供者和控制器上已注册的生命周期钩子方法

| 生命周期钩子方法             | 触发钩子方法调用的生命周期事件                               |
| ---------------------------- | ------------------------------------------------------------ |
| onModuleInit()               | 当宿主模块的依赖项已解析完成时调用。                         |
| onApplicationBootstrap()     | 在所有模块初始化完成但尚未开始监听连接时调用。               |
| onModuleDestroy()*           | 在接收到终止信号（例如 SIGTERM）后调用。                     |
| beforeApplicationShutdown()* | 在所有 onModuleDestroy() 处理程序完成（Promise 已解决或拒绝）后调用；一旦完成（Promise 已解决或拒绝），所有现有连接将被关闭（调用了 app.close()）。 |
| onApplicationShutdown()*     | 在连接关闭后调用（app.close() 解析完成时）。                 |



### 装饰器和注解

@

#### 自定义装饰器



### 面向切面编程

面向切面编程是针对业务处理过程中的切面进行提取，在某个步骤和阶段进行一些操作。面向切面编程是对面向对象编程的一种补充。

在nest中，面向切面编程主要分为下面几个部分：中间件、守卫、拦截器、管道、过滤器

洋葱模型

#### 中间件

nest的中间件和express的语言，可以直接使用express的中间件

```javascript
import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';
import { CatsController } from './cats/cats.controller';

@Module({
 imports: [CatsModule],
})
export class AppModule implements NestModule {
 configure(consumer: MiddlewareConsumer) {
   consumer
     .apply(LoggerMiddleware)
     .forRoutes(CatsController);
 }
}
```

#### 管道

管道有两种类型：

将输入数据转化为所需的数据输出，或者对输入数据进行验证，验证成功则继续传递，否则抛出异常。即转换管道和验证管道。管道也是具有@Injecttable装饰器的类

nest自带5个开箱即用的管道，从@nestjs/common包中导出，ValidationPipe、ParseIntPipe、ParseBoolPipe、ParseArrayPipe、ParseUUIDPipe。

Pipe 是具有 `@Injectable()` 装饰器的类，并实现了 `PipeTransform` 接口。

实例

验证管道

Nest 与 [class-validator](https://github.com/pleerock/class-validator) 配合得很好。class-validator库允许您使用基于装饰器的验证。装饰器的功能非常强大，尤其是与 Nest 的 Pipe 功能相结合使用时，因为我们可以通过访问 `metatype` 信息做很多事情，



转换管道

管道一般作为全局pipe使用

```javascript
async function bootstrap() {
  const app = await NestFactory.create(ApplicationModule);
  app.setGlobalPrefix('api/v1');
  
  app.useGlobalPipes(new ValidationPipe());
  
  await app.listen(3000);
}
bootstrap();
```

假设我们没有这层 pipe，那在 controller 中就会进行参数校验，这样就会打破单一职责的原则。有了这一层 pipe 帮助我们校验参数，有效地降低了类的复杂度，提高了可读性和可维护性。

#### 守卫

守卫与前端(vue)中的路由守卫一样，主要确定请求是否由该路由程序处理，通过守卫可以知道上下文的信息，所以与中间件相比，守卫可以确切知道在next之后要执行什么

守卫在中间件之后执行，在拦截器和管道之前

在控制器中绑定守卫

守卫可以是全局范围或者控制范围内的，使用@UserGuards()装饰器设置一个控制范围的守卫，这个装饰器可以传递单个或多个守卫，用逗号隔开

```js
import { UseGuards } from '@nestjs/common'
@Controller('cats')
@UseGuards(RolesGuard)
export  default CatsControllers {}
```

全局守卫用于整个应用程序, 每个控制器和每个路由处理程序。全局守卫

为了设置一个全局守卫，使用Nest应用程序实例的 `useGlobalGuards()` 方法：

```ts
const app = await NestFactory.create(AppModule);
app.useGlobalGuards(new RolesGuard());
```

**授权**是守卫的绝佳应用场景，因为特定路由应当仅在调用者（通常是已认证的特定用户）拥有足够权限时才可用。我们将要构建的 `AuthGuard` 假设用户已通过认证（因此请求头中附带了令牌）。它将提取并验证令牌，利用提取的信息来判断是否允许该请求继续执行

```typescript
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Observable } from 'rxjs';

@Injectable()
export class AuthGuard implements CanActivate {
 canActivate(
   context: ExecutionContext,
 ): boolean | Promise<boolean> | Observable<boolean> {
   const request = context.switchToHttp().getRequest();
   return validateRequest(request);
 }
}
```





#### 拦截器

拦截器可以：

在函数执行之前/之后绑定额外的逻辑

转换从函数返回的结果 

转换从函数抛出的异常

扩展基本函数行为

根据所选条件完全重写函数

实例



#### 异常处理/过滤器

标准异常

Nest 提供了一个内置的 `HttpException` 类，该类从 `@nestjs/common` 包中导出。对于基于 HTTP REST/GraphQL API 的典型应用程序，最佳实践是在发生某些错误条件时发送标准的 HTTP 响应对象

例如，在 `CatsController` 中，我们有一个 `findAll()` 方法（一个 `GET` 路由处理程序）。假设这个路由处理程序由于某种原因抛出了异常。

```javascript
@Get()
async findAll() {
 try {
   await this.service.findAll()
 } catch (error) {
   throw new HttpException({
     status: HttpStatus.FORBIDDEN,
     error: 'This is a custom message',
   }, HttpStatus.FORBIDDEN, {
     cause: error
   });
 }
}
```

`HttpException` 构造函数接收两个必选参数来决定响应内容：

- `response` 参数定义了 JSON 响应体，可以是如下所述的 `string` 或 `object` 类型。
- `status` 参数定义了 [HTTP 状态码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

默认情况下，JSON 响应体包含两个属性：

- `statusCode`：默认为 `status` 参数中提供的 HTTP 状态码
- `message`：基于 `status` 的 HTTP 错误简短描述

若要仅覆盖 JSON 响应体中的消息部分，请在 `response` 参数中传入字符串。若要覆盖整个 JSON 响应体，则在 `response` 参数中传入对象。Nest 会将该对象序列化后作为 JSON 响应体返回。

第二个构造参数 `status` 应为有效的 HTTP 状态码。最佳实践是使用从 `@nestjs/common` 导入的 `HttpStatus` 枚举。

还存在**第三个**构造参数（可选）——`options`，可用于提供错误[原因](https://nodejs.org/en/blog/release/v16.9.0/#error-cause) 。该 `cause` 对象不会被序列化到响应对象中，但对日志记录很有帮助，能提供引发 `HttpException` 的内部错误的有价值信息

多数情况下，您无需编写自定义异常，直接使用内置的 Nest HTTP 异常即可（详见下一节）。如需创建定制化异常，最佳实践是建立**异常层级结构** ，让自定义异常继承基础 `HttpException` 类。通过这种方式，Nest 能识别您的异常并自动处理错误响应

虽然基础的（内置）异常过滤器能自动处理许多情况，但您可能希望对异常层进行**完全控制** 。例如，您可能希望基于某些动态因素添加日志记录或使用不同的 JSON 模式。 **异常过滤器**正是为此目的而设计。它们让您可以精确控制流程以及返回给客户端的响应内容

内置的 Exception filters 负责处理整个应用程序中的所有抛出的异常，也是 Nestjs 中在 response 前，最后能捕获异常的机会。

```javascript
import { ExceptionFilter, Catch, ArgumentsHost } from '@nestjs/common';

@Catch()
export class AnyExceptionFilter implements ExceptionFilter {
  catch(exception: any, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();

    response
      .status(status)
      .json({
        statusCode: exception.getStatus(),
        timestamp: new Date().toISOString(),
        path: request.url,
      });
  }
}
```

而 Interceptor 则负责对成功请求结果进行包装：

```javascript
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
} from '@nestjs/common'
import { Observable } from 'rxjs'
import { map } from 'rxjs/operators'

interface Response<T> {
  data: T
}

@Injectable()
export class TransformInterceptor<T>
  implements NestInterceptor<T, Response<T>> {
  intercept(
    context: ExecutionContext,
    next: CallHandler,
  ): Observable<Response<T>> {
    return next.handle().pipe(
      map(rawData => {
          return {
            data: rawData,
            status: 0,
            message: '请求成功',
          }
        }
      )
    )
  }
}
```

同样 Interceptor 和 Exception Filter 需要把它定义在全局范围内：

```javascript
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.setGlobalPrefix('api/v1');

  app.useGlobalFilters(new ExceptionsFilter());
  app.useGlobalInterceptors(new TransformInterceptor());
  app.useGlobalPipes(new ValidationPipe());

  await app.listen(3000);
}
```

### 微服务

安装包

```js
npm i --save @nestjs/microservices
```

创建微服务

```js

```



#### Redis





### GraphQL

在nest中开发GraphQL有两种方式，一种是代码先行，一种是架构先行

安装包

```js
npm i @nestjs/graphql graphql-tools graphql apollo-server-express
```

@nestjs/graphql是对apollo server的封装

数据量较少时可以将schema和resolver写在一个文件内，数据量较多时最好写在不同的js/ts文件中

定义模型schema

```js
import {Field,Int,ObjectType} from '@nestjs/graphql';
//也可以从其他模型文件中引入schema
import { Post } from './post.graphql'

@ObjectType()
export class Author {
  @Field(type =>Int)
  id: number;

  @Field({ nullable: true})
  firstName?: String;
  
  @Field({ nullable: true})
  lastName?: String;
  
  @Field(type => [Post])
  posts: Post[];
}

//post.graphql.ts
import {Field,Int,ObjectType} from '@nestjs/graphql';

@ObjectType()
export class Post {
    @Field(type => Int)
    id: number;

    @Field()
    title: string;

    @Field(type =>Int,{nullable:true})
    votes?: number;
}
```

定义resolver

```js
import {
  Resolver,
  Query,
  Parent,
  ResolveField,
  Args,
  Int,
} from '@nestjs/graphql';
import { Author } from './graphql/author.graphql';
import { Post } from './graphql/post.graphql';

@Resolver(() => Author)
export class AuthorsResolver {
  constructor(
  private authorsService: AuthorsService,
  private postsService: PostsService) {}

  // @Query 表示创建Query 操作类型
  // @Args 表示传入的参数
  @Query(() => Author)
  async author(@Args('id', { type: () => Int }) id: number): Promise<any> {
    // 这里注释掉的是启用 `service` 对数据库进行访问
    // return this.authorsService.findOneById(id);
    return {
      id,
      firstName: 'name',
      lastName: 'mase',
    };
  }
  
  // @ResolveField 表示下面装饰的方法与父类型（在当前示例中为Author类型）相关联
  @ResolveField()
  async posts(@Parent() author: Author): Promise<any> {
    const { id } = author;
    return [
      {
        id: 4,
        title: 'hello',
        votes: 2412,
      },
    ];
  }
}
```

在module文件中引入

```ts
import { Module } from '@nestjs/common';
import { AuthorsResolver } from './authors.resolver'

@Module({
  providers: [AuthorsResolver],
})
export class AuthorModule {}
```

在主文件中引入module

```ts
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';
import { AuthorModule } from './author/author.module'
import { join } from 'path';

@Module({
  imports: [
    ...
    GraphQLModule.forRoot({
      autoSchemaFile: join(process.cwd(), 'src/schema.gql'), // 最后生成的`Schema 文件，不可修改`
    }),
    ConfigModule.forRoot({
      load: [configuration],
    }),
    AuthorModule
  ],
})
```



### Websocket

安装包

```js
npm i --save @nestjs/websockets @nestjs/platform-socket.io
npm i --save-dev @types/socket.io
```



### passport/JWT认证

通过用户认证可以判断该访问角色的合法性和权限。通常认证要么基于 Session，要么基于 Token。这里就以基于 Token 的 JWT（JSON Web Token） 方式进行用户认证。

```shell
$ npm install --save @nestjs/passport passport @nestjs/jwt passport-jwt
```

创建`jwt.strategy.ts`，用来验证 token，当 token 有效时，允许进一步处理请求，否则返回`401(Unanthorized)`

首先概述适用于**任何** Passport 策略的流程。将 Passport 视为一个迷你框架会很有帮助，其精妙之处在于它将认证过程抽象为几个基本步骤，您可以根据所实现的策略进行定制。它之所以像框架，是因为您通过提供定制参数（作为普通 JSON 对象）和回调函数形式的自定义代码来配置它，Passport 会在适当时机调用这些回调函数。`@nestjs/passport` 模块将这个框架封装成 Nest 风格的包，使其易于集成到 Nest 应用中。下面我们将使用 `@nestjs/passport`，但先来看看**原生 Passport** 的工作原理。

在原生的 Passport 中，您需要通过提供两样东西来配置策略：

1. 一组特定于该策略的选项。例如，在 JWT 策略中，您可能需要提供一个用于签名令牌的密钥。
2. "验证回调"，即您告诉 Passport 如何与用户存储（管理用户账户的地方）进行交互的地方。在此处，您需要验证用户是否存在（和/或创建新用户）以及其凭证是否有效。Passport 库期望此回调在验证成功时返回完整的用户对象，失败时返回 null（失败定义为用户未找到，或在 passport-local 策略中密码不匹配）。

使用 `@nestjs/passport` 时，您通过扩展 `PassportStrategy` 类来配置 Passport 策略。通过在子类中调用 `super()` 方法传递策略选项（上述第 1 项），可选择传入选项对象。通过子类中实现 `validate()` 方法来提供验证回调（上述第 2 项）。



### cls

在复杂系统中，每一个请求过来，我们会调用不同的异步服务(db, fs，微服务等等)，调用过程中如果某一环节出现问题，如何去做链路追踪，或者说如何获取到原始的请求上下文。

在 JAVA/C++ 等多线程服务中，可以通过 TLS(Thread-local storage，线程局部存储)获取请求上下文，但是在 node 这种单线程事件驱动的系统中，如何去请求获取请求上下文。

但是在 node 中，这样的全局变量会被下一个请求复写，导致出现异常时拿到的请求上下文并不是我们想要的那个请求。

另外一个方案是通过在不同服务中透传原始请求，这样的确可以解决问题，但是会引进很多冗余代码，当系统庞大之后，不好扩展

CLS 就是解决上述问题的一个社区方案，全称 `Continuation Local Storage`

CLS 的工作方式类似于 TLS，其基于 node 的回调链而不是线程。换句话说，CLS 可以在 node 这个异步调用链中获取到同一个上下文信息

CLS 中有两个结构概念分别为

1. namespace 命名空间，理论上一个应用分配一个 namespace
2. context 上下文，namespace 通过一个数组存储多个 context

每次执行 `namespace.run` 都会生成一个上下文，CLS 通过 `process.addAsyncListener` 监听异步事件。在创建异步事件的时候将当前上下文传入，执行异步事件时，检出传入的上下文，异步事件执行结束销毁上下文。 `process.addAsyncListener` 是 [node v0.11 版本的 API](https://link.juejin.cn?target=https%3A%2F%2Fnodejs.org%2Fdocs%2Fv0.11.11%2Fapi%2Fprocess.html%23process_process_addasynclistener_callbacksobj_userdata)，当前已废弃，可以使用社区实现的 polyfill [async-listener](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fothiym23%2Fasync-listener)。

由于单线程事件驱动的特性，node 无法通过类似线程局部变量的方式跟踪收到请求后的全链路，通过传参的方式跟踪变量过于冗余繁杂，社区给出的方案是引入请求上下文，维护一个上下文的容器，一个请求对对应一个上下文，监听异步资源，在异步执行过程中切换上下文实现全链路追踪

异步本地存储 (Async Local Storage)

`AsyncLocalStorage` 是一个 [Node.js API](https://nodejs.org/api/async_context.html#async_context_class_asynclocalstorage)（基于 `async_hooks` API），它提供了一种无需显式传递函数参数就能在应用中传播本地状态的替代方案。这类似于其他语言中的线程本地存储。

异步本地存储的核心思想是我们可以用 `AsyncLocalStorage#run` 调用*包装*某些函数调用。所有在被包装调用内执行的代码都能访问相同的 `store`，且每个调用链都将拥有唯一的存储空间。

在 NestJS 上下文中，这意味着如果我们能在请求生命周期中找到某个位置来包装请求的剩余代码，就能访问和修改仅对该请求可见的状态，这可以作为 REQUEST 作用域提供程序的替代方案，并解决其部分局限性

async_hooks

async hooks 是 node v8 引入的新特性，通过 async_hooks.createHook(callbacks)创建每个异步事件 `init`, `before`, `after`, `destory` 的生命周期

通过 asyncHooks 可以非常方便的追逐异步事件

```javascript
const async_hooks = require('async_hooks')
const fs = require('fs')
let indent = 0
async_hooks
  .createHook({
    init(asyncId, type, triggerAsyncId) {
      const eid = async_hooks.executionAsyncId()
      const indentStr = '├' + '─'.repeat(indent) + ' '
      fs.writeSync(
        process.stdout.fd,
        `${indentStr}${type}(${asyncId}):` +
          ` trigger: ${triggerAsyncId} execution: ${eid}\n`
      )
    },
    before(asyncId) {
      const indentStr = '├' + '─'.repeat(indent) + ' '
      fs.writeSync(process.stdout.fd, `${indentStr}before:  ${asyncId}\n`)
      indent += 2
    },
    after(asyncId) {
      indent -= 2
      const indentStr = '├' + '─'.repeat(indent) + ' '
      fs.writeSync(process.stdout.fd, `${indentStr}after:  ${asyncId}\n`)
    },
    destroy(asyncId) {
      const indentStr = '├' + '─'.repeat(indent) + ' '
      fs.writeSync(process.stdout.fd, `${indentStr}destroy:  ${asyncId}\n`)
    },
  })
  .enable()

require('net')
  .createServer(() => {})
  .listen(8080, () => {
    // Let's wait 10ms before logging the server started.
    setTimeout(() => {
      console.log('>>>', async_hooks.executionAsyncId())
    }, 10)
  })
```

[nestjs-cls](https://github.com/Papooch/nestjs-cls) 包相比直接使用原生 `AsyncLocalStorage`（`CLS` 是 *continuation-local storage* 的缩写）提供了多项开发者体验改进。它将实现抽象为一个 `ClsModule`，为不同传输方式（不仅限于 HTTP）提供多种初始化 `store` 的方法，同时还支持强类型。

在根模块中导入 `ClsModule`

```javascript
@Module({
  imports: [
    // Register the ClsModule,
    ClsModule.forRoot({
      middleware: {
        // automatically mount the
        // ClsMiddleware for all routes
        mount: true,
        // and use the setup method to
        // provide default store values.
        setup: (cls, req) => {
          cls.set('userId', req.headers['x-user-id']);
        },
      },
    }),
  ],
  providers: [CatsService],
  controllers: [CatsController],
})
export class AppModule {}
```

在模块中使用

```javascript
@Injectable()
export class CatsService {
  constructor(
    // We can inject the provided ClsService instance,
    private readonly cls: ClsService,
    private readonly catsRepository: CatsRepository,
  ) {}

  getCatForUser() {
    // and use the "get" method to retrieve any stored value.
    const userId = this.cls.get('userId');
    return this.catsRepository.getForUser(userId);
  }
}
```



### axios

[Axios](https://github.com/axios/axios) 是一个功能丰富的 HTTP 客户端包，被广泛使用。Nest 封装了 Axios 并通过内置的 `HttpModule` 暴露它。`HttpModule` 导出了 `HttpService` 类，该类提供了基于 Axios 的方法来执行 HTTP 请求。该库还将生成的 HTTP 响应转换为 `Observables`

安装过程完成后，要使用 `HttpService`，首先需要导入 `HttpModule`

```javascript
@Module({
  imports: [HttpModule],
  providers: [CatsService],
})
export class CatsModule {}

@Injectable()
export class CatsService {
  constructor(private readonly httpService: HttpService) {}

  findAll(): Observable<AxiosResponse<Cat[]>> {
    return this.httpService.get('http://localhost:3000/cats');
  }
}
```



### 模版引擎

在 Nestjs 中，可以使用 hbs 作为模板渲染引擎：

```shell
$ npm install --save hbs
```

在`main.ts`中，我们告诉 express，`static`文件夹用来存储静态文件，`views`中含了模板文件：

```javascript
import { NestFactory } from '@nestjs/core'
import { NestExpressApplication } from '@nestjs/platform-express'
import { join } from 'path'

import { AppModule } from './app.module'
import config from './config'
import { Logger } from './shared/utils/logger'

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule, {
    cors: true,
  })

  app.setGlobalPrefix('api/v1')

  app.useStaticAssets(join(__dirname, '..', 'static'))
  app.setBaseViewsDir(join(__dirname, '..', 'views'))
  app.setViewEngine('hbs')

  await app.listen(config.port, config.hostName, () => {
    Logger.log(
      `Awesome-nest API server has been started on http://${config.hostName}:${config.port}`,
    )
  })
}
```

在`views`下新建一个`catsPage.hbs`的文件，假设，我们需要在里面填充的数据结构是这样：

```javascript
{
  cats: [
    {
      id: 1,
      name: 'yyy',
      age: 12,
      breed: 'black cats'
    }
  ],
  title: 'Cats List',
}
```

此时，可以这样写模板：

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <meta http-equiv="X-UA-Compatible" content="ie=edge"/>
    <style>
        .table .default-td {
            width: 200px;
        }

        .table tbody>tr:nth-child(2n-1) {
            background-color: rgb(219, 212, 212);
        }

        .table tbody>tr:nth-child(2n) {
            background-color: rgb(172, 162, 162);
        }
    </style>
</head>
<body>
<p>{{ title }}</p>
<table class="table">
    <thead>
    <tr>
        <td class="id default-td">id</td>
        <td class="name default-td">name</td>
        <td class="age default-td">age</td>
        <td class="breed default-td">breed</td>
    </tr>
    </thead>
    <tbody>
    {{#each cats}}
        <tr>
            <td>{{id}}</td>
            <td>{{name}}</td>
            <td>{{age}}</td>
            <td>{{breed}}</td>
        </tr>
    {{/each}}
    </tbody>
</table>
</body>
</html>
```



### http请求

Nestjs 中对[Axios](https://github.com/axios/axios)进行了封装，并把它作为 `HttpService` 内置到`HttpModule`中。`HttpService`返回的类型和 Angular 的 `HttpClient Module`一样，都是`observables`，所以可以使用 [rxjs](https://rxjs.dev/) 中的操作符处理各种异步操作。

```javascript
import { Global, HttpModule, Module } from '@nestjs/common'

import { LunarCalendarService } from './services/lunar-calendar/lunar-calendar.service'

@Global()
@Module({
  imports: [HttpModule],
  providers: [LunarCalendarService],
  exports: [HttpModule, LunarCalendarService],
})
export class SharedModule {}
```



### redis

@nestjs-modules/ioredis

使用

```typescript
// redis.module.ts
import { Module } from '@nestjs/common';
import { RedisService } from './redis.service';
import { RedisModule as BaseRedisModule } from '@nestjs-modules/ioredis';
import { ConfigService } from '@nestjs/config';
import { getBaseConfig } from 'src/common/config';

@Module({
  imports: [
    BaseRedisModule.forRootAsync({
      useFactory: (configService: ConfigService) => ({
        type: 'single',
        url: getBaseConfig(configService).redis.url,
      }),
      inject: [ConfigService],
    }),
  ],
  providers: [RedisService],
  exports: [RedisService],
})
export class RedisModule {}

// redis.service.ts
import { Injectable } from '@nestjs/common';
import { InjectRedis } from '@nestjs-modules/ioredis';
import Redis from 'ioredis';
import { ConfigService } from '@nestjs/config';
import { createHash } from 'crypto';
import { getBaseConfig } from 'src/common/config';

@Injectable()
export class RedisService {
  constructor(
    @InjectRedis() private readonly redis: Redis,
    private readonly configService: ConfigService,
  ) {}
}
```



### ORM

通过ORM可以使用面向对象编程的方式操作关系型数据库。Java中通常会有DAO(data access object，数据访问对象)层，DAO包含了各种数据库的操作方法，通过DAO对数据进行相关的操作。DAO的主要作用是分离数据层与业务层，避免业务层与数据层耦合。

为方便使用，Nest 原生提供了与 TypeORM 和 Sequelize 的深度集成，分别通过 `@nestjs/typeorm` 和 `@nestjs/sequelize` 包实现（本章将介绍这些内容），以及与 Mongoose 的集成通过 `@nestjs/mongoose` 包（详见[本章](https://docs.nestjs.cn/techniques/mongodb) ）。这些集成提供了额外的 NestJS 专属特性，如模型/仓库注入、可测试性和异步配置，使访问所选数据库更加便捷。，支持MySQL、MariaDB、MongoDB、NoSQL、SQLite、Postgres、CockroachDB、Oracle。

安装库

```shell
$ npm install --save @nestjs/typeorm
```

在typeORM中数据库的表对应的就是一个类，通过一个类创建实体，实体是一个映射到数据库表的类，通过@Entity来标记

```js
import {Entity,PrimaryGeneratedColumn,Column} from "typeorm";

@Entity()
export class User{
    @PrimaryGeneratedColumn()
    id: number;
    
    @Column()
    firstName: String;

    @Column()
    lastName: String;

    @Column()
    age: number;
}
```

通过@InjectRepository()修饰器注入对应的Repository，就可以在Repository对象上

进行数据库的操作。

```js
import {Injectable} from '@nestjs/common';
import {InjectRepository } from '@nestjs/typeorm';
import {Rspository } from '@nestjs/typeorm';
import {User} from './user.entity'

@Injectable()
export class UserService{
    constructor(
     @InjectRepository(User)
     private readonly userRepository: Repository<User>,
     ){}
    
    async findAll() Promise<User[]>{
        return await this.userRepository.find();
    }
}

```

`forRoot()` 方法支持 [TypeORM](https://typeorm.io/data-source-options#common-data-source-options) 包中 `DataSource` 构造函数公开的所有配置属性。此外，还支持以下描述的若干额外配置属性。

| `retryAttempts`    | 数据库连接尝试次数（默认：10）           |
| ------------------ | ---------------------------------------- |
| `retryDelay`       | 连接重试间隔时间（毫秒）（默认：3000）   |
| `autoLoadEntities` | 若为 true，实体将自动加载（默认：false） |

#### Mongo

安装包

```ts
yarn add @nestjs/typeorm typeorm mongodb
```

在app.module.ts中配置数据库连接

```ts
@Module({
      imports: [
        TypeOrmModule.forRoot({
          type: 'mongodb',
          host: 'localhost',
          port: 27017,
          database: 'typeorm', // 数据库名
          entities: [join(__dirname, '**/entity/*.{ts,js}')], // 需要自动实体映射的文件路径匹配
          useNewUrlParser: true, // 使用新版mongo连接Url解析格式
          synchronize: true, // 自动同步数据库生成entity
        })
      ],
```



#### Mysql

安装包

```shell
npm install --save typeorm mysql
```

配置数据库连接

```js
import { createConnection } from 'typeorm';

export const databaseProviders = [
  {
    provide: 'DATABASE_CONNECTION',
    useFactory: async () => await createConnection({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'root',
      database: 'test',
      entities: [
          __dirname + '/../**/*.entity{.ts,.js}',
      ],
      synchronize: true,
    }),
  },
];

```

### 数据库迁移

在持续交付项目中，项目会不断迭代上线，这时候就会出现数据库改动的问题，对一个投入使用的系统，通常会使用 migration 帮我们同步数据库。TypeORM 也自带了一个 [CLI 工具](https://github.com/typeorm/typeorm/blob/master/docs/zh_CN/using-cli.md)帮助我们进行数据库的同步。



### CRUD

对于一般的 CRUD 的操作，在 Nestjs 中可以使用[@nestjsx/crud](https://github.com/nestjsx/crud/wiki/Controllers#getting-started)这个库来帮我们减少开发量。

首先安装相关依赖：

```javascript
npm i @nestjsx/crud @nestjsx/crud-typeorm class-transformer class-validator --save
```



### 安全防范

对 JWT 的认证方式，因为没有 cookie，所以也就不存在 CSRF。如果你不是用的 JWT 认证方式，可以使用[csurf](https://github.com/expressjs/csurf)这个库去解决这个安全问题。

对于 XSS，可以使用[helmet](https://github.com/helmetjs/helmet)去做安全防范。helmet 中有 12 个中间件，它们会设置一些安全相关的 HTTP 头。比如`xssFilter`就是用来做一些 XSS 相关的保护。

对于单 IP 大量请求的暴力攻击，可以用[express-rate-limit](https://github.com/nfriedly/express-rate-limit)来进行限速。

对于常见的跨域问题，Nestjs 提供了两种方式解决，一种通过`app.enableCors()`的方式启用跨域，另一种像下面一样，在 Nest 选项对象中启用。

最后，所有这些设置都是作为全局的中间件启用，最后`main.ts`中，和安全相关的设置如下：

```javascript
import * as helmet from 'helmet'
import * as rateLimit from 'express-rate-limit'

async function bootstrap() {
  const app = await NestFactory.create(AppModule, { cors: true })

  app.use(helmet())
  app.use(
    rateLimit({
      windowMs: 15 * 60 * 1000, // 15 minutes
      max: 100, // limit each IP to 100 requests per windowMs
    }),
  )

  await app.listen(config.port, config.hostName, () => {
    Logger.log(
      `Awesome-nest API server has been started on http://${config.hostName}:${config.port}`,
    )
  })
}
```



### Swagger

Nest提供对Swagger的支持，方便追踪和测试api。

安装npm包

```shell
$ npm install --save @nestjs/swagger swagger-ui-express
```

在`main.ts`中构建文档：

```typescript
const options = new DocumentBuilder()
    .setTitle('Awesome-nest')
    .setDescription('The Awesome-nest API Documents')
    .setBasePath('api/v1')
    .addBearerAuth()
    .setVersion('0.0.1')
    .build()

const document = SwaggerModule.createDocument(app, options)
SwaggerModule.setup('docs', app, document)
```

访问`http://localhost:3300/docs`就可以看到 swagger 文档的页面。

对于不同的 API 可以在 controller 中使用`@ApiUseTags()`进行分类，对于需要认证的 API，可以加上`@ApiBearerAuth()`，这样在 swagger 中填完 token 后，就可以直接测试 API：

```javascript
@ApiUseTags('cats')
@ApiBearerAuth()
@Controller('cats')
@UseGuards(AuthGuard())
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

  @Get('page')
  @Render('catsPage')
  getCatsPage(): Promise<any> {
    return this.catsService.getCats()
  }
}
```

#### 装饰器

所有可用的 OpenAPI 装饰器都带有 `Api` 前缀，以区别于核心装饰器。以下是导出的装饰器完整列表，并标注了每个装饰器可应用的层级。

|                                                         |             |
| ------------------------------------------------------- | ----------- |
| @ApiBasicAuth()                                         | 方法/控制器 |
| @ApiBearerAuth()                                        | 方法/控制器 |
| @ApiBody()                                              | 方法        |
| @ApiConsumes()                                          | 方法/控制器 |
| @ApiCookieAuth()                                        | 方法/控制器 |
| @ApiExcludeController()                                 | 控制器      |
| @ApiExcludeEndpoint()                                   | 方法        |
| @ApiExtension()                                         | 方法        |
| @ApiExtraModels()                                       | 方法/控制器 |
| @ApiHeader()                                            | 方法/控制器 |
| @ApiHideProperty()                                      | 模型        |
| @ApiOAuth2()                                            | 方法/控制器 |
| @ApiOperation()                                         | 方法        |
| @ApiParam()                                             | 方法/控制器 |
| @ApiProduces()                                          | 方法/控制器 |
| @ApiSchema()                                            | 模型        |
| @ApiProperty{ description: '所属分组', maxLength: 50 }) | 模型        |
| @ApiPropertyOptional()                                  | 模型        |
| @ApiQuery()                                             | 方法/控制器 |
| @ApiResponse()                                          | 方法/控制器 |
| @ApiSecurity()                                          | 方法/控制器 |
| @ApiTags()                                              | 方法/控制器 |
| @ApiCallbacks()                                         | 方法/控制器 |



### 其他工具

#### 类型校验和转换器(序列化)

验证发送到 Web 应用程序的任何数据的正确性是最佳实践。为了自动验证传入请求，Nest 提供了几个开箱即用的管道：

- `ValidationPipe`
- `ParseIntPipe`
- `ParseBoolPipe`
- `ParseArrayPipe`
- `ParseUUIDPipe`

`ValidationPipe` 利用了强大的 [class-validator](https://github.com/typestack/class-validator) 包及其声明式验证装饰器。`ValidationPipe` 提供了一种便捷的方法来强制执行所有传入客户端负载的验证规则，其中特定规则通过每个模块中本地类/DTO 声明中的简单注解来声明。

自动验证

```typescript
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe());
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

Nest 提供了内置功能来帮助确保这些操作能够以简单直接的方式完成。`ClassSerializerInterceptor` 拦截器利用强大的 [class-transformer](https://github.com/typestack/class-transformer) 包，提供了一种声明式且可扩展的对象转换方式。其基本操作是获取方法处理程序返回的值，并应用 [class-transformer](https://github.com/typestack/class-transformer) 中的 `instanceToPlain()` 函数。在此过程中，它可以应用实体/DTO 类上由 `class-transformer` 装饰器表达的规则，如下所述。

```typescript
@SerializeOptions({
  excludePrefixes: ['_'],
})
@Get()
findOne(): UserEntity {
  return new UserEntity();
}

@Transform(({ value }) => value.name)
role: RoleEntity;
```

#### 环境变量

`NestJS`很贴心的帮我做了环境变量配置的工作，提供了`@nestjs/config`包来进行环境变量的配置

```shell
npm install @nestjs/config --save
```

使用

```typescript
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';
​
@Module({
  imports: [ConfigModule.forRoot({这里放置配置信息})],
})
export class AppModule {}
```

默认情况下，这样配置之后，系统会解析项目根目录下的`.env`文件，提取其中的`key/value`对信息，并附加到`process.env`对象中，后面可以通过`ConfigService`获取到这些`key/value`值，一个简单的`.env`示例如下

默认情况下直接找的是根目录下的`.env`文件，我们也可以自定义，自定义接收一个数组，如果有重复定义的变量，那么谁在前谁生效

```typescript
ConfigModule.forRoot({
  isGlobal: true,
  envFilePath: ['.env.development.local', '.env.development'],
});
```

使用yaml

```shell
$ npm i js-yaml
$ npm i -D @types/js-yaml
```

https://juejin.cn/post/7177407436381388858#heading-8

#### 循环依赖

循环依赖指的是两个类相互依赖的情况。例如，类 A 需要类 B，而类 B 也需要类 A。在 Nest 中，模块之间以及提供者之间都可能出现循环依赖。

虽然应尽可能避免循环依赖，但有时无法完全避免。针对这种情况，Nest 提供了两种解决提供者间循环依赖的方法。使用前向引用

```javascript
@Injectable()
export class CatsService {
 constructor(
   @Inject(forwardRef(() => CommonService))
   private commonService: CommonService,
 ) {}
}

@Injectable()
export class CommonService {
 constructor(
   @Inject(forwardRef(() => CatsService))
   private catsService: CatsService,
 ) {}
}
```

#### 任务调度

任务调度允许您安排任意代码（方法/函数）在固定日期/时间、按重复间隔或在指定间隔后执行一次。在 Linux 领域，这通常由操作系统层面的 [cron](https://en.wikipedia.org/wiki/Cron) 等包处理。对于 Node.js 应用，有多个包可模拟类似 cron 的功能。Nest 提供了 `@nestjs/schedule` 包，它与流行的 Node.js[cron](https://github.com/kelektiv/node-cron) 包集成。

安装

```shell
$ npm install --save @nestjs/schedule
```

使用, 要激活任务调度功能，请将 `ScheduleModule` 导入根模块 `AppModule`，并运行如下所示的 `forRoot()` 静态方法

```javascript
import { Module } from '@nestjs/common';
import { ScheduleModule } from '@nestjs/schedule';

@Module({
 imports: [
   ScheduleModule.forRoot()
 ],
})
export class AppModule {}
```

`.forRoot()` 调用会初始化调度器并注册应用中所有声明式的 [cron 任务](https://docs.nestjs.cn/techniques/techniques/task-scheduling#声明式-cron-任务) 、 [超时任务](https://docs.nestjs.cn/techniques/techniques/task-scheduling#声明式超时) 和 [间隔任务](https://docs.nestjs.cn/techniques/techniques/task-scheduling#声明式间隔任务) 。注册过程发生在 `onApplicationBootstrap` 生命周期钩子触发时，确保所有模块都已加载并声明了计划任务

#### bullMq

安装

```shell
$ npm install --save @nestjs/bullmq bullmq
```

使用

```typescript
import { Processor, WorkerHost } from '@nestjs/bullmq';
import { Job } from 'bullmq';

@Processor('audio')
export class AudioConsumer extends WorkerHost {
  async process(job: Job<any, any, string>): Promise<any> {
    let progress = 0;
    for (let i = 0; i < 100; i++) {
      await doSomething(job.data);
      progress += 1;
      await job.updateProgress(progress);
    }
    return {};
  }
}
```

#### 静态资源

为了提供静态内容服务（如单页应用 SPA），我们可以使用 [`@nestjs/serve-static`](https://www.npmjs.com/package/@nestjs/serve-static) 包中的 `ServeStaticModule` 模块

使用

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ServeStaticModule } from '@nestjs/serve-static';
import { join } from 'path';

@Module({
  imports: [
    ServeStaticModule.forRoot({
      rootPath: join(__dirname, '..', 'client'),
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```



#### 健康检查

Terminus 集成提供了**就绪性/存活状态**健康检查功能。在复杂的后端架构中，健康检查至关重要。简而言之，在 Web 开发领域，健康检查通常由一个特殊地址组成，例如 `https://my-website.com/health/readiness` 。您的服务或基础设施组件（如 [Kubernetes](https://kubernetes.io/)）会持续检查该地址。根据对该地址 `GET` 请求返回的 HTTP 状态码，当收到"不健康"响应时，服务将采取相应措施。由于"健康"或"不健康"的定义因服务类型而异，**Terminus** 集成通过一组**健康指标**为您提供支持。

例如，如果您的 Web 服务器使用 MongoDB 存储数据，了解 MongoDB 是否仍在运行将是关键信息。在这种情况下，您可以使用 `MongooseHealthIndicator`。如果配置正确（稍后会详细介绍），您的健康检查地址将根据 MongoDB 是否运行返回健康或不健康的 HTTP 状态码

https://docs.nestjs.cn/recipes/terminus#typeorm-%E5%81%A5%E5%BA%B7%E6%8C%87%E6%A0%87



### 热重载

在开发的时候，运行`npm run start:dev`的时候，是进行全量编译，如果项目比较大，全量编译耗时会比较长，这时候我们可以利用 webpack 来帮我们做增量编译，这样会大大增加开发效率。

首先，安装 webpack 相关依赖：

```shell
$ npm i --save-dev webpack webpack-cli webpack-node-externals ts-loader
```

在根目录下创建一个`webpack.config.js`：

```javascript
const webpack = require('webpack');
const path = require('path');
const nodeExternals = require('webpack-node-externals');

module.exports = {
  entry: ['webpack/hot/poll?100', './src/main.ts'],
  watch: true,
  target: 'node',
  externals: [
    nodeExternals({
      whitelist: ['webpack/hot/poll?100'],
    }),
  ],
  module: {
    rules: [
      {
        test: /.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  mode: 'development',
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  plugins: [new webpack.HotModuleReplacementPlugin()],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'server.js',
  },
};
```

在main.ts中启动HMR，

```javascript
declare const module: any;

async function bootstrap() {
  const app = await NestFactory.create(ApplicationModule);
  await app.listen(3000);

  if (module.hot) {
    module.hot.accept();
    module.hot.dispose(() => app.close());
  }
}
bootstrap();
```

在`package.json`中增加下面两个命令：

```javascript
{
  "scripts": {
    "start": "node dist/server",
		"webpack": "webpack --config webpack.config.js"
  }
}
```

运行`npm run webpack`之后，webpack 开始监视文件，然后在另一个命令行窗口中运行`npm start`。



## Nodejs游戏框架pomelo

http://nextzeus.github.io/pomelo/#



