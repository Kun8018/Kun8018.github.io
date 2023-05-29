---
title: Javascript开发（十九）
date: 2021-01-18 21:40:33
categories: IT
tags:
    - IT，Web,Node
toc: true
thumbnail: http://cdn.kunkunzhang.top/javascript.png
---

​     第十九篇主要讲常用的js库.主要偏向于JavaScript的库和前端库

<!--more-->

## Protobufjs

在js中使用protobuf这种数据结构。protobuf.js是基于ByteBuffer.js的Protocol Buffers纯JavaScript实现，主要功能是解析.proto文件，构建Message类，编码解码。

安装

```shell
npm install protobufjs --save
```

protobuf.js 依赖 long.js、bytebuffer.js

创建.proto文件

```shell
vim msg.proto
```

编辑

```protobuf
syntax = "proto3";

package ns;

message Login {
    string name = 1;
    string pwd = 2;
}
message Address{
  required string province = 1;
  required string city = 2;
  required string country = 3;
}
```

使用

```javascript
let pbroot = require("protobufjs").Root;
let json = require("msg.json");
let root = pbroot.fromJSON(json);

let Message = root.lookupType("ns.Address");
```

加载

```javascript
import { load } from "protobufjs"; // respectively "./node_modules/protobufjs"

load("awesome.proto", function(err, root) {
  if (err)
    throw err;

  // example code
  const AwesomeMessage = root.lookupType("awesomepackage.AwesomeMessage");

  let message = AwesomeMessage.create({ awesomeField: "hello" });
  console.log(`message = ${JSON.stringify(message)}`);

  let buffer = AwesomeMessage.encode(message).finish();
  console.log(`buffer = ${Array.prototype.toString.call(buffer)}`);

  let decoded = AwesomeMessage.decode(buffer);
  console.log(`decoded = ${JSON.stringify(decoded)}`);
});
```

命令行工具

```javascript
var pbjs = require("protobufjs/cli/pbjs"); // or require("protobufjs/cli").pbjs / .pbts

pbjs.main([ "--target", "json-module", "path/to/myproto.proto" ], function(err, output) {
    if (err)
        throw err;
    // do something with output
});
```

## deepmerge

融合对象

安装

```javascript
npm install deepmerge
```

使用

```javascript
const merge = require('deepmerge')

const x = {
    foo: { bar: 3 },
    array: [{
        does: 'work',
        too: [ 1, 2, 3 ]
    }]
}
 
const y = {
    foo: { baz: 4 },
    quux: 5,
    array: [{
        does: 'work',
        too: [ 4, 5, 6 ]
    }, {
        really: 'yes'
    }]
}
 
const output = {
    foo: {
        bar: 3,
        baz: 4
    },
    array: [{
        does: 'work',
        too: [ 1, 2, 3 ]
    }, {
        does: 'work',
        too: [ 4, 5, 6 ]
    }, {
        really: 'yes'
    }],
    quux: 5
}
 
merge(x, y) // => output

const foobar = { foo: { bar: 3 } }
const foobaz = { foo: { baz: 4 } }
const bar = { bar: 'yay!' }
 
merge.all([ foobar, foobaz, bar ]) // => { foo: { bar: 3, baz: 4 }, bar: 'yay!' }
```

## pluralize

安装

```shell
npm install pluralize --save
```

将任何单词复数和单数化

```javascript
pluralize('test') //=> "tests"
pluralize('test', 0) //=> "tests"
pluralize('test', 1) //=> "test"
pluralize('test', 5) //=> "tests"
pluralize('test', 1, true) //=> "1 test"
pluralize('test', 5, true) //=> "5 tests"
pluralize('蘋果', 2, true) //=> "2 蘋果"
 
// Example of new plural rule:
pluralize.plural('regex') //=> "regexes"
pluralize.addPluralRule(/gex$/i, 'gexii')
pluralize.plural('regex') //=> "regexii"
 
// Example of new singular rule:
pluralize.singular('singles') //=> "single"
pluralize.addSingularRule(/singles$/i, 'singular')
pluralize.singular('singles') //=> "singular"
 
// Example of new irregular rule, e.g. "I" -> "we":
pluralize.plural('irregular') //=> "irregulars"
pluralize.addIrregularRule('irregular', 'regular')
pluralize.plural('irregular') //=> "regular"
 
// Example of uncountable rule (rules without singular/plural in context):
pluralize.plural('paper') //=> "papers"
pluralize.addUncountableRule('paper')
pluralize.plural('paper') //=> "paper"
 
// Example of asking whether a word looks singular or plural:
pluralize.isPlural('test') //=> false
pluralize.isSingular('test') //=> true
```



## reconnecting-websocket

websocket重连包

```shell
npm install --save reconnecting-websocket
```

使用

```javascript
import ReconnectingWebSocket from 'reconnecting-websocket';
 
const urls = ['ws://my.site.com', 'ws://your.site.com', 'ws://their.site.com'];
let urlIndex = 0;
 
// round robin url provider
const urlProvider = () => urls[urlIndex++ % urls.length];
 
const rws = new ReconnectingWebSocket(urlProvider);
```

也可以这样

```javascript
import ReconnectingWebSocket from 'reconnecting-websocket';
import WS from 'ws';
 
const options = {
    WebSocket: WS, // custom WebSocket constructor
    connectionTimeout: 1000,
    maxRetries: 10,
};
const rws = new ReconnectingWebSocket('ws://my.site.com', [], options);
```

## octokit

使用javascript管理github restful api

octokitjs由GitHub维护 https://github.com/octokit/octokit.js

使用personal access token进行身份验证.首先，从 `octokit` 导入 `Octokit`。 然后，在创建 `Octokit` 实例时传递 personal access token

```javascript
import { Octokit } from "octokit";

const octokit = new Octokit({ 
  auth: 'YOUR-TOKEN',
});
```

如果要代表组织或其他用户使用 API，GitHub 建议使用 GitHub App。

导入 `App`，而不是从 `octokit` 导入 `Octokit`。 在以下示例中，将 `APP_ID` 替换为对应用 ID 的引用。 将 `PRIVATE_KEY` 替换为对应用私钥的引用。 将 `INSTALLATION_ID` 替换为要代表其进行身份验证的应用的安装 ID。 可以在应用的设置页面上找到应用的 ID 并生成私钥。

```javascript
import { App } from "octokit";

const app = new App({
  appId: APP_ID,
  privateKey: PRIVATE_KEY,
});

const octokit = await app.getInstallationOctokit(INSTALLATION_ID);
```

再github action中进行身份验证

如果要在 GitHub Actions 工作流中使用 API，则 GitHub 建议使用内置 `GITHUB_TOKEN` 进行身份验证，而不是创建令牌。 可以使用 `permissions` 密钥向 `GITHUB_TOKEN` 授予权限

如果使用 `run` 关键字在 GitHub Actions 工作流中执行 JavaScript 脚本，则可以将 `GITHUB_TOKEN` 的值存储为环境变量。 脚本可以作为 `process.env.VARIABLE_NAME` 访问环境变量

```yaml
- name: Run script
  env:
    TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    node .github/actions-scripts/use-the-api.mjs
```

工作流运行的脚本使用 `process.env.TOKEN` 进行身份验证

```javascript
import { Octokit } from "octokit";

const octokit = new Octokit({ 
  auth: process.env.TOKEN,
});
```

Octokit 支持多种请求方式。 如果知道终结点的 HTTP 谓词和路径，则可以使用 `request` 方法发出请求。 如果要利用 IDE 中的自动完成和键入功能，可以使用 `rest` 方法。 对于分页终结点，可以使用 `paginate` 方法来请求多页数据

若要使用 `request` 方法发出请求，请将 HTTP 方法和路径作为第一个参数传递。 将对象中的任何主体、查询或路径参数作为第二个参数传递。 例如，向 `/repos/{owner}/{repo}/issues` 发出 `GET` 请求并传递 `owner`、`repo` 和 `per_page` 参数

```javascript
await octokit.request("GET /repos/{owner}/{repo}/issues", {
  owner: "github",
  repo: "docs",
  per_page: 2
});
```

`request` 方法会自动传递 `Accept: application/vnd.github+json` 标头。 若要传递其他标头或不同的 `Accept` 标头，请将 `headers` 属性添加到作为第二个参数传递的对象。 `headers` 属性的值是将标头名称作为键并将标头值作为值的对象。 例如，发送值为 `text/plain`的 `content-type` 标头和值为 `2022-11-28` 的 `x-github-api-version` 标头

```javascript
await octokit.request("POST /markdown/raw", {
  text: "Hello **world**",
  headers: {
    "content-type": "text/plain",
    "x-github-api-version": "2022-11-28",
  },
});
```

每个 REST API 终结点在 Octokit 中都有一个关联的 `rest` 终结点方法。 为方便起见，这些方法通常会在 IDE 中自动完成。 可以将任何参数作为对象传递给该方法

```javascript
await octokit.rest.issues.listForRepo({
  owner: "github",
  repo: "docs",
  per_page: 2
});
```

参考：https://docs.github.com/zh/rest/guides/scripting-with-the-rest-api-and-javascript?apiVersion=2022-11-28

https://docs.github.com/zh/rest/guides/rendering-data-as-graphs?apiVersion=2022-11-28

