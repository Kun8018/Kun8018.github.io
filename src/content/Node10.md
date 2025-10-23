---
title: JavaScript开发（七）-TS开发（二）
date: 2021-01-21 21:40:33
categories: IT
tags:
    - IT，Web
toc: true
thumbnail: http://cdn.kunkunzhang.top/typescript.jpg
---

万万没想到会来到第七篇，第七篇主要对TypeScript的应用作一些说明和示例，算进阶篇。

<!--more-->

## JSdoc

JSDoc 是一个针对 JavaScript 的 API 文档生成器，类似于 Java 中的 Javadoc 或者 PHP 中的 phpDocumentor；在源代码中添加指定格式的注释，JSDoc 工具便会自动扫描你的代码并生成一个 API 文档网站（在指定目录下生成相关的网页文件）

生成 API 文档只是一方面，其更主要的贡献在于对代码注释格式进行了规范化

安装

```shell
npm install -g jsdoc
```

在js文件中写入对应的函数和注释

```javascript
/**
 * Returns the sum of a and b
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function sum(a, b) {
    return a + b;
}
/**
 * Return the diff fo a and b
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function diff(a, b) {
    return a - b;
}
```

然后就是在当前目录执行以下命令

```shell
jsdoc doc.js
```

最后就会在当前目录下生成一个名为 `out` 的目录（也可以另外指定），里面有包含接口文档的html页面

常用写法：

@description：也可写作 `@desc`，描述当前注释对象的详细信息

@file：注释写在文件开头，用于描述当前文件的相关信息

@class 描述一个 `class` 类

@returns 或者写作 `@return`，描述函数的返回值的信息；

@param 与 `@arg`, `@argument` 含义相同，描述一个函数的参数信息；

@function 与 `@func`, `@method` 含义相同，描述一个函数；

@todo 描述接下来准备做的事情；

@copyright 描述当前文件的版权相关信息

@file 注释写在文件开头，用于描述当前文件的相关信息

### better-doc

安装

```shell
yarn add --dev jsdoc better-docs
```

编写jsdoc.json

```json
{
  "tags": {
      "allowUnknownTags": true
  },
  "source": {
      "include": ["./src"]
  },
  "plugins": [
      "plugins/markdown",
      "better-docs/component"
  ],
  "opts": {
      "encoding": "utf8",
      "destination": "docs/",
      "recurse": true,
      "verbose": true,
      "template": "./node_modules/better-docs"
  },
  "templates": {
      "better-docs": {
          "name": "My React components"
      }
  }
}
```

编写组件

```react

import React from 'react'
import PropTypes from 'prop-types'

/**
 * Some documented component
 * 
 * @component
 * @example
 * const size = 12
 * const text = 'I am documented!'
 * return (
 *   <Documented size={size} text={text} />
 * )
 */
const Documented = (props) => {
  const { text, size } = props
  return (
    <p style={{ fontSize: size, padding: 10, border: '1px solid #ccc'}}>{text}</p>
  )
}

Documented.propTypes = {
  /**
   * Text is a text :)
   */
  text: PropTypes.string.isRequired,
  /**
   * Font size
   */
  size: PropTypes.number,
}

Documented.defaultProps = {
  text: 'Hello World',
  size: 12,
}

export default Documented
```

生成文档

```shell
yarn docs
```

https://wojciechkrysiak.medium.com/document-reactjs-components-with-preview-by-using-jsdoc-70d39d2cc777

## TS包

### DefinitelyTyped



### typeDoc

安装

```shell
yarn add -D typedoc
```

TypeDoc将TypeScript源代码中的注释转换为呈现的HTML文档或JSON模型；TypeScript项目的文档生成器。

使用TypeDoc的前提是使用typescript开发

在package.json中编写执行脚本命令

```json
  "scripts": {
    "doc": "npx typedoc --tsconfig typedoc.json"
  }
```

编写typedoc.json

```json
{
  // 输入选项 - 入口文件/文件夹
  "entryPoints": [
    "src/modules"
  ],
  // 输入选项 - 排除文件/文件夹
  "exclude": [
    "node_moudles",
    "src/modules/WebViewer/**/*.ts"
  ],
  // 输出选项 - 将软件包package.json中的版本version添加到项目名称中
  "includeVersion": true,
  // 输出选项 - 设置将在模板标题中使用的项目的名称。默认为package.json中的name
  "name": "测试title",
  // 输出选项 - 不在页面末尾打印TypeDoc链接
  "hideGenerator": true,
  // 输出选项 - 禁用描述代码位置
  "disableSources": true
}
```

运行命令npm run doc，在输出docs文件夹查看

### tiny-invariant

通过使用`tiny-invariant`，您可以在断言不正确时在运行时抛出异常，并在 `Sentry` 或任何其他提供程序上捕获该异常。这将增加您对代码的信心并检测任何不一致之处。

```typescript
import invariant from 'tiny-invariant'; 
interface User {                       
    name?: string;                           
    email?: string;                      
}                                       
const u: User = { name: 'Joe', email: 'joe@no-reply.com'};  
invariant(u.name, 'Name should not be null for this scenario')
// ✅ Compiles without the need of `!`                        
console.log(u.name.toUpperCase());
```

代码更有弹性，我们可以清除很多冗余`if`语句。

这个包是最小的，如果你愿意，你可以选择实现你自己的`invariant`功能。

### type-fest

TypeScript 最强大的功能之一是映射类型。

让我们看一个例子。TypeScript 原生的`Optional`方法非常有限。它只是让我们将所有属性标记为可选。它缺乏细节

```typescript
import { type MarkOptional, type OptionalKeys } from 'ts-essentials';

interface User {
    name: string;
    email: string;
}
// 原生方法：所有的属性都是可选的
    type PartialUser = Partial<User>;
// Result:  
   // {
    // name?: string;
    // email?: string;
} 

// 💪 仅支持选中的属性是可选的
type PartialUserEmail = MarkOptional<User, 'email'>;

// Result:
// {
    // name: string
    // email?: string;
   }
   
 // 💪 从类型中获取可选的key
type PartialKeys = OptionalKeys<PartialUserEmail>; 
// Result:
// email   
```



### ts-essentials

shell

```shell
npm install --save-dev ts-essentials
```

使用

```typescript
import { type MarkOptional, type OptionalKeys } from 'ts-essentials';

interface User {
    name: string;
    email: string;
}
// 原生方法：所有的属性都是可选的
    type PartialUser = Partial<User>;
// Result:  
   // {
    // name?: string;
    // email?: string;
// } 

// 💪 仅支持选中的属性是可选的
type PartialUserEmail = MarkOptional<User, 'email'>;

// Result:
// {
    // name: string
    // email?: string;
//   }
   
 // 💪 从类型中获取可选的key
type PartialKeys = OptionalKeys<PartialUserEmail>; 
// Result:
// email   
```



### typebox

typebox让json的schema具有像ts一样的类型规范

```shell
npm install @sinclair/typebox --save
```

使用

```javascript
import { Static, Type } from '@sinclair/typebox'

const T = Type.Object({                              // const T = {
  x: Type.Number(),                                  //   type: 'object',
  y: Type.Number(),                                  //   required: ['x', 'y', 'z'],
  z: Type.Number()                                   //   properties: {
})                                                   //     x: { type: 'number' },
                                                     //     y: { type: 'number' },
                                                     //     z: { type: 'number' }
                                                     //   }
                                                     // }

type T = Static<typeof T>                            // type T = {
                                                     //   x: number,
                                                     //   y: number,
                                                     //   z: number
                                                     // }
```



### tsup

编译ts为js

```shell
npm i tsup -D
```

直接使用

```shell
tsup src/index.ts src/cli.ts
```

#### 用tsup打包typescript

`Tsup` 可以快速打包 `typescript` 库，无需任何配置，并且基于[esbuild](https://link.juejin.cn/?target=https%3A%2F%2Fesbuild.github.io%2F)进行打包，打包 `ts` 文件速度毫秒级，方便又高效

tsup同时支持tsup.config.ts、tsup.config.js、tsup.config.cjs、tsup.config.json四个文件

```javascript
import { defineConfig } from 'tsup'

export default defineConfig({
  // 入口文件 或者可以使用 entryPoints 底层是 esbuild
  entry: ['src/index.ts'],
  
  // 打包类型  支持以下几种 'cjs' | 'esm' | 'iife'
  format: ["cjs", "esm"],

  // 生成类型文件 xxx.d.ts
  dts: false,

  // 代码分割 默认esm模式支持 如果cjs需要代码分割的话就需要配置为 true
  splitting: false,

  // sourcemap 
  sourcemap: false,

  // 每次打包先删除dist
  clean: true,
});
```

### unbuild

js打包工具

https://github.com/unjs/unbuild?tab=readme-ov-file



### ts-morgh



### ts-ast-viewer

查看ts的编译语法树： https://ts-ast-viewer.com/



### type-challenges



### ts-boolbelt

ts工具类型

```shell
npm install ts-toolbelt --save
```

使用

```typescript
import {
  Any,
  Boolean,
  Class,
  Function,
  Iteration,
  List,
  Number,
  Object,
  String,
  Union,
} from "ts-toolbelt";

import {Object} from "ts-toolbelt"
// Check the docs below for more

// Merge two `object` together
type merge = Object.Merge<{name: string}, {age?: number}>
// {name: string, age?: number}

// Make a field of an `object` optional
type optional = Object.Optional<{id: number, name: string}, "name">
// {id: number, name?: string}
```



### simplyTypeds



### utility-types

一些工具类型

### effect

ts包

原有代码

```typescript
const divide = (a: number, b: number): number => {
  if (b === 0) {
    throw new Error("Cannot divide by zero")
  }
  return a / b
}
```

使用effect

```typescript
import { Effect } from "effect"
 
const divide = (a: number, b: number): Effect.Effect<number, Error, never> =>
  b === 0
    ? Effect.fail(new Error("Cannot divide by zero"))
    : Effect.succeed(a / b)
```

或者

```typescript
import { Effect } from "effect"
 
const result1 = Effect.runSyncExit(Effect.succeed(1))
console.log(result1)
/*
Output:
{
  _id: "Exit",
  _tag: "Success",
  value: 1
}
*/
 
const result2 = Effect.runSyncExit(Effect.fail("my error"))
console.log(result2)
/*
Output:
{
  _id: "Exit",
  _tag: "Failure",
  cause: {
    _id: "Cause",
    _tag: "Fail",
    failure: "my error"
  }
}
*/

import { Effect, Console, Schedule, Fiber } from "effect"
 
const program = Effect.repeat(
  Console.log("running..."),
  Schedule.spaced("200 millis")
)
 
const fiber = Effect.runFork(program)
 
setTimeout(() => {
  Effect.runFork(Fiber.interrupt(fiber))
}, 500)
```



### tsconfig-path

安装

```shell
npm install --save-dev tsconfig-paths
```

设置tsconfig.json的路径

```javascript
const tsConfig = require("./tsconfig.json");
const tsConfigPaths = require("tsconfig-paths");

const baseUrl = "./"; // Either absolute or relative path. If relative it's resolved to current working directory.
const cleanup = tsConfigPaths.register({
  baseUrl,
  paths: tsConfig.compilerOptions.paths,
});

// When path registration is no longer needed
cleanup();
```

### ttypecript

GitHub: ttypescript

安装

```shell
npm i ttypescript -D
```

Tsconfig.json

```json
{
  "compilerOptions": {
    "plugins": [
      { "transform", "transformer-module" },
    ]
  }
}
```

Parcel

parcel plugin

```shell
npm i parcel-plugin-ttypescript
```

webpack

```javascript
	{
        test: /\.(ts|tsx)$/,
        loader: require.resolve('awesome-typescript-loader'),
        // or
        loader: require.resolve('ts-loader'),
        options: {
            compiler: 'ttypescript'
        }
  }
```

rollup

```javascript
// rollup.config.js
import ttypescript from 'ttypescript'
import tsPlugin from 'rollup-plugin-typescript2'

export default {
    // ...
    plugins: [
        // ...
        tsPlugin({
            typescript: ttypescript
        })
    ]
}
```

Jest ts-jest

```javascript
module.exports = {
  // [...]
  globals: {
    'ts-jest': {
      compiler: 'ttypescript'
    }
  }
};
```



### ts-unused-exports

检查ts中没有用到的export变量

```shell
npm install --save-dev ts-unused-exports
```

运行

```shell
ts-unused-exports path/to/tsconfig.json [file1.ts ...] [options]
```

https://github.com/pzavolinsky/ts-unused-exports

### unimported

检查当前代码系统中没有被引用的文件

```shell
$ npx unimported
```

https://www.npmjs.com/package/unimported



### ts-prune

发现项目中未使用的export

```shell
# npm
npm install ts-prune --save-dev
# yarn
yarn add -D ts-prune
```

在package.json中添加

```json
{
  "scripts": {
    "find-deadcode": "ts-prune"
  }
}
```



### depcheck

检查依赖的使用情况

```shell
npm install -g depcheck
```

使用

```shell
npx depcheck
```



### knip

检查代码中没有用的变量

```shell
npm install -D knip
```

在package.json中添加命令行

```shell
{
  "scripts": {
    "knip": "knip"
  }
}
```

然后运行knip，检查无用代码的报告

https://github.com/webpro/knip

### jscpd

检查代码中的重复代码

https://github.com/kucherenko/jscpd

安装

```shell
npm install -g jscpd
```

使用

```shell
npx jscpd /path/to/source
```



## ts简单辨析

### never与void、any、unknown的区别：

任意未明确声明类型并切无法推导出类型的值都默认为any类型，any是检测弱，兼容性问题解决方案。

当一个函数返回空值时，它的返回值为 void 类型，但是，当一个函数永不返回时（或者总是抛出错误），它的返回值为 never 类型。

void 类型可以被赋值（在 strictNullChecking 为 false 时），但是除了 never 本身以外，其他任何类型不能赋值给 never。

unknown相对于any，任意类型都可以赋值给unknow，但是不可对其进行任何访问操作（仅仅为类型安全，any操作访问也安全）

```typescript
let val:any

let val_void:void = val;

let val_undefined:undefined = val;
let val_null:null = val;
let val_number:number = val;

let val: unknown;
let val__unknown:unknown = val;
// 报错:不能将类型“unknown”分配给类型“string”
let val_string:string = val;
// 报错:不能将类型“unknown”分配给类型“number”
let val_number:number = val;
// 报错:不能将类型“unknown”分配给类型“boolean”
```

但是unkown可以通过别的方式来缩小类型

```typescript
declare const maybe: unknown;

if (maybe === true) {
  // TypeScript knows that maybe is a boolean now
  const aBoolean: boolean = maybe;
  // So, it cannot be a string
  const aString: string = maybe;
Type 'boolean' is not assignable to type 'string'.
}
 
if (typeof maybe === "string") {
  // TypeScript knows that maybe is a string
  const aString: string = maybe;
  // So, it cannot be a boolean
  const aBoolean: boolean = maybe;
Type 'string' is not assignable to type 'boolean'.
}
```



### 数字枚举与字符串枚举的区别

我们可以使用字符串枚举或者数值枚举，

```typescript
enum NoYes {
  No,
  Yes,
}

enum NoYes {
  No = 0,
  Yes = 1,
}

enum NoYes {
  No = 'No',
  Yes = 'Yes',
}
```



### type与interface的区别

type与interface都用于描述一个对象或函数, 两者都可以实现继承

interface 可以 extends， 但 type 是不允许 extends 和 implement 的，但是 type 可以通过交叉类型 实现 interface 的 extend 行为，并且两者并不是相互独立的，也就是说 interface 可以 extends type, type 也可以 与 interface 类型交叉 。

```typescript
//interface使用extends继承，type可以通过交叉类型继承
interface Name { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}

type Name = { 
  name: string; 
}
type User = Name & { age: number  };
//interface与type混合extends与交叉
type Name = { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}

interface Name { 
  name: string; 
}
type User = Name & { 
  age: number; 
}
```

不同点：

interface可以声明合并，type不行

```typescript
interface User {
  name: string
  age: number
}

interface User {
  sex: string
}

/*
User 接口为 {
  name: string
  age: number
  sex: string 
}
*/
```

对象、函数两者都适用，type可以声明基本类型别名，联合类型，元组等类型，还可以使用 typeof 获取实例的类型进行赋值，interface不行

```typescript
// 基本类型别名
type Name = string

// 联合类型
interface Dog {
    wong();
}
interface Cat {
    miao();
}

type Pet = Dog | Cat

// 具体定义数组每个位置的类型
type PetList = [Dog, Pet]

// 获取类型进行赋值
let div = document.createElement('div');
type B = typeof div
```

type 支持计算属性，生成映射类型,；interface 不支持。

type 能使用 in 关键字生成映射类型, 内部使用了 for .. in。 具有三个部分：类型变量 K，它会依次绑定到每个属性。
字符串字面量联合的 Keys，它包含了要迭代的属性名的集合。
属性的结果类型。

```typescript
type Keys = "firstname" | "surname"

type DudeType = {
  [key in Keys]: string
}

const test: DudeType = {
  firstname: "Pawel",
  surname: "Grzybek"
}

// 报错
//interface DudeType2 {
//  [key in keys]: string
//}
```

一般来说，如果不清楚什么时候用interface/type，能用 interface 实现，就用 interface , 如果不能就用 type 。

### 元组与数组的区别

数组的类型在[]前面, 元组的类型在[]内部。数组的类型规定数组全部的类型，而元组内部的类型是逐个指定的，也就是元组需要规定元素数量

```typescript
let arr:(number | string)[] = ['s',3,'a'];
let arr:any[] = ['a',2,true];

// let arr:[number] = [2,3,4];
 let arr:[number] = [2]; // 这个时候才是对的！
 let arr:[string,number] = ['a',1];
// 报错:不能将类型“string”分配给类型“number”
// let arr:[string,number] = [1,'d'];
// any元组也需要规定元素数量
let arr:[any,any,any] = ['s',2,true];
```

### 索引签名和工具类型Record的区别

其实Record工具类型的本质就是索引签名，不同之处只是用法，仅仅需要继承就可以了，不需要再写一遍

```typescript
interface inf{
    name:string;
    age:number;
    [k:string]:any;
}

interface inf extends Record<string,any>{
    name:string;
    age:number;
}

let obj:inf = {
    name:'yiye',
    age:33,
    city:'foshan'
}
```



## 编译 

### npm run tsc

如果一个目录下存在一个`tsconfig.json`文件，那么它意味着这个目录是TypeScript项目的根目录。 `tsconfig.json`文件中指定了用来编译这个项目的根文件和编译选项。 一个项目可以通过以下方式之一来编译

使用tsconfig.json

- 不带任何输入文件的情况下调用`tsc`，编译器会从当前目录开始去查找`tsconfig.json`文件，逐级向上搜索父目录。
- 不带任何输入文件的情况下调用`tsc`，且使用命令行参数`--project`（或`-p`）指定一个包含`tsconfig.json`文件的目录。

当命令行上指定了输入文件时，`tsconfig.json`文件会被忽略。

配置关键字

`"compilerOptions"`可以被忽略，这时编译器会使用默认值。

`"files"`指定一个包含相对或绝对文件路径的列表。 `"include"`和`"exclude"`属性指定一个文件glob匹配模式列表。 支持的glob通配符有：

- `*` 匹配0或多个字符（不包括目录分隔符）
- `?` 匹配一个任意字符（不包括目录分隔符）
- `**/` 递归匹配任意子目录

如果一个glob模式里的某部分只包含`*`或`.*`，那么仅有支持的文件扩展名类型被包含在内（比如默认`.ts`，`.tsx`，和`.d.ts`， 如果 `allowJs`设置能`true`还包含`.js`和`.jsx`）。

如果`"files"`和`"include"`都没有被指定，编译器默认包含当前目录和子目录下所有的TypeScript文件（`.ts`, `.d.ts` 和 `.tsx`），排除在`"exclude"`里指定的文件。JS文件（`.js`和`.jsx`）也被包含进来如果`allowJs`被设置成`true`。 如果指定了 `"files"`或`"include"`，编译器会将它们结合一并包含进来。 使用 `"outDir"`指定的目录下的文件永远会被编译器排除，除非你明确地使用`"files"`将其包含进来（这时就算用`exclude`指定也没用）。

使用`"include"`引入的文件可以使用`"exclude"`属性过滤。 然而，通过 `"files"`属性明确指定的文件却总是会被包含在内，不管`"exclude"`如何设置。 如果没有特殊指定， `"exclude"`默认情况下会排除`node_modules`，`bower_components`，`jspm_packages`和`<outDir>`目录。

任何被`"files"`或`"include"`指定的文件所引用的文件也会被包含进来。 `A.ts`引用了`B.ts`，因此`B.ts`不能被排除，除非引用它的`A.ts`在`"exclude"`列表中。

需要注意编译器不会去引入那些可能做为输出的文件；比如，假设我们包含了`index.ts`，那么`index.d.ts`和`index.js`会被排除在外。 通常来讲，不推荐只有扩展名的不同来区分同目录下的文件。

`tsconfig.json`文件可以是个空文件，那么所有默认的文件（如上面所述）都会以默认配置选项编译。

在命令行上指定的编译选项会覆盖在`tsconfig.json`文件里的相应选项。

`@types`，`typeRoots`和`types`

默认所有*可见的*"`@types`"包会在编译过程中被包含进来。 `node_modules/@types`文件夹下以及它们子文件夹下的所有包都是*可见的*； 也就是说， `./node_modules/@types/`，`../node_modules/@types/`和`../../node_modules/@types/`等等。

如果指定了`typeRoots`，*只有*`typeRoots`下面的包才会被包含进来

```json
{
   "compilerOptions": {
       "typeRoots" : ["./typings"]
   }
}
```

这个配置文件会包含*所有*`./typings`下面的包，而不包含`./node_modules/@types`里面的包。

如果指定了`types`，只有被列出来的包才会被包含进来。

```json
{
   "compilerOptions": {
        "types" : ["node", "lodash", "express"]
   }
}
```

这个`tsconfig.json`文件将*仅会*包含 `./node_modules/@types/node`，`./node_modules/@types/lodash`和`./node_modules/@types/express`。/@types/。 `node_modules/@types/*`里面的其它包不会被引入进来。

指定`"types": []`来禁用自动引入`@types`包。

extends

`tsconfig.json`文件可以利用`extends`属性从另一个配置文件里继承配置。

`extends`是`tsconfig.json`文件里的顶级属性（与`compilerOptions`，`files`，`include`，和`exclude`一样）。 `extends`的值是一个字符串，包含指向另一个要继承文件的路径。

在原文件里的配置先被加载，然后被来至继承文件里的配置重写。 如果发现循环引用，则会报错。

来至所继承配置文件的`files`，`include`和`exclude`*覆盖*源配置文件的属性。

@ts-nocheck @ts-check

### babel编译

最开始 typescript 代码只有自带的 tyepscript compiler（tsc）能编译，编译不同版本的 typescript 代码需要用不同版本的 tsc，通过配置 tsconfig.json 来指定如何编译。

但是 tsc 编译 ts 代码为 js 是有问题的：

tsc 不支持很多还在草案阶段的语法，这些语法都是通过 babel 插件来支持的，所以很多项目的工具链是用 tsc 编译一遍 ts 代码，之后再由 babel 编译一遍。这样编译链路长，而且生成的代码也不够精简。

所以，typescript 找 babel 团队合作，在 babel7 中支持了 typescript 的编译，可以通过插件来指定 ts 语法的编译。比如 api 中是这样用：

```javascript
const parser = require('@babel/parser');

parser.parse(sourceCode, {
    plugins: ['typescript']
});
```

babel编译ts的流程：

- parser: 把源码 parse 成 ast
- traverse：遍历 ast，生成作用域信息和 path，调用各种插件来对 ast 进行转换
- generator：把转换以后的 ast 打印成目标代码，并生成 sourcemap

 typescript compiler 的编译流程是这样的：

- scanner + parser： 分词和组装 ast，从源码到 ast 的过程

- binder + checker： 生成作用域信息，进行类型推导和检查

- transform：对经过类型检查之后的 ast 进行转换

- emitter： 打印 ast 成目标代码，生成 sourcemap 和类型声明文件（根据配置）

能不能基于 babel 的插件在 traverse 的时候实现 checker 呢？

答案是不可以。

因为 tsc 的类型检查是需要拿到整个工程的类型信息，需要做类型的引入、多个文件的 namespace、enum、interface 等的合并，而 babel 是单个文件编译的，不会解析其他文件的信息。所以做不到和 tsc 一样的类型检查。

**一个是在编译过程中解析多个文件，一个是编译过程只针对单个文件，流程上的不同，导致 babel 无法做 tsc 的类型检查。**

其实 babel 只是能够 parse ts 代码成 ast，不会做类型检查，会直接把类型信息去掉，然后打印成目标代码。

这导致了有一些 ts 语法是 babel 所不支持的：

- const enum 不支持。const enum 是在编译期间把 enum 的引用替换成具体的值，需要解析类型信息，而 babel 并不会解析，所以不支持。可以用相应的插件把 const enum 转成 enum。
- namespace 部分支持。不支持 namespace 的跨文件合并，不支持导出非 const 的值。这也是因为 babel 不会解析类型信息且是单文件编译。

上面两种两个是因为编译方式的不同导致的不支持。

- export = import = 这种 ts 特有语法不支持，可以通过插件转为 esm
- 如果开启了 jsx 编译，那么 <string> aa 这种类型断言不支持，通过 aa as string 来替代。这是因为这两种语法有冲突，在两个语法插件(jsx、typescript)里，解决冲突的方式就是用 as 代替。

这四种就是 babel 不支持的 ts 语法，其实影响并不大，这几个特性不用就好了。

**结论：babel 不能编译所有 typescript 代码，但是除了 namespace 的两个特性外，其余的都可以做编译。**

Babel编译的优势：

1.产物体积更小

这与配置编译目标有关

在tsc中配置编译目标如下：

在 compilerOptions 里面配置 target，target 设置目标语言版本

```javascript
{
    compilerOptions: {
        target: "es5" // es3、es2015
    }
}
```

在入口文件里面引入 core-js.

```javascript
import 'core-js';
```

而在babel7中，

配置编译目标：

在 preset-env 里面指定 targets，直接指定目标运行环境（浏览器、node）版本，或者指定 query 字符串，由 browserslist 查出具体的版本。

引入polyfill也是在preset-env 中配置，指定 polyfill 用哪个（corejs2 还是 corejs3），如何引入（entry 在入口引入 ，usage 每个模块单独引入用到的）

```javascript
{
    presets: [
        [
            "@babel/preset-env",
            {
                // targets: {
                //    chrome: 45
                // }
                targets: "last 1 version,> 1%,not dead",
                corejs: 3,
                useBuiltIns: 'usage'
            }
        ]
    ]
}
```

**先根据 targets 查出支持的目标环境的版本，再根据目标环境的版本来从所有特性中过滤支持的，剩下的就是不支持的特性。只对这些特性做转换和 polyfill 即可**

而且 babel 还可以通过 @babel/plugin-transform-runtime 来把全局的 corejs 的 import 转成模块化引入的方式。

显然，用 babel 编译 typescript 从产物上看有两个优点：

- 能够做更精准的按需编译和 polyfill，产物体积更小
- 能够通过插件来把 polyfill 变成模块化的引入，不污染全局环境

2.支持的语言特性：

typescript 默认支持很多 es 的特性，但是不支持还在草案阶段的特性，babel 的 preset-env 支持所有标准特性，还可以通过 proposal 来支持更多还未进入标准的特性。

```javascript
{
    plugins: ['@babel/proposal-xxx'],
    presets: ['@babel/presets-env', {...}]
}
```

3.编译速度

tsc 会在编译过程中进行类型检查，类型检查需要综合多个文件的类型信息，要对 AST 做类型推导，比较耗时，而 babel 不做类型检查，所以编译速度会快很多。

从编译速度来看， babel 胜。

总之，从编译产物大小（主要）、支持的语言特性、编译速度来看，babel 完胜。

### 结合

babel 可以编译生成更小的产物，有更快的编译速度和更多的特性支持，所以我们选择用 babel 编译 typescript 代码。但是类型检查也是需要的，可以在 npm scripts 中配一个命令：

```javascript
{
    "scripts": {
        "typeCheck": "tsc --noEmit"
    }
}
```

这样在需要进行类型检查的时候单独执行一下 npm run typeCheck 就行了，但最好在 git commit 的 hook 里（通过 husky 配置）再执行一次强制的类型检查。



## WebAssembly-AssemblyScript

AssemblyScript定义了一个TypeScript的子集，意在帮助TS背景的同学，通过标准的JavaScript API来完成到wasm的编译，从而消除语言的差异，让程序猿可以快乐的编码。

AssemblyScript项目主要分为三个子项目：

- [AssemblyScript](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FAssemblyScript%2Fassemblyscript)：将TypeScript转化为wasm的主程序
- [binaryen.js](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FAssemblyScript%2Fbinaryen.js)：AssemblyScript主程序转化为wasm的底层实现，依托于[binaryen](https://link.juejin.cn?target=http%3A%2F%2Fgithub.com%2FWebAssembly%2Fbinaryen)库，是对binaryen的TypeScript封装。
- [wast.js](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FAssemblyScript%2Fwabt.js)：AssemblyScript主程序转化为wasm的底层实现，依托于[wast](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FWebAssembly%2Fwabt)库，是对wast的TypeScript封装。

首先安装assemblyScript

```shell
git clone https://github.com/AssemblyScript/assemblyscript.git
cd assemblyscript
npm install
npm link
```

在node的项目中添加wasm命令

```json
 "scripts": {
    "build": "npm run build:untouched && npm run build:optimized",
    "build:untouched": "asc assembly/module.ts -t dist/module.untouched.wat -b dist/module.untouched.wasm --validate --sourceMap --measure",
    "build:optimized": "asc assembly/module.ts -t dist/module.optimized.wat -b dist/module.optimized.wasm --validate --sourceMap --measure --optimize"
 }
```



```sh
npm install --save @assemblyscript/loader
npm install --save-dev assemblyscript
```

初始化node-modules

```shell
npx asinit .
```

构建

```shell
npm run asbuild
```





## js调用wasm

对于JavaScript调用wasm，一般采用如下步骤：

1. 加载wasm的字节码。
2. 将获取到字节码后转换成 ArrayBuffer，只有这种结构才能被正确编译。编译时会对上述ArrayBuffer进行验证。验证通过方可编译。编译后会通过Promise resolve一个 WebAssembly.Module。
3. 在获取到 module 后需要通过 WebAssembly.Instance API 去同步的实例化 module。
4. 上述第2、3步骤可以用instaniate 异步API等价代替。
5. 之后就可以和像使用JavaScript模块一样调用了。



## 学习资源

typescript手册：https://www.typescriptlang.org/docs/handbook/

深入理解typescript：https://jkchao.github.io/typescript-book-chinese/

typescript入门教程：https://ts.xcatliu.com/basics/type-of-function.html

ts中文手册：https://typescript.bootcss.com/

