---
title: NodeJs开发（四）
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

### fast-glob

https://juejin.cn/post/7229169602420801593

全局扫描所有文件的工具，[fast-glob](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fmrmlnc%2Ffast-glob)，速度非常快的 `glob` 工具库。

`glob` 是什么？是一种语法概念，允许使用者通过 “通配符” 来匹配目录和文件，像在nodejs或者webpack plugin里经常遇到的 `**/*.js` `src/**/package.json` 都属于这种语法。`fast-glob` 则是一款速度非常快的`glob` 工具库。

首先需要安装包：`npm install fast-glob`，因为有很多第三方包已经包含了此依赖，所以如果有包含的，也可以不用单独安装，可以通过`npm ls fast-glob`来检查是否包含

```typescript
import glob from 'fast-glob';

async function scan() {
    const files = await glob(['src/**/*.{js,jsx,json}']);
    for (const file of files) {
        const fileName = file.split('/').reverse()[0];
        const content = fs.readFileSync(file, 'utf-8');
        console.log(file, fileName, content);
    }
}
```

替换组件写法

```typescript
// scan.mjs
import glob from 'fast-glob';
import fs from 'fs';

const results = [];
async function scan() {
    const files = await glob(['src/**/*.{js,jsx,json}']);
    for (const file of files) {
        const content = fs.readFileSync(file, 'utf-8');
        const lines = content.split('\r\n');
        let codes = [];
        let linenum = 0;
        let length = 0;

        lines.forEach((line, i) => {
            const text = line.trim();
            if (text.startsWith('<CommonButton')) {
                length = line.indexOf('<CommonButton')
                codes.push(line.slice(length));
                linenum = i + 1;
            }
            else if (codes.length > 0) {
                codes.push(line.slice(length));
            }
            if (codes.length > 0 && text.endsWith('/>')) {
                const all = codes.join(' ');
                const isNotView = !all.includes(' view ') && !all.includes(' view={true} '); // match noview || `view={false}` || `view={xx}`
                const isMultiple = all.includes('multiple') && !all.includes(' multiple={false} '); // match `multiple` `multiple={true}` || `multiple={xx}`
                if (isNotView && isMultiple) {
                    results.push({ file: file.slice(3), line: linenum, code: codes });
                }
                codes = [];
            }
        });
    }

    fs.writeFileSync('./result.json', JSON.stringify(results, null, 2), { encoding: 'utf8' }, (err) => {
        if (err) throw err;
    });
}
await scan();
```





### 其他应用

#### 飞书sdk

```shell
npm install @larksuiteoapi/node-sdk
```

使用

```javascript
import * as lark from '@larksuiteoapi/node-sdk';

const client = new lark.Client({
    appId: 'app id',
    appSecret: 'app secret',
    appType: lark.AppType.SelfBuild,
    domain: lark.Domain.Feishu,
});

const res = await client.im.message.create({
    params: {
        receive_id_type: 'chat_id',
    },
    data: {
        receive_id: 'receive_id',
        content: JSON.stringify({text: 'hello world'}),
        msg_type: 'text',
  },
});
```

#### 阿里云sdk

访问凭证

安装

```shell
npm install @alicloud/credentials
```

使用

```typescript
const Credential = require('@alicloud/credentials');
const { RuntimeOptions } = require('@alicloud/tea-util');

const runtime = new RuntimeOptions({
  // 设置http代理
  httpProxy: "http://xx.xx.xx.xx:8089",
  // 设置https代理
  httpsProxy: "https://xxx.xxx.xxx.xxx:9999",
  // 设置非代理地址
  noProxy: '127.0.0.1,localhost',
});

const credentialsConfig = new Credential.Config({
    type: 'sts',
    // 设置accessKeyId值，此处已从环境变量中获取accessKeyId为例。
    accessKeyId: process.env.ALIBABA_CLOUD_ACCESS_KEY_ID,
    // 设置accessKeySecret值，此处已从环境变量中获取accessKeySecret为例。
    accessKeySecret: process.env.ALIBABA_CLOUD_ACCESS_KEY_SECRET,
    // 设置securityToken值，此处已从环境变量中获取securityToken为例。
    securityToken: process.env.ALIBABA_CLOUD_SECURITY_TOKEN,
});
const cred = new Credential.default(credentialsConfig);

const cfg = new OpenApiConfig({
      credential: cred as any,
      ...(isAccountEp ? {} : { regionId: regionId || base.regionId }),
    } as any);

```

通过`@alicloud/tea-util`库的RuntimeOptions类中的 **ignoreSSL** 参数来设置是否启用SSL/TLS证书校验。例如，在测试环境中，您可以将**ignoreSSL**设置为`true`，以忽略证书校验进行临时测试。



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

```prisma
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

##### 性能优化

https://juejin.cn/post/7426545445876957222#heading-7

1.使用数据库连接池

和 `mysql2` 一样，Prisma 也支持数据库连接池管理。连接池通过在一组预先建立的数据库连接中重用连接，可以减少连接建立的开销，显著提高数据库的性能。

Prisma 默认使用数据库连接池，但需要确保你的数据库支持这一功能，并且配置得当。例如，PostgreSQL、MySQL 等数据库都支持连接池

`connection_limit` 参数控制连接池中同时允许的最大连接数。你可以根据应用的并发负载来调整这个数值，过高可能导致数据库服务器压力过大，过低可能导致等待时间增加

2.避免N+1查询问题

在处理数据库关系时，N+1 查询问题常常成为性能瓶颈。Prisma 提供了**预加载（`include` 或 `select`）**的功能，允许一次性加载相关数据，从而避免多次查询造成的性能损耗。

3.缓存查询结果

对于经常查询但结果不经常变化的数据，可以使用缓存来减少查询次数。Next.js 支持服务器端的缓存策略，你可以结合 Redis 等缓存工具，将查询结果缓存一段时间。

```javascript
import Redis from 'ioredis';
import prisma from './prisma';

const redis = new Redis();

export default async function getUsers() {
  const cachedUsers = await redis.get('users');

  if (cachedUsers) {
    return JSON.parse(cachedUsers); // 返回缓存数据
  }

  const users = await prisma.user.findMany();
  
  await redis.set('users', JSON.stringify(users), 'EX', 3600); // 缓存数据，1 小时过期
  return users;
}
```

Prisma6



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

##### 实体

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
  
  @Column({ type: 'json', nullable: true, comment: '差异列表 [{field, from, to}]' })
  diff: Array<{ field: string; from: any; to: any }> | null;
}
```

实体是一个映射到数据库表（或使用 MongoDB 时的集合）的类。 你可以通过定义一个新类来创建一个实体，并用`@Entity()`来标记

基本实体由列和关系组成。 每个实体**必须**有一个主列@PrimaryGeneratedColumn。`@PrimaryGeneratedColumn()` 创建一个主列，该值将使用自动增量值自动生成。 它将使用`auto-increment` /`serial` /`sequence`创建`int`列（取决于数据库）。 你不必在保存之前手动分配其值，该值将会自动生成

标有`@Column`的每个实体类属性都将映射到数据库表列

有几种特殊的列类型可以使用：

- `@CreateDateColumn` 是一个特殊列，自动为实体插入日期。无需设置此列，该值将自动设置。
- `@UpdateDateColumn` 是一个特殊列，在每次调用实体管理器或存储库的`save`时，自动更新实体日期。无需设置此列，该值将自动设置。
- `@VersionColumn` 是一个特殊列，在每次调用实体管理器或存储库的`save`时自动增长实体版本（增量编号）。无需设置此列，该值将自动设置

@Index 此装饰器允许你为特定列创建数据库索引。 它还允许你将列或列标记为唯一。 此装饰器可以应用于列或实体本身。 单列索引时使用或多列索引时使用。

`@Unique`此装饰器允许你为特定列创建数据库唯一约束。 该装饰器只能应用于实体本身。

TypeORM 支持所有最常用的数据库支持的列类型。 列类型是特定于数据库类型的 - 这为数据库架构提供了更大的灵活性。 你可以将列类型指定为`@Column`的第一个参数 或者在`@Column`的列选项中指定

`mysql`/`mariadb`的列类型

`int`, `tinyint`, `smallint`, `mediumint`, `bigint`, `float`, `double`, `dec`, `decimal`, `numeric`, `date`, `datetime`, `timestamp`, `time`, `year`, `char`, `varchar`, `nvarchar`, `text`, `tinytext`, `mediumtext`, `blob`, `longtext`, `tinyblob`, `mediumblob`, `longblob`, `enum`, `json`, `binary`, `geometry`, `point`, `linestring`, `polygon`, `multipoint`, `multilinestring`, `multipolygon`, `geometrycollection`

`postgres`的列类型

`int`, `int2`, `int4`, `int8`, `smallint`, `integer`, `bigint`, `decimal`, `numeric`, `real`, `float`, `float4`, `float8`, `double precision`, `money`, `character varying`, `varchar`, `character`, `char`, `text`, `citext`, `hstore`, `bytea`, `bit`, `varbit`, `bit varying`, `timetz`, `timestamptz`, `timestamp`, `timestamp without time zone`, `timestamp with time zone`, `date`, `time`, `time without time zone`, `time with time zone`, `interval`, `bool`, `boolean`, `enum`, `point`, `line`, `lseg`, `box`, `path`, `polygon`, `circle`, `cidr`, `inet`, `macaddr`, `tsvector`, `tsquery`, `uuid`, `xml`, `json`, `jsonb`, `int4range`, `int8range`, `numrange`, `tsrange`, `tstzrange`, `daterange`, `geometry`, `geography`

##### 关系

一对一是一种 A 只包含一个 B 实例，而 B 只包含一个 A 实例的关系

将`@OneToOne`添加到`profile`并将目标关系类型指定为`Profile`。 我们还添加了`@JoinColumn`，这是必选项并且只能在关系的一侧设置。 你设置`@JoinColumn`的哪一方，哪一方的表将包含一个"relation id"和目标实体表的外键

同样，`@JoinColumn`必须仅设置在关系的一侧且必须在数据库表中具有外键的一侧

同样，关系可以是单向的和双向的。 单向是仅在一侧与关系装饰器的关系。 双向是与关系两侧的装饰者的关系

注意，反向关系没有`@JoinColumn`。 `@JoinColumn`必须只在关系的一侧且拥有外键的表上



多对多是一种 A 包含多个 B 实例，而 B 包含多个 A 实例的关系

`@JoinTable()`是`@ManyToMany`关系所必需的。 你必须把`@JoinTable`放在关系的一个（拥有）方面

我们只是创建了双向关系。 注意，反向关系没有`@JoinTable`。 `@JoinTable`必须只在关系的一边

`@JoinTable` 装饰器可接收一个 **对象参数**，包括以下常用配置：

- `name`：可选，关系中间表表名，若不设置，则 [**`TypeORM`**](https://link.juejin.cn?target=https%3A%2F%2Ftypeorm.io%2F) 会根据两个关联的实体按照规则自动生成。
- `joinColumn`：可选，设置 **主实体** 的外键列，如 `Role` 的 `id` 列，以及中间表存储该列数据的列名称（`roleId`），作用同 [`@JoinColumn`](https://link.juejin.cn?target=https%3A%2F%2Ftypeorm.io%2Frelations%23joincolumn-options) 装饰器。
- `inverseJoinColumn`：可选，设置 **从实体** 的外键列，如 `Auth` 的 `id` 列，以及中间表存储该列数据的列名称（`authId`），作用同 [`@JoinColumn`](https://link.juejin.cn?target=https%3A%2F%2Ftypeorm.io%2Frelations%23joincolumn-options) 装饰器。

```typescript
@JoinTable({
  name: 'application_organizations',
  joinColumn: {
    name: 'application_id',
    referencedColumnName: 'id',
  },
  inverseJoinColumn: {
    name: 'organization_id',
    referencedColumnName: 'id',
  },
})
```



可以指定几个选项：

- `eager: boolean` - 如果设置为 true，则在此实体上使用`find *` 或`QueryBuilder`时，将始终使用主实体加载关系
- `cascade: boolean` - 如果设置为 true，则将插入相关对象并在数据库中更新。
- `onDelete: "RESTRICT"|"CASCADE"|"SET NULL"` - 指定删除引用对象时外键的行为方式
- `primary: boolean` - 指示此关系的列是否为主列。
- `nullable: boolean` -指示此关系的列是否可为空。 默认情况下是可空。
- `orphanedRowAction: "nullify" | "delete"` - 将子行从其父行中删除后，确定该子行是孤立的（默认值）还是删除的

```typescript
@OneToMany(() => MaterialCommonAttachment, (attachment) => attachment.mechanismScriptsFilesMaterial, {
  cascade: true,
  onDelete: 'CASCADE',
})
```

关联查询

调用查询类 `api`，如 `Repository.find`，在 `FindOptions` 参数中指定 `relations` 配置项

```typescript
const roleRepository = dataSource.getRepository(Role)
const roles = await roleRepository.find({
    relations: {
        authorizations: true,
    },
})
```

或者借助 `leftJoinAndSelect` api 添加左连接查询



##### find和Repository

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

Repository

`Repository`就像`EntityManager`一样，但其操作仅限于具体实体。

你可以通过`getRepository（Entity）`，`Connection＃getRepository`或`EntityManager＃getRepository`访问存储库。

```typescript
import { getRepository } from "typeorm";
import { InjectRepository } from '@nestjs/typeorm';
import { User } from "./entity/User";

const userRepository = getRepository(User); // 你也可以通过getConnection().getRepository()或getManager().getRepository() 获取
const user = await userRepository.findOne(1);
user.name = "Umed";
await userRepository.save(user)

export class InitializationService implements OnModuleInit {
  constructor(
    @InjectRepository(BaseOrganizations, 'main')
    private readonly organizationRepository: Repository<BaseOrganizations>,
  ) {}
  
  let organization = await this.organizationRepository.findOne({
      where: { tenantKey: tenantKey },
    });

    if (!organization) {
      this.logger.log(`创建组织: ${tenantKey}`);
      organization = this.organizationRepository.create({
        id: 'fb48d52e-27bc-4024-9e39-447db3333040',
        apaasId: '1842860125227016',
        tenantKey: tenantKey,
        fName: '仙工智能',
        avatarOrigin:
          'https://s1-imfile.feishucdn.com/static-resource/v1/v3_00bk_98f4834b-56d5-42c7-abbd-be03c1da66dg~?image_size=noop&cut_type=&quality=&format=png&sticker_format=.webp',
      });
      organization = await this.organizationRepository.save(organization);
      this.logger.log(`组织创建成功: ${organization.fName}`);
    }
}
```

有三种类型的存储库：

- `Repository` - 任何实体的常规存储库。
- `TreeRepository` - 用于树实体的`Repository`的扩展存储库（比如标有`@ Tree`装饰器的实体）。有特殊的方法来处理树结构。
- `MongoRepository` - 具有特殊功能的存储库，仅用于 MongoDB。

也可以使用数据源

```javascript
const entityManager = dataSource.manager;
const user = new User();
user.name = 'John Doe';
await entityManager.save(user);

const userRepository = dataSource.getRepository(User);
const user = await userRepository.findOne({ where: { name: 'John Doe' } });

const queryRunner = dataSource.createQueryRunner();
await queryRunner.connect();
await queryRunner.startTransaction();
try {
  const user = new User();
  user.name = 'John Doe';
  await queryRunner.manager.save(user);
  await queryRunner.commitTransaction();
} catch (error) {
  await queryRunner.rollbackTransaction();
} finally {
  await queryRunner.release();
}
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

创建querybuilder的几种方式

```typescript
// 使用connection
import { getConnection } from "typeorm";

const user = await getConnection()
  .createQueryBuilder()
  .select("user")
  .from(User, "user")
  .where("user.id = :id", { id: 1 })
  .getOne();

// 使用entity manager
import { getManager } from "typeorm";

const user = await getManager()
  .createQueryBuilder(User, "user")
  .where("user.id = :id", { id: 1 })
  .getOne();

// 使用repository
import { getRepository } from "typeorm";

const user = await getRepository(User)
  .createQueryBuilder("user")
  .where("user.id = :id", { id: 1 })
  .getOne();
```



##### 迁移

一旦上线生产环境，你将需要将模型更改同步到数据库中。 通常在数据库中获取数据后，使用`synchronize：true`进行生产模式同步是不安全的。

```shell
typeorm migration:create -n PostRefactoring
```





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

##### datamapper

使用 Active Record 方法，你可以在模型本身内定义所有查询方法，并使用模型方法保存、删除和加载对象。

简单来说，Active Record 模式是一种在模型中访问数据库的方法。 你可以在[Wikipedia](https://en.wikipedia.org/wiki/Active_record_pattern)上查看有关 Active Record 模式的更多信息。

所有 active-record 实体都必须扩展`BaseEntity`类，它提供了与实体一起使用的方法

使用 Data Mapper 方法，你可以在名为"repositories"的单独类中定义所有查询方法，并使用存储库保存、删除和加载对象。 在数据映射器中，你的实体非常笨，它们只是定义了相应的属性，并且可能有一些很笨的方法。

简单来说，数据映射器是一种在存储库而不是模型中访问数据库的方法



##### 订阅发布

订阅实体。实体有修改时触发逻辑

```javascript
@EventSubscriber()
export class PostSubscriber implements EntitySubscriberInterface<Post> {
  /**
   * 表示此订阅者仅侦听Post事件。
   */
  listenTo() {
    return Post;
  }

  /**
   * 插入post之前调用。
   */
  beforeInsert(event: InsertEvent<Post>) {
    console.log(`BEFORE POST INSERTED: `, event.entity);
  }
}
```

https://typeorm.bootcss.com/listeners-and-subscribers#%E8%AE%A2%E9%98%85%E8%80%85

##### 外键约束错误

enum增加枚举



##### 和prisma对比

https://kuizuo.me/blog/with-prisma-dont-use-typeorm/#prisma-%E7%94%9F%E6%80%81



#### mikro-orm

https://github.com/mikro-orm/mikro-orm



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







