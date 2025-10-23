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

## 工具泛型

### Key/Keyof

`keyof` 可以用来取得一个对象接口的所有 `key` 值.in 则可以遍历枚举类型

```typescript
interface Foo {
  name: string;
  age: number
}
type T = keyof Foo // -> "name" | "age"

type Keys = "a" | "b"
type Obj =  {
  [p in Keys]: any
} // -> { a: any, b: any }
```

`keyof` 产生联合类型, `in` 则可以遍历枚举类型, 所以他们经常一起使用

keyof配合泛型使用

```typescript
interface IProps<T> {
    tableProps: Pick<TableProps<T>, keyof TableProps<T>>;
}
```

keyof配合typeof使用

```typescript
const defaultProps = {
    name: '张三',
    age: 18
}

const selfKey: keyof typeof defaultProps = 'name'; // right
const selfKey: keyof typeof defaultProps = 'age'; // right
const selfKey: keyof typeof defaultProps = 'other'; // error
```

### partial

Partial 作用是将传入的属性变为可选项.
首先我们需要理解两个关键字 `keyof` 和 `in`, `keyof` 可以用来取得一个对象接口的所有 `key` 值.

```typescript
type Partial<T> = { [P in keyof T]?: T[P] };
```

### required

Required 的作用是将传入的属性变为必选项, 源码如下

```typescript
type Required<T> = { [P in keyof T]-?: T[P] };
```

### readonly(只读)

typescript类型系统允许在一个接口中使用readonly来标记属性，也就是只读的方式，不可预期的改变是很糟糕的。

可以在接口、类中用此方法定义

```typescript
type Readonly<T> = { readonly [P in keyof T]: T[P] };
```

### Mutable

将 T 的所有属性的 readonly 移除,

```typescript
type Mutable<T> = {
  -readonly [P in keyof T]: T[P]
}
```

### record

将 K 中所有的属性的值转化为 T 类型

```typescript
type Record<K extends keyof any, T> = { [P in K]: T };
```

### pick

从 T 中取出 一系列 K 的属性

```typescript
type Pick<T, K extends keyof T> = { [P in K]: T[P] };
```

### omit

用之前的 Pick 和 Exclude 进行组合, 实现忽略对象某些属性功能, 

```typescript
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>

// 使用
type Foo = Omit<{name: string, age: number}, 'name'> // -> { age: number }
```

### exclude

Exclude 的作用是从 T 中找出 U 中没有的元素, 换种更加贴近语义的说法其实就是从T 中排除 U

```typescript
type T = Exclude<1 | 2, 1 | 3> // -> 2
```

### extract

Extract 的作用是提取出 T 包含在 U 中的元素, 换种更加贴近语义的说法就是从 T 中提取出 U

```typescript
type Extract<T, U> = T extends U ? T : never;
```

### NonNullable<T>

排除T为null或者undefined的情况

```typescript
type T = NonNullable<string | string[] | null | undefined>; //string | string[] 
```



### infer关键字与Returntype

官方类型库中提供了ReturnType可以获取方法的返回类型，实例

```typescript
type stringPromiseReturnType = ReturnType<typeof stringPromise>;
```

Returntype的定义如下

```typescript
type ReturnType<T extends (...args:any) => any >= T extends(...args:any)=> infer R?R:any;
```

利用infer反解promise中的泛型

```typescript 
type PromiseType<T> = (args:any[]) => Promise<T>;
type UnPromisify<T> = T extends PromiseType<infer U>? U:never
```

也可以解析函数入参的类型

```typescript
type VariadicFn<A extends 
```



```typescript
type FunctionReturnType<T> = T extends (...args: any[]) => infer R ? R : T;

type Foo = FunctionReturnType<() => void>;  // void
type Bar = FunctionReturnType<(name: string) => string>; // string
type Buz = FunctionReturnType<(name: string, other: string) => boolean>; // boolean
```



## 函数重载与方法重载

### 联合类型的使用

有一个函数在处理两种类型的数据时使用同一套逻辑，所以你希望复用，比如

```typescript
function getDataByID(ID: string):DataType {
  // TODO: 通过单个ID获取单个用户信息
}

function getDataByIDS(IDS: string[]):DataTypep[] {
  // TODO: 通过多个ID获取多个用户信息
}

// 合并变成下面的函数
function getData(ID: string | string[]):DataType|DataTypep[] {
  // TODO: 通过单个或者许多ID获取单个或者多个用户信息
  // 此时函数体变为
  let data;
  if(Array.isArray(ID)) {
    return data as DataType[]
  }
  return data as DataType
}
```

此时调用函数时，ts会返回两种类型，因为需要再进行类型断言才能进行下一步处理

```typescript
const data = getData(['xxx']) as DataType[]
```

重载可以实现精准返回类型

```typescript
function getData(ID: string): DataType;
function getData(ID: string[]): DataType[];
function getData(ID: string | string[]):DataType|DataTypep[] {
  // TODO: 通过单个或者许多ID获取单个或者多个用户信息
}
```

js 因为是动态类型，本身不需要支持重载，直接对参数进行类型判断即可，但是ts为了保证类型安全，支持了函数签名的类型重载

如在JavaScript中：

```javascript
function add(x, y) {
  return x + y;
}

add(1, 2); // 3
add("1", "2"); //"12"
```

由于 TypeScript 是 JavaScript 的超集，因此以上的代码可以直接在 TypeScript 中使用，但当 TypeScript 编译器开启 `noImplicitAny` 的配置项时，以上代码会提示以下错误信息：

```typescript
Parameter 'x' implicitly has an 'any' type.
Parameter 'y' implicitly has an 'any' type.
```

该信息告诉我们参数 x 和参数 y 隐式具有 `any` 类型。为了解决这个问题，我们可以为参数设置一个类型。因为我们希望 `add` 函数同时支持 string 和 number 类型，因此我们可以定义一个 `string | number` 联合类型，然后在函数中使用

```typescript
type Combinable = string | number;

function add(a: Combinable, b: Combinable) {
  if (typeof a === 'string' || typeof b === 'string') {
    return a.toString() + b.toString();
  }
  return a + b;
}
```

但是此时如果在结果中使用字符串函数会报错

```typescript
const result = add('semlinker', ' kakuqo');
result.split(' ');

// Property 'split' does not exist on type 'Combinable'.
// Property 'split' does not exist on type 'number'.
```

`Combinable` 和 `number` 类型的对象上并不存在 `split` 属性。这时我们就可以利用 TypeScript 提供的函数重载。

函数重载或方法重载是使用相同名称和不同参数数量或类型创建多个方法的一种能力。

```typescript
function add(a: number, b: number): number;
function add(a: string, b: string): string;
function add(a: string, b: number): string;
function add(a: number, b: string): string;
function add(a: Combinable, b: Combinable) {
  // type Combinable = string | number;
  if (typeof a === 'string' || typeof b === 'string') {
    return a.toString() + b.toString();
  }
  return a + b;
}
```

方法重载是指在同一个类中方法同名，参数不同（参数类型不同、参数个数不同或参数个数相同时参数的先后顺序不同），调用时根据实参的形式，选择与它匹配的方法执行操作的一种技术。所以类中成员方法满足重载的条件是：在同一个类中，方法名相同且参数列表不同。

```typescript
class Calculator {
  add(a: number, b: number): number;
  add(a: string, b: string): string;
  add(a: string, b: number): string;
  add(a: number, b: string): string;
  add(a: Combinable, b: Combinable) {
  if (typeof a === 'string' || typeof b === 'string') {
    return a.toString() + b.toString();
  }
    return a + b;
  }
}

const calculator = new Calculator();
const result = calculator.add('Semlinker', ' Kakuqo');
```

当 TypeScript 编译器处理函数重载时，它会查找重载列表，尝试使用第一个重载定义。 如果匹配的话就使用这个。 因此，在定义重载的时候，一定要把最精确的定义放在最前面。另外在 Calculator 类中，`add(a: Combinable, b: Combinable){ }` 并不是重载列表的一部分，因此对于 add 成员方法来说，我们只定义了四个重载方法。

### declare关键字实现重载

```typescript
declare function pickCard(x: { suit: string; card: number }[]): number;
declare function pickCard(x: number): number;
```

### 总结

**函数重载的主要意义：**

1. **灵活的参数处理**：
   - 允许一个函数接受不同类型或不同数量的参数。
   - 例如，一个处理用户事件的函数可能需要接受字符串类型的事件名（如点击事件）或对象类型的事件数据（如得分事件），函数重载可以精确地定义这些不同的情况。
2. **更精确的类型检查**：
   - 通过定义不同的重载签名，TypeScript 编译器可以在调用函数时，根据实际传入的参数，进行严格的类型匹配和检查。
   - 这有助于在编译阶段捕获潜在的类型错误，防止在运行时出现意外行为。
3. **提高代码的可读性和可维护性**：
   - 重载使得函数的接口更加清晰，明确了函数在不同输入下的行为和返回值类型。
   - 用户在使用函数时，能够更直观地理解如何正确调用函数，从而提升开发效率和代码的可维护性





## 声明语句与声明文件、声明合并

假如我们想使用第三方库 jQuery，一种常见的方式是在 html 中通过 `<script>` 标签引入 jQuery，然后就可以使用全局变量 `$` 或 `jQuery` 了。

但是在 ts 中，编译器并不知道 `$` 或 `jQuery` 是什么东西[1](https://github.com/xcatliu/typescript-tutorial/tree/master/examples/declaration-files/01-jquery)：

这时，我们需要使用 `declare var` 来定义它的类型

通常我们会把声明语句放到一个单独的文件（`jQuery.d.ts`）中，这就是声明文件。声明文件必需以 `.d.ts` 为后缀。一般来说，ts 会解析项目中所有的 `*.ts` 文件，当然也包含以 `.d.ts` 结尾的文件。所以当我们将 `jQuery.d.ts` 放到项目中时，其他所有 `*.ts` 文件就都可以获得 `jQuery` 的类型定义了。

假如仍然无法解析，那么可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `jQuery.d.ts` 文件。

TS可以在编译时自动生成.d.ts文件，只需要在tsconfig.json配置文件中开启即可

```json
{
  "compilerOptions": {
    "declaration": true
  }
}
```

一般只有三种情况需要手动定义声明文件：

1.通过script标签引入第三方库

2.使用的第三方npm包没有提供声明文件

3.自己团队内比较优秀的js库或者插件，为了提升开发体验

声明文件只是对类型的定义，不能赋值

声明文件有全局的类型声明和局部的类型声明两种。

`.d.ts` 里面，没有使用 `import`、`export`，默认是全局的。全局的类型声明在项目的任何地方都可以直接使用，无需引入。但是要特别注意类型命名冲突。在 `.d.ts` 文件中，只要有一个类型定义使用了 `export`，那这个声明文件就会变成模块化的。想要使用里面的类型定义，需要先通过 `import` 的方式将其引入才行。

以react的ts声明文件为例

```typescript
// @types/react/index.d.ts
 
export = React;
export as namespace React;

declare namespace React {
    type ReactType<P = any> = ElementType<P>;
    ...
}
```

导出的都是以一个以原库同名的命名空间。引用库时相当于也把它的类型声明也引进来了，当然在使用的时候，会自动提示

对于没有提供声明文件的npm包，可以创建一个types目录，来管理自己写的声明文件，同时在配置文件tsconfig.json中的paths和baseUrl配置

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "baseUrl": "./",
    "paths": {"*":["types/*"]}
  }
}
```

npm包的声明文件主要有以下几种语法

```typescript
export const/let
export namespace
export default
export = 
```

### 复用公共的接口/类型

对于那些同一个类型，可能会在项目中的其它地方用到的，复用类型是一个不错的选择

全局的类型：直接放在最外层的 `global.d.ts` 或者 `typing.d.ts`中，不使用 `export` 导出

模块级的类型。在每个功能模块下，定义一个 `index.d.ts` 文件。在这个文件中写需要复用的类型定义。再通过 `export` 的方式将其导出。在需要使用类型的地方，再通过 `import` 导入使用。

- `antd` 在每个独立的模块文件夹下面多了一个`index.d.ts`，见 `node_modules/antd/lib` 下面
- `react-bulma-components 1.1k` 在每个独立的模块文件夹下面多了一个`index.d.ts`
- `swiper` - `27.6k star`，公共的单独放于 `types` 文件夹里面，其它的和文件同级，添加 `文件名.d.ts` 文件

```typescript
// typing.d.ts 全局的

interface IObject {
    [name: string]: any;
}

declare type IResponse = {
    total: number;
    list: IObject[];
}

// index.d.ts 局部的
export type IRecord = {
    id: number;
    name: string;
    hasBrother: boolean;
}

export type INewRecord = IRecord & {
    num: number;
}

// person.tsx
// IRecord, INewRecord 需要引入才能使用
import { IRecord, INewRecord } from 'index.d';

// IResponse 直接使用
const res: IResponse = await api.get('****');

const newList: INewRecord = IResponse.list.map((item: IRecord) => ({ ...item, num: Math.random() }))
```



## 类型声明空间与变量声明空间

ts从「类型声明空间」到「变量声明空间」，最基础的，我们可以通过类型注解，为变量提供类型约束

https://zhuanlan.zhihu.com/p/401138248

## 命名空间

在 JavaScript 使用命名空间时， 这有一个常用的、方便的语法：

```javascript
(function(something) {
  something.foo = 123;
})(something || (something = {}));

console.log(something);
// { foo: 123 }

(function(something) {
  something.bar = 456;
})(something || (something = {}));

console.log(something); // { foo: 123, bar: 456 }
```

在确保创建的变量不会泄漏至全局命名空间时，这种方式在 JavaScript 中很常见。当基于文件模块使用时，你无须担心这点，但是该模式仍然适用于一组函数的逻辑分组。因此 TypeScript 提供了 `namespace` 关键字来描述这种分组，

```typescript
namespace Utility {
  export function log(msg) {
    console.log(msg);
  }
  export function error(msg) {
    console.log(msg);
  }
}

// usage
Utility.log('Call me');
Utility.error('maybe');
```

值得注意的一点是，命名空间是支持嵌套的。因此，你可以做一些类似于在 `Utility` 命名空间下嵌套一个命名空间 `Messaging` 的事情。



## 一些特殊用法

### typeof与类型别名混用

```typescript
const defaultProps = {
    name: '张三',
    age: 18,
    score: 722,
}

type IProps = typeof defaultProps & {
    favorite: [string];
}

等价于：

type IProps = {
    name: string;
    age: number;
    score: number;
    favorite: [string];
}
```

### promise类型

在异步操作时常常会使用async函数，函数调用时会return一个promise对象，可以使用then方法添加回调函数

```typescript
interface IResponse<T> {
  message: string,
  result: T,
  success: boolean,
}
  
async function getResult (): Promise<IResponse<number[]>> {
  return {
    message: 'success',
    result: [1,2,3],
    success: true
  }
}

getResult()
	.then(result => {
		console.log(result.result)
	})
```



### 动态分配属性

在 JavaScript 中，我们可以很容易地为对象动态分配属性，但是在typescript中直接给对象添加属性会报错，这个时候需要使用一种宽松的属性对象

```typescript
let developer = {};
developer.name = "semlinker";

//Property 'name' does not exist on type '{}'.(2339) 
interface LooseObject {
  [key: string]: any
}

let developer: LooseObject = {};
developer.name = "semlinker";
```



### 索引签名

JavaScript 在一个对象类型的索引签名上会隐式调用 `toString` 方法，而在 TypeScript 中，为防止初学者砸伤自己的脚（我总是看到 stackoverflow 上有很多 JavaScript 使用者都会这样。），它将会抛出一个错误。

```typescript
const obj = {
  toString() {
    return 'Hello';
  }
};

const foo: any = {};

// ERROR: 索引签名必须为 string, number....
foo[obj] = 'World';

// FIX: TypeScript 强制你必须明确这么做：
foo[obj.toString()] = 'World';
```

声明索引签名

```typescript
const foo: {
  [index: string]: { message: string };
} = {};

// 储存的东西必须符合结构
// ok
foo['a'] = { message: 'some message' };

// Error, 必须包含 `message`
foo['a'] = { messages: 'some message' };

// 读取时，也会有类型检查
// ok
foo['a'].message;

// Error: messages 不存在
foo['a'].messages;
```

当你声明一个索引签名时，所有明确的成员都必须符合索引签名

这可以给你提供安全性，任何以字符串的访问都能得到相同结果。

```typescript
// ok
interface Foo {
  [key: string]: number;
  x: number;
  y: number;
}

// Error
interface Bar {
  [key: string]: number;
  x: number;
  y: string; // Error: y 属性必须为 number 类型
}

type Index = 'a' | 'b' | 'c';
type FromIndex = { [k in Index]?: number };
```

在 JavaScript 社区你将会见到很多滥用索引签名的 API。如 JavaScript 库中使用 CSS 的常见模式

```typescript
interface NestedCSS {
  color?: string; // strictNullChecks=false 时索引签名可为 undefined
  [selector: string]: string | NestedCSS;
}

const example: NestedCSS = {
  color: 'red',
  '.subclass': {
    color: 'blue'
  }
};

// 尽量不要使用这种把字符串索引签名与有效变量混合使用。如果属性名称中有拼写错误，这个错误不会被捕获到,比如下面这样

const failsSilently: NestedCSS = {
  colour: 'red' // 'colour' 不会被捕捉到错误
};
```

可以用索引签名的嵌套避免这种滥用，我们把索引签名分离到自己的属性里，如命名为 `nest`（或者 `children`、`subnodes` 等）

```typescript
interface NestedCSS {
  color?: string;
  nest?: {
    [selector: string]: NestedCSS;
  };
}

const example: NestedCSS = {
  color: 'red',
  nest: {
    '.subclass': {
      color: 'blue'
    }
  }
}

const failsSliently: NestedCSS {
  colour: 'red'  // TS Error: 未知属性 'colour'
}
```

你需要把属性合并至索引签名，可以使用交叉类型

```typescript
type FieldState = {
  value: string;
};

type FormState = { isValid: boolean } & { [fieldName: string]: FieldState };
```



### 空值合并运算符

??

### 非空断言操作符

非空断言操作符会从变量中移除 undefined 和 null，在变量后面添加一个 ! 就会忽略 undefined 和 null

```typescript
function simpleExample(a: number | undefined) {
   const b: number = a; // 报错，COMPILATION ERROR: undefined is not assignable to number.
   const c: number = a!; // OK
}
```

这种操作符在传递可选props、后端加载数据或者ref取dom时会使用比较频繁，因为这三种情况需要等浏览器加载dom或者组件，值可能为空，如果不使用非空断言操作符，这些情况需要手动添加undefined｜null类型或者使用if/三目运算符进行判断，比较麻烦

```typescript
const ScrolledInput = () => {
   const ref = React.createRef<HTMLInputElement>();

   // const goToInput = () => ref.current.scrollIntoView(); //compilation error: ref.current is possibly null
   const goToInput = () => ref.current!.scrollIntoView();
   return (
       <div>
           <input ref={ref}/>
           <button onClick={goToInput}>Go to Input</button>
       </div>
   );
};
```

### mapped type

```typescript
enum Link{
  a = 'a',
  b = 'b',
  c = 'c'
}
interface Common{
  name:string;
  age: number
}
interface A extends Common{
  id: string;
}
interface B extends Common{
  value: string | number;
}
interface C extends Common{
  id: string | number;
}
type U = A | B | C;

type Props = {
    [key in Link]: U;
} & { type: string;}

const p:Props = {
  [Link.a]: {
    name: 'li',
    age: 24,
    id: '123'
  },
  [Link.b]:{
    name:'li',
    age: 23,
    value:'123'
  },
  [Link.c]:{
    name: 'li',
    age: 24,
    id: '34'
  },
  type: 'c'
}
```





## 代码检查

### Es-lint

安装es-lint

```shell
npm install --save-dev eslint
```

由于 ESLint 默认使用 [Espree](https://github.com/eslint/espree) 进行语法解析，无法识别 TypeScript 的一些语法，故我们需要安装 [`@typescript-eslint/parser`](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/parser)，替代掉默认的解析器，别忘了同时安装 `typescript`：

```shell
npm install --save-dev typescript @typescript-eslint/parser
```

接下来需要安装对应的插件 [@typescript-eslint/eslint-plugin](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin) 它作为 eslint 默认规则的补充，提供了一些额外的适用于 ts 语法的规则。

```shell
npm install --save-dev @typescript-eslint/eslint-plugin
```

创建自己的规则

ESLint 需要一个配置文件来决定对哪些规则进行检查，配置文件的名称一般是 `.eslintrc.js` 或 `.eslintrc.json`。

当运行 ESLint 的时候检查一个文件的时候，它会首先尝试读取该文件的目录下的配置文件，然后再一级一级往上查找，将所找到的配置合并起来，作为当前被检查文件的配置。

```javascript
module.exports = {
    parser: '@typescript-eslint/parser',
    plugins: ['@typescript-eslint'],
    rules: {
        // 禁止使用 var
        'no-var': "error",
        // 优先使用 interface 而不是 type
        '@typescript-eslint/consistent-type-definitions': [
            "error",
            "interface"
        ]
    }
}
```

执行检查

我们的项目源文件一般是放在 `src` 目录下，所以需要将 `package.json` 中的 `eslint` 脚本改为对一个目录进行检查。由于 `eslint` 默认不会检查 `.ts` 后缀的文件，所以需要加上参数 `--ext .ts`：

```javascript
{
    "scripts": {
        "eslint": "eslint src --ext .ts"
    }
}
```

此时执行 `npm run eslint` 即会检查 `src` 目录下的所有 `.ts` 后缀的文件。

在 VSCode 中集成 ESLint 检查[§](https://ts.xcatliu.com/engineering/lint.html#在-vscode-中集成-eslint-检查)

在编辑器中集成 ESLint 检查，可以在开发过程中就发现错误，甚至可以在保存时自动修复错误，极大的增加了开发效率。

要在 VSCode 中集成 ESLint 检查，我们需要先安装 ESLint 插件，点击「扩展」按钮，搜索 ESLint，然后安装即可。

VSCode 中的 ESLint 插件默认是不会检查 `.ts` 后缀的，需要在「文件 => 首选项 => 设置 => 工作区」中（也可以在项目根目录下创建一个配置文件 `.vscode/settings.json`），添加以下配置：

```json
{
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "typescript"
    ],
    "typescript.tsdk": "node_modules/typescript/lib"
}
```

此时打开ts文件，在错误处就会有提示

插件

`eslint-plugin-simple-import-sort` 调整导入顺序

```shell
npm install --save-dev eslint-plugin-simple-import-sort
```

添加插件到eslint.rc

```json
{
  "plugins": ["simple-import-sort"]
}

{
  "rules": {
    "simple-import-sort/imports": "error",
    "simple-import-sort/exports": "error"
  }
}
```

#### 支持tsx

如果需要同时支持对 tsx 文件的检查，则需要对以上步骤做一些调整：

安装 eslint-plugin-react

```shell
npm install --save-dev eslint-plugin-react
```

在package.json和vscode的插件中添加配置

```json
{
    "scripts": {
        "eslint": "eslint src --ext .ts,.tsx"
    }
}
```

```javascript
{
    "files.eol": "\n",
    "editor.tabSize": 4,
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "eslint.autoFixOnSave": true,
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        {
            "language": "typescript",
            "autoFix": true
        },
        {
            "language": "typescriptreact",
            "autoFix": true
        }
    ],
    "typescript.tsdk": "node_modules/typescript/lib"
}
```



#### eslint9

改进点：

1.ESLint v9 与时俱进，放弃了对 Node.js v18.18.0 以下版本的支持。

2.在旧版本的 ESLint 中，可以使用它作代码风格约束，例如：尾随逗号，空格，空行等。这些规则可以让我们的代码风格一致，但是这些规则却与 ESLint 的核心功能有点相悖
因为 ESLint 主要的点是分析代码的语法错误而不应该是风格错误，所以风格检查有点画蛇添足了，况且现在有更加快速且强大的 `prettier`来做代码风格约束，所以 ESLint 的风格约束是时候放弃了

以前使用 `extends`来组合规则，现在使用数组的每一项来组合，所以 ESLint 官方才新出品了 `@eslint/js`包来导出 ESLint 默认规则。
而且，这会导致以前的 `eslint`的其他社区规范文件需要发布适用于 v9 版本的新规则。不过好在 ESLint 官方已经想到了这个问题，他们提供了一个 `@eslint/eslintrc`包，用于将现有的插件和预设转换为符合新版本规范的代码

```javascript
import { FlatCompat } from "@eslint/eslintrc";
import path from "path";
import { fileURLToPath } from "url";

// mimic CommonJS variables -- not needed if using CommonJS
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const compat = new FlatCompat({
    baseDirectory: __dirname
});

export default [
    // mimic ESLintRC-style extends
    ...compat.extends("standard", "example"),

    // mimic environments
    ...compat.env({
        es2020: true,
        node: true
    }),

    // mimic plugins
    ...compat.plugins("airbnb", "react"),

    // translate an entire config
    ...compat.config({
        plugins: ["airbnb", "react"],
        extends: "standard",
        env: {
            es2020: true,
            node: true
        },
        rules: {
            semi: "error"
        }
    })
];
```



3.全新的配置文件：ESLint v9 采用全新的配置文件：

- eslint.config.js
- eslint.config.mjs
- eslint.config.cjs

其他的更新点：https://juejin.cn/post/7355096015583920128#heading-3

### Prettier 

ESLint 包含了一些代码格式的检查，比如空格、分号等。但前端社区中有一个更先进的工具可以用来格式化代码，那就是 [Prettier](https://prettier.io/)。

Prettier 聚焦于代码的格式化，通过语法分析，重新整理代码的格式，让所有人的代码都保持同样的风格。

安装Prettier

```shell
npm install --save-dev prettier
```

然后创建一个 `prettier.config.js` 文件，里面包含 Prettier 的配置项。Prettier 的配置项很少，这里我推荐大家一个配置规则，作为参考：

```js
// prettier.config.js or .prettierrc.js
module.exports = {
    printWidth: 100,  		   // 一行最多 100 字符
    tabWidth: 4,      			 // 使用 4 个空格缩进
    useTabs: false,  				 // 不使用缩进符，而使用空格
    semi: true,      			   // 行尾需要有分号
    singleQuote: true,			 // 使用单引号
    quoteProps: 'as-needed', // 对象的 key 仅在必要时用引号
    jsxSingleQuote: false,   // jsx 不使用单引号，而使用双引号
    trailingComma: 'none',   // 末尾不需要逗号
    bracketSpacing: true,    // 大括号内的首尾需要空格
    jsxBracketSameLine: false,// jsx 标签的反尖括号需要换行
    arrowParens: 'always',   // 箭头函数，只有一个参数的时候，也需要括号
    rangeStart: 0,           // 每个文件格式化的范围是文件的全部内容
    rangeEnd: Infinity,
    requirePragma: false,    // 不需要写文件开头的 @prettier
    insertPragma: false,     // 不需要自动在文件开头插入 @prettier
    proseWrap: 'preserve',   // 使用默认的折行标准
    htmlWhitespaceSensitivity: 'css',// 根据显示样式决定 html 要不要折行
    endOfLine: 'lf'          // 换行符使用 lf
};
```

### style-lint



### oxc



### biome

https://biomejs.dev/linter/



## 对Node的支持

想用typescript写nodejs，需要引入第三方声明文件

```shell
npm install @type/node --save
```

https://ts.xcatliu.com/basics/type-of-function.html

