---
title: NodeJs开发（三）
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

### yalc

管理自己的npm包。比npm link更方便

安装

```shell
yarn global add yalc
```



### qnm

查找当前node_modules中某一模块的使用

安装

```shell
npm i -global qnm
```

使用

```shell
qnm [module]
```

### cli-table

在cli工具终端生成table

安装

```shell
npm install cli-table3
```

使用

```javascript
var Table = require('cli-table3');

// instantiate
var table = new Table({
    head: ['TH 1 label', 'TH 2 label']
  , colWidths: [100, 200]
});

// table is an Array, so you can `push`, `unshift`, `splice` and friends
table.push(
    ['First value', 'Second value']
  , ['First value', 'Second value']
);

console.log(table.toString());
```

### cac

安装

```shell
yarn add cac
```

使用

```javascript
// examples/basic-usage.js
const cli = require('cac')()

cli.option('--type [type]', 'Choose a project type', {
  default: 'node',
})
cli.option('--name <name>', 'Provide your name')

cli.command('lint [...files]', 'Lint files').action((files, options) => {
  console.log(files, options)
})

// Display help message when `-h` or `--help` appears
cli.help()
// Display version number when `-v` or `--version` appears
// It's also used in help message
cli.version('0.0.0')

cli.parse()
```

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
    .name("intl helper")
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

### signale

命令行高亮。https://github.com/klaudiosinani/signale/blob/master/docs/readme.zh_CN.md#interactive

Signale 的核心是可扩展和可配置的，可将其用于日志记录、状态报告以及处理其他 Node 模块和应用的输出渲染方式。

使用

```javascript
const signale = require('signale');

signale.success('Operation successful');
signale.debug('Hello', 'from', 'L59');
signale.pending('Write release notes for %s', '1.2.0');
signale.fatal(new Error('Unable to acquire lock'));
signale.watch('Recursively watching build directory...');
signale.complete({prefix: '[task]', message: 'Fix issue #59', suffix: '(@klauscfhq)'});
```

### colorette

控制台字体颜色和样式

```javascript
import { blue, bold, underline } from "colorette"

console.log(
  blue("I'm blue"),
  bold(blue("da ba dee")),
  underline(bold(blue("da ba daa")))
)
```

### consola

```typescript
// ESM
import { consola, createConsola } from "consola";

// CommonJS
const { consola, createConsola } = require("consola");

consola.info("Using consola 3.0.0");
consola.start("Building project...");
consola.warn("A new version of consola is available: 3.0.1");
consola.success("Project built!");
consola.error(new Error("This is an example error. Everything is fine!"));
consola.box("I am a simple box");
await consola.prompt("Deploy to the production?", {
  type: "confirm",
});
```





### madge



### dependency-cruiser

https://github.com/sverweij/dependency-cruiser



## 功能模块

### through2

https://github.com/rvagg/through2

基于node的stream流包了一层，避免使用问题

```javascript
fs.createReadStream('ex.txt')
  .pipe(through2(function (chunk, enc, callback) {
    for (let i = 0; i < chunk.length; i++)
      if (chunk[i] == 97)
        chunk[i] = 122 // swap 'a' for 'z'

    this.push(chunk)

    callback()
   }))
  .pipe(fs.createWriteStream('out.txt'))
  .on('finish', () => doSomethingSpecial())
```



### tsoa

基于Node生成标准的Open Api接口

安装

```shell
yarn add tsoa express
yarn add -D typescript @types/node @types/express
```

配置tsoa.json

```json
{
  "entryFile": "src/app.ts",
  "noImplicitAdditionalProperties": "throw-on-extras",
  "controllerPathGlobs": ["src/**/*Controller.ts"],
  "spec": {
    "outputDirectory": "build",
    "specVersion": 3
  },
  "routes": {
    "routesDir": "build"
  }
}
```

定义controller

```typescript
import {
  Body,
  Controller,
  Get,
  Path,
  Post,
  Query,
  Route,
  SuccessResponse,
} from "tsoa";
import { User } from "./user";
import { UsersService, UserCreationParams } from "./usersService";

@Route("users")
export class UsersController extends Controller {
  @Get("{userId}")
  public async getUser(
    @Path() userId: number,
    @Query() name?: string
  ): Promise<User> {
    return new UsersService().get(userId, name);
  }

  @SuccessResponse("201", "Created") // Custom success response
  @Post()
  public async createUser(
    @Body() requestBody: UserCreationParams
  ): Promise<void> {
    this.setStatus(201); // set return status 201
    new UsersService().create(requestBody);
    return;
  }
}
```



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



### archiver

有时候我们需要将一些文件压缩打包成zip格式或tar格式的压缩包，也有可能需要将目录进行打包。在[Node](https://so.csdn.net/so/search?q=Node&spm=1001.2101.3001.7020).js中就可以用到`archiver`这个第三方包来进行操作。

```shell
npm install archiver --save
```

使用

```javascript
// 第一步，导入必要的模块
const fs = require('fs');
const archiver = require('archiver');

// 第二步，创建可写流来写入数据
const output = fs.createWriteStream(__dirname + "/hello.zip");// 将压缩包保存到当前项目的目录下，并且压缩包名为test.zip
const archive = archiver('zip', {zlib: {level: 9}});// 设置压缩等级

// 第三步，建立管道连接
archive.pipe(output);

// 第四步，压缩指定文件
var stream = fs.createReadStream(__dirname + "/hello.txt");// 读取当前目录下的hello.txt
archive.append(stream, {name: 'hello.txt'});

// 第五步，完成压缩
archive.finalize();
```



### change-case

操作字符串大小写

```javascript
import * as changeCase from "change-case";

changeCase.camelCase("TEST_VALUE"); //=> "testValue"
```

https://github.com/blakeembrey/change-case/tree/main/packages/change-case



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

### minimist

```javascript
var argv = require('minimist')(process.argv.slice(2));
console.log(argv);

// shell
$ node example/parse.js -x 3 -y 4 -n5 -abc --beep=boop foo bar baz
{ _: [ 'foo', 'bar', 'baz' ],
  x: 3,
  y: 4,
  n: 5,
  a: true,
  b: true,
  c: true,
  beep: 'boop' }
```





### dangerjs

做一些危险操作时进行提示

```shell
yarn add danger --dev
```

创建`dangerfile.js`或者`dangerfile.ts`

```javascript
import {warn, message, danger} from "danger"

const modifiedMD = danger.git.modified_files.join("- ")
message("Changed Files in this PR: \n - " + modifiedMD)

import { danger } from "danger"

const docs = danger.git.fileMatch("**/*.md")
const app = danger.git.fileMatch("src/**/*.ts")
const tests = danger.git.fileMatch("*/__tests__/*")

if (docs.edited) {
  message("Thanks - We :heart: our [documentarians](http://www.writethedocs.org/)!")
}

if (app.modified && !tests.modified) {
  warn("You have app changes without tests.")
}
```



### consola

https://github.com/unjs/consola

在命令行输出

```javascript
// ESM
import { consola, createConsola } from "consola";

// CommonJS
const { consola, createConsola } = require("consola");

consola.info("Using consola 3.0.0");
consola.start("Building project...");
consola.warn("A new version of consola is available: 3.0.1");
consola.success("Project built!");
consola.error(new Error("This is an example error. Everything is fine!"));
consola.box("I am a simple box");
await consola.prompt("Deploy to the production?", {
  type: "confirm",
});
```



### execa

execa是可以调用shell和本地外部程序的javascript封装。会启动子进程执行。支持多操作系统，包括windows。如果父进程退出，则生成的全部子进程都被杀死

[`execa`](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fexeca) 为 `child_process` 模块提供了一个包装器，以便于使用

`execa` 相对于内置的 Node.js `child_process` 模块有几个好处。

首先，`execa` 公开了一个基于 promise 的 API。这意味着我们可以在代码中使用 `async/await`，而不需要像使用异步 `child_process` 模块方法那样创建回调函数。如果我们需要它，还有一个 `execaSync` 方法可以同步运行命令。

`execa` 还可以方便地转义并引用我们传递给它的任何命令参数。例如，如果我们传递一个带有空格或引号的字符串，`execa` 将为我们处理转义。

程序在输出的末尾添加一行新行是很常见的。这有利于命令行的可读性，但在脚本上下文中没有帮助，因此默认情况下，`execa` 会自动为我们删除这些新行。

如果执行脚本（`node`）的进程因任何原因死掉，我们可能不希望子进程挂起。`execa` 会自动为我们清理子进程，确保我们不会出现 “僵尸” 进程。

使用 `execa` 的另一个好处是，它支持在 Windows 上使用 Node.js 运行子进程。它使用了流行的 [`cross-spawn`](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fcross-spawn) 包，该包可以解决 Node.js 的已知问题

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

### tRPC

构建类型安全的接口代码

安装

```shell
npm install @trpc/server @trpc/client @trpc/react-query @trpc/next @tanstack/react-query
```

使用

```typescript
import { initTRPC } from '@trpc/server';
 
const t = initTRPC.create();
 
const router = t.router;
const publicProcedure = t.procedure;
 
const appRouter = router({});
 
// Export type router type signature,
// NOT the router itself.
export type AppRouter = typeof appRouter;
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

配置参数

```javascript
// proxy 中间件的选择项
var options = {
      target: 'http://www.example.org', // 目标服务器 host
      changeOrigin: true,               // 默认false，是否需要改变原始主机头为目标URL
      ws: true,                         // 是否代理websockets
      pathRewrite: {
          '^/api/old-path' : '/api/new-path',     // 重写请求，比如我们源访问的是api/old-path，那么请求会被解析为/api/new-path
          '^/api/remove/path' : '/path'           // 同上
      },
      router: {
          // 如果请求主机 == 'dev.localhost:3000',
          // 重写目标服务器 'http://www.example.org' 为 'http://localhost:8000'
          'dev.localhost:3000' : 'http://localhost:8000'
      }
      onProxyReq: fixRequestBody,
      onError(err) {
        MiscLogger.warn('Proxy upgrade center.', 'upgrade center', err);
      },
      proxyTimeout: 0 // 超时时间,
      timeout: 0,
      secure: false,
  };
```

http-proxy-middleware 对于post请求不会转发body，需要写一个处理函数

```javascript
// restream parsed body before proxying
var restream = function(proxyReq, req, res, options) {
    if (req.body) {
        let bodyData = JSON.stringify(req.body);
        // incase if content-type is application/x-www-form-urlencoded -> we need to change to application/json
        proxyReq.setHeader('Content-Type','application/json');
        proxyReq.setHeader('Content-Length', Buffer.byteLength(bodyData));
        // stream the content
        proxyReq.write(bodyData);
    }
}

var apiProxy = proxyMiddleware('/api', {
    target: 'https://xxx.xxx.xxx.xxx',
    onProxyReq: restream
});

```



### http-errors



### redbird

node反向代理包，支持http2

安装

```shell
npm install redbird
```

使用

```javascript
var proxy = require('redbird')({port: 80});

// OPTIONAL: Setup your proxy but disable the X-Forwarded-For header
var proxy = require('redbird')({port: 80, xfwd: false});

// Route to any global ip
proxy.register("optimalbits.com", "http://167.23.42.67:8000");

// Route to any local ip, for example from docker containers.
proxy.register("example.com", "http://172.17.42.1:8001");

// Route from hostnames as well as paths
proxy.register("example.com/static", "http://172.17.42.1:8002");
proxy.register("example.com/media", "http://172.17.42.1:8003");

// Subdomains, paths, everything just works as expected
proxy.register("abc.example.com", "http://172.17.42.4:8080");
proxy.register("abc.example.com/media", "http://172.17.42.5:8080");

// Route to any href including a target path
proxy.register("foobar.example.com", "http://172.17.42.6:8080/foobar");

// You can also enable load balancing by registering the same hostname with different
// target hosts. The requests will be evenly balanced using a Round-Robin scheme.
proxy.register("balance.me", "http://172.17.40.6:8080");
proxy.register("balance.me", "http://172.17.41.6:8080");
proxy.register("balance.me", "http://172.17.42.6:8080");
proxy.register("balance.me", "http://172.17.43.6:8080");

// You can unregister routes as well
proxy.register("temporary.com", "http://172.17.45.1:8004");
proxy.unregister("temporary.com", "http://172.17.45.1:8004");

// LetsEncrypt support
// With Redbird you can get zero conf and automatic SSL certificates for your domains
redbird.register('example.com', 'http://172.60.80.2:8082', {
  ssl: {
    letsencrypt: {
      email: 'john@example.com', // Domain owner/admin email
      production: true, // WARNING: Only use this flag when the proxy is verified to work correctly to avoid being banned!
    }
  }
});

//
// LetsEncrypt requires a minimal web server for handling the challenges, this is by default on port 3000
// it can be configured when initiating the proxy. This web server is only used by Redbird internally so most of the time
// you  do not need to do anything special other than avoid having other web services in the same host running
// on the same port.

//
// HTTP2 Support using LetsEncrypt for the certificates
//
var proxy = require('redbird')({
  port: 80, // http port is needed for LetsEncrypt challenge during request / renewal. Also enables automatic http->https redirection for registered https routes.
  letsencrypt: {
    path: __dirname + '/certs',
    port: 9999 // LetsEncrypt minimal web server port for handling challenges. Routed 80->9999, no need to open 9999 in firewall. Default 3000 if not defined.
  },
  ssl: {
    http2: true,
    port: 443, // SSL port used to serve registered https routes with LetsEncrypt certificate.
  }
});
```



### history fallback包



```javascript
import history from 'connect-history-api-fallback'
import express from 'express'

const app = express()

app.use(history())
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
import * as dotenv from 'dotenv' 
// see https://github.com/motdotla/dotenv#how-do-i-use-dotenv-with-import
dotenv.config()
```

### nock

模拟http请求响应，更多地应用于node/前端的单元测试中

比如有一个http请求函数

```javascript
async function getUser(id) {
  const response = await fetch(`/api/users/${id}`);

  // User does not exist
  if (response.status === 404) return null;
  // Some other error occurred
  if (response.status > 400) {
    throw new Error(`Unable to fetch user #${id}`);
  }

  const { firstName, lastName } = await response.json();
  return {
    firstName,
    lastName,
    fullName: `${firstName} ${lastName}`
  };
}

// 添加对应的测试代码
it('should properly decorate the fullName', async () => {
  nock('http://localhost')
    .get('/api/users/123')
    .reply(200, { firstName: 'John', lastName: 'Doe });

  const user = await getUser(123);
  expect(user).toEqual({
    firstName: 'John',
    lastName: 'Doe,
    fullName: 'John Doe'
  });
});  

it('should return null if the user does not exist', async () => {
  nock('http://localhost')
    .get('/api/users/1337')
    .reply(404);

  const user = await getUser(1337);
  expect(user).toBe(null);
});  

it('should return null when an error occurs', async () => {
  nock('http://localhost')
    .get('/api/users/42')
    .reply(404);

  const userPromise = getUser(42);
  expect(userPromise).rejects.toThrow('Unable to fetch user #42');
});
```



### supertest



### glob

全局匹配

安装

```shell
npm i glob
```

使用

```javascript
// load using import
import { glob, globSync, globStream, globStreamSync, Glob } from 'glob'
// or using commonjs, that's fine, too
const {
  glob,
  globSync,
  globStream,
  globStreamSync,
  Glob,
} = require('glob')

// the main glob() and globSync() resolve/return array of filenames

// all js files, but don't look in node_modules
const jsfiles = await glob('**/*.js', { ignore: 'node_modules/**' })

// pass in a signal to cancel the glob walk
const stopAfter100ms = await glob('**/*.css', {
  signal: AbortSignal.timeout(100),
})

// multiple patterns supported as well
const images = await glob(['css/*.{png,jpeg}', 'public/*.{png,jpeg}'])

// but of course you can do that with the glob pattern also
// the sync function is the same, just returns a string[] instead
// of Promise<string[]>
const imagesAlt = globSync('{css,public}/*.{png,jpeg}')

// you can also stream them, this is a Minipass stream
const filesStream = globStream(['**/*.dat', 'logs/**/*.log'])

// construct a Glob object if you wanna do it that way, which
// allows for much faster walks if you have to look in the same
// folder multiple times.
const g = new Glob('**/foo')
// glob objects are async iterators, can also do globIterate() or
// g.iterate(), same deal
for await (const file of g) {
  console.log('found a foo file:', file)
}
// pass a glob as the glob options to reuse its settings and caches
const g2 = new Glob('**/bar', g)
// sync iteration works as well
for (const file of g2) {
  console.log('found a bar file:', file)
}

// you can also pass withFileTypes: true to get Path objects
// these are like a Dirent, but with some more added powers
// check out http://npm.im/path-scurry for more info on their API
const g3 = new Glob('**/baz/**', { withFileTypes: true })
g3.stream().on('data', path => {
  console.log(
    'got a path object',
    path.fullpath(),
    path.isDirectory(),
    path.readdirSync().map(e => e.name)
  )
})

// if you use stat:true and withFileTypes, you can sort results
// by things like modified time, filter by permission mode, etc.
// All Stats fields will be available in that case. Slightly
// slower, though.
// For example:
const results = await glob('**', { stat: true, withFileTypes: true })

const timeSortedFiles = results
  .sort((a, b) => a.mtimeMS - b.mtimeMS)
  .map(path => path.fullpath())

const groupReadableFiles = results
  .filter(path => path.mode & 0o040)
  .map(path => path.fullpath())

// custom ignores can be done like this, for example by saying
// you'll ignore all markdown files, and all folders named 'docs'
const customIgnoreResults = await glob('**', {
  ignore: {
    ignored: p => /\.md$/.test(p.name),
    childrenIgnored: p => p.isNamed('docs'),
  },
})

// another fun use case, only return files with the same name as
// their parent folder, plus either `.ts` or `.js`
const folderNamedModules = await glob('**/*.{ts,js}', {
  ignore: {
    ignored: p => {
      const pp = p.parent
      return !(p.isNamed(pp.name + '.ts') || p.isNamed(pp.name + '.js'))
    },
  },
})

// find all files edited in the last hour, to do this, we ignore
// all of them that are more than an hour old
const newFiles = await glob('**', {
  // need stat so we have mtime
  stat: true,
  // only want the files, not the dirs
  nodir: true,
  ignore: {
    ignored: p => {
      return new Date() - p.mtime > 60 * 60 * 1000
    },
    // could add similar childrenIgnored here as well, but
    // directory mtime is inconsistent across platforms, so
    // probably better not to, unless you know the system
    // tracks this reliably.
  },
})
```
