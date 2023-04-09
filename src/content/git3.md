---
title: Git使用技巧（三）
date: 2020-06-18 21:40:33
categories: 技术博客
tags:
    - IT，Github
thumbnail: http://cdn.kunkunzhang.top/git-logo.jpg
---

　　MonoRepo相关

<!--more-->

## Monorepo与Multirepo

Monorepo全称是monolithic repo，即单体式仓库， 适用于多个项目之间互有依赖的情况，将所有的相关项目都放在一个仓库里，比如react、Augular、bable、google以及最近放出的vue3等

Multirepo则比较常见，即常见的多仓库管理方式，按项目模块分为多个仓库，开源项目中有webpack、rollup使用这种管理方式

Multirepo的优缺点：

优点：

1. 各仓库自由度较高，可以自行选择构建工具，依赖管理、单元测试等配套工具
2. 各模块仓库体积不会太大

缺点：

1. 仓库分散不好管理，
2. 版本更新繁琐，尤其是基本组件库或者公共模块发生变化时，需要对所有的模块进行更新。一个模块改动时，可能需要去相关的板块进行修改再次提交
3. CHANGELOG梳理很麻烦，无法很好地关联各个模块的变动联系

Monorepo的优缺点：

优点：

1. 一个仓库维护多个模块，不用到处找仓库，提升团队效率
2. 方便版本管理和依赖管理。模块之间的引用、调试都非常方便，配合相应工具，可以一个命令搞定，降低基建成本
3. 方便统一生成changelog，

缺点：

1. 统一构建工具，对工具要求较高
2. 仓库体积会变大

实施方案：

从零开始定制一套完善的Monorepo工程化工具是一套很难的事情，可以基于社区进行构建

比较底层的方案比如lerna，封装了monorepo的依赖安装，脚本批量执行，等基本功能，但没有一套完整的构建、测试、部署工具链，因此整体monorepo的功能比较弱，但要用到项目当中，往往需要基于它进行顶层能力的封装，提供全面工程能力的支撑

也有一些集成的monorepo的方案，比如nx、rushstack



## Monorepo

随着业务的发展和团队的变化，业务型 Monorepo 中的项目会逐渐增加，极端一点的例子就是 Google 将整个公司的代码都放到一个仓库中，仓库的大小达到了 80TB。

业务型 Monorepo：不同于 lib 型 Monorepo（React、Vue3、Next.js 以及 Babel 等广义上的 packages）,业务型 Monorepo 将多个业务应用 App 及其依赖的公用组件库或工具库组织到了一个仓库中。 ——《Eden Monorepo 系列：浅析 Eden Monorepo 工程化建设》

项目数量的增加意味着在享受 Monorepo 优势的同时，也带来了巨大的挑战，优秀的 Monorepo 工具可以让开发者毫无负担的享受 Monorepo 的优势，而不好用的 Monorepo 工具可以让开发者痛不欲生，甚至让人怀疑 Monorepo 存在的意义。

### Monorepo的能力

MonoRepo除了具备最基本的代码共享能力之外，还应该具备三种能力：

- 依赖管理能力。随着依赖数量的增加，依旧能够保持依赖结构的正确性、稳定性以及安装效率。

- 任务编排能力。能够以最大的效率以及正确的顺序执行 Monorepo 内项目的任务（可以狭义理解为 npm scripts，如 build、test 以及 lint 等），且复杂度不会随着 Monorepo 内项目增多而增加。

- 版本发布能力。能够基于改动的项目，结合项目依赖关系，正确地进行版本号变更、CHANGELOG 生成以及项目发布。

一些流行工具的支持能力：

|                | 依赖管理   | 任务编排 | 版本管理 |
| -------------- | ---------- | -------- | -------- |
| Pnpm Workspace | ✅          | ✅        | ❌        |
| Rush           | ✅(by Pnpm) | ✅        | ✅        |
| Lage           | ❌          | ✅        | ❌        |
| Turborepo      | ❌          | ✅        | ❌        |
| Lerna          | ❌          | ✅        | ✅        |

[Pnpm](https://link.zhihu.com/?target=https%3A//pnpm.io/)：Pnpm 具备一定的任务编排能力 （`--filter` 参数），故此处也将其列入，同时作为 Package Manager，其自身更是大型 Monorepo 不可或缺的一部分。

[Rush](https://link.zhihu.com/?target=https%3A//rushjs.io/)：由微软开源的可扩展 Monorepo 管理方案，内置 PNPM 以及类 Changesets 发包方案，其插件机制是一大亮点，使得利用 Rush 内置能力实现自定义功能变得极为方便，迈出了 Rush 插件生态圈的第一步。

[Lage](https://link.zhihu.com/?target=https%3A//microsoft.github.io/lage/) ：同样由微软开源，**个人认为**是 Turborepo 的前身，Turborepo 是 Lage 的 Go 语言版本。Lage 自称为 "Monorepo Task Runner"，相较于 Turborepo 的 "High-Performance Build System" 内敛许多，Star 数也相差了一个数量级（Lage 300+，而 Turborepo 5k+），更多可查看该 [PR](https://link.zhihu.com/?target=https%3A//github.com/vercel/turborepo/pull/370)。在后文中 Lage 等同于 Turborepo。

[Lerna](https://link.zhihu.com/?target=https%3A//lerna.js.org/)：已经停止维护，故后续讨论不会将其纳入。

依赖管理过于底层，[版本控制](https://www.zhihu.com/search?q=版本控制&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2351290684})较为简单且已成熟，将这两项能力再做突破是比较困难的，实践中基本都是结合 Pnpm 以及 [Changesets](https://link.zhihu.com/?target=https%3A//github.com/changesets/changesets) 补全整体能力，甚至就干脆专精于一点，即任务编排，也就是 Lage 以及 Turborepo 的发力点。

Monorepo工具链：

1. Pnpm Workspace + Changesets：成本低，满足大多数场景
2. Pnpm Workspace + Changesets + Turborepo/Lage：在 1 的基础上增强任务编排能力
3. Rush：考虑全面，扩展性强

任务编排可以划分为三个步骤，各工具支持如下：

|                | 范围界定 | 并行执行 | 云端缓存 |
| -------------- | -------- | -------- | -------- |
| Pnpm           | ✅        | ✅        | ❌        |
| Rush           | ✅        | ✅        | ✅        |
| Turborepo/Lage | ✅        | ✅        | ✅        |

任务编排中三种能力如下

范围界定：按需执行子任务

该能力在日常开发中具有丰富的使用场景。

例如第一次拉取仓库，启动项目 app1 需要构建 Monorepo 内 app1 的前置依赖 package1 以及 package2。

而在 SCM 上打包项目 app1 时，需要构建 app1 自身以及 Monorepo 内 app1 的前置依赖 package1 以及 package2。

此时则应该根据需要筛选出需要构建的项目，而不应该引入与当前意图无关的项目构建。

在不同的 Monorepo 工具中，这一行为有着不同的称呼：

Rush 中称之为 [Selecting subsets of projects](https://link.zhihu.com/?target=https%3A//rushjs.io/pages/developer/selecting_subsets/)，选择项目子集，在本示例中应当使用如下命令

```shell
// 本地启动 app1 开发模式，app1 为依赖图的顶端，但不需要构建 app1 自身
$ rush build --to-except @monorepo/app1

// SCM 打包 app1，app1 为依赖图的顶端，且需要构建 @monorepo/app1 自身
$ rush build --to @monorepo/app1
```

Pnpm 中称之为 [Filtering](https://link.zhihu.com/?target=https%3A//pnpm.io/filtering)，即过滤，将命令限制于包的特定子集，在本示例中应当使用如下命令：

```shell
// 本地启动 app1 开发模式，app1 为依赖图的顶端，但不需要构建 app1 自身
$ pnpm build --filter @monorepo/app1^...

// SCM 打包 app1，app1 为依赖图的顶端，且需要构建 @monorepo/app1 自身
$ pnpm build --filter @monorepo/app1...
```

Turborepo/Lage 中称之为[Scoped Tasks](https://link.zhihu.com/?target=https%3A//turborepo.org/docs/features/scopes)，但目前（2022/02/13）这一能力过于局限，Vercel 团队正在设计一套与 Pnpm 基本一致的 filter 语法，详情参见 [RFC: New Task Filtering Syntax](https://link.zhihu.com/?target=https%3A//github.com/vercel/turborepo/discussions/105)

**并行执行：充分释放机器性能**

假设挑选出了 20 个子集任务，应该如何执行这 20 个任务来保证正确性以及效率呢？

Project 之间存在依赖关系，那么任务之间也存在依赖关系，以 build 任务为例，只有前置依赖构建完毕，才可构建当前项目。

**云端缓存：跨多环境复用缓存**

Rush 具备[增量构建](https://link.zhihu.com/?target=https%3A//rushjs.io/pages/advanced/incremental_builds/)的特性，使 rush build 能够跳过自上次构建以来**输入文件**（input files）没有变化的项目，配合第三方存储服务，可以达到跨多环境复用缓存的效果。

Rush 在 5.57.0 版本引入了[插件机制](https://link.zhihu.com/?target=https%3A//github.com/microsoft/rushstack/pull/2900) ，进而支持了第三方远端缓存能力（在此之前仅支持 azure 与 amazon），赋予了开发者实现基于企业内部服务的构建[缓存方案](https://www.zhihu.com/search?q=缓存方案&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2351290684})的能力。

落地到日常开发场景中，本地开发、CI 以及 SCM 各开发环节都能从中受益。



### pnpm workspace

`pnpm`跟`yarn/npm`一样是一款包管理工具，不同于`yarn/npm`的扁平化的依赖管理机制，`pnpm`采用软硬链接的机制实现依赖的管理和引用，不仅安装速度极快，高效利用磁盘空间，也解决了幽灵依赖的问题，而且`pnpm workplace` 提供了`monorepo`的支持，我们完全可以用`pnpm`替换`lerna+yarn`

初始化pnpm管理工具

```shell
pnpm init
```

新建`pnpm-workspace.yaml`文件，定义工作空间的根目录，并能够使您从工作空间中包含 / 排除目录 。 默认情况下，包含所有子目录

```yaml
packages: # 所有在 packages/ 子目录下的 package
- 'packages/**'
# 不包括在 test 文件夹下的 package
- '!**/test/**'
```

在`packages`目录下创建多个模块,比如charts、components、utils。

安装packages目录下所有模块公用的依赖包

如果想把它安装到根目录下，执行命令时需要增加`-w`标识（或者 `--workspace-root`）

```shell
pnpm install lodash -w
```

得益于`pnpm`的包管理机制，`node_modules`目录下只展示了`lodash`一个依赖包，`lodash`的相关依赖包完全没展示在其中，全都展示在了`.pnpm`里面，再通过软链接的形式指向真实的地址，简洁分明，避免了互相依赖的情况

安装局部包依赖：安装`packages`目录下每个包的依赖

```shell
cd packages/charts

pnpm install echarts
```

安装项目间依赖

`packages`目录下的模块之间相互引用

比如`charts`模块依赖`utils`模块，为了让依赖实时更新最新版本，使用用通配符更新版本

```shell
pnpm add utils@* --filter charts
```

执行完上述命令，就会在`charts`模块安装`utils`的依赖，如下图，我们可以在`package.json`中的`dependencies`看到`"utils": "workspace:*"`，这里的`workspace:*`匹配的就是本地包，`node_modules`目录下也生成了`utils`的软连接，`utils`包一更新，`charts`里引用`utils`的地方就会实时更新

workspace是局部依赖，`pnpm publish`会转成真实路径依赖

### changesets

https://toutiao.io/posts/wjwu1nn/preview

 lerna 这一套的发包方案，这套方案随之带来了不少问题

- ignoreChanges不能做到文件的完全忽略，存在优先级问题
- lerna version根据 commit 以及 tag 更新出来的包版本不符合预期
- 生成的 CHANGELOG 文件信息不完整
- lifecycle scripts经常命中一些用户自定义的 script(例如publish等)
- CI 中自动化发包场景需要很高的定制成本
- lerna 本身不支持 workspace 协议，导致基于 pnpm 开发的一些仓库无法使用

changesets 是用于管理版本及变更日志的工具，专注多包管理

`Changesets` 是一个用于 `monorepo` 项目下版本以及 `changelog` 文件管理的工具。管理`monorepo` 项目下子项目版本的更新、`changelog` 文件生成、包的发布

```shell
pnpm install @changesets/cli -w
```

初始化.执行命令 `pnpm changeset init`进行初始化，在项目根目录下生成一个 `.changeset` 目录，里面会生成一个 `.changeset` 的 `config.json` 文件

```shell
pnpm changeset init
```

根据自己的需求s修改config.json中的配置

changelog: changelog 生成方式

commit: 不要让 changeset 在 publish 的时候帮我们做 git add

linked: 配置哪些包要共享版本

access: 公私有安全设定，内网建议 restricted ，开源使用 public

baseBranch: 项目主分支

updateInternalDependencies: 确保某包依赖的包发生 upgrade，该包也要发生 version upgrade 的衡量单位（量级）

ignore: 不需要变动 version 的包

执行命令`pnpm changeset` 或 `pnpm changeset add`，该命令将询问一系列问题，首先是您要发布的包，然后是每个包的semver bump类型，然后是整个变更集的摘要。在最后一步，它将显示它将生成的变更集，并确认您要添加它

生成changelog

```shell
npx changeset version
```

1. 删除 changeset 文件
2. 更语义化新版本号
3. 生成 changelog
   1. 包含代码提交人
   2. commit 链接
   3. 变更摘要

发布

执行命令`pnpm changeset publish`进行包的发布

```shell
pnpm changeset publish
```

https://juejin.cn/post/7157634070615162893

配合github修改

添加changelog-github包

```json
devDependencies: {
	"@changesets/changelog-github": "^0.3.0",
	"@changesets/cli": "^2.19.0"
}
```

相关的github actions https://github.com/changesets/action

### rush

安装

```shell
npm install -g @microsoft/rush
```

常用命令

```shell
# 安装 NPM 包：
# (如果你没有配置 Github email, 那么加上 "--bypass-policy" 选项。)
$ rush update

# 增量安装：
$ rush update  # <-- 瞬时完成！

# 强制所有项目重新构建：
$ rush rebuild

# 增量构建：
$ rush build    # <-- 瞬时完成！

# 使用 "--verbose" 来展示每个项目在构建过程中的日志信息。
# 尽管项目是并行构建的，但是它们的日志是有序的。
$ rush rebuild --verbose
```

https://rushjs.io/zh-cn/pages/developer/tab_completion/

### lerna+yarn

Lerna 有两种管理项目的模式：

- Fixed/Locked 模式 (默认)： 所有的包共用一个版本号。
- Independent mode： 在初始化的时候指定 --independent 参数：

learn为我们提供了以下命令：

- lerna publish
- lerna version
- lerna bootstrap
- lerna list
- lerna changed
- lerna diff
- lerna exec
- lerna run
- lerna init
- lerna add
- lerna clean
- lerna import
- lerna link
- lerna create
- lerna info

https://juejin.cn/post/7033673456751214600

### nx

Nx 是一个用来构建 monorepos 的开发工具。自己从 Angular 6.X 版本的时候开始尝试使用 Nx 管理项目代码，那时候的 Nx 还只支持对 Angular 框架的扩展，现在的 Nx 添加了对 React 框架的支持，并且可以集成使用 Cypress, Jest, Prettier, TypeScript 等现在构建工具，支持 NestJs (一款 nodejs 后端框架)，完成了对整个全栈生态的覆盖。

创建一个空的 Nx workSpace 项目非常简单，你可以使用以下命令安装

- npx: `npx create-nx-workspace@latest todoapp`
- npm: `npm init nx-workspace todoapp`
- yarn: `yarn create nx-workspace todoapp`

Nx 支持 Angular， React 和 Nodejs，通过社区扩展也可以支持 Vue， Svelte 或者 Stencil。

前端已经有了那么多的构建工具了，为啥要用 Nx， Nx 的核心优势是啥？Nx 主要是为大型项目构建的，多个项目维护在一个代码库中， 实现项目之间的代码共享，及所谓的 Monorepo，添加了 Cypress, Jest 测试工具，Storybook，集成后端框架，Express，Nest，Next。 所有的项目都可以使用一个代码库管理，并且不会有心智上的负担，不用频繁地切换仓库，也不需要繁琐的配置，还可以在多个项目中共享基础的组件。

首先安装Nx Cli

```shell
npm install nx -g
```

创建react项目

先添加 React 工具

```shell
npm install -D @nrwl/react
```

创建项目

```shell
nx g @nrwl/react:application reactdemo
```

运行时在 start 命令后面加上项目名称就可以

```shell
npm run start reactdemo
```

有的时候前端项目需要在后端添加一个 Node，用来处理一些特殊的需求，比如 SSR，跨域，API 封装。在 Nx 中添加 Node 项目也很简单， 官方支持 Express 和 Nest。以 Nest 为例，添加 Nest 工具：

```shell
npm install -D @nrwl/nest
```

创建一个 Nest 项目，并设置代理：

```shell
nx generate @nrwl/nest:application nestapp --frontendProject demoapp
```

创建nextjs项目

```shell
npx create-nx-workspace@latest nx-nextjs-monorepo
```



### turborepo

Turborepo 是一个适用于 JavaScript 和 Typescript monorepo 的高性能构建工具，它不是一个侵入式的工具，你可以在项目中渐进的引入和使用它，它通过足够的封装度，使用一些简单的配置来达到高性能的项目构建。 和esbuild一样，Turborepo也是基于go实现的工具，在语言层面上就具有一定的性能优势。

与Lerna的区别

Lerna是现在常用的monorepo构建工具，它不仅能支持包任务的运行，也能很好的进行包的依赖和版本管理。

和Lerna比较，Turborepo有更好的任务调度机制，并且Lerna运行任务的时候是不会进行缓存的，所以在缓存方面Turborepo也有很大的优势。

对于包的publish以及version的更新，Turborepo还没有进行实现，所以在现阶段可以一起使用Lerna和Turborepo，让他们各司其职

当多人开个一个项目的时候，团队的成员可以共享构建的缓存，从而加快项目的构建速度。

当一个成员把某个分支构建的缓存文件推送到远程的git仓库是，另一个成员如果在同一个分支上进行开发，那么Turborepo 可以支持你去选择某个成员的构建缓存，并在运行相关的构建任务时，从远端拉去缓存文件到本地，加快构建的速度

开始使用

```shell
npm install turbo -D
```

在 package.json 中增加 Turborepo 的配置项

```json
// package.json
{
     "turbo": {    
     }
}
```

在turbo中，管道(pipeline)是一个核心的概念，Turborepo也是通过管道来处理各个任务和他们的依赖关系的。

在传统的monorepo仓库中，比如使用了lerna或者yarn的workspace进行管理，每个npm包的script(如build或者test)，都是依赖执行或者独立并行的执行。如果一个命令存在包的依赖关系，那么在执行的时候，CPU的核心可能会被闲置，这样会导致计算性能和时间上的浪费。

Turborepo提供了一种声明式的方法来指定各个任务之间的关系，这种方式能够更容易理解各个任务之间的关系，并且Turborepo也能通过这种显式的声明来优化任务的执行并充分调度CPU的多核心性能

配置pipeline

pipeline中每个键名都可以通过运行`turbo run`来执行，并且可以使用`dependsOn`来执行当前管道的依赖项。

```json
{
    "turbo": {
        "pipeline": {
            "build": {
                "dependsOn": ["^build"],           
            },
            "test": {
                "dependsOn": ["build"],
                "outputs": []                            
            },
            "lint": {
                "outputs": []
            },
            "deploy": {
                "dependsOn": ["build", "test", "lint"]           
            }
        }
    }
}
```

#### 依赖

通过`dependsOn`的配置，可以看出各个命令的执行顺序：

- 因为A和C依赖于B，所以包的构建存在依赖关系，根据build的dependsOn配置，会先执行依赖项的build命令，依赖项执行完后才会执行自己的build命令。从上面的瀑布流中也可以看出，B的build先执行，执行完以后A和C的build会并行执行
- 对于test，只依赖自己的build命令，只要自己的build命令完成了，就立即执行test
- lint没有任何依赖，在任何时间都可以执行
- 自己完成build、test、lint后，再执行deploy命令

常规依赖

如果一个任务的执行，只依赖自己包其他的任务，那么可以把依赖的任务放在dependsOn数组里

```json
{
    "turbo": {
        "pipeline": {
            "deploy": {
                "dependsOn": ["build", "test", "lint"]           
            } 
        }    
    }
}
```

拓扑依赖

可以通过`^`符号来显式声明该任务具有拓扑依赖性，需要依赖的包执行完相应的任务后才能开始执行自己的任务

```json
{
    "turbo": {
        "pipeline": {
            "build": {
                "dependsOn": ["^build"],           
            }
        }    
    }
}
```

空依赖

如果一个任务的dependsOn为`undefined`或者`[]`，那么表明这个任务可以在任意时间被执行

```json
{
    "turbo": {
        "pipeline": {
            "lint": {
                "outputs": []
            }, 
        }    
    }
}
```

特定依赖

在一些场景下，一个任务可能会依赖某个包的特定的任务，这时候我们需要去手动指定依赖关系。

```json
{
    "turbo": {
        "pipeline": {
            "build": {
                "dependsOn": ["^build"],           
            },
            "test": {
                "dependsOn": ["build"],
                "outputs": []                            
            },
            "lint": {
                "outputs": []
            },
            "deploy": {
                "dependsOn": ["build", "test", "lint"]           
            },
            "frontend#deploy": {
                "dependsOn": ["ui#test", "backend#deploy"]            
            }
        }    
    }
}
```

#### Inputs

默认为`[]`。告诉turbo在确定特定任务的包是否已更改时要考虑的文件集。将其设置为文件输入地址将导致仅当与这些真正子包中需要配置输入匹配的文件发生更改时才重新运行任务。例如，如果您想跳过运行测试，除非源文件发生更改，这会很有帮助。

指定`[]`意味着任务在任何文件发生更改时重新运行

```json
{
  "$schema": "https://turborepo.org/schema.json",
  "pipeline": {
   "test": {
      // A package's `test` task should only be rerun when
      // either a `.tsx` or `.ts` file has changed.
      "inputs": ["src/**/*.tsx", "src/**/*.ts", "test/**/*.ts"]
    }
  }
}
```

#### OutputMode

`outputMode`代表输出的模式类型是字符串

`full` 也是默认值代表 显示任务的整个输出

`hash-only`仅显示计算的任务哈希

`new-only`显示缓存未命中的完整输出和缓存命中的计算哈希值。意思就是返回带有hash的日志并且当如果有未命中的子包缓存或者打包错误导致缓存未命中再次打包时会输出上一次缓存未命中的的子包完整任务输出日志

`none` 使用“none”隐藏任务输出。意思就是不会在控制台中打印我们拓扑顺序以及打包输入的日志，但是依然会正确执行 `build` 命令

#### Pipeline缓存

`cache` 表示是否缓存，通常我们执行 `dev` 命令的时候会结合 `watch` 模式，所以我们一般在项目启动模式下不需要开启 `turbo` 缓存机制

```json
{
  "$schema": "https://turborepo.org/schema.json",
  "pipeline": {
    "dev": {
      "cache": false
    }
  }
}
```

#### Output

`outputs` 表示命令执行输出的文件缓存目录

默认值为`["dist/**", "build/**"]`

我们还可以通过传递一个空数组用来告诉`turbo`任务是一个副作用，这样我们不会输入任何文件

```json
"pipeline": {
  "build": {
    // "Cache all files emitted to package's dist/** or .next
    // directories by a `build` task"
    "outputs": ["dist/**", ".next/**"],
    "dependsOn": ["^build"]
  },
 }
```

#### 远程缓存

正常情况下 我们使用turbo 的时候在构建过程中只能将我们的任务缓存在本地系统上，turbo支持了一种多人开发共享缓存的模式

开发人员团队和/或持续集成 (CI) 系统使用远程缓存来共享构建输出。如果您的构建是可重现的，那么一台机器的输出可以安全地在另一台机器上重复使用，这可以显着加快构建速度。

如果要将本地 turborepo 链接到远程缓存，请首先使用 Vercel 帐户对 Turborepo CLI 进行身份验证

```shell
npx turbo login
```

#### turbo带来的启发

为什么要turborepo使用rust而不是go开发：https://vercel.com/blog/turborepo-migration-go-rust



### pnpm替换lerna+yarn

1.全局安装pnpm

```shell
 npm install -g pnpm
```

2.全局建一个pnpm-workspace.yaml

```yaml
packages: # 所有在 packages/ 子目录下的 package
- 'packages/**'
```

3.删除项目中已有的`lerna.json`, 用`pnpm`的 workspace 来替代`lerna`实现`monorepo`的包管理方式

4.执行`pnpm install`安装项目需要的依赖（安装过程中可能出现卡住的情况，可能是因为在内网环境下源不稳定重新安装即可） 安装成功后如果是Windows环境会在你当前项目所在硬盘的根目录下新增了一个`.pnpm-store`文件夹，刚刚装的依赖文件都在里面

5.把项目中的`package-lock.json`和 `yarn.lock`删掉，所有锁都由 `pnpm-lock.yaml`来提供，至此`pnpm`的替换工作结束

6.替换掉脚本命令，与`yarn`相关的命令替换为: pnpm  或者 pnpm run 

7.调整 `pipeline`、以及`Dockfile`或者其他`CI/CD`配置文件里面的依赖安装命令

只允许pnpm

```json
{
  "scripts": {
    "preinstall": "npx only-allow pnpm"
  }
}
```

**[preinstall](https://link.zhihu.com/?target=https%3A//docs.npmjs.com/cli/v6/using-npm/scripts%23pre--post-scripts)** 脚本会在 `install` 之前执行，现在，只要有人运行 `npm install` 或 `yarn install`，就会调用 **[only-allow](https://link.zhihu.com/?target=https%3A//github.com/pnpm/only-allow)** 去限制只允许使用 `pnpm` 安装依赖。

从package-lock.json或者yarn.lock文件生成pnpm-lock.yaml

使用`pnpm import`命令



### 相关文章

知乎字节巨老，有很多monorepo的文章/回答：https://www.zhihu.com/people/zhao-qing-90-84/posts

https://github.com/worldzhao/blog/issues/11
