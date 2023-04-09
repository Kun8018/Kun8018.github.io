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

## npm

`npm` 是 Node.js 标准的软件包管理器。

在 2017 年 1 月时，npm 仓库中就已有超过 350000 个软件包，这使其成为世界上最大的单一语言代码仓库，并且可以确定几乎有可用于一切的软件包。

它起初是作为下载和管理 Node.js 包依赖的方式，但其现在也已成为前端 JavaScript 中使用的工具。

`npm` 可以管理项目依赖的下载。

如果项目具有 `package.json` 文件，则通过运行npm install 安装

它会在 `node_modules` 文件夹（如果尚不存在则会创建）中安装项目所需的所有东西。

也可以通过运行以下命令安装特定的软件包

npm install package-name

通常会在此命令中看到更多标志：

- `--save` 安装并添加条目到 `package.json` 文件的 dependencies。
- `--save-dev` 安装并添加条目到 `package.json` 文件的 devDependencies。

区别主要是，`devDependencies` 通常是开发的工具（例如测试的库），而 `dependencies` 则是与生产环境中的应用程序相关

更新软件包与安装类似，只是命令不同

```shell
npm update
```

package.json 文件支持一种用于指定命令行任务（可通过使用以下方式运行）的格式

```shell
npm run <task-name>
```

例如

```shell
{
  "scripts": {
    "start-dev": "node lib/server-development",
    "start": "node lib/server-production"
  },
}

{
  "scripts": {
    "watch": "webpack --watch --progress --colors --config webpack.conf.js",
    "dev": "webpack --progress --colors --config webpack.conf.js",
    "prod": "NODE_ENV=production webpack -p --config webpack.conf.js",
  },
}
```

### npm包版本

如果 Node.js 软件包中有一件很棒的事情，那就是它们都同意使用语义版本控制作为版本编号

语义版本控制的概念很简单：所有的版本都有 3 个数字：`x.y.z`。

- 第一个数字是主版本。
- 第二个数字是次版本。
- 第三个数字是补丁版本。

当发布新的版本时，不仅仅是随心所欲地增加数字，还要遵循以下规则：

- 当进行不兼容的 API 更改时，则升级主版本。
- 当以向后兼容的方式添加功能时，则升级次版本。
- 当进行向后兼容的缺陷修复时，则升级补丁版本。

该约定在所有编程语言中均被采用，每个 `npm` 软件包都必须遵守该约定，这一点非常重要，因为整个系统都依赖于此

`npm` 设置了一些规则，可用于在 `package.json` 文件中选择要将软件包更新到的版本（当运行 `npm update` 时

规则使用了这些符号及详情如下：

- `^`: 只会执行不更改最左边非零数字的更新。 如果写入的是 `^0.13.0`，则当运行 `npm update` 时，可以更新到 `0.13.1`、`0.13.2` 等，但不能更新到 `0.14.0` 或更高版本。 如果写入的是 `^1.13.0`，则当运行 `npm update` 时，可以更新到 `1.13.1`、`1.14.0` 等，但不能更新到 `2.0.0` 或更高版本。
- `~`: 如果写入的是 `〜0.13.0`，则当运行 `npm update` 时，会更新到补丁版本：即 `0.13.1` 可以，但 `0.14.0` 不可以。
- `>`: 接受高于指定版本的任何版本。
- `>=`: 接受等于或高于指定版本的任何版本。
- `<=`: 接受等于或低于指定版本的任何版本。
- `<`: 接受低于指定版本的任何版本。
- `=`: 接受确切的版本。
- `-`: 接受一定范围的版本。例如：`2.1.0 - 2.6.2`。
- `||`: 组合集合。例如 `< 2.1 || > 2.6`

可以合并其中的一些符号，例如 `1.0.0 || >=1.1.0 <1.2.0`，即使用 1.0.0 或从 1.1.0 开始但低于 1.2.0 的版本。

还有其他的规则：

- 无符号: 仅接受指定的特定版本（例如 `1.2.1`）。
- `latest`: 使用可用的最新版本

### npx

`npx` 可以运行使用 Node.js 构建并通过 npm 仓库发布的代码

`npx` 是一个非常强大的命令，从 **npm** 的 5.2 版本（发布于 2017 年 7 月）开始可用

`npx` 的另一个重要的特性是，无需先安装命令即可运行命令

这非常有用，主要是因为：

1. 不需要安装任何东西。
2. 可以使用 @version 语法运行同一命令的不同版本。

npx的典型应用场景有

- 运行 `vue` CLI 工具以创建新的应用程序并运行它们：`npx @vue/cli create my-vue-app`。
- 使用 `create-react-app` 创建新的 `React` 应用：`npx create-react-app my-react-app`。

当被下载完，则下载的代码会被擦除。

### npm命令集

本地npm包相关

npm outdated 检查本地npm包是否有过期包

npm ci: 使用package-lock.json安装本地依赖

npm rebuild: 必须使用新的二进制文件重新编译所有 C++ 插件

npm docs: 

npm包发布相关

npm star/unstar <package-name> : 为一个包加星标（"Starring"）意味着你对这个包感兴趣。 这是一种你表达关注的方式。减星标（"Unstarring"）与加星标相反

npm team:

npm publish：

npm deprecate: 此命令将更新 npm 注册表中指定包所对应的数据条目， 为尝试安装它的所有人提示版本作废的警告信息

其他

npm ping： Ping 已配置的或给定的 npm 注册表地址并进行身份验证。 如果 ping 执行成功，则会输出类似下面的内容

npm config：

npm repo: 此命令尝试猜测指定包的源码仓库的 URL ，然后再使用 `--browser` 配置参数打开它。 如果没有提供包名称，它将在当前文件夹中搜索`package.json` 文件， 并使用其 `name` 属性的值

`--cache-min`参数指定一个时间（单位为分钟），只有超过这个时间的模块，才会从 registry 下载

```shell
$ npm install --cache-min Infinity <package-name>
```

#### npm 脚本的原理

npm 脚本的原理非常简单。每当执行`npm run`，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面

比较特别的是，`npm run`新建的这个 Shell，会将当前目录的`node_modules/.bin`子目录加入`PATH`变量，执行结束后，再将`PATH`变量恢复原样

这意味着，当前目录的`node_modules/.bin`子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径。比如，当前项目的依赖里面有 Mocha，只要直接写`mocha test`就可以了

由于 npm 脚本的唯一要求就是可以在 Shell 执行，因此它不一定是 Node 脚本，任何可执行文件都可以写在里面。

npm 脚本的退出码，也遵守 Shell 脚本规则。如果退出码不是`0`，npm 就认为这个脚本执行失败。

通配符

由于 npm 脚本就是 Shell 脚本，因为可以使用 Shell 通配符

```json
"lint": "jshint *.js"
"lint": "jshint **/*.js"
```

`*`表示任意文件名，`**`表示任意一层子目录。

如果要将通配符传入原始命令，防止被 Shell 转义，要将星号转义

```json
"test": "tap test/\*.js"
```



**钩子**

npm 脚本有`pre`和`post`两个钩子。举例来说，`build`脚本命令的钩子就是`prebuild`和`postbuild`

```json
"prebuild": "echo I run before the build script",
"build": "cross-env NODE_ENV=production webpack",
"postbuild": "echo I run after the build script"
```

用户执行`npm run build`的时候，会自动按照下面的顺序执行。

```shell
npm run prebuild && npm run build && npm run postbuild
```

npm默认提供了一些钩子

```
prepublish，postpublish
preinstall，postinstall
preuninstall，postuninstall
preversion，postversion
pretest，posttest
prestop，poststop
prestart，poststart
prerestart，postrestart
```

自定义的脚本命令也可以加上`pre`和`post`钩子。比如，`myscript`这个脚本命令，也有`premyscript`和`postmyscript`钩子。不过，双重的`pre`和`post`无效，比如`prepretest`和`postposttest`是无效的。

npm 提供一个`npm_lifecycle_event`变量，返回当前正在运行的脚本名称，比如`pretest`、`test`、`posttest`等等。所以，可以利用这个变量，在同一个脚本文件里面，为不同的`npm scripts`命令编写代码。

```javascript
const TARGET = process.env.npm_lifecycle_event;

if (TARGET === 'test') {
  console.log(`Running the test task!`);
}

if (TARGET === 'pretest') {
  console.log(`Running the pretest task!`);
}

if (TARGET === 'posttest') {
  console.log(`Running the posttest task!`);
}
```

注意，`prepublish`这个钩子不仅会在`npm publish`命令之前运行，还会在`npm install`（不带任何参数）命令之前运行。这种行为很容易让用户感到困惑，所以 npm 4 引入了一个新的钩子`prepare`，行为等同于`prepublish`，而从 npm 5 开始，`prepublish`将只在`npm publish`命令之前运行

其他变量

npm 脚本有一个非常强大的功能，就是可以使用 npm 的内部变量

首先，通过`npm_package_`前缀，npm 脚本可以拿到`package.json`里面的字段。比如，下面是一个`package.json`

```json
{
  "name": "foo", 
  "version": "1.2.5",
  "scripts": {
    "view": "node view.js"
  }
}
```

那么，变量`npm_package_name`返回`foo`，变量`npm_package_version`返回`1.2.5`。

```javascript
// view.js
console.log(process.env.npm_package_name); // foo
console.log(process.env.npm_package_version); // 1.2.5
```



常用脚本

```json
// 删除目录
"clean": "rimraf dist/*",

// 本地搭建一个 HTTP 服务
"serve": "http-server -p 9090 dist/",

// 打开浏览器
"open:dev": "opener http://localhost:9090",

// 实时刷新
 "livereload": "live-reload --port 9091 dist/",

// 构建 HTML 文件
"build:html": "jade index.jade > dist/index.html",

// 只要 CSS 文件有变动，就重新执行构建
"watch:css": "watch 'npm run build:css' assets/styles/",

// 只要 HTML 文件有变动，就重新执行构建
"watch:html": "watch 'npm run build:html' assets/html",

// 部署到 Amazon S3
"deploy:prod": "s3-cli sync ./dist/ s3://example-com/prod-site/",

// 构建 favicon
"build:favicon": "node scripts/favicon.js",
```



### npm install

过程

1.发出npm install命令

2.查询node_modules目录之中是否已经存在指定模块，若存在，不再重新安装

3.若不存在，npm 向 registry 查询模块压缩包的网址下载压缩包，存放在根目录下的.npm目录里，

4.解压压缩包到当前项目的node_modules目录

npm实现原理：

输入 npm install 命令并敲下回车后，会经历如下几个阶段：

1.执行工程自身preinstall。如果工程定义了preinstall钩子会被执行。

2.确定首层依赖模块。dependencies 和 devDependencies 属性中直接指定的模块，工程本身是整棵依赖树的根节点，每个首层依赖模块都是根节点下面的一棵子树，npm 会开启多进程从每个首层依赖模块开始逐步寻找更深层级的节点。

3.获取模块。

获取模块是一个递归的过程，分为以下几步：

获取模块信息。在下载一个模块之前，首先要确定其版本，这是因为 package.json 中往往是 semantic version（semver，语义化版本）。此时如果版本描述文件（npm-shrinkwrap.json 或 package-lock.json）中有该模块信息直接拿即可，如果没有则从仓库获取。如 packaeg.json 中某个包的版本是 ^1.1.0，npm 就会去仓库中获取符合 1.x.x 形式的最新版本。

获取模块实体。上一步会获取到模块的压缩包地址（resolved 字段），npm 会用此地址检查本地缓存，缓存中有就直接拿，如果没有则从仓库下载

查找该模块依赖，如果有依赖则回到第1步，如果没有则停止。

4.模块扁平化。上一步获取到的是一棵完整的依赖树，其中可能包含大量重复模块。比如 A 模块依赖于 loadsh，B 模块同样依赖于 lodash。

从 npm3 开始默认加入了一个 dedupe 的过程。它会遍历所有节点，逐个将模块放在根节点下面，也就是 node-modules 的第一层。当发现有**重复模块**时，则将其丢弃。

**重复模块**的定义，它指的是**模块名相同**且 **semver 兼容。\**每个 semver 都对应一段版本允许范围，如果两个模块的版本允许范围存在交集，那么就可以得到一个\**兼容**版本，而不必版本号完全一致，这可以使更多冗余模块在 dedupe 过程中被去掉。

5.安装模块。更新工程中的 node_modules，并执行模块中的生命周期函数（按照 preinstall、install、postinstall 的顺序）。

6.执行工程自身生命周期。当前 npm 工程如果定义了钩子此时会被执行（按照 install、postinstall、prepublish、prepare 的顺序）。

7.更新或生成版本描述文件，npm install过程完成

`--legacy-peer-deps` :安装时忽略所有 peerDependencies，采用 npm 版本 4 到版本 6 的样式。

`--strict-peer-deps` :在遇到任何冲突的 peerDependencies 时失败并中止安装过程。默认情况下，npm 只会因根项目直接依赖导致的 peerDependencies 冲突而崩溃。

#### 离线安装方案

社区已经为npm的离线使用，提出了几种解决方案。它们可以大大加快模块安装的速度

第一种是使用代理

- [npm-proxy-cache](https://www.npmjs.com/package/npm-proxy-cache)
- [local-npm](https://github.com/nolanlawson/local-npm)（[用法](https://addyosmani.com/blog/using-npm-offline/)）
- [npm-lazy](https://github.com/mixu/npm_lazy)

在本机起一个 Registry 服务，所有`npm install`命令都要通过这个服务代理。有了本机的Registry服务，就能完全实现缓存安装，可以实现离线使用。

第二种是代替npm install

如果能够改变`npm install`的行为，就能实现缓存安装。[`npm-cache`](https://www.npmjs.com/package/npm-cache) 工具就是这个思路。凡是使用`npm install`的地方，都可以使用`npm-cache`替代。

```shell
$ npm-cache install
```

第三种 使用node_modules作为缓存目录

这个方案的思路是，不使用`.npm`缓存，而是使用项目的`node_modules`目录作为缓存。

- [Freight](https://github.com/node-freight/freight)
- [npmbox](https://github.com/arei/npmbox)

上面两个工具，都能将项目的`node_modules`目录打成一个压缩包，以后安装的时候，就从这个压缩包之中取出文件

### pnpm

当使用 npm 或 Yarn 时，如果你有 100 个项目使用了某个依赖（dependency），就会有 100 份该依赖的副本保存在硬盘上。  而在使用 pnpm 时，依赖会被存储在内容可寻址的存储中，所以：

1. 如果你用到了某依赖项的不同版本，只会将不同版本间有差异的文件添加到仓库。 例如，如果某个包有100个文件，而它的新版本只改变了其中1个文件。那么 `pnpm update` 时只会向存储中心额外添加1个新文件，而不会因为仅仅一个文件的改变复制整新版本包的内容。
2. 所有文件都会存储在硬盘上的某一位置。 当软件包被被安装时，包里的文件会硬链接到这一位置，而不会占用额外的磁盘空间。 这允许你跨项目地共享同一版本的依赖。

pnpm在package.json中的配置

```json
{
  "pnpm": {
    "peerDependencyRules": {
      "ignoreMissing": ["babel-loader"],
      "allowedVersions": {
        "@angular/common": "13"
      }
    }
  }
}
```



### pnpm、yarn、cnpm、npm的区别

yarn相比于npm：

yarn 出生之后，解决了历史上 npm 的某些不足，比如 npm 缺乏对于依赖的完整性和一致性保障，以及 npm 安装速度过慢的问题等，尽管 npm 发展至今，已经在很多方面向 yarn 看齐，但 yarn 的安装理念仍然需要我们关注。 yarn 提出的安装理念很好的解决了当时 npm 的依赖管理问题：

- 确定性。通过 yarn.lock 等机制，保证了确定性，这里的确定性包括但不限于明确的依赖版本、明确的依赖安装结构等。即在任何机器和环境下，都可以以相同的方式被安装。
- 模块扁平化安装。将依赖包的不同版本，按照一定策略，归结为单个版本，以避免创建多个副本造成冗余。（npm 也有相同的优化)
- 更好的网络性能。Yarn 采用了请求排队的理念，类似并发连接池，能够更好地利用网络资源；同时引入了更好的安装失败时的重试机制。（npm 较早的版本是顺序下载，当第一个包完全下载完成后，才会将下载控制权交给下一个包)
- 引入缓存机制，实现离线策略。（npm 也有类似的优化)

pnpm 本质上就是一个包管理器，这一点跟 npm/yarn 没有区别，但它作为杀手锏的两个优势在于:

- 包安装速度极快；
- 磁盘空间利用非常高效

**速度**

pnpm，在绝多大数场景下，包安装的速度都是明显优于 npm/yarn，速度会比 npm/yarn 快 2-3 倍

yarn 有 `PnP 安装模式`(https://classic.yarnpkg.com/en/docs/pnp/)吗？直接去掉 node_modules，将依赖包内容写在磁盘，节省了 node 文件 I/O 的开销，这样也能提升安装速度

**支持mono repo**

随着前端工程的日益复杂，越来越多的项目开始使用 monorepo。之前对于多个项目的管理，我们一般都是使用多个 git 仓库，但 monorepo 的宗旨就是用一个 git 仓库来管理多个子项目，所有的子项目都存放在根目录的`packages`目录下，那么一个子项目就代表一个`package`。如果你之前没接触过 monorepo 的概念，建议仔细看看这篇文章(https://www.perforce.com/blog/vcs/what-monorepo)以及开源的 monorepo 管理工具`lerna`，项目目录结构可以参考一下 `babel 仓库`(https://github.com/babel/babel)。

pnpm 与 npm/yarn 另外一个很大的不同就是支持了 monorepo，体现在各个子命令的功能上，比如在根目录下 `pnpm add A -r`, 那么所有的 package 中都会被添加 A 这个依赖，当然也支持 `--filter`字段来对 package 进行过滤

**高效利用磁盘空间**

pnpm 内部使用`基于内容寻址`的文件系统来存储磁盘上所有的文件，这个文件系统出色的地方在于

不会重复安装同一个包。用 npm/yarn 的时候，如果 100 个项目都依赖 lodash，那么 lodash 很可能就被安装了 100 次，磁盘中就有 100 个地方写入了这部分代码。但在使用 pnpm 只会安装一次，磁盘中只有一个地方写入，后面再次使用都会直接使用 `hardlink`

即使一个包的不同版本，pnpm 也会极大程度地复用之前版本的代码。举个例子，比如 lodash 有 100 个文件，更新版本之后多了一个文件，那么磁盘当中并不会重新写入 101 个文件，而是保留原来的 100 个文件的 `hardlink`，仅仅写入那`一个新增的文件`

**依赖管理**

npm install 的原理：

主要分为两个部分, 首先，执行 npm/yarn install之后，`包如何到达项目 node_modules 当中`。其次，node_modules `内部如何管理依赖`。

执行命令后，首先会构建依赖树，然后针对每个节点下的包，会经历下面四个步骤:

 \- 1. 将依赖包的版本区间解析为某个具体的版本号
 \- 2. 下载对应版本依赖的 tar 包到本地离线镜像
 \- 3. 将依赖从离线镜像解压到本地缓存
 \- 4. 将依赖从缓存拷贝到当前目录的 node_modules 目录

然后，对应的包就会到达项目的`node_modules`当中。

在 `npm1`、`npm2` 中呈现出的是嵌套结构，如果不同的依赖包有着相同包的不同版本，会出现以下问题：

- 依赖层级太深，会导致文件路径过长的问题，尤其在 window 系统下。
- 大量重复的包被安装，文件体积超级大。比如跟 foo 同级目录下有一个baz，两者都依赖于同一个版本的lodash，那么 lodash 会分别在两者的 node_modules 中被安装，也就是重复安装。
- 模块实例不能共享。比如 React 有一些内部变量，在两个不同包引入的 React 不是同一个模块实例，因此无法共享内部变量，导致一些不可预知的 bug。安全性**

从npm3开始，以及yarn中，都着手来通过`扁平化依赖`的方式来解决这个问题

所有的依赖都被拍平到`node_modules`目录下，不再有很深层次的嵌套关系。这样在安装新的包时，根据 node require 机制，会不停往上级的`node_modules`当中去找，如果找到相同版本的包就不会重新安装，解决了大量包重复安装的问题，而且依赖层级也不会太深。

但是铺平的node_modules依然有很多问题：

1. 依赖结构的**不确定性**。
2. 扁平化算法本身的**复杂性**很高，耗时较长。
3. 项目中仍然可以**非法访问**没有声明过依赖的包

第一个问题直接导致了 `lock 文件`的诞生，无论是`package-lock.json`(npm 5.x才出现)还是`yarn.lock`，都是为了保证 install 之后都产生确定的`node_modules`结构

不同于npm/yarn，使用pnpm安装包后，会在node_modules下会生成包的软连接，有助于快速找到安装了哪些包

同时，所有的包都放在.pnpm文件夹下，按照<package-name> @version/node_modules <package-name>的嵌套结构在.pnpm下。再看看`.pnpm`，`.pnpm`目录下虽然呈现的是扁平的目录结构，但仔细想想，顺着`软链接`慢慢展开，其实就是嵌套的结构。这样将`包本身`和`依赖`放在同一个`node_module`下面，与原生 Node 完全兼容，又能将 package 与相关的依赖很好地组织到一起，设计十分精妙

**非法访问的问题**

在npm/yarn中，如果 A 依赖 B， B 依赖 C，那么 A 就算没有声明 C 的依赖，由于有依赖提升的存在，C 被装到了 A 的`node_modules`里面，那我在 A 里面是可以用 C的，并且跑起来也没有问题。

但是当包依赖变化时， 如果 B 更新之后，可能不需要 C 了，那么安装依赖的时候，C 都不会装到`node_modules`里面，A 当中引用 C 的代码直接报错。还有一种情况，在 monorepo 项目中，如果 A 依赖 X，B 依赖 X，还有一个 C，它不依赖 X，但它代码里面用到了 X。由于依赖提升的存在，npm/yarn 会把 X 放到根目录的 node_modules 中，这样 C 在本地是能够跑起来的，因为根据 node 的包加载机制，它能够加载到 monorepo 项目根目录下的 node_modules 中的 X。但试想一下，一旦 C 单独发包出去，用户单独安装 C，那么就找不到 X 了，执行到引用 X 的代码时就直接报错了。

这些，都是依赖提升潜在的 bug。如果是自己的业务代码还好，试想一下如果是给很多开发者用的工具包，那危害就非常严重了。

npm 也有想过去解决这个问题，指定`--global-style`参数即可禁止变量提升，但这样做相当于回到了当年嵌套依赖的时代，一夜回到解放前，前面提到的嵌套依赖的缺点仍然暴露无遗。

npm/yarn 本身去解决依赖提升的问题貌似很难完成，不过社区针对这个问题也已经有特定的解决方案: **dependency-check**，地址: https://github.com/dependency-check-team/dependency-check

pnpm 做的更加彻底，独创的一套依赖管理方式不仅解决了依赖提升的安全问题，还大大优化了时间和空间上的性能。

### npm私库的搭建

npm 作为一种包管理工具，无论你是泛前端还是大前端都已经离不开它。它的出现方便了万千少年。让我们跨过了 Ctrl+C、Ctrl+V ，通过 ``npm install x``的方式将别人的优秀代码模块引入到自己的项目中。这些优秀的模块能被共享的原因，一方面是有 npm 这么一个包管理工具，另外就是 npm 仓库。

对于 npm 仓库，如果你还停留在使用 npm 或者 cnpm 这类官方源的情况下。那么你有必要想想如何搭建一个私有的 npm 仓库。

搭建npm私库的原因：

1.稳定性

网络访问稳定性，私有仓库因为是自己公司在维护，有什么问题能第一时间处理，比如服务宕机…其次资源的稳定性，试想一下，如果哪天你依赖的某个很重要的模块突然被作者删了，那是不是完犊子了

2.私密性

每个公司都有和自己业务强相关的模块，或者对某些开源模块进行个性化的改造，改造后的模块只满足本公司的业务场景，这些模块我们并不希望发布到公共的仓库中去，这时就可以发布到自己的私有仓库在公司内部共享

3.安全性

有了私有仓库后，可以在 npm 模块的质量和安全上做文章，能够有效的防治恶意代码攻击。

搭建

选择[cnpmjs.org](https://www.npmjs.com/package/cnpmjs.org)方案，目前国内像淘宝这样的大厂内部也是选择的它，足以证明它的可靠性和稳定性，拓展性强，配置多样化

环境

- Linux 服务器
- node 环境
- 数据库( Mysql )
- nginx

安装

首先安装cnpmjs.org

```shell
git clone https://github.com/cnpm/cnpmjs.org.git
```

安装项目依赖

```shell
npm i
```

安装完成后找到项目根目录下的配置文件`config/index.js` ，这里配置文件非常多，刚开始可以只关注下面几项即可，[详细配置](https://gitee.com/199253/cnpmjs/blob/master/config/index.js)戳这里。

服务访问端口

```yaml
registryPort: 7001,         //仓库服务访问端口
webPort: 7002,              //web站点访问端口
bindingHost: '',   //监听绑定的 Host，默认127.0.0.1，外网访问注释掉此项即可，一般我们不会把我们内部端口暴露出去，可以在nginx层做一个转发，所以这个配置可以注释掉。如果直接外网访问，配置为 0.0.0.0
```

数据库配置

```yaml
database: {
  db: 'npm',数据库名称
  username: 'admin',//用户
  password: 'admin123',//密码
  // 数据库类型
  // - 目前支持 'mysql', 'sqlite', 'postgres', 'mariadb'
  dialect: 'mysql',//默认是sqlite，我选择的mysql
  host: '127.0.0.1', //数据库服务地址
  port: 3306,    // 端口
  // 数据库连接池使用默认配置就好
  // 目前只支持  mysql 和 postgresql (since v1.5.0)
  pool: {
    maxConnections: 10,
    minConnections: 0,
    maxIdleTime: 30000
  },
  ...//其他的暂时不用关注
},
```

是否启用私有模式

```yml
enablePrivate: false,//默认不启用
```

私有模式下，只有管理员才能发布模块。非管理员发布模块式命名必须以 scopes 字段开头例如：`@catfly/packagename`

发布前缀

```yaml
scopes: ['@catfly'],
```

这个和启用非私有模式配套使用，非私有模式要发布必须配置该项。

管理员配置

```yaml
admins: {
      fengmk2: 'fengmk2@gmail.com',
      admin: 'admin@cnpmjs.org',
      dead_horse: 'dead_horse@qq.com',
}
```

如果启用私有模式，只有该配置项中的用户可以发布私有包。至于其他的配置项暂时不用关注，后面根据需要在逐渐配置起来。

同步模式

```yaml
// 同步模式选项
// none: 不进行同步，只管理用户上传的私有模块，公共模块直接从上游获取
// exist: 只同步已经存在于数据库的模块
// all: 定时同步所有源registry的模块
syncModel:'exist'
```

数据库

我选择的 mysql ，请[戳这里](https://www.runoob.com/mysql/mysql-install.html)。当然你也可以选择其他数据库，目前支持mysql 、 sqlite 、 postgres 、 mariadb ，默认是 sqlite 。

确认数据库启动

```shell
service mysql status
```

登陆数据库

```shell
mysql -u root -p  test123456
```

创建数据库

```shell
create database npm
```

查看数据库列表

```shell
show database
```

执行sql文件

cnpmjs.org项目docs目录下已经给我们备好了创建数据库的脚本db.sql.执行

```shell
source docs/db.sql
```

然后使用数据库

```shell
use npm 
show tables
```

上面两步完成后，就可以将项目跑起来一睹芳容了。因为我们通过 git 克隆的，所以需要进入到项目目录下执行启动服务的命令

```shell
npm run start
```

如果服务器的7002端口访问不了，可能是防火墙的原因，可以关闭防火墙或者开放指定端口

```shell
iptables -A INPUT -p tcp --drop -j 7002 DROP
```

访问 web 页面：xxx.xxx.xxx.xx:7002，就可以看见熟悉的部署在本地的 cnpm 页面了

如果配置域名访问则需要使用nginx代理，这里简单贴一下nginx.conf配置

```conf
server{
      listen  80;
       server_name www.mirrors.catfly.vip;
       #charset koi8-r;
       #access_log  logs/host.access.log  main;
       location / { 
            proxy_pass http://127.0.0.1:7002/; #代理到cnpmjs.org提供的web服务
            proxy_set_header        X-Real-IP $remote_addr;
       }
       location /registry/ {
           proxy_pass http://127.0.0.1:7001/; # 代理到cnpmjs.org提供的注册服务
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header Host $host;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       }
       #error_page  404              /404.html;
       # redirect server error pages to the static page /50x.html
       # error_page   500 502 503 504  /50x.html;
       location = /50x.html {
           root   html;
       }
}
```

验证

在本地安装一个nrm工具，使用比较方便

```shell
npm i nrm -g
```

安装成功后新增我们自己的私有源到nrm源列表中。

```shell
nrm add catfly http://www.mirrors.catfly.vip/registry
```

切换到私有源

```shell
nrm use catfly
```

这个时候本地执行 npm 操作的时候就会去找到我们自己的私有地址

#### 进程管理

推荐使用 pm2 进行进程管理，虽然项目本身提供了`npm run start`和`npm run stop`的能力，但是这对于一个企业级的应用来说还是太弱了，使用 pm2 的好处如下：

1. 随时随地多进程管理
2. 完善的监控机制，我们可以清晰地看见整个集群的模式、状态，CPU 利用率甚至是内存大小
3. 负责均衡
4. 进程守护
5. ...

全局安装pm2

```shell
npm i pm2 -g
```

启动项目

```shell
pm2 start ./dispatch.js
```

查看服务进程信息

```shell
pm2 monit dispatch
```

#### 私有库上云

cnpmjs.org 项目配置项里面有一个 `nfs`配置，这里定义了一个 npm 文件系统（NFS）。私有仓库在同步和上传的时候，会交给 NFS 对象相应的函数去处理，NFS 对象返回处理结束之后再返回下载链接，所以通过自定义 NFS 模块可以实现 npm 包的各种定制存储。目前官方默认使用`fs-cnpm`，该模块会将上传或者同步的包保存在服务器本地的`/root/.cnpmjs.org/doenloads/`目录下。这种方式比较传统，一方面随着私有包数量的不断增加，存储资源会是一个瓶颈。

这个时候将私有包或者同步的资源放到云上就是一个非常好的方案。cnpmjs.org 官方早就为我们想到了这点，给出了下面几种 NFS 模块：

- [upyun-cnpm](https://link.jianshu.com/?t=https://github.com/cnpm/upyun-cnpm)：又拍云存储插件
- [fs-cnpm](https://link.jianshu.com/?t=https://github.com/cnpm/fs-cnpm)：本地存储的插件
- [sfs-client](https://link.jianshu.com/?t=https://github.com/cnpm/sfs-client)： [SFS](https://link.jianshu.com/?t=https://github.com/cnpm/sfs)（Simple FIle Store）存储插件
- [qn-cnpm](https://link.jianshu.com/?t=https://github.com/cnpm/qn-cnpm)：七牛云存储插件
- [oss-cnpm](https://link.jianshu.com/?t=https://github.com/cnpm/oss-cnpm)：阿里云 OSS 存储插件

这些模块已经能够满足我们绝大部分的场景，如果你有特殊的需求，可以参看[nfs模块规范](https://www.v2ex.com/t/294255)进行定制化开发。这里拿阿里云 oss 存储作为示例。

首先在 cnpmjs.org 项目目录下安装`oss-cnpm`模块

```shell
cnpm i oss-cnpm
```

然后在云服务控制台 oss 管理中新增了一个 bucket 来存储 npm 包，也可以通过上传路径区分来复用其他 bucket，毕竟在公司中 bucket 资源一般还是比较紧张的。然后修改项目配置文件，将默认的`fs-cnpm`模块替换成`oss-cnpm`

```javascript
var oss = require("oss-cnpm");
var nfs = oss.create({
  accessKeyId: 'xxxx',
  accessKeySecret: 'xxx',
  endpoint: 'oss-cn-beijing.aliyuncs.com',
  bucket: 'catfly-xxx',
  mode: 'private',
})
var config = {
  ...,
  nfs:nfs,
  ...
}
```

重启项目，这个时候再发布或者同步资源的时候，服务器本地目录不会有新发布或同步的包了，在 oss 对应的 bucket 里面能找到刚刚发布或者同步的资源。

## Node版本管理工具

### n



### fnm

fnm是居于rust的node版本管理工具

https://github.com/Schniz/fnm



### nvm

## 常用方法

### sleep函数

阻塞主线程，

```javascript
function sleep(ms) {
  return new Promise(resolve => setTimeout(() => resolve(), ms));
}

await sleep(5000);

function sleep(ms) {
	var start = Date.now()
  expire = start + ms;
  while (Date.now() < expire){
    return;
  }
}
```



## 修改Node_modules源码

如果使用的`npm`包有bug，或者`npm`包只要再修改一点点就能满足自己的需求这样尴尬的情况。如果给包作者提需求，作者一般也不会马上给你修改，这时候就需要使用各种修改npm包源码的骚操作了

使用patch-package

`patch-package`是一个用来给其他`npm`包打补丁的包，实际原理也是在本工程保存一份修改的代码，只不过不是用全量代码的形式保存，而是保存了`git diff`的结果，节省了代码体积

安装

```shell
npm i -S patch-package
```

在node_modules下修改需要修改的包源码。修改时引用的是build/dist/源文件，确保修改生效

执行`npx patch-package 包名`, `patch-package`会将当前`node_modules`下的源码与原始源码进行`git diff`，并在项目根目录下生成一个patch补丁文件

后续只要执行`npx patch-package`命令，就会把项目patches目录下的补丁应用到node_modules的对应包中，这个执行时机一般可以设置为`postinstall`这个勾子

```json
"scripts": {
    "postinstall": "patch-package"
}
```

单文件修改

原理是先找到要修改的`npm`包的文件，先把这个文件拷贝一份到项目目录下，修改，然后只要想办法让这个文件最终被使用就行了

还是用`postinstall`这个勾子，在这个勾子执行`cp 修改过的文件 ./node_modules/包名/原始文件`拷贝过去，最终`node_modules`下的文件就变成了修改后的文件了
 例如：
 想修改`lodash`中的array方法，`array-hack.js`是被修改后的js文件，现在想用这个文件替换原始文件，只需在`package.json`加入

```json
"scripts": {
    "postinstall": "cp ./array-hack.js ./node_modules/lodash/array.js"
}
```



## Node运行原理

### 运行原理

Node.js 被分为了四层，分别是 `应用层`、`V8引擎层`、`Node API层` 和 `LIBUV层`。

应用层： 即 JavaScript 交互层，常见的就是 Node.js 的模块，比如 http，fs

V8引擎层： 即利用 V8 引擎来解析JavaScript 语法，进而和下层 API 交互

NodeAPI层： 为上层模块提供系统调用，一般是由 C 语言来实现，和操作系统进行交互 。

LIBUV层： 是跨平台的底层封装，实现了 事件循环、文件操作等，是 Node.js 实现异步的核心

### 事件循环

node事件循环与浏览器循环是不同的

当Node.js启动时会初始化`event loop`, 每一个`event loop`都会包含按如下顺序六个循环阶段：

1.**`timers` 阶段**: 这个阶段执行 `setTimeout(callback)` 和 `setInterval(callback)` 预定的 callback, timer指定一个下限时间而不是准确时间，在达到这个下限时间后执行回调。在指定时间过后，timers会尽可能早地执行回调，但系统调度或者其它回调的执行可能会延迟它们。

2.**`I/O callbacks` 阶段**: 此阶段执行某些系统操作的回调，例如TCP错误的类型。 例如，如果TCP套接字在尝试连接时收到 ECONNREFUSED，则某些* nix系统希望等待报告错误。 这将操作将等待在==I/O回调阶段==执行;

3.**`idle, prepare` 阶段**: 仅node内部使用;

4.**`poll` 阶段**: 

获取新的I/O事件, 例如操作读取文件等等，适当的条件下node将阻塞在这里;

如果 poll 队列不空，event loop会遍历队列并同步执行回调，直到队列清空或执行的回调数到达系统上限；

如果 poll 队列为空，则发生以下两件事之一：

如果代码已经被setImmediate()设定了回调, event loop将结束 poll 阶段进入 check 阶段来执行 check 队列（里面的回调 callback）。

如果代码没有被setImmediate()设定回调，event loop将阻塞在该阶段等待回调被加入 poll 队列，并立即执行。setImmediate() 实际上是一个特殊的timer，跑在event loop中一个独立的阶段。它使用`libuv`的API 来设定在 poll 阶段结束后立即执行回调。

5.**`check` 阶段**: 执行 `setImmediate()` 设定的callbacks，check阶段在poll阶段之后;

6.**`close callbacks` 阶段**: 比如 `socket.on(‘close’, callback)` 的callback会在这个阶段执行;如果一个 socket 或 handle 被突然关掉，close事件将在这个阶段被触发，否则将通过process.nextTick()触发

日常开发的绝大部分异步任务都在timers、poll、check这3个阶段处理的

### Node事件循环与浏览器事件循环的区别

在浏览器环境中，microtask任务队列是每个macrotask执行完之后执行，而在Nodejs中microtask在事件循环的各个阶段之间执行



### setimmediate与settimeout与next tick

两者非常相似，区别在于调用时机不同：

setimmediate设计在poll阶段完成时执行，即check阶段；

setTimeout设计在poll阶段为空闲时，且设定事件达到后执行，但它在timer阶段执行

但当二者在异步i/o callback内部调用时，总是先执行setimmediate，再执行setTimeout

```javascript
setTimeout(function(){
  console.log('timeout')
},0);

setImmediate(function() {
  console.log('immediate')
})
//setTimeout可能先执行也可能后执行
const fs = require('fs')

fs.readFile(_filename,()=>{
  setTimeout(function(){
    console.log('timeout')
  },0);

	setImmediate(function() {
    console.log('immediate')
  })
})
//setImmediate总是先于setTimeout
```

### process.nextTick

这个函数是独立于Event Loop之外的，有自己的队列，当每个阶段完成时，如果存在nextTick队列就清空队列中的所有回调函数，并且优先于其他microtask执行
