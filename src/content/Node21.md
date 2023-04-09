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



