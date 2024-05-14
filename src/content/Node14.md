---
title: NodeJs开发（二）
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

### chrono

解析自然语言中的日期

https://github.com/wanasit/chrono

```shell
npm install --save chrono-node
```

使用

```javascript
import * as chrono from 'chrono-node';

chrono.parseDate('An appointment on Sep 12-13');
// Fri Sep 12 2014 12:00:00 GMT-0500 (CDT)
    
chrono.parse('An appointment on Sep 12-13');
/* [{ 
    index: 18,
    text: 'Sep 12-13',
    start: ...
}] */
```



### p-queue

异步任务队列

安装

```shell
npm install p-queue
```

使用

```javascript
import PQueue from 'p-queue';
import got from 'got';

const queue = new PQueue({concurrency: 1});

(async () => {
	await queue.add(() => got('https://sindresorhus.com'));
	console.log('Done: sindresorhus.com');
})();

(async () => {
	await queue.add(() => got('https://avajs.dev'));
	console.log('Done: avajs.dev');
})();

(async () => {
	const task = await getUnicornTask();
	await queue.add(task);
	console.log('Done: Unicorn task');
})();
```



### p-map

并发池，依次并发

```shell
npm install p-map
```

使用

```javascript
import pMap from 'p-map';
import got from 'got';

const sites = [
	getWebsiteFromUsername('sindresorhus'), //=> Promise
	'https://avajs.dev',
	'https://github.com'
];

const mapper = async site => {
	const {requestUrl} = await got.head(site);
	return requestUrl;
};

const result = await pMap(sites, mapper, {concurrency: 2});

console.log(result);
//=> ['https://sindresorhus.com/', 'https://avajs.dev/', 'https://github.com/']
```

更多promise的控制：https://github.com/sindresorhus/promise-fun

### globby

相对于 glob，globby有以下增强功能

- Promise 接口
- 多模式匹配
- 否定模式匹配
- 扩展目录: `dir` → `dir/**/*`
- 支持 `.gitignore`

```javascript
(async () => {
  const paths = await globby(['images','photos'],{
    expandDirectories: true
  });
  console.log(paths);
})();
```

### cuid2

uuid生成器

安装

```shell
npm install --save @paralleldrive/cuid2

yarn add @paralleldrive/cuid2
```

使用

```javascript
import { createId } from '@paralleldrive/cuid2';

const ids = [
  createId(), // 'tz4a98xxat96iws9zmbrgj3a'
  createId(), // 'pfh0haxfpzowht3oi213cqos'
  createId(), // 'nc6bzmkmd014706rfda898to'
];

import { createId, isCuid } from '@paralleldrive/cuid2';


console.log(
  isCuid(createId()), // true
  isCuid('not a cuid'), // false
);
```

### uuid

uuid是通用唯一识别码(Universally Unique Identifier)的缩写。是一种软件建构辨准，亦为开发软件基金会组织在分布式计算环境领域的一部分。其目的是让分布式系统中的所有元素具有唯一的辨识信息，而不需要通过中央控制端来做辨识信息的指定。

UUID由一组32位数的16进制数字构成。对于UUID，就算每纳秒产生一百万个UUID，要花100亿年才会将所有UUID用完。

格式

uuid32个16进制数字用连字号分成五组来显示，所以共有36个字符

UUID版本通过M表示，当前规范有5个版本，可选值为1、2、3、4、5，这5个版本使用不同的算法，利用不同的信息产生UUID，各版本有各版本的优势，具体来说：

uuid.v1()：创建版本1(时间戳)UUID

uuid.v3()：创建版本3(md5命名空间)UUID

uuid.v4()：创建版本4(随机)UUID

uuid.v5()：创建版本5(带SHA-1的命名空间)IIOD

安装

```shell
npm install uuid 
```

使用

```javascript
import { v4 as uuidv4} from 'uuid'

uuidv4()
```

可以使用uuid进行验证登陆,未登陆状态下生产uuid

```javascript
let uuid = sessionStorage.getItem('uuid')
if(!uuid){
  sessionStorage.setItem('uuid')
}

if(getToken()){
  sessionStorage.removeItem('uuid');
}else {
  let uuid = sessionStorage.getItem('uuid');
  if(!uuid){
    sessionStorage.setItem('uuid',uuidv4());
  }
}
```



### minimatch

正则匹配工具包

```javascript
// hybrid module, load with require() or import
import { minimatch } from 'minimatch'
// or:
const { minimatch } = require('minimatch')

minimatch('bar.foo', '*.foo') // true!
minimatch('bar.foo', '*.bar') // false!
minimatch('bar.foo', '*.+(bar|foo)', { debug: true }) // true, and noisy!
```



### jsonwebtoken

安装

```shell
npm i jsonwebtoken --save
```

使用

```javascript
//authorization.js
const jwt = require("jsonwebtoken");

const secretKey = "secretKey";

// 生成token
module.exports.generateToken = function (payload) { 
  const token =
    "Bearer " +
    jwt.sign(payload, secretKey, {
      expiresIn: 60 * 60,
    });
  return token;
};

// 验证token
module.exports.verifyToken = function (req, res, next) {
  const token = req.headers.authorization.split(" ")[1];
  jwt.verify(token, secretKey, function (err, decoded) {
    if (err) {
      console.log("verify error", err);
      return res.json({ code: "404", msg: "token无效" });
    }
    console.log("verify decoded", decoded);
    next();
  });
};
```

在登陆接口生成token返回给前端

```javascript
// login.js
const express = require("express");
const router = express.Router();
const { generateToken } = require("./authorization");

// 路由
router.post("/", (req, res) => {
  const username = req.body.username;
  const password = req.body.password;
  const token = generateToken({ username: username });
  res.json({
    code: 200,
    msg: "登录成功",
    data: { token },
  });
});

module.exports = router;
```

注册中间件

```javascript
const loginRouter = require("./login");
const auth = require("./authorization");
const userRouter = require("./user");

app.use("/api/login", loginRouter);
app.use("/api/*", auth.verifyToken); // 注册token验证中间件
app.use("/api/user", userRouter);
```



### json-schema-faker



### write-good

检查语法

Vscode Atom都使用

```shell
npm install write-good

## global cli
npx write-good *.md
```

使用

```javascript
write-good *.md

write-good --text="I can't see a problem there that's not been defined yet.Should be defined again."

var writeGood = require('write-good');

var suggestions = writeGood('Never write read-only sentences.');
// suggestions: [{ index: 17, offset: 4, reason: '"only" can weaken meaning' }]

var filtered = writeGood('Never write read-only sentences.', { whitelist: ['read-only'] });
// filtered: []
```

### request-ip

获取发起请求的ip

```shell
npm install request-ip
```

使用

```javascript
// 中间件
const requestIp = require('request-ip');

// inside middleware handler
const ipMiddleware = function(req, res, next) {
    const clientIp = requestIp.getClientIp(req); 
    next();
};

// on localhost you'll see 127.0.0.1 if you're using IPv4 
// or ::1, ::ffff:127.0.0.1 if you're using IPv6

const requestIp = require('request-ip');
app.use(requestIp.mw())

app.use(function(req, res) {
    const ip = req.clientIp;
    res.end(ip);
});
```

### ua-parser-js

https://github.com/faisalman/ua-parser-js

解析user-agent这个http header的包

使用

```javascript


const request = {
    headers : {
        'user-agent' : 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36',

        'sec-ch-ua-mobile' : '?1',
        'sec-ch-ua-model' : 'Galaxy S3 Marketing',
        'sec-ch-ua-platform' : 'Android'
    }
};

const result1 = UAParser(request.headers);                      // parse only "user-agent" header
const result2 = UAParser(request.headers).withClientHints();    // update with "sec-ch-ua" headers
```

### ipaddr

操作ip地址

```shell
npm install ipaddr.js
```

使用

```javascript
const addr  = ipaddr.parse('2001:db8:1234::1');
const range = ipaddr.parse('2001:db8::');

addr.match(range, 32); // => true

const addr = ipaddr.parse('2001:db8:1234::1');

addr.match(ipaddr.parseCIDR('2001:db8::/32')); // => true

```

### cidr-regex

ip的正则

```shell
npm i cidr-regex
```

使用

```javascript
import cidrRegex from "cidr-regex";

// Contains a CIDR IP address?
cidrRegex().test("foo 192.168.0.1/24");
//=> true

// Is a CIDR IP address?
cidrRegex({exact: true}).test("foo 192.168.0.1/24");
//=> false

cidrRegex.v6({exact: true}).test("1:2:3:4:5:6:7:8/64");
//=> true

// Extract CIDRs from string
"foo 192.168.0.1/24 bar 1:2:3:4:5:6:7:8/64 baz".match(cidrRegex());
//=> ["192.168.0.1/24", "1:2:3:4:5:6:7:8/64"]
```

### node-ip

操作ip相关

安装

```shell
npm install ip
```

使用

```javascript
var ip = require('ip');

ip.address() // my ip address
ip.isEqual('::1', '::0:1'); // true
ip.toBuffer('127.0.0.1') // Buffer([127, 0, 0, 1])
ip.toString(new Buffer([127, 0, 0, 1])) // 127.0.0.1
ip.fromPrefixLen(24) // 255.255.255.0
ip.mask('192.168.1.134', '255.255.255.0') // 192.168.1.0
ip.cidr('192.168.1.134/26') // 192.168.1.128
ip.not('255.255.255.0') // 0.0.0.255
ip.or('192.168.1.134', '0.0.0.255') // 192.168.1.255
ip.isPrivate('127.0.0.1') // true
ip.isV4Format('127.0.0.1'); // true
ip.isV6Format('::ffff:127.0.0.1'); // true

// operate on buffers in-place
var buf = new Buffer(128);
var offset = 64;
ip.toBuffer('127.0.0.1', buf, offset);  // [127, 0, 0, 1] at offset 64
ip.toString(buf, offset, 4);            // '127.0.0.1'

// subnet information
ip.subnet('192.168.1.134', '255.255.255.192')
// { networkAddress: '192.168.1.128',
//   firstAddress: '192.168.1.129',
//   lastAddress: '192.168.1.190',
//   broadcastAddress: '192.168.1.191',
//   subnetMask: '255.255.255.192',
//   subnetMaskLength: 26,
//   numHosts: 62,
//   length: 64,
//   contains: function(addr){...} }
ip.cidrSubnet('192.168.1.134/26')
// Same as previous.

// range checking
ip.cidrSubnet('192.168.1.134/26').contains('192.168.1.190') // true


// ipv4 long conversion
ip.toLong('127.0.0.1'); // 2130706433
ip.fromLong(2130706433); // '127.0.0.1'
```

### ip-regex

安装

```shell
npm install ip-regex
```

使用

```javascript
import ipRegex from 'ip-regex';

// Contains an IP address?
ipRegex().test('unicorn 192.168.0.1');
//=> true

// Is an IP address?
ipRegex({exact: true}).test('unicorn 192.168.0.1');
//=> false

ipRegex.v6({exact: true}).test('1:2:3:4:5:6:7:8');
//=> true

'unicorn 192.168.0.1 cake 1:2:3:4:5:6:7:8 rainbow'.match(ipRegex());
//=> ['192.168.0.1', '1:2:3:4:5:6:7:8']

// Contains an IP address?
ipRegex({includeBoundaries: true}).test('192.168.0.2000000000');
//=> false

// Matches an IP address?
'192.168.0.2000000000'.match(ipRegex({includeBoundaries: true}));
//=> null
```

### lorem-ipsum

```javascript
import { LoremIpsum } from "lorem-ipsum";
// const LoremIpsum = require("lorem-ipsum").LoremIpsum;

const lorem = new LoremIpsum({
  sentencesPerParagraph: {
    max: 8,
    min: 4
  },
  wordsPerSentence: {
    max: 16,
    min: 4
  }
});

lorem.generateWords(1);
lorem.generateSentences(5);
lorem.generateParagraphs(7);
```



### websocket

#### WebSocket-Node

https://github.com/theturtle32/WebSocket-Node

#### socket.io

https://github.com/socketio/socket.io?tab=readme-ov-file

#### uWebSockets

https://github.com/uNetworking/uWebSockets

http://websocketd.com/ websocket 工具

### auth相关

#### basic-auth

快速验证

安装

```shell
npm install basic-auth
```

使用

```javascript
var auth = require('basic-auth')
var user = auth(req)
// => { name: 'something', pass: 'whatever' }

var http = require('http')
var auth = require('basic-auth')
var compare = require('tsscmp')

// Create server
var server = http.createServer(function (req, res) {
  var credentials = auth(req)

  // Check credentials
  // The "check" function will typically be against your user store
  if (!credentials || !check(credentials.name, credentials.pass)) {
    res.statusCode = 401
    res.setHeader('WWW-Authenticate', 'Basic realm="example"')
    res.end('Access denied')
  } else {
    res.end('Access granted')
  }
})

// Basic function to validate credentials for example
function check (name, pass) {
  var valid = true

  // Simple method to prevent short-circut and use timing-safe compare
  valid = compare(name, 'john') && valid
  valid = compare(pass, 'secret') && valid

  return valid
}

// Listen
server.listen(3000)
```



#### passportjs

安装

```shell
npm install passport
```

使用

```javascript
passport.use(new LocalStrategy(
  function(username, password, done) {
    User.findOne({ username: username }, function (err, user) {
      if (err) { return done(err); }
      if (!user) { return done(null, false); }
      if (!user.verifyPassword(password)) { return done(null, false); }
      return done(null, user);
    });
  }
));

// session

passport.serializeUser(function(user, done) {
  done(null, user.id);
});

passport.deserializeUser(function(id, done) {
  User.findById(id, function (err, user) {
    done(err, user);
  });
});
```



相关包 https://www.passportjs.org/packages/

passport-ldapauth

安装

```shell
npm install passport-ldapauth
```

使用

```javascript
var express      = require('express'),
    passport     = require('passport'),
    bodyParser   = require('body-parser'),
    LdapStrategy = require('passport-ldapauth');

var OPTS = {
  server: {
    url: 'ldap://localhost:389',
    bindDN: 'cn=root',
    bindCredentials: 'secret',
    searchBase: 'ou=passport-ldapauth',
    searchFilter: '(uid={{username}})'
  }
};

var app = express();

passport.use(new LdapStrategy(OPTS));

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));
app.use(passport.initialize());

app.post('/login', passport.authenticate('ldapauth', {session: false}), function(req, res) {
  res.send({status: 'ok'});
});

app.listen(8080);
```

passport-ldapjs

安装

```shell
npm install passport-ldapjs
```

使用

```javascript
var LdapStrategy = require('passport-ldapjs').Strategy;

var opts = {
  server: {
    url: 'ldap://0.0.0.0:1389',
  },
  base: 'OU=Users,OU=Company,DC=company,DC=com',
  search: {
    filter: '(sAMAccountName={{username}})',
    attributes: ['displayName', 'givenName', 'mail', 'title', 'telephoneNumber', 'physiscalDeliveryOfficeName', 'userPrincipalName', 'sAMAccountName'],
    scope: 'sub'
  },
  uidTag: 'cn',
  usernameField: 'email',
  passwordField: 'passwd',
};

passport.use(new LdapStrategy(opts, function(profile, done) {
  User.findOne({email: email}, '-salt -password', function(err, user) {
    if (err) {
      return done(err);
    }

    if (user) {
      return done(null, user);
    } else {
      return done('User not found');
    }
  });
}));
```



#### ldapjs

node端使用ldap验证

安装

```shell
npm install ldapjs
```

使用

```javascript
var ldap = require('ldapjs');

var server = ldap.createServer();

server.search('dc=example', function(req, res, next) {
  var obj = {
    dn: req.dn.toString(),
    attributes: {
      objectclass: ['organization', 'top'],
      o: 'example'
    }
  };

  if (req.filter.matches(obj.attributes))
  res.send(obj);

  res.end();
});

server.listen(1389, function() {
  console.log('ldapjs listening at ' + server.url);
});
```

### 数据库相关

#### prisma

数据库orm

安装

```shell
npm install prisma -D
```

Schema.prisma是prisma主要的配置文件，配置主要分为：

1.DB连接的配置

2.Prisma Client的配置

3.data model的定义

```javascript
datasource db {
  provider = "sqlite"
  url = "file:dev.db"
}

generator client {
	provider = "prisma-client-js"
}

model User {
  id     Int
  email  String
  name   String
}
```

生成数据表

```shell
prisma generate
```

安装Prisma-client

```shell
npm install @prisma/client
```

引入

```javascript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  // ... you will write your Prisma Client queries here
}

main()
  .then(async () => {
    await prisma.$disconnect()
  })
  .catch(async (e) => {
    console.error(e)
    await prisma.$disconnect()
    process.exit(1)
  })
```

增删改查

增加修改upsert

```typescript
const upsertUser = await prisma.user.upsert({
  where: {
    email: 'viola@prisma.io',
  },
  update: {
    name: 'Viola the Magnificent',
  },
  create: {
    email: 'viola@prisma.io',
    name: 'Viola the Magnificent',
  },
})
```

删

删除单条

```typescript
const deleteUser = await prisma.user.delete({
  where: {
    email: 'bert@prisma.io',
  },
})
```

删除多条

```typescript
const deleteUsers = await prisma.user.deleteMany({
  where: {
    email: {
      contains: 'prisma.io',
    },
  },
})
```

删除所有

```typescript
const deleteUser = await prisma.user.delete({
  where: {
    email: 'bert@prisma.io',
  },
})
```

查询单条

```typescript
const getUser: User | null = await prisma.user.findUnique({
  where: {
    id: 22,
  },
})
```

使用select只返回指定字段

```typescript
// Returns an object or null
const getUser: object | null = await prisma.user.findUnique({
  where: {
    id: 22,
  },
  select: {
    email: true,
    name: true,
  },
})
```



##### pulse

类型安全的基于prisma-client的数据流，可以使用发布订阅

https://www.prisma.io/data-platform/pulse

```typescript
// Subscribe to new events on the `message` table
const liveQuery = prisma.message.subscribe()
    
// Waiting loop that prints new events when something changes in the database
for await (const event of liveQuery) {
  console.log(event.action); // 'create', 'update', 'delete'
}

// Subscribe to new events on the `message` table
const liveQuery = prisma.message.subscribe({
  // Return update events where the filter criteria matches the "after" state
  update: { after: { chatId: 'id'} },
  // Return all delete change events 
  delete: { before: {} }
}); 
for await (const event of liveQuery) {
  console.log(event.action); // 'update', 'delete'
}
```

##### accelerate

https://www.prisma.io/data-platform/accelerate

#### Sequelize

安装

```shell
npm i sequelize
```

手动为所选数据库安装驱动程序

```shell
# 使用 npm
npm i pg pg-hstore # PostgreSQL
npm i mysql2 # MySQL
npm i mariadb # MariaDB
npm i sqlite3 # SQLite
npm i tedious # Microsoft SQL Server
npm i ibm_db # DB2
# 使用 yarn
yarn add pg pg-hstore # PostgreSQL
yarn add mysql2 # MySQL
yarn add mariadb # MariaDB
yarn add sqlite3 # SQLite
yarn add tedious # Microsoft SQL Server
yarn add ibm_db # DB2
```

要连接到数据库,必须创建一个 Sequelize 实例. 这可以通过将连接参数分别传递到 Sequelize 构造函数或通过传递一个连接 URI 来完成

```javascript
const { Sequelize } = require('sequelize');

// 方法 1: 传递一个连接 URI
const sequelize = new Sequelize('sqlite::memory:') // Sqlite 示例
const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname') // Postgres 示例

// 方法 2: 分别传递参数 (sqlite)
const sequelize = new Sequelize({
  dialect: 'sqlite',
  storage: 'path/to/database.sqlite'
});

// 方法 3: 分别传递参数 (其它数据库)
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* 选择 'mysql' | 'mariadb' | 'postgres' | 'mssql' 其一 */
});
```

测试连接

```javascript
try {
  await sequelize.authenticate();
  console.log('Connection has been established successfully.');
} catch (error) {
  console.error('Unable to connect to the database:', error);
}
```

默认情况下,Sequelize 将保持连接打开状态,并对所有查询使用相同的连接. 如果你需要关闭连接,请调用 `sequelize.close()`(这是异步的并返回一个 Promise)

#### TypeOrm

TypeORM 是一个[ORM](https://en.wikipedia.org/wiki/Object-relational_mapping)框架，它可以运行在 NodeJS、Browser、Cordova、PhoneGap、Ionic、React Native、Expo 和 Electron 平台上，可以与 TypeScript 和 JavaScript (ES5,ES6,ES7,ES8)一起使用。 它的目标是始终支持最新的 JavaScript 特性并提供额外的特性以帮助你开发任何使用数据库的（不管是只有几张表的小型应用还是拥有多数据库的大型企业应用）应用程序。

TypeORM 的一些特性:

- 支持 [DataMapper](https://typeorm.bootcss.com/active-record-data-mapper#what-is-the-data-mapper-pattern) 和 [ActiveRecord](https://typeorm.bootcss.com/active-record-data-mapper#what-is-the-active-record-pattern) (随你选择)
- 实体和列
- 数据库特性列类型
- 实体管理
- 存储库和自定义存储库
- 清晰的对象关系模型
- 关联（关系）
- 贪婪和延迟关系
- 单向的，双向的和自引用的关系
- 支持多重继承模式
- 级联
- 索引
- 事务
- 迁移和自动迁移
- 连接池
- 主从复制
- 使用多个数据库连接
- 使用多个数据库类型
- 跨数据库和跨模式查询
- 优雅的语法，灵活而强大的 QueryBuilder
- 左联接和内联接
- 使用联查查询的适当分页
- 查询缓存
- 原始结果流
- 日志
- 监听者和订阅者（钩子）
- 支持闭包表模式
- 在模型或者分离的配置文件中声明模式
- json / xml / yml / env 格式的连接配置
- 支持 MySQL / MariaDB / Postgres / SQLite / Microsoft SQL Server / Oracle / sql.js
- 支持 MongoDB NoSQL 数据库
- 可在 NodeJS / 浏览器 / Ionic / Cordova / React Native / Expo / Electron 平台上使用
- 支持 TypeScript 和 JavaScript
- 生成高性能、灵活、清晰和可维护的代码
- 遵循所有可能的最佳实践
- 命令行工具

安装

```shell
npm install typeorm reflect-metadata --save
```

在全局位置导入

```typescript
import "reflect-metadata";
```

通过使用 `TypeORM` 你的 `models` 看起来像这样

```typescript
import { Entity, PrimaryGeneratedColumn, Column } from "typeorm";

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column()
  age: number;
}
```

逻辑操作

```typescript
const user = new User();
user.firstName = "Timber";
user.lastName = "Saw";
user.age = 25;
await user.save();

const allUsers = await User.find();
const firstUser = await User.findOne(1);
const timber = await User.findOne({ firstName: "Timber", lastName: "Saw" });

await timber.remove();
```

每个实体都有自己的存储库，可以处理其实体的所有操作。当你经常处理实体时，Repositories 比 EntityManagers 更方便使用

```typescript
import { createConnection } from "typeorm";
import { Photo } from "./entity/Photo";

createConnection(/*...*/)
  .then(async connection => {
    let photo = new Photo();
    photo.name = "Me and Bears";
    photo.description = "I am near polar bears";
    photo.filename = "photo-with-bears.jpg";
    photo.views = 1;
    photo.isPublished = true;

    let photoRepository = connection.getRepository(Photo);

    await photoRepository.save(photo);
    console.log("Photo has been saved");

    let savedPhotos = await photoRepository.find();
    console.log("All photos from the db: ", savedPhotos);
  })
  .catch(error => console.log(error));
```

queryBuilder

`QueryBuilder`是 TypeORM 最强大的功能之一 ，它允许你使用优雅便捷的语法构建 SQL 查询，执行并获得自动转换的实体。

```typescript
const firstUser = await connection
  .getRepository(User)
  .createQueryBuilder("user")
  .where("user.id = :id", { id: 1 })
  .getOne();
```

Repository

`Repository`就像`EntityManager`一样，但其操作仅限于具体实体。

你可以通过`getRepository（Entity）`，`Connection＃getRepository`或`EntityManager＃getRepository`访问存储库。

```typescript
import { getRepository } from "typeorm";
import { User } from "./entity/User";

const userRepository = getRepository(User); // 你也可以通过getConnection().getRepository()或getManager().getRepository() 获取
const user = await userRepository.findOne(1);
user.name = "Umed";
await userRepository.save(user);
```

有三种类型的存储库：

- `Repository` - 任何实体的常规存储库。
- `TreeRepository` - 用于树实体的`Repository`的扩展存储库（比如标有`@ Tree`装饰器的实体）。有特殊的方法来处理树结构。
- `MongoRepository` - 具有特殊功能的存储库，仅用于 MongoDB。

迁移



事务

日志

你只需在连接选项中设置`logging：true`即可启用所有查询和错误的记录

```json
{
    name: "mysql",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    ...
    logging: true
}
```

TypeORM 附带 4 种不同类型的记录器：

- `advanced-console` - 默认记录器，它将使用颜色和 sql 语法高亮显示所有记录到控制台中的消息（使用[chalk](https://github.com/chalk/chalk)）。
- `simple-console` - 简单的控制台记录器，与高级记录器完全相同，但它不使用任何颜色突出显示。 如果你不喜欢/或者使用彩色日志有问题，可以使用此记录器。
- `file` - 这个记录器将所有日志写入项目根文件夹中的`ormlogs.log`（靠近`package.json`和`ormconfig.json`）。
- `debug` - 此记录器使用[debug package](https://github.com/visionmedia/debug)打开日志记录设置你的 env 变量`DEBUG = typeorm：*`（注意记录选项对此记录器没有影响）。

```json
{
    host: "localhost",
    ...
    logging: true,
    logger: "file"
}
```

#### Knex

辅助node构建sql语句

安装

```shell
$ npm install knex --save

# Then add one of the following (adding a --save) flag:
$ npm install pg
$ npm install pg-native
$ npm install sqlite3
$ npm install better-sqlite3
$ npm install mysql
$ npm install mysql2
$ npm install oracledb
$ npm install tedious
```

使用

```javascript
import { Knex, knex } from 'knex'

knex({ a: 'table', b: 'table' })
  .select({
    aTitle: 'a.title',
    bTitle: 'b.title'
  })
  .whereRaw('?? = ??', ['a.column_1', 'b.column_2'])

interface User {
  id: number;
  name: string;
  age: number;
}

knex('users')
  .where('id')
  .first(); // Resolves to any

knex<User>('users') // User is the type of row in database
  .where('id', 1) // Your IDE will be able to help with the completion of id
  .first(); // Resolves to User | undefined


knex.avg('sum_column1')
  .from(function() {
    this.sum('column1 as sum_column1')
      .from('t1')
      .groupBy('column1')
      .as('t1')
  })
  .as('ignored_alias')
```

https://knexjs.org/guide/query-builder.html#select

https://github.com/knex/knex

#### safeql

https://github.com/ts-safeql/safeql

#### kysely

https://github.com/kysely-org/kysely

#### node-postgres

https://github.com/brianc/node-postgres

#### drizzle-orm

https://github.com/drizzle-team/drizzle-orm



#### slonik

https://github.com/gajus/slonik

stop using knexjs：https://gajus.medium.com/stop-using-knex-js-and-earn-30-bf410349856c

顺便一提，此巨佬有别的文章很好：https://gajus.medium.com/cto-vs-head-of-engineer-8845da32ea67

#### pg-promise

https://github.com/vitaly-t/pg-promise

#### Node-pg-migrate

安装

```shell
npm install node-pg-migrate pg
```

在package.json中添加命令

```json
{
  "script": {
		"migrate": "node-pg-migrate"
  }
}
```

然后允许迁移命令，生成迁移文件

```shell
npm run migrate create my first migration
```

在`migrations`文件夹下打开`xxx_my-first-migration.js`文件，修改文件

```javascript
exports.up = (pgm) => {
  pgm.createTable('users', {
    id: 'id',
    name: { type: 'varchar(1000)', notNull: true },
    createdAt: {
      type: 'timestamp',
      notNull: true,
      default: pgm.func('current_timestamp'),
    },
  })
  pgm.createTable('posts', {
    id: 'id',
    userId: {
      type: 'integer',
      notNull: true,
      references: '"users"',
      onDelete: 'cascade',
    },
    body: { type: 'text', notNull: true },
    createdAt: {
      type: 'timestamp',
      notNull: true,
      default: pgm.func('current_timestamp'),
    },
  })
  pgm.createIndex('posts', 'userId')
}
```

配置DATABASE_URL环境变量

`DATABASE_URL=postgres://test:test@localhost:5432/test`

运行迁移命令

```shell
npm run migrate up
```



https://github.com/jawj/zapatos

https://github.com/adelsz/pgtyped

#### stripe

发布订阅

https://github.com/stripe/stripe-node







