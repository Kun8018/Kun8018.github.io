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

## cli工具

### 创建一个cli工具

进入文件夹，初始化node项目

```shell
 npm init -y
```

在package.json中添加bin配置，并添加对应的js文件

```json
{
  'name': 'mycli-demo',
  'version': '1.0.0',
  'main': 'index.js',
  'bin': {
    'mycli': './bin/cli.js'
  },
  'scripts': {
    'test': 'echo \"Error: no test" && exit 1'
  }
}
```

在项目中创建bin/cli.js

```javascript
#!/usr/bin/env node 
// 第一行指明运行环境 很重要
console.log('cli log')
```

然后在本地使用npm link或者yarn link安装工具

```shell
npm link
## yarn link
```

使用unlink可以删除掉

```shell
npm unlink mycli
```

发布在npm上

在项目根目录下新建publish.sh

```shell
#!/usr/bin/env bash
set -e
# 修改npm源地址
npm config get registry
npm config set registry=http://registry.npmjs.org
# 登陆输入自己的npm账号和密码，还有邮箱
echo '登录'
npm login
echo "发布中..."
npm publish
# 改回npm源地址
npm config set registry=https://registry.npm.taobao.org
echo -e "\n发布成功\n"
exit
```

发布完成后测试

```shell
npm i -g mycli
```

发布后取消或者删除包

强制取消，仅允许最近 72 小时内发布的版本取消发布

```shell
## 强制取消
npm unpublish --force
## 删除
npx force-unpublish package-name '原因描述'
```



https://l-x-f.github.io/2019/12/28/node/cli/

https://segmentfault.com/a/1190000022721056

https://juejin.cn/post/6844904153030852621#heading-4



### oclif cli工具

oclif是用来构建基于node的cli工具框架

利用oclif创建cli

```shell
$ npx oclif generate mynewcli
? npm package name (mynewcli): mynewcli
$ cd mynewcli
$ ./bin/dev hello world
hello world! (./src/commands/hello/world.ts)
```

使用

```typescript
import {Command} from '@oclif/core'

export class MyCommand extends Command {
  static description = 'description of this example command'

  async run() {
    console.log('running my command')
  }
}
```

https://openbase.com/js/oclif#-examples

文档：https://oclif.io/docs/introduction

### 创建一个init cli

/bin/vea-cli

```javascript
// 告诉执行环境用node来执行
#!/usr/bin/env node
// 添加命令的库
const program = require('commander')
// 拿到package.json 里的版本号
const packageJson = require('../package.json')
const init = require('../lib/init')
//  执行  vea-cli -V 会输出版本号
program.version(packageJson.version)
// 添加init命令，简写是i， <name> 是参数  action回调里可以拿到
program
  .command('init <name>')
  .alias('i')
  .description('vue admin 项目初始化工具')
  .action(name => {
    init(name)
  })
// 解析命令行参数
program.parse(process.argv)
```

/lib/init.js

```javascript
const chalk = require("chalk");
// 用户与命令行交互的工具
const Prompt = require("inquirer");
const clone = require("./clone");
// 对应github仓库地址https://github.com/l-x-f/admin-template
// #dev 是dev分支，不写默认master分支
const remote = "github:l-x-f/admin-template#dev";
const initQuestions = name => [
  {
    type: "confirm",
    name: "isInit",
    message: `确定要在${chalk.green(name)}文件夹下创建项目?`,
    prefix: "?"
  }
];
const init = async name => {
  try {
    const { isInit } = await Prompt.prompt(initQuestions(name));
    if (isInit) {
      await clone(remote, name);
    } else {
      console.log(chalk.red("程序提前结束"));
    }
  } catch (error) {
    console.log(chalk.red(error));
  }
};
module.exports = init;
```

/lib/clone.js

```javascript
// node的 util 模块 promisify可以把回调promise化
const { promisify } = require("util");
// 进度显示工具
const ora = require("ora");
// 颜色显示工具
const chalk = require("chalk");
// 下载git 仓库代码工具
const download = promisify(require("download-git-repo"));
/**
 *
 * @param {string} repo 仓库地址
 * @param {string}  dir 文件夹
 * @param {object}  opotions 配置项
 */
const clone = async function(repo, dir, opotions = {}) {
  const process = ora(`开始下载 ${chalk.blue(repo)}`);
  process.start();
  process.color = "yellow";
  process.text = `正在下载..... ${chalk.yellow(repo)} `;
  try {
    await download(repo, dir, opotions);
    process.color = "green";
    process.text = `下载成功 ${chalk.green(repo)} `;
    process.succeed();
  } catch (error) {
    process.color = "red";
    process.text = "下载失败";
    process.fail();
  }
};
module.exports = clone;
```



## 功能模块

### semver

语义化控制版本包

```shell
npm install semver
```

用法

```javascript
const semver = require('semver')
 
semver.clean(' =v1.1.1 ')；// 1.1.1，解析版本号，忽略版本号前面的符号
 
semver.valid('1.1.1'); // true，版本号是否合法
semver.valid('a.b.c'); // false
 
semver.satisfies('1.2.4', '1.2.3 - 1.2.5'); // true, 判断版本是否在某个范围

semver.gt('1.2.3', '9.8.7') // false
semver.lt('1.2.3', '9.8.7') // true
semver.minVersion('>=1.0.0') // '1.0.0'
```



### graceful-fs



### commander.js

前端开发node cli 必备技能。

安装

```shell
npm install commander
```

api

```javascript
var program = require('commander');
 
program
    .name("intl helper");
    .version('0.0.1')
    .parse(process.argv);
    
//执行结果：
node index.js -V
 
0.0.1
//如果希望程序响应-v选项而不是-V选项，
//只需使用与option方法相同的语法将自定义标志传递给version方法
program
  .version('0.0.1', '-v, --version')
```

commander.js中命令行有两种可变性，一个叫做`option`，意为选项。一个叫做`command`，意为命令。

常用api

`version`

用法： `.version('x.y.z')`

用于设置命令程序的版本号，

`option`

用户：`.option('-n, --name <name>', 'your name', 'GK')`

- 第一个参数是选项定义，分为短定义和长定义。用|，,， 连接。
  - 参数可以用`<>`或者`[]`修饰，前者意为必须参数，后者意为可选参数。
- 第二个参数为选项描述
- 第三个参数为选项参数默认值，可选。

`command`

用法：`.command('init <path>', 'description')`

- `command`的用法稍微复杂，原则上他可以接受三个参数，第一个为命令定义，第二个命令描述，第三个为命令辅助修饰对象。
- 第一个参数中可以使用`<>`或者`[]`修饰命令参数
- 第二个参数可选。
  - 当没有第二个参数时，commander.js将返回`Command`对象，若有第二个参数，将返回原型对象。
  - 当带有第二个参数，并且没有显示调用`action(fn)`时，则将会使用子命令模式。
  - 所谓子命令模式即，`./pm`，`./pm-install`，`./pm-search`等。这些子命令跟主命令在不同的文件中。
- 第三个参数一般不用，它可以设置是否显示的使用子命令模式。

`description`

用法：`.description('command description')`

用于设置命令的描述

用法：`.action(fn)`

用于设置命令执行的相关回调。`fn`可以接受命令的参数为函数形参，顺序与`command()`中定义的顺序一致。

`parse`

用法：`program.parse(process.argv)`

此api一般是最后调用，用于解析`process.argv`。

`outputHelp`

用法：`program.outputHelp()`

一般用于未录入参数时自动打印帮助信息。

### inquire

`Inquirer.js`可以理解成就是给输入命令行的用户提供一个好看的界面，提供一下功能：

- 有错误反馈；
- 向用户提问；
- 解析输入；
- 校验回答；
- 能在用户输入的时候提供友好的提示。

安装

```shell
yarn add inquirer --save-dev
```

Inquirer 提供`prompt`对象，该对象中提供配置项，`then`会在用户回答完所有问题后执行，`catch`则是报出异常：

prompt是一个对象数组，对象主要包含以下几种配置：

type： 类型，主要类型有input、number、confirm、list、rawlist、expand、checkbox、password、editor；

name：可以理解成当前回答的变量名；

message：问题描述；

default：问题的默认值；

choice：问题选项；

validate：回答的校验器；

filter：回答的过滤器；

transformer：接收用户输入，回答散列和选项标志，并返回一个转换后的值显示给用户。

when：是否应该问这个问题

PageSize：控制选项显示的个数，就是是否当前最多显示多少个选项，如果超过则需要向下才能显示更多；

prefix：更改默认的前缀消息。

suffix：更改默认后缀消息。

askAnswered：如果答案已经存在，就必须提出问题。

loop：是否启用列表循环。

```javascript
var inquirer = require('inquirer');
inquirer.prompt([
  {
    type: 'list',
    name: 'preset',
    message: 'Please pick a preset:',
    choices: ['default(babel, eslint)', 'Manually select feature'],
    filter: function(val){
      return val.toLowerCase();
    }
  },
  {
    type: 'input',
    name: 'key',
    message: "input the text key:",
  },
  {
  type: 'checkbox',
  name: 'features',
  message: 'Checkout the feature needed for you project:',
  choices: [{
    name: 'Babel',
  }, {
    name: 'TypeScript',
  },{
    name: 'Progressive Web App (PWA) Support',
  }, {
    name: 'Router',
  },{
    name: 'Vuex',
  }, {
    name: 'CSS Pre-processors',
  }, {
    name: 'Linter / Formatter',
  }, {
    name: 'Unit Testing',
  }, {
    name: 'E2E Testing',
  }],
  pageSize: 9,
  validate: function(answer){
    if(answer.length < 1){
      return 'You must choose at least one topping.';
    }

    return true;
  }
}]).then(answers => {
  console.log(JSON.stringify(answers, null, '  '));
}).catch(error => {
  console.log(error);
})
```

### chalk

`chalk` 包的作用是修改控制台中字符串的样式，包括：

1. 字体样式(加粗、隐藏等)
2. 字体颜色
3. 背景颜色

使用

```javascript
const chalk = require('chalk');
console.log(chalk.red.bold.bgWhite('Hello World'));
```



### process

[progress ](https://www.npmjs.com/package/progress)是现在最常用的 `npm` 包用来渲染进度条。

```shell
npm install --save progress
```

使用

```javascript
var ProgressBar = require('progress');

var bar = new ProgressBar(':bar', { total: 10 });
var timer = setInterval(function () {
  bar.tick();
  if (bar.complete) {
    console.log('\ncomplete\n');
    clearInterval(timer);
  }
}, 100);
```



### ora

控制命令行loading样式

安装ora

```shell
npm install ora
```

使用

```javascript
import ora from 'ora';

const spinner = ora('Loading unicorns').start();

setTimeout(() => {
	spinner.color = 'yellow';
	spinner.text = 'Loading rainbows';
}, 1000);
```

### execa

execa是可以调用shell和本地外部程序的javascript封装。会启动子进程执行。支持多操作系统，包括windows。如果父进程退出，则生成的全部子进程都被杀死

安装

```shell
npm i execa --save
```

使用

```javascript
execa = require("execa")
execa("echo",["hello world"]).then(result => {
    console.log(result.stdout);
    //=> 'hello world'
});
execa("grep",["hello","index.js"]).then(result => {
    console.log(result.stdout);
}).catch(err => console.log(err));

execa.shell("ls",["a","l"]).then(r=>console.log(r.stdout));

(async () => {
	const {stdout} = await execa('echo', ['你好！']);
	console.log(stdout);
	//=> 'unicorns'
})();
```

### rimraf

nodejs中执行rm -rf命令



### npm-run-all

Npm-run-all这个工具是为了解决官方的 `npm run` 命令无法同时运行多个脚本的问题，它可以把诸如 `npm run clean && npm run build:css && npm run build:js && npm run build:html` 的一长串的命令通过 glob 语法简化成 `npm-run-all clean build:*` 这样精致小巧的模样

安装

```shell
npm install npm-run-all --save-dev
```

使用

```shell
## 顺序执行
## 依次执行三个任务，注意如果某个脚本退出时返回值为空值，那么后续脚本默认是不会执行的，你可以使用参数 --continue-on-error 来规避这种行为
$ npm-run-all clean lint build

## 并行执行
$ npm-run-all --parallel lint build

## 混合执行
## 首先按顺序执行 clean lint 两个脚本，然后同时执行 watch:html 和 watch:js 的任务
$ npm-run-all clean lint --parallel watch:html watch:js
```



### http(s)-proxy-agent

代理转发http终端

```shell
npm install http-proxy-agent
```

使用

```javascript
var url = require('url');
var http = require('http');
var HttpProxyAgent = require('http-proxy-agent');

// HTTP/HTTPS proxy to connect to
var proxy = process.env.http_proxy || 'http://168.63.76.32:3128';
console.log('using proxy server %j', proxy);

// HTTP endpoint for the proxy to connect to
var endpoint = process.argv[2] || 'http://nodejs.org/api/';
console.log('attempting to GET %j', endpoint);
var opts = url.parse(endpoint);

// create an instance of the `HttpProxyAgent` class with the proxy server information
var agent = new HttpProxyAgent(proxy);
opts.agent = agent;

http.get(opts, function (res) {
  console.log('"response" event!', res.headers);
  res.pipe(process.stdout);
});
```



### http-proxy-middleware包

http-proxy-middleware用于把请求转发到其他服务器的中间件

安装

```shell
npm install --save-dev http-proxy-middleware
```

使用

```javascript
import express from 'express'
import { createProxyMiddleware } from 'http-proxy-middleware';

app.use(
	'/api-metrics/*',
  createProxyMiddleware({
    target: '192.168.8.8:9090',
    pathRewrite: {
			'api-metrics': '/api/v1',
    },
    changeOrigin: true,
  })
)
```



### history fallback包



```javascript
import history from 'connect-history-api-fallback'
import express from 'express'

const app = express()

app.use(history())
```



### node-fetch

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



### unfetch

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

### prisma

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

引入

```javascript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()
```



### 文件包fs-extra

fs-extra继承了fs，所以不需要再使用原生fs模块

安装

```shell
npm install fs-extra
```

文件包可以替代原生的node fs模块，实现更强大的文件处理功能。

导入

```javascript
const fse = require('fs-extra')
```

异步拷贝文件

```javascript
// Async with promises:
fs.copy('/tmp/myfile', '/tmp/mynewfile')
  .then(() => console.log('success!'))
  .catch(err => console.error(err))

// Sync:
try {
  fs.copySync('/tmp/myfile', '/tmp/mynewfile')
  console.log('success!')
} catch (err) {
  console.error(err)
}
```

https://juejin.cn/post/6844903641594216455#heading-14

### portfinder

如果你使用 [vue-cli](https://link.zhihu.com/?target=https%3A//cli.vuejs.org/zh/) 创建过项目，一定会发现一个现象：同时启多个项目，并不会遇到端口占用的情况。理论上来说使用 **vue-cli** 创建的项目在使用 **npm run serve** 启动后会默认跑在 **8080** 端口。但在不停止第一个项目的情况下，同时启动多个项目，它们占用的端口会“自动”变成 8081、8082

这种功能是通过portfinder实现的

安装

```shell
$ npm install portfinder
```

使用

```javascript
var portfinder = require('portfinder');

portfinder.getPort(function (err, port) {
  //
  // `port` is guaranteed to be a free port
  // in this scope.
  //
});

portfinder.setBasePort(3000);    // default: 8000
portfinder.setHighestPort(3333); // default: 65535
```



### chokidar

node包，监听当前文件夹/文件变化

chokidar依赖node的fs包，但是相比于fs.watch和fs.watchFile功能更强，

安装

```shell
npm install chokidar
```

使用

```typescript
const watcher = chokidar.watch('file, dir, glob, or array', {
  ignored: /(^|[\/\\])\../, // ignore dotfiles
  persistent: true
});

watcher
  .on('add', path => log(`File ${path} has been added`))
  .on('change', path => log(`File ${path} has been changed`))
  .on('unlink', path => log(`File ${path} has been removed`));

// More possible events.
watcher
  .on('addDir', path => log(`Directory ${path} has been added`))
  .on('unlinkDir', path => log(`Directory ${path} has been removed`))
  .on('error', error => log(`Watcher error: ${error}`))
  .on('ready', () => log('Initial scan complete. Ready for changes'))
  .on('raw', (event, path, details) => { // internal
    log('Raw event info:', event, path, details);
  });

// 'add', 'addDir' and 'change' events also receive stat() results as second
// argument when available: https://nodejs.org/api/fs.html#fs_class_fs_stats
watcher.on('change', (path, stats) => {
  if (stats) console.log(`File ${path} changed size to ${stats.size}`);
});

// Watch new files.
watcher.add('new-file');
watcher.add(['new-file-2', 'new-file-3', '**/other-file*']);

// Get list of actual paths being watched on the filesystem
var watchedPaths = watcher.getWatched();

// Un-watch some files.
await watcher.unwatch('new-file*');

// Stop watching.
// The method is async!
watcher.close().then(() => console.log('closed'));
```



### youdao-node

使用有道云api进行翻译



### pino

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



### Sequelize

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



### 转码包

node默认支持utf8、base64、binary，如果要请求或处理GBK或者Gb2312页面或文件就需要转码

安装iconv-lite

```shell
npm install iconv-lite --save 
```

引入

```javascript
const iconv = require('iconv-lite')
```

在原来的输出语句中加入解码函数就可以

```javascript
console.log('stdout'+iconv.decode(data,'GBK'))
```



### Dotenv

dotenv可以使用env文件给node precess中添加变量

安装

```shell
npm install dotenv --save
```

创建一个env文件

```env
S3_BUCKET="YOURS3BUCKET"
SECRET_KEY="YOURSECRETKEYGOESHERE"
```

在js中使用环境变量

```javascript
import * as dotenv from 'dotenv' // see https://github.com/motdotla/dotenv#how-do-i-use-dotenv-with-import
dotenv.config()
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

### node-redis



### Graphql

安装依赖

```js
npm install apollo-server@2.13.1 graphql@14.6.0  type-graphql@0.17.6
```

引入

```js
import "reflect-metadata"
import {buildSchema,ObjectType,Field,ID,Resolver,Query} from "type-graphql";
import {ApolloServer} from "apollo-server";
```

后端定义schema和resolver

```js
@ObjectType()
class Post{
    @Field(type => ID)
    id: string;

    @Field()
    created: Data;

    @Field()
    content: String;
}

@Resolver(Post)
class PostResolver {
    @Query(returns => [Post])
    async posts(): Promise<Post[]>{
        return [
           {
              id:"0",
              created: new Date(),
              content:'aaa'
            },
            {
              id:"1",
              created: new Date(),
              content:'bbb'
            },
            {
              id:"2",
              created: new Date(),
              content:'ccc'
            },
        ]
    }
}
```

运行项目，在localhost:4444打开graphql的playground进行测试

#### grapnel-yoga

基于graphql的后端框架

安装

```shell
pnpm add graphql-yoga graphql
```

使用

```javascript
import { createSchema, createYoga } from 'graphql-yoga'
import { createServer } from 'node:http'

const yoga = createYoga({
  schema: createSchema({
    typeDefs: /* GraphQL */ `
      type Query {
        hello: String
      }
    `,
    resolvers: {
      Query: {
        hello: () => 'Hello from Yoga!'
      }
    }
  })
})

const server = createServer(yoga)

server.listen(4000, () => {
  console.info('Server is running on http://localhost:4000/graphql')
})
```



#### nexus

类型安全的graph schema

安装

```shell
npm install nexus graphql
```

使用

```javascript
import { queryType, stringArg, makeSchema } from 'nexus'
import { GraphQLServer } from 'graphql-yoga'

const Query = queryType({
  definition(t) {
    t.string('hello', {
      args: { name: stringArg() },
      resolve: (parent, { name }) => `Hello ${name || 'World'}!`,
    })
  },
})

const schema = makeSchema({
  types: [Query],
  outputs: {
    schema: __dirname + '/generated/schema.graphql',
    typegen: __dirname + '/generated/typings.ts',
  },
})

const server = new GraphQLServer({
  schema,
})

server.start(() => `Server is running on http://localhost:4000`)
```

Nexus-prisma



#### graphql-code-generator

安装

```shell
yarn add -D @graphql-codegen/cli
```

使用

```shell
yarn graphql-codegen init
```



### winston

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

### depcheck

检查项目中的依赖哪些是没有用的。哪些是使用的

```shell
npm install -g depcheck
```

或者直接npx命令

```shell
npx depcheck
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

### MD5加密包

Js-md5



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

## Node常见问题汇总



### npm ERR! Maximum call stack size exceeded

解决方法：全局更新npm

```node
npm install npm -g
```

### core-js

warning react-native > create-react-class > fbjs > core-js@1.2.7: core-js@<2.6.8 is no longer maintained. Please, upgrade to core-js@3 or at least to actual version of core-js@2

旧包不在维护，安装新包，自动卸载旧版本

```node
npm install --save core-js@^3
```

注意：警告可能是由于你所安装的新包在使用旧版本的依赖所导致的警告，但是如果不是你自己开发的，你不能更改包的源码和依赖项，所以这种情况忽略警告吧

## 学习资源

node问答：https://github.com/jimuyouyou/node-interview-questions

https://javascript.ruanyifeng.com/

https://markpop.github.io/2014/10/29/NodeJs%E6%95%99%E7%A8%8B/

node包讲解：https://github.com/chyingp/nodejs-learning-guide

