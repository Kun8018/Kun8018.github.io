---
title: 前端工程化（八）-CI/CD工具
date: 2020-12-15 21:40:33
categories: IT
tags:
    - IT，Web,数据库
toc: true
thumbnail: https://cdn.kunkunzhang.top/babel-js.png
---

　　前端CI/CD工具

<!--more-->

## 自动化部署与负载均衡

### pm2

pm2是node进程管理工具，利用它进行node应用管理的性能监控、自动重启、负载均衡等。

全局安装

```shell
npm install -g pm2
```

使用pm2控制进程

```shell
pm2 start app.js  //启动进程pm2 restart app.js   //重启进程pm2 list   //获取当前应用等名字/进程idpm2 stop app_name|app_id  //停止进程应用pm2 delete app_name|app_id  //删除特定进程应用pm2 stop all //停止所有应用
```

其他命令行参数

```shell
pm2 start app.js -i max //根据有效CPU数启动最大进程数目pm2 start app.js -i 3 //启动3个进程pm2 start app.js -n name //启动进程时指定进程名字namepm2 start app.js --watch //监听应用目录的变化，一旦发生变化就自动重启pm2 monit  //查看当前pm2的运行进程的状态pm2 start big-array.js --max-memory-restart 20M  //超过内存上限后自动重启
```

配置启动环境

在node中指定启动环境（开发环境、生产环境等）

```javascript
"env":{  "NODE_ENV":"production",  "REMOTE_ADDR":"http://www.example.com/"},"env_dev":{  "NODE_ENV":"development",   "REMOTE_ADDR":"http://wdev.example.com/"}"env_test":{  "NODE_ENV":"test",  "REMOTE_ADDR":"http://wtest.example.com/"}
```

启动时设置环境

```shell
pm2 start app.js --env dev
```

pm2支持线上系统和第三方扩展，如常用的log、rotate，



### jenkins

jenkins是基于java和docker的自动化部署和管理工具

安装之前确保电脑安装java(java 1.8)和docker

mac安装

使用brew工具安装和启动

```shell

```

#### 流水线

Jenkins 流水线 (或简单的带有大写"P"的"Pipeline") 是一套插件，它支持实现和集成 *continuous delivery pipelines* 到Jenkins。

`Jenkinsfile` 能使用两种语法进行编写 - 声明式和脚本化。

声明式和脚本化的流水线从根本上是不同的。 声明式流水线的是 Jenkins 流水线更近的特性:

- 相比脚本化的流水线语法，它提供更丰富的语法特性,
- 是为了使编写和读取流水线代码更容易而设计的。

然而，写到`Jenkinsfile`中的许多单独的语法组件(或者 "步骤"), 通常都是声明式和脚本化相结合的流水线。

本质上，Jenkins 是一个自动化引擎，它支持许多自动模式。 流水线向Jenkins中添加了一组强大的工具, 支持用例 简单的持续集成到全面的CD流水线。通过对一系列的相关任务进行建模, 用户可以利用流水线的很多特性:

- **Code**: 流水线是在代码中实现的，通常会检查到源代码控制, 使团队有编辑, 审查和迭代他们的交付流水线的能力。
- **Durable**: 流水线可以从Jenkins的主分支的计划内和计划外的重启中存活下来。
- **Pausable**: 流水线可以有选择的停止或等待人工输入或批准，然后才能继续运行流水线。
- **Versatile**: 流水线支持复杂的现实世界的 CD 需求, 包括fork/join, 循环, 并行执行工作的能力。
- **Extensible**:流水线插件支持扩展到它的DSL [[1](https://www.jenkins.io/zh/doc/book/pipeline/#_footnotedef_1)]的惯例和与其他插件集成的多个选项。

然而， Jenkins一直允许以将自由式工作链接到一起的初级形式来执行顺序任务, [[4](https://www.jenkins.io/zh/doc/book/pipeline/#_footnotedef_4)] 流水线使这个概念成为了Jenkins的头等公民。

流水线是用户定义的一个CD流水线模型 。流水线的代码定义了整个的构建过程, 他通常包括构建, 测试和交付应用程序的阶段 。

节点是一个机器 ，它是Jenkins环境的一部分 and is capable of执行流水线。

`stage` 块定义了在整个流水线的执行任务的概念性地不同的的子集(比如 "Build", "Test" 和 "Deploy" 阶段), 它被许多插件用于可视化 或Jenkins流水线目前的 状态/进展. [[6](https://www.jenkins.io/zh/doc/book/pipeline/#_footnotedef_6)]

本质上 ，一个单一的任务, a step 告诉Jenkins 在特定的时间点要做_what_ (或过程中的 "step")。 举个例子,要执行shell命令 ，请使用 `sh` 步骤: `sh 'make'`。当一个插件扩展了流水线DSL, [[1](https://www.jenkins.io/zh/doc/book/pipeline/#_footnotedef_1)] 通常意味着插件已经实现了一个新的 *step*。

在声明式流水线语法中, `pipeline` 块定义了整个流水线中完成的所有的工作。

```jenkinsfile
pipeline {
    agent any 
    stages {
        stage('Build') { 
            steps {
                // 
            }
        }
        stage('Test') { 
            steps {
                // 
            }
        }
        stage('Deploy') { 
            steps {
                // 
            }
        }
    }
}
```

在脚本化流水线语法中, 一个或多个 `node` 块在整个流水线中执行核心工作。 虽然这不是脚本化流水线语法的强制性要求, 但它限制了你的流水线的在`node`块内的工作做两件事:

1. 通过在Jenkins队列中添加一个项来调度块中包含的步骤。 节点上的执行器一空闲, 该步骤就会运行。
2. 创建一个工作区(特定为特定流水间建立的目录)，其中工作可以在从源代码控制检出的文件上完成。
   **Caution:** 根据你的 Jenkins 配置,在一系列的空闲后，一些工作区可能不会自动清理 。

```jenkins
node {  
    stage('Build') { 
        // 
    }
    stage('Test') { 
        // 
    }
    stage('Deploy') { 
        // 
    }
}
```

#### step

Pipelines 由多个步骤（step）组成，允许你构建、测试和部署应用。 Jenkins Pipeline 允许您使用一种简单的方式组合多个步骤， 以帮助您实现多种类型的自动化构建过程。

可以把“步骤（step）”看作一个执行单一动作的单一的命令。 当一个步骤运行成功时继续运行下一个步骤。 当任何一个步骤执行失败时，Pipeline 的执行结果也为失败。

当所有的步骤都执行完成并且为成功时，Pipeline 的执行结果为成功。

在 Linux、BSD 和 Mac OS（类 Unix ) 系统中的 shell 命令， 对应于 Pipeline 中的一个 `sh` 步骤（step）。

```shell
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
    }
}
```

 Windows 的系统使用 `bat` 步骤表示执行批处理命令。

```shell
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                bat 'set'
            }
        }
    }
}
```

Jenkins Pipeline 提供了很多的步骤（step），这些步骤可以相互组合嵌套，方便地解决像重复执行步骤直到成功（重试）和如果一个步骤执行花费的时间太长则退出（超时）等问题。

```shell
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                retry(3) {
                    sh './flakey-deploy.sh'
                }

                timeout(time: 3, unit: 'MINUTES') {
                    sh './health-check.sh'
                }
            }
        }
    }
}
```

完成时动作

当 Pipeline 运行完成时，你可能需要做一些清理工作或者基于 Pipeline 的运行结果执行不同的操作， 这些操作可以放在 `post` 部分。

```shell
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'echo "Fail!"; exit 1'
            }
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
```

环境变量

环境变量可以像下面的示例设置为全局的，也可以是阶段（stage）级别的。 如你所想，阶段（stage）级别的环境变量只能在定义变量的阶段（stage）使用。

```shell
pipeline {
    agent any

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }

    stages {
        stage('Build') {
            steps {
                sh 'printenv'
            }
        }
    }
}
```

通过 `archiveArtifacts` 步骤和文件匹配表达式可以很容易的完成构建结果记录和存储，

```shell
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh './gradlew build'
            }
        }
        stage('Test') {
            steps {
                sh './gradlew check'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
            junit 'build/reports/**/*.xml'
        }
    }
}
```

如果在 `archiveArtifacts` 步骤中指定了多个参数， 那么每个参数的名称必须在步骤代码中明确指定， 即文件的路径、文件名和 `fingerprint` 三个参数。 如果您只需指定文件的路径和文件名， 那么你可以省略参数名称 `artifacts` ，例如： `archiveArtifacts 'build/libs/**/*.jar'`



回放



#### 发布后通知企微

1.配置企业微信

- 进入某个群 -> 右击群标签，添加群机器人
  注意：企业微信貌似没有像钉钉那样设置配置群机器人权限，因此任何人都能添加机器人。相对于钉钉有点落后
- 填写名称，创建成功后复制hook地址

2.在jenkins中进入系统管理-管理插件

搜索插件Qy Wechat Notification，安装这个插件

然后配置Jenkins的Webhook

- 进入 Jenkins Job，进入 Job 配置， 前往`构建后的操作` -> 选择`企业微信通知`

设置Webhook，把企微中的地址填入



### Circle CI

circle CI可以与github action串接

参考rocksdb ci流程



### travis



### Dagger

创建并初始化项目

```shell
$ mkdir rootProject && cd rootProject

$ dagger project init
```

更新依赖

```shell
$ dagger project update
```

创建main.cue文件

```cue
package main

import (
    "dagger.io/dagger"
)

dagger.#Plan & {
    actions: {
        hello: #Run & {
            script: contents: "echo \"Hello!\""
        }
    }
}
```

然后创建另一个文件夹

```shell
$ cd .. && mkdir personal && cd personal && git init
```

创建一个新的main.cue

```cue
package personal

import(
  "universe.dagger.io/alpine"
  "universe.dagger.io/bash"
)

#Run: {
    _img: alpine.#Build & {
        packages: bash: _
    }

    bash.#Run & {
        always: true
        input:  _img.output
    }
}
```



#### Cue

Cue是json的超集，可以编译成json或者yaml文件



### keephq

https://www.keephq.dev/blog?page=all-blog



## 前端监控

### Sentry

sentry是一个开源的错误追踪工具，可以帮助开发人员实时监控和修复系统中的错误。其专注于错误监控以及提取一切事后处理所需的信息，支持几乎所有主流开发语言（JS/Java/Python/php）和平台，并提供了web来展示错误。

[sentry.io](https://sentry.io) [docs.sentry.io/platforms](https://docs.sentry.io/platforms/)



官方推荐使用docker或者python安装

在前端项目中使用

```shell
npm install @sentry/browser @sentry/integrations
```

初始化

```javascript
import * as Sentry from '@sentry/browser'
import * as Intergrations from '@sentry/integrations'
process.env.NODE_ENV === "production" && 
 Sentry.init ({  
  dsn: 'https://e028cb7b8dd645978cf5d84a@sentry.io/18726',  
  integrations: [new Integrations.Vue{}],
 })
```

使用

```javascript
import * as Sentry from '@sentry/browser';

// Set user information, as well as tags and further extras
Sentry.configureScope(scope => {
  scope.setExtra('battery', 0.7);
  scope.setTag('user_mode', 'admin');
  scope.setUser({ id: '4711' });
  // scope.clear();
});

// Add a breadcrumb for future events
Sentry.addBreadcrumb({
  message: 'My Breadcrumb',
  // ...
});

// Capture exceptions, messages or manual events
Sentry.captureMessage('Hello, world!');
Sentry.captureException(new Error('Good bye'));
Sentry.captureEvent({
  message: 'Manual',
  stacktrace: [
    // ...
  ],
});
```

### Webfunny





## CD平台

### render.com



### Nitro



### argoCD

面向k8s的CD平台

创建

```shell
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```



### tekton





### zadig

https://github.com/koderover/zadig

Zadig 是 KodeRover 公司基于 Kubernetes 自主设计、研发的开源分布式持续交付 (Continuous Delivery) 产品，具备灵活易用的高并发工作流、面向开发者的云原生环境、高效协同的测试管理、强大免运维的模板库、客观精确的效能洞察以及云原生 IDE 插件等重要特性，为工程师提供统一的协作平面。Zadig 内置了 K8s YAML、Helm Chart、主机等复杂场景最佳实践，适用大规模微服务、高频高质量交付等场景。我们的目标是通过云原生技术的运用和工程产品赋能，打造极致、高效、愉悦的开发者工作体验，让工程师成为企业创新的核心引擎

文档：https://docs.koderover.com/zadig/Zadig%20v1.17.0/install/helm-deploy/#%E5%89%8D%E7%BD%AE%E6%9D%A1%E4%BB%B6



### Spinnaker

https://spinnaker.io/docs/concepts/



### Flux

https://github.com/fluxcd/flux2

https://moelove.info/2021/12/18/GitOps-%E5%BA%94%E7%94%A8%E5%AE%9E%E8%B7%B5%E7%B3%BB%E5%88%97-Flux-CD-%E5%8F%8A%E5%85%B6%E6%A0%B8%E5%BF%83%E7%BB%84%E4%BB%B6/

- 2016 年 10 月 28 日，[Flux single-user service](https://github.com/fluxcd/flux/releases/tag/pre-split) 版本发布。

它奠定了 flux 的两个基调：

- 集中式运行的服务

- 以守护进程的方式，在自动模式下运行在 k8s 集群中




### dpxdt



### n8n

https://github.com/n8n-io/n8n



## SonarQuebe

扫描代码。检查代码

可以报错

