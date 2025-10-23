---
title: Golang语言开发（六）
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - Web,IT,Go
toc: true
thumbnail: https://s1.ax1x.com/2020/04/20/J1Iu4O.th.jpg
---

　　本篇主要内容为go镜像相关

<!--more-->

## goreleaser

打包go代码为二进制

https://github.com/goreleaser/goreleaser



## 编排工具

架构即代码，没有运行时，自动编排

实现多云管理的基础设施即代码的工具包括 `Terraform`、`Pulumi` 等等，`Terraform` 更为流行，使用更加广泛。在使用 `Terraform` 管理基础设施时，有一个最大的痛点：“配置语法太过简单，导致配置繁琐，需要额外地学习 `HasiCorp` 创造的表达式语言 `DSL-HCL`”。作为后起之秀，也许使用 `Pulumi` 能帮助我们解决这个问题

### Iac

2000 年之后，随着 IT 技术发展，用户的需求越来越复杂，软件系统以及基础设施也变的越来越复杂。最早的运维都是手工式的，面临着几个问题：

1. 交互式变更所引入的人的因素太大，导致了变更的不可控性
2. 基础设施变化越来越快，手工操作成本高且效率低
3. 交互式变更难以管控，且无法实现版本控制

IaC 就是在这个时期出现的想要解决这些问题的概念，[维基百科](https://en.wikipedia.org/wiki/Infrastructure_as_code)定义的 IaC 指通过 machine-readable 的定义文件，而不是物理硬件配置或交互式配置工具来管理和配置计算机数据中心的过程。该过程管理的 IT 基础设施包括物理设备（如 Bare-metal 或 VM），以及相关的配置资源。定义文件可能在版本控制系统 VCS 中，文件中代码可能使用脚本或者声明式定义，但 IaC 通常使用声明式方法管理基础设施。

IaC 有几个核心特征：

1. 最终产物为 machine-readable 的文件，可以是脚本、声明式代码或者配置文件。
2. 基于最终产物，可以利用 VCS（Git、SVN） 实现版本管理。
3. 利用 CI/CD 系统（如 Jenkins、GitLab CI）实现持续集成 / 持续交付。
4. 基于同样的定义文件，最终表现出来的行为是一致且幂等的。

这个时期出现了一些 IaC 工具，典型的如 Puppet、Chef、Ansible，实际上这些工具，可能设计上各有所取舍（比如 Pull/Push 模型的取舍），但是其核心的特征不会变化：

1. 框架内部提供了常见的比如 SSH 链接管理，多机并行执行，auto retry 等功能。
2. 基于上面描述的这一套基础功能，提供了一套 DSL 封装。让开发者更专注于 IaC 的逻辑，而非基础层面的细节。
3. 其开源开放，并形成了一套完善的插件机制。社区可以基于这一套提供更丰富的生态。比如 SDN 社区基于 Ansible 提供了各种交换机的 playbook；Ansible 官方提供的 [AWS playbook](https://docs.ansible.com/ansible/latest/collections/amazon/aws/index.html) 等。

云上资源编排

2006 年 8 月 Amazon 正式发布了 EC2 服务，从这时整个基础设施开始快步向 Cloud 时代迈进。截止目前，各家云厂商提供了各种各样的服务，经过十多年的演进，诞生出了诸如 IaaS，PaaS，DaaS，FaaS 等等各种各样的服务模式。这些服务模式，让我们的基础设施的构建，变得更加的简单，更加的快速。但是这些服务模式，也带来了一些问题：

1. 需要人工在云厂商控制台开通服务、创建资源，资源数量过多后难以管理：人工变更会引入不可控因素，且效率低，也无法实现版本控制，和最早基础设施管理面临的问题是类似的。
2. 云服务逐渐标准化，大部分企业出于成本考虑可能会使用多个云服务，多云场景下的资源管理更加复杂，仅依靠人工操作基本是不可行的。

云时代的，面向云资源管理的新型 IaC 工具的需求也愈发的迫切，这个时候，Terraform 这样的新型工具应运而生。Terraform 通过声明式定义描述 ECS、RDS、Redis、MQ 等多种基础设施，使资源编排变得十分简单，使用 Terraform 很容易就能定义一台 ECS 实例

同时随着各家 SaaS 的发展，研发人员也尝试着将这些 SaaS 服务也进行代码化 / 描述式配置化。以 Terraform 为例，我们可以通过 Terraform 的 Provider 来进行对接，比如 [GitLab Provider](https://registry.terraform.io/providers/gitlabhq/gitlab/latest/docs)、[GitHub Provider](https://registry.terraform.io/providers/integrations/github/latest/docs) 等等。

在 IaC 工具帮助我们完成基础设施描述的标准化之后，在此基础上能做更多有趣的事情：比如我们可以基于 [Infracost](https://www.infracost.io/) 来计算每次资源变更所带来的资源花费变更；利用 [atlantis](https://www.runatlantis.io/) 来基于 PR 实现 Terraform 流程自动化。

Terraform 已经能够解决多云资源编排场景下的绝大多数问题了，不过并不意味着 Terraform 在各个地方都是完美的，Terraform 也存在一些问题，使得其他资源编排工具有了存在的必要

https://www.zhenran.me/posts/terraform-pulumi-crossplane/

### terraform



### pulumi

Pulumi 是一个基础设施即代码（IaC，Infrastructure as Code）平台，支持使用如 Go、Java、Python、JavaScript、C# 等常见编程语言及相关工具，以实现云基础设施的构建、部署和管理

- Project：包含 Pulumi.yaml 配置文件的目录。可以通过 pulumi new 使用各个语言的模板创建新的 project。
- Stack：每个 Pulumi 程序都部署到一个 Stack。Stack 是 Pulumi 程序的一个相互隔离、可独立配置的实例，Stack 通常用于表示不同的开发阶段（如 dev、staging、production）或功能分支（如 feature-x-dev）。一个 Project 下可以有多个 Stack，当使用 pulumi new 创建 project 时，默认会创建一个 dev Stack
- Program：Project 中包含的资源编排代码以及运行程序的元数据信息。在 project 目录中执行 pulumi up 命令，pulumi 会创建一个隔离、可配置的 stack 实例，Stack 类似在测试和线上应用程序使用的不同部署环境。

https://www.pulumi.com/docs/clouds/aws/get-started/begin/

https://juejin.cn/post/7018373444928536584

```go
package main

import (
	appsv1 "github.com/pulumi/pulumi-kubernetes/sdk/v4/go/kubernetes/apps/v1"
	corev1 "github.com/pulumi/pulumi-kubernetes/sdk/v4/go/kubernetes/core/v1"
	metav1 "github.com/pulumi/pulumi-kubernetes/sdk/v4/go/kubernetes/meta/v1"
	"github.com/pulumi/pulumi/sdk/v3/go/pulumi"
)

package main

import (
	appsv1 "github.com/pulumi/pulumi-kubernetes/sdk/v4/go/kubernetes/apps/v1"
	corev1 "github.com/pulumi/pulumi-kubernetes/sdk/v4/go/kubernetes/core/v1"
	metav1 "github.com/pulumi/pulumi-kubernetes/sdk/v4/go/kubernetes/meta/v1"
	"github.com/pulumi/pulumi/sdk/v3/go/pulumi"
)

func main() {
	pulumi.Run(func(ctx *pulumi.Context) error {

		appLabels := pulumi.StringMap{
			"app": pulumi.String("nginx"),
		}
		deployment, err := appsv1.NewDeployment(ctx, "app-dep", &appsv1.DeploymentArgs{
			Spec: appsv1.DeploymentSpecArgs{
				Selector: &metav1.LabelSelectorArgs{
					MatchLabels: appLabels,
				},
				Replicas: pulumi.Int(1),
				Template: &corev1.PodTemplateSpecArgs{
					Metadata: &metav1.ObjectMetaArgs{
						Labels: appLabels,
					},
					Spec: &corev1.PodSpecArgs{
						Containers: corev1.ContainerArray{
							corev1.ContainerArgs{
								Name:  pulumi.String("nginx"),
								Image: pulumi.String("nginx"),
							}},
					},
				},
			},
		})
		if err != nil {
			return err
		}

		ctx.Export("name", deployment.Metadata.Name())

		return nil
	})
}
```

发布

