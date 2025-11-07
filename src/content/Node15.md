---
title: NodeJs开发（五）
date: 2021-01-20 21:40:33
categories: IT
tags:
    - IT，Web,Node
toc: true
thumbnail: http://cdn.kunkunzhang.top/nodejs.png
---

Javascript第八篇，NodeJs第二篇，注重Node后端开发。

<!--more-->

## 功能模块

### ts相关

#### node-dev

监听js文件并rerun node

```shell
node-dev server.js
```

#### ts-node

运行node ts文件的命令行工具REPL

```shell
ts-node script.ts
```

#### tsx

直接运行nodejs中的ts文件

```shell
tsx file.js
```

相比于ts-node，tsx使用esbuild编译ts文件

#### jiti

直接运行nodejs中的ts文件

```shell
npx jiti ./index.ts
```





#### tslib

`tslib`: 一个运行时类型支持库，可以帮助 TypeScript 编译器生成更优化的 JavaScript 代码。在使用一些高级 TypeScript 特性（例如 `async/await`）时，可能需要引入 `tslib`

当使用 TypeScript 编写代码并将其编译为 JavaScript 时，编译器会为一些 TypeScript 特性生成额外的辅助代码，如类型断言、装饰器、枚举、泛型等。
这些辅助代码中包含了一些常见的函数和方法，如 `__extends`（用于实现类继承）、`__assign`（用于对象合并）、`__decorate`（用于装饰器相关逻辑）等。

`tslib` 封装了这些常用的辅助函数，使得编译后的 JavaScript 代码不必重复包含这些函数的定义。
通过在编译时使用 `importHelpers` 编译选项（在 `tsconfig.json` 中设置），TypeScript 编译器会将这些辅助函数的调用替换为对 `tslib` 中相应函数的引用。这样做的好处包括：

1. **减小代码体积**：避免每个编译后的 JavaScript 文件都包含相同的辅助函数定义，通过引用 `tslib` 单独的模块，可以显著减少生成代码的大小，有利于提高应用加载速度和减少网络传输量。
2. **优化压缩效果**：使用 `tslib` 后，辅助函数在所有模块中都是共享的，这使得压缩工具（如 UglifyJS）在压缩代码时能更有效地消除重复，进一步减小文件尺寸。
3. **简化构建过程**：将辅助函数集中到一个单独的库中，使得构建工具和模块打包器（如 webpack、rollup）在处理依赖关系时更为简单和高效。
4. **代码可读性**：编译后的 JavaScript 代码中，原本由 TypeScript 特性生成的辅助函数调用被替换为更简短的 `tslib` 函数引用，有助于提高代码的可读性。

`tslib` 作为 TypeScript 编译过程中的辅助工具库，主要用于优化 TypeScript 编译产物的大小和结构，提升代码加载性能和构建效率，同时也增强了编译后 JavaScript 代码的可读性。
在大型 TypeScript 项目中，使用 `tslib` 通常是一个标准的最佳实践。

#### ts-node-dev

Node-dev的ts版本

安装

```shell
yarn add typescript ts-node-dev -D
```

安装完成后配置npm脚本

```json
{
  "scripts": {
    "start": "tsnd -P ./tsconfig.json --respawn ./main.ts"
  }
}
```

tsnd 是 ts-node-dev 命令的缩写。上述命令中，只进行了两个配置，-P 代表着配置 tsconfig 的路径，是 --project 的缩写。--respawn 即为观察文件变更以重新运行脚本。

在生产环境运行tsc就可以

```json
{
  "scripts": {
    "build": "tsc --project ./"
  }
}
```

#### @types/node

node.js 类型声明包，这样才可以对 node.js 提供 API 方法有默认的类型提示和检查

```shell
yarn add @types/node
```



### http库

各个包对比图：https://blog.csdn.net/weixin_42900858/article/details/116065875

#### node-fetch

安装

```shell
npm install node-fetch@2
```

使用

```javascript
import fetch from 'node-fetch';

const response = await fetch('https://api.github.com/users/github');
const data = await response.json();

const body = {a: 1};

const response = await fetch('https://httpbin.org/post', {
	method: 'post',
	body: JSON.stringify(body),
	headers: {'Content-Type': 'application/json'}
});

console.log(data);
```



#### unfetch

node的fetch包

安装

```shell
$ npm i isomorphic-unfetch

$ npm i unfetch
```

使用

```javascript
// using JS Modules:
import fetch from 'unfetch'

// or using CommonJS:
const fetch = require('unfetch')

// usage:
fetch('/foo.json')
  .then( r => r.json() )
  .then( data => console.log(data) )

// complex POST request with JSON, headers:
fetch('/bear', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ hungry: true })
}).then( r => {
  open(r.headers.get('location'));
  return r.json();
})
```

#### Got

安装

```shell
npm install got
```

使用

```javascript
import got from 'got';

const {data} = await got.post('https://httpbin.org/anything', {
	json: {
		hello: 'world'
	}
}).json();

console.log(data);
//=> {"hello": "world"}
```



#### request



#### superagent



#### ky



### 请求数据处理相关

#### express-formidable

```shell
npm install express-formidable
```

使用

```javascript
const express = require('express');
const formidableMiddleware = require('express-formidable');
 
var app = express();
 
app.use(formidableMiddleware());
 
app.post('/upload', (req, res) => {
  req.fields; // contains non-file fields
  req.files; // contains files
});
```



#### form-data

node无法直接像html中使用new [FormData](https://so.csdn.net/so/search?q=FormData&spm=1001.2101.3001.7020)() 创建对象，要使用form-data库

使用

```javascript
var FormData = require('form-data');
var fs = require('fs');

var form = new FormData();
form.append('my_field', 'my value');
form.append('my_buffer', new Buffer(10));
form.append('my_file', fs.createReadStream('/foo/bar.jpg'));
```

也可以使用流

```javascript
var FormData = require('form-data');
var http = require('http');

var form = new FormData();

http.request('http://nodejs.org/images/logo.png', function(response) {
  form.append('my_field', 'my value');
  form.append('my_buffer', new Buffer(10));
  form.append('my_logo', response);
});
```

#### body-parser

`body-parser`是非常常用的一个`express`中间件，作用是对post请求的请求体进行解析。使用非常简单

```javascript
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

`body-parser`实现的要点如下：

1. 处理不同类型的请求体：比如`text`、`json`、`urlencoded`等，对应的报文主体的格式不同。
2. 处理不同的编码：比如`utf8`、`gbk`等。
3. 处理不同的压缩类型：比如`gzip`、`deflare`等。
4. 其他边界、异常的处理。

此中间件已经被express集成，无需调用安装body-parser，可以直接采用express.json()和express.urlencoded()实现相同功能。

**bodyParser不会对处理过的数据进行再次paser，所以如果有多个中间件用到body-parser，只需要在第一个使用body-parser的中间件配置即可**

**bodyParser.json([options])**

解析并返回 json格式的数据，这是常用的方法。内部会查看content-type，只有是正确的content-type默认是application/json才进入这个中间件解析处理。

```json
{
  // default = true
  // 是否开启压缩体解析
  "inflate": true,
  
  // default = '100kb'
  // 最大请求数据，传入数字默认单位是bytes，传入字符串要带上单位
  "limit": "100kb",
  
  // 指导reviver就相当于在JSON.parse()方法传入了第二个参数reviver做数据的预处理。
  "reviver": (key, value)=> {...},
    
   // default = true
   // 开启严格模式只能接收能被JSON.parse()方法解析的数据 
   "strict": true,
     
   // 接收数据的类型，默认是"application/json"
   "type": "application/json",
     
   // 验证数据，如果无效就可以提前抛出错误信息
   "verify": (req, res, buf, encoding) => {...}
}
```

**bodyParser.urlencoded([options])**

这是常用的方法，常见的前端请求解决方案如表单post提交、axios、fetch等库的post请求都需要这个中间件进行解析，返回json的格式数据。当请求的数据类型是application/x-www-form-urlencoded时才会进入这个中间件进行处理

参数

```json
{
    // default = true
  // 解析URL-encode数据的方法，true的话使用qs库来解析，false的话使用querystring库去解决，qs库文档：https://www.npmjs.com/package/qs#readme
  "extended": true,
  
  // default = true
  // 是否开启压缩体解析
  "inflate": true,
  
  // default = '100kb'
  // 最大请求数据，传入数字默认单位是bytes，传入字符串要带上单位
  "limit": "100kb",
  
  // default = 1000
  // 控制url编码数据中最大参数数量，超过这个数量返回413
  "parameterLimit": 1000,
  
  // 接收数据的类型，默认是"application/x-www-form-urlencoded"
  "type": "application/x-www-form-urlencoded",
  
  // 验证数据，如果无效就可以提前抛出错误信息
  "verify": (req, res, buf, encoding) => {...}
}
```

**bodyParser.text([options])**

当默认数据类型为text/*时候会进入这个中间件处理，用的少，由于json数据更友好，能直接在数据库使用或是保存为json格式的文件，如果你更改下options.type = 'application/json' 也可以处理json的数据。

所以bodyParser.json()相当于在此基础上进行封装优化，既然有更好用的，这个就不太用的上了，完全可以被取代。。options多了一个解码方式的选择，options.defaultCharset = 'utf-8'

**bodyParser.raw([options])**

处理默认数据为application/octet-stream时候的中间件，应用场景是post传入语音、短视频等媒体类型的数据，默认处理小于100kb的数据，以buffer的形式解析

### mq相关

#### bull

https://github.com/OptimalBits/bull



#### bullmq

基于redis的消息队列框架

https://github.com/taskforcesh/bullmq



#### pg-boss

安装

```shell
# npm
npm install pg-boss
```

使用

```javascript
async function readme() {
  const PgBoss = require('pg-boss');
  const boss = new PgBoss('postgres://user:pass@host/database');

  boss.on('error', error => console.error(error));

  await boss.start();

  const queue = 'some-queue';

  let jobId = await boss.send(queue, { param1: 'foo' })

  console.log(`created job in queue ${queue}: ${jobId}`);

  await boss.work(queue, someAsyncJobHandler);
}

async function someAsyncJobHandler(job) {
  console.log(`job ${job.id} received with data:`);
  console.log(JSON.stringify(job.data));

  await doSomethingAsyncWithThis(job.data);
}
```

#### worker

https://github.com/graphile/worker



#### amqplib

rabbitmq的node客户端，RabbitMQ 使用高级消息队列协议 (AMQP)。

安装

```shell
npm install amqplib
```

使用

```javascript
const amqplib = require('amqplib');

(async () => {
  const queue = 'tasks';
  const conn = await amqplib.connect('amqp://localhost');

  const ch1 = await conn.createChannel();
  await ch1.assertQueue(queue);

  // Listener
  ch1.consume(queue, (msg) => {
    if (msg !== null) {
      console.log('Received:', msg.content.toString());
      ch1.ack(msg);
    } else {
      console.log('Consumer cancelled by server');
    }
  });

  // Sender
  const ch2 = await conn.createChannel();

  setInterval(() => {
    ch2.sendToQueue(queue, Buffer.from('something to do'));
  }, 1000);
})();
```

#### memphis

https://github.com/memphisdev/memphis

Memphis 是一个具有嵌入式分布式消息队列的开源实时数据处理平台。它旨在消除应用内流式传输用例的繁重任务。

孟菲斯是云原生的。它通过为生产者 - 消费者范式提供实时数据处理平台而蓬勃发展。

孟菲斯的特别之处：

1. Memphis 是为异步通信而构建的分布式消息代理
2. 它支持众多云部署平台。
3. 与其他使用主题和队列的消息代理和队列不同，孟菲斯使用站。

工作站提供易于使用的消息队列。它将您从创建一个永无止境的生产者、消费者、编排、手动扩展和分散监控流中抽象出来。一个站为您处理所有这些。

Memphis 非常适合云原生应用程序开发。它使用现代工具来创建开发堆栈。这些包括：

1. Docker - 允许您的应用程序利用虚拟化容器和隔离资源并大规模运行微服务。
2. Kubernetes - 它提供编排服务，让您决定如何以及在何处运行容器。
3. Terraform - 将资源定义为代码的 IaC（基础设施即代码）工具。
4. Node.js 支持。Node.js 是一种流行的 JavaScript 运行时。它非常适合创建任何类型的实时应用程序和微服务。Node.js 允许您创建用于连接微服务的虚拟服务器和路由。



#### Redpanda

https://github.com/redpanda-data/redpanda



#### kafka

使用

```typescript
import { Kafka, Consumer, Producer, EachBatchPayload, logLevel, EachMessagePayload } from 'kafkajs';

this.kafka = new Kafka({
  clientId: cfg.clientId ?? 'card-message-client',
  brokers: cfg.brokers,
  ssl: {
    rejectUnauthorized: true,
    ca: fs
      .readFileSync(caPath, 'utf8')
      .split(/(?=-----BEGIN CERTIFICATE-----)/g)
      .map((v) => v.trim())
      .filter(Boolean),
  },
  sasl: {
    mechanism: 'plain',
    username: cfg.sasl.username,
    password: cfg.sasl.password,
  },
  connectionTimeout: cfg.connectionTimeout ?? 10_000,
  requestTimeout: cfg.requestTimeout ?? 30_000,
  logLevel: logLevel.INFO,
});

/* -------- producer -------- */
this.producer = this.kafka.producer();
await this.producer.connect();
this.logger.log('[Kafka] Producer 已连接');

/* -------- consumer -------- */
this.consumer = this.kafka.consumer({
  groupId: cfg.groupId ?? 'card-message-group',
  sessionTimeout: cfg.sessionTimeout ?? 30_000,
  heartbeatInterval: cfg.heartbeatInterval ?? 3_000,
  retry: { retries: 8 },
});

await this.consumer.connect();
await this.consumer.subscribe({
  topic: cfg.topic,
  fromBeginning: false,
});

this.consumer.run({
  eachMessage: async ({ topic, partition, message, heartbeat }: EachMessagePayload) => {
    const content = JSON.parse(message.value?.toString() ?? '{}');
    this.logger.log(`[Kafka] 消费 offset=${message.offset}`);

    const heartbeatInterval = setInterval(() => heartbeat(), 1000);
    await this.messageService.seedCardMessage(
      content.receive,
      content.template_id,
      content.message_type,
      content.template_variable,
    );
    await heartbeat();
  })
})
```





### 定时任务/任务队列

#### node-cron

安装

```shell
npm install cron 
```

使用

```javascript
var CronJob = require('cron').CronJob;
var job = new CronJob(
    '* * * * * *',
    function() {
        console.log('You will see this message every second');
    },
    null,
    true,
    'America/Los_Angeles'
);
// job.start() - See note below when to use this
```

#### croner

```javascript
job.nextRun( /*optional*/ startFromDate );	// Get a Date object representing the next run.
job.nextRuns(10, /*optional*/ startFromDate ); // Get an array of Dates, containing the next n runs.
job.msToNext( /*optional*/ startFromDate ); // Get the milliseconds left until the next execution.
job.currentRun(); 		// Get a Date object showing when the current (or last) run was started.
job.previousRun( ); 		// Get a Date object showing when the previous job was started.

job.isRunning(); 	// Indicates if the job is scheduled and not paused or killed (true or false).
job.isStopped(); 	// Indicates if the job is permanently stopped using `stop()` (true or false).
job.isBusy(); 		// Indicates if the job is currently busy doing work (true or false).

job.getPattern(); 	// Returns the original pattern string
```

#### machinery

我们在使用某些APP时，登陆系统后一般会收到一封邮件或者一个短信提示我们在某个时间某某地点登陆了。而邮件或短信都是在我们已经登陆后才收到，这里就是采用的异步机制

假设我们现在采用同步的方式实现，用户在登录时，首先会去检验一下账号密码是否正确，验证通过后去给用户发送登陆提示信息，假如在这一步出错了，那么就会导致用户登陆失败，这样是大大影响用户的体验感的，一个登陆提示的优先级别并不是很高，所以我们完全可以采用异步的机制实现，即使失败了也不会影响用户的体验。

任务队列有着广泛的应用场景，比如大批量的计算任务，当有大量数据插入，通过拆分并分批插入任务队列，从而实现串行链式任务处理或者实现分组并行任务处理，提高系统鲁棒性，提高系统并发度；或者对数据进行预处理，定期的从后端存储将数据同步到到缓存系统，从而在查询请求发生时，直接去缓存系统中查询，提高查询请求的响应速度。适用任务队列的场景有很多，这里就不一一列举了。回归本文主题，既然我们要学习`machinery`，就要先了解一下他都有哪些特性呢？

- 任务重试机制
- 延迟任务支持
- 任务回调机制
- 任务结果记录
- 支持Workflow模式：Chain，Group，Chord
- 多Brokers支持：Redis, AMQP, AWS SQS
- 多Backends支持：Redis, Memcache, AMQP, MongoDB

任务队列，简而言之就是一个放大的生产者消费者模型，用户请求会生成任务，任务生产者不断的向队列中插入任务，同时，队列的处理器程序充当消费者不断的消费任务。基于这种框架设计思想，我们来看下machinery的简单设计结构图例

- Sender：业务推送模块，生成具体任务，可根据业务逻辑中，按交互进行拆分；
- Broker：存储具体序列化后的任务，machinery中目前支持到Redis, AMQP,和SQS；
- Worker：工作进程，负责消费者功能，处理具体的任务；
- Backend：后端存储，用于存储任务执行状态的数据；



#### agenda

Agenda 是基于 node 的一个轻量级任务调度类库，它的优势在于轻量级、持久化、使用简单。

Agenda 引入mongodb从而实现持久化 。Agenda 将所有的任务（job）存储到[mongodb](https://zhida.zhihu.com/search?content_id=2565813&content_type=Article&match_order=2&q=mongodb&zhida_source=entity)中，对于使用mongodb做数据库的项目有天然优势。

在 Agenda 启动时会将预定义好的 processors 遍历一遍，从mongodb中查询出符合条件 job（这里的 job 可能不止一条）。对于遍历查询出来的job：如果时间已到或者已经是过去时间，那么立即执行；如果时间还未到，那么就延迟执行。

```javascript
const mongoConnectionString = 'mongodb://127.0.0.1/agenda';

const agenda = new Agenda({ db: { address: mongoConnectionString } });

// Or override the default collection name:
// const agenda = new Agenda({db: {address: mongoConnectionString, collection: 'jobCollectionName'}});

// or pass additional connection options:
// const agenda = new Agenda({db: {address: mongoConnectionString, collection: 'jobCollectionName', options: {ssl: true}}});

// or pass in an existing mongodb-native MongoClient instance
// const agenda = new Agenda({mongo: myMongoClient});

agenda.define('delete old users', async job => {
	await User.remove({ lastLogIn: { $lt: twoDaysAgo } });
});

(async function () {
	// IIFE to give access to async/await
	await agenda.start();

	await agenda.every('3 minutes', 'delete old users');

	// Alternatively, you could also do:
	await agenda.every('*/3 * * * *', 'delete old users');
})();
```



### 加解密包

#### node-rsa

在node中使用rsa算法

安装

```shell
npm install node-rsa
```

使用

```javascript
const NodeRSA = require("node-rsa")

const key = new NodeRSA({ b:2048 }) //2048 密钥长度
ket.setOptions({ encryptionSchema: 'pkcs1' }); //指定加密格式，不改格式的话可能会报错


```

#### node-forge

https://juejin.cn/post/7153205571385032712#heading-1

加解密

```javascript
import forge from 'node-forge'

const message = '要加密我了' // 原文长度有限制，而且中文还要url编码，所以不能加密太长的字符串。一般也只用来加密密码。
const publicKey = '-----BEGIN PUBLIC KEY-----\nMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCqM+l9ZWy1Frt6felFFLmfZNls\nVbU1dKpF8Rx83FtKCsztO5k/iV5N9BbfHFUg9Y40b/EK2j/BPc1xlLYAHMXn6563\nXCwZ4IuCxvfOwz9qT9gkKBxkI5b0rnikkSWTGlJEk2PdZ7Plc73Fa+bx3PvuKvMd\ncKWvd80+vt9+b/7hrwIDAQAB\n-----END PUBLIC KEY-----'
const publicK = forge.pki.publicKeyFromPem(publicKey)
const encrypted = publicK.encrypt(encodeURIComponent(message), 'RSA-OAEP') // 经过url编码，后端解密后需要url解码
console.log('密文：', encrypted) // 虽然乱码，但可以直接发给后端解密
const base64 = window.btoa(unescape(encodeURIComponent(encrypted)))
console.log('密文base64：', base64) // 一般会把它转为base64传给后端
```

node

```javascript
const forge = require('node-forge')
const privateKey = '-----BEGIN PRIVATE KEY-----\nMIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBAKoz6X1lbLUWu3p9\n6UUUuZ9k2WxVtTV0qkXxHHzcW0oKzO07mT+JXk30Ft8cVSD1jjRv8QraP8E9zXGU\ntgAcxefrnrdcLBngi4LG987DP2pP2CQoHGQjlvSueKSRJZMaUkSTY91ns+VzvcVr\n5vHc++4q8x1wpa93zT6+335v/uGvAgMBAAECgYArxUnou6qnL39rUvIol9ncyfy4\nRZpicuxPLGCdI7Y+ZmSpJciVdGhSN9Gh8xFZdozpo1gj6Fi5A4HQEeR0RvIF9Rgh\nERblj1rRWqxPcsIddOO9VaknQPICWKqEW9+E1bEcyNUblCHA4LGyQwmuEFUb/Tkj\nxAghIHuEBCe0GFiVwQJBAN5i5QSoOIpdFHA0c981E4VhHc/muXwjx1HfE1pcuuFb\nTy3OwEoZdFp3LIjBnBkPRneLTNjo5WTIwrmfsy6VDF8CQQDD7c6d/nKiJwIESlr+\n/idqXAPNR/iS1YX3Nqtk9jgrgf5zULHr2nbk7MDas5S9Z9XPdUmxtnP44dhoGvDk\nzyyxAkB7XBxyQuZqSkvGGjKUhJq5iC/DXddSd35fegEARSQdUktPu7qK4Cfc7vKz\nQcLXW9PZCFqukDJ/f6YU1fPNSTy9AkADQ78hms/GK+g4shR6EzoM56OYlA5sQ+qL\nh/mrIP8mmm/m8/1C9MzuW5OLEVr1HPnPDyE/OM8N4pV8hpZk+Z7BAkEAzaFstazA\nxLzZOBWhvOzzo722glZ7HVezhMocLu7Y3EOXP/nbx09JpU3U7Egp5UVp0aiknh/Q\nez4Cc4ksMedxdA==\n-----END PRIVATE KEY-----\n'
const privateK = forge.pki.privateKeyFromPem(privateKey)
const encrypted = Buffer.from(base64, 'base64').toString() // base64 为前端传过来的密文base64
const decrypted = privateK.decrypt(encrypted, 'RSA-OAEP')
console.log('原文：', decodeURIComponent(decrypted)) // decrypted 为原文
```



#### crypto-js



#### crypto-browserify

使用

```javascript
import crypto from 'crypto-browserify';

// 将对象、数组等数据结构序列化json.stringfy
const aesEncode = function (
	string: string,
  key: string,
) {
  const cipher = crypto.createCipher('aes192', key);
  const crypted = cipher.update(string, 'utf-8', 'hex');
  crypted += cipher.final('hex');
  return crypted;
};
```



#### ursa



其他有crypt和cryptico

https://npmtrends.com/crypt-vs-cryptico-vs-crypto-browserify-vs-crypto-js-vs-node-forge-vs-node-rsa-vs-ursa



#### Js-md5

MD5加密包

### node-redis

### rust

#### neon

使用rust编写原生的node模块

https://github.com/neon-bindings/neon



#### node-bindgen

https://github.com/infinyon/node-bindgen



#### napi-rs

### 压缩文件相关

#### pako

https://github.com/nodeca/pako

#### jszip

https://github.com/Stuk/jszip

#### tarballjs





### 日志工具

#### pino

安装

```shell
npm install pino
```

使用

```javascript
const logger = require('pino')()

logger.info('hello world')

const child = logger.child({ a: 'property' })
child.info('hello child!')
```



#### winston

node日志工具

使用

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: { service: 'user-service' },
  transports: [
    //
    // - Write all logs with importance level of `error` or less to `error.log`
    // - Write all logs with importance level of `info` or less to `combined.log`
    //
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

//
// If we're not in production then log to the `console` with the format:
// `${info.level}: ${info.message} JSON.stringify({ ...rest }) `
//
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple(),
  }));
}
```

根据时间分割日志，我们需要换别的 Transport ，`winston`提供了很多[transport](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fwinstonjs%2Fwinston%2Fblob%2FHEAD%2Fdocs%2Ftransports.md%23winston-core),感兴趣的可以点进去看下。我们这里使用`DailyRotateFile`来分割日志

我们首先要安装`npm install winston-daily-rotate-file -S`,然后导入就可以使用了

```typescript
import winston from "winston";
import "winston-daily-rotate-file";
const { format, transports } = winston;
const logger = winston.createLogger({
  level: "debug",
  format: format.simple(),
  transports: [
    new transports.Console(),
    new transports.DailyRotateFile({
      level: "debug",
      dirname: "logs",
      filename: "index-%DATE%.log",
      datePattern: "YYYY-MM-DD-HH-mm",
      maxSize: 1024,
    }),
  ],
});

logger.info("cxkhtw");
logger.debug("cxkhtw");
logger.warn("cxkhtw");
logger.info("cxkhtw");
```

#### log4js





### 性能

https://github.com/bestiejs/benchmark.js

#### benchmark

Node.js 中的 benchmark 测试是一种用于评估代码性能的方法，包括测试代码的执行时间、内存占用等指标。在编写高性能的 Node.js 应用程序时，了解如何编写基准测试是非常重要的。

基准测试是一种用于评估代码性能的方法。它可以帮助开发人员确定代码在不同条件下的性能表现。基准测试通常包括以下步骤：

1. 编写测试代码：编写一段代码，用于测试特定的功能或操作。
2. 运行测试代码：运行测试代码，并记录运行时间或其他指标。
3. 分析测试结果：分析测试结果，以确定代码的性能表现。

基准测试可以帮助开发人员确定代码的瓶颈，并找到优化代码的方法。

**1. .on('cycle')**

当 benchmark 执行完一次测试时，会触发 'cycle' 事件。在 'cycle' 事件的回调函数中，可以获取测试的结果，包括测试的名称、测试的次数、测试的平均时间、测试的标准差等指标。例如：

**2. .on('complete')**

当 benchmark 执行完所有测试时，会触发 'complete' 事件。在 'complete' 事件的回调函数中，可以获取所有测试的结果，包括测试的名称、测试的次数、测试的平均时间、测试的标准差等指标。例如：

**3. .run({ 'async': true })**

在 benchmark 中，可以使用.run ({'async': true}) 方法实现异步测试。当设置 async 参数为 true 时，benchmark 将会异步执行测试，而不是同步执行测试。在异步测试中，需要在测试完成后调用 done () 方法通知 benchmark 测试已经完成。

**4. teardown**

当 benchmark 执行测试后，会触发 'teardown' 事件。在 'teardown' 事件的回调函数中，可以进行一些清理工作，例如关闭数据库连接或者删除测试数据。

**5. cycle start**

当 benchmark 开始执行某个测试时，会触发 'cycle start' 事件。在 'cycle start' 事件的回调函数中，可以进行一些初始化操作，例如打印测试开始的信息。

**6. start**

当 benchmark 开始执行测试时，会触发'start' 事件。在'start' 事件的回调函数中，可以执行一些初始化操作，例如打印测试开始的信息。

**7. error**

当 benchmark 执行过程中发生错误时，会触发 'error' 事件。在 'error' 事件的回调函数中，可以处理错误信息并进行相应的操作，例如输出错误信息或者停止测试。

**9. setup**

当 benchmark 执行测试前，会触发'setup' 事件。在'setup' 事件的回调函数中，可以进行一些准备工作，例如初始化测试数据或者打开数据库连接。

```javascript
const benchmark = require('benchmark');

const suite = new benchmark.Suite;

// add tests
suite.add('RegExp#test', function() {
  /o/.test('Hello SteveRocket!');
})
.add('String#indexOf', function() {
  'Hello SteveRocket!'.indexOf('o') > -1;
})
// add listeners
.on('cycle', function(event) {
  console.log(String(event.target));
})
.on('complete', function() {
  console.log('Fastest is ' + this.filter('fastest').map('name'));
})
// run async
.run({ 'async': true });
```

以下是一些编写基准测试的实用技巧和建议：

1. 确定测试目标：在编写基准测试之前，应该先确定测试的目标。例如，你可能想测试特定函数的性能，或者比较不同算法的性能。
2. 使用真实数据：在编写基准测试时，应该使用真实的数据。如果测试数据不真实，测试结果可能会失真。
3. 多次运行测试：为了获得更准确的测试结果，应该多次运行测试，并取平均值。
4. 使用 benchmark.js 库：benchmark.js 库提供了许多有用的功能，例如自动调整测试次数和输出测试结果。使用该库可以轻松地编写基准测试。
5. 了解 JavaScript 引擎：JavaScript 引擎的实现方式可能会影响代码的性能表现。因此，在编写基准测试时，应该了解所使用的 JavaScript 引擎的特点。
6. 避免优化：在编写基准测试时，应该避免使用过多的优化技巧。优化可能会影响测试结果的准确性。
7. 分析测试结果：在测试完成后，应该仔细分析测试结果，并找出代码的瓶颈。根据测试结果，可以采取不同的优化措施。

### prom-client prometheus

node的prometheus客户端

https://github.com/siimon/prom-client

```javascript
// Async version:
const client = require('prom-client');
new client.Gauge({
  name: 'metric_name',
  help: 'metric_help',
  async collect() {
    // Invoked when the registry collects its metrics' values.
    const currentValue = await somethingAsync();
    this.set(currentValue);
  },
});
```

### typebot

机器人回复

https://docs.typebot.io/self-hosting/configuration



### @kubernetes/client-node

node调用k8s客户端信息

```shell
npm install @kubernetes/client-node
```

列出所有的pods

```javascript
const k8s = require('@kubernetes/client-node');

const kc = new k8s.KubeConfig();
kc.loadFromDefault();

const k8sApi = kc.makeApiClient(k8s.CoreV1Api);

k8sApi.listNamespacedPod('default').then((res) => {
    console.log(res.body);
});

// 创建一个新的命名空间
var namespace = {
    metadata: {
        name: 'test',
    },
};

k8sApi.createNamespace(namespace).then(
    (response) => {
        console.log('Created namespace');
        console.log(response);
        k8sApi.readNamespace(namespace.metadata.name).then((response) => {
            console.log(response);
            k8sApi.deleteNamespace(namespace.metadata.name, {} /* delete options */);
        });
    },
    (err) => {
        console.log('Error!: ' + err);
    },
);
```



### 剪贴板的使用

使用第三方包，安装

```js
npm install clipboard-polyfill
```

引用

```js
import clipboard from "clipboard-polyfill"
```

实例

```js
clipboard.writeText("this");
clipboard.readText().then(console.log,console.error);
```

### 终端二维码

qrcode-terminal

安装

```shell
npm install -D qrcode-terminal
```

使用

```javascript
const qrcode = require('qrcode-terminal')

const url = 'https:www.baidu.com'

qrcode.generate(url,{small:true},(qrcode)=> {
  console.log(qrcode)
})
```



### 判断设备信息

使用navigator对象

```js
export function checkdevice() {
  var browser = {
    versions: (function() {
      var u = navigator.userAgent,
        app = navigator.appVersion;
      return {
        //移动终端浏览器版本信息
        trident: u.indexOf("Trident") > -1, //IE内核
        presto: u.indexOf("Presto") > -1, //opera内核
        webKit: u.indexOf("AppleWebKit") > -1, //苹果、谷歌内核
        gecko: u.indexOf("Gecko") > -1 && u.indexOf("KHTML") == -1, //火狐内核
        mobile: !!u.match(/AppleWebKit.*Mobile.*/), //是否为移动终端
        ios: !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/), //ios终端
        android: u.indexOf("Android") > -1 || u.indexOf("Linux") > -1, //android终端或uc浏览器
        iPhone: u.indexOf("iPhone") > -1, //是否为iPhone或者QQHD浏览器
        iPad: u.indexOf("iPad") > -1, //是否iPad
        webApp: u.indexOf("Safari") == -1, //是否web应该程序，没有头部与底部
      };
    })(),
    language: (navigator.browserLanguage || navigator.language).toLowerCase(),
  };

  if (browser.versions.mobile) {
    //判断是否是移动设备打开。browser代码在下面
    // 此时为移动端打开.跳转到移动站
    // if(window.location.href.indexOf("ooo0o.com/mobile") != -1){
    //     return;
    // }else {
    //     window.location.href = "https://www.ooo0o.com/mobile"
    // }

    var ua = navigator.userAgent.toLowerCase(); //获取判断用的对象
    if (ua.match(/MicroMessenger/i) == "micromessenger") {
      //在微信中打开
      if (browser.versions.ios) {
        return "weixinios";
      } else {
        return "weixin";
      }
    } else if (browser.versions.android) {
      //是否在安卓浏览器打开

      // alert('安卓手机中打开的');
      /*window.location.href="https://jushizhibo.com/android/app-release.apk";*/
      // window.open('https://jushizhibo.com/android/app-release.apk','_self')
      return "anzhuo";
    } else if (browser.versions.ios) {
      //是否在IOS浏览器打开
      // alert('IOS中打开的');
      /*window.location.href="https://www.baidu.com";*/
      // window.open('transparentfactory://xiangqingye','_self')
      return "ios";
    }
  } else {
    //此时是非移动端,则跳转PC站
    // alert('PC中打开的');
    // if(window.location.href.indexOf("ooo0o.com/mobile") != -1){
    //     window.location.href = "https://www.ooo0o.com"
    // }
    return "pc";
  }
}
```

使用时导入

```js
import {checkdevice}  from 'checkdevice.js'
```

### 七牛云的使用

安装七牛包

```node
npm install qiniu
```

新建文件，设置七牛云参数

```js
var bucket='',
var imageUrl='',
var accessKey = '',
var secretKey = '',
var mac = new qiniu.auth.digest.Mac(accessKey,secretKey);

var option={
    scope:bucket,
}
var putPolicy= new qiniu.rs.PutPolicy(option)
var uploadToken = putPolicy.uploadToken(mac);
```

上传代码

```js
var config = new qiniu.conf.Config()

config.zone= qiniu.zone.Zone_z0;//选择七牛云的机房
//是否使用https、是否使用cdn加速
config.usehttpsDomain=true;
config.useCdnDomain = true;

var formUploader = new qiniu.form_up.FormUploader(config);
var putExtra = new qiniu.form_up.PutExtra();
var key = '';

formUploader.putFile(uploadToken,key,path.resolve(pathName),putExtra,function(respErr,respBody,respInfo){
       if(resqErr){
         throw respErr;
       }
       if(respInfo.statusCode == 200){
       console.log(respBody);
       }else{
           console.log(respInfo.statusCode);
           console.log(respBody)
       }                                                   });

```

https://segmentfault.com/a/1190000017064729

### 发邮件

导入模块Nodemailer

```node
npm install nodemailer
```

使用方法(包官网https://nodemailer.com/)

```js
//引入包
const nodemailer = require("nodemailer");

//创建邮件请求对象（qq邮箱、163邮箱或其他）
  let transporter = nodemailer.createTransport({
    host: "smtp.ethereal.email",//邮箱服务器
    port: 587,（端口号）
    secure: false, // true for 465, false for other ports
    auth: {
      user: testAccount.user, // 账号
      pass: testAccount.pass // 你的邮箱服务器请求密码
    }
  });
  //所发送的邮件信息
  let mailobj={
    from: '"Fred Foo 👻" <foo@example.com>', // sender address
    to: "bar@example.com, baz@example.com", // list of receivers
    subject: "Hello ✔", // Subject line
    text: "Hello world?", // plain text body
    html: "<b>Hello world?</b>" // html body
  }
  //发送邮件
  transporter.sendMail(mailobj);


```





### http爬虫



### node应用打包可执行文件

pkg可以将node项目打包为一个单独的可执行文件，在未安装nodejs的机器上运行。支持win、linux等多系统

```shell
npm install pkg --save-dev
```



### Node应用部署Docker 

Docker允许你以应用程序所有的依赖打包成一个标准化的单元，这被称为一个容器，对于应用开发而言，一个容器就是一个蜕化到最基础的linux操作系统，一个镜像是你加载到容器中的软件

在node app应用的目录下新建一个Dockerfile，编辑这个文件

```dockerfile
#从Docker站点获取相关镜像
From node:12
#在镜像中创建一个文件夹存放应用程序代码，这将是应用程序工作的目录
WORKDIR /usr/src/app
#安装应用程序的所有依赖
COPY package*.json ./

RUN npm install 
#在Docker镜像中使用COPY命令绑定你的应用程序
COPY . .
#定义映射端口，如应用程序的端口为8080，则与docker的镜像做映射
EXPOSE 8080
#最后要定义运行时的CMD命令来运行应用程序，这里使用node serverjs启动服务器
CMD ["node","server.js"]
```

在dockerfile的同一个文件夹下创建.dockerignore文件，带有以下内容

```dockerfile
node_modules
npm-debug.log
```

这将避免本地模块和调试日志被拷贝进入你的Docker镜像中，不会把镜像中安装的模块覆盖

准备好之后就可以使用命令行构建和运行镜像

进入dockerfile所在的目录，运行命令构建镜像

```shell
docker build -t <username>/node-web-app
```

构建之后就可以显示或者运行镜像

```dockerfile
docker images
```

使用-d模式以分离模式运行docker容器，使得容器在后台自助运行

开关符-p在容器中把一个公共端口导向到私有的端口

```shell
docker run -p 49160:8080 -d <username>/node-web-app
```



## 学习资源

node问答：https://github.com/jimuyouyou/node-interview-questions

https://javascript.ruanyifeng.com/

https://markpop.github.io/2014/10/29/NodeJs%E6%95%99%E7%A8%8B/

node包讲解：https://github.com/chyingp/nodejs-learning-guide

awesome-nodejs: https://github.com/sindresorhus/awesome-nodejs
