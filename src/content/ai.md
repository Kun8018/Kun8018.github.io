---
title: 人工智能
date: 2020-03-02 21:40:33
categories: IT
tags:
    - 
toc: true
thumbnail: 
---

<!--more-->

## 强化学习  svm



## Tensor flow

https://tensorflow.google.cn/

https://github.com/tensorflow/tensorflow



### js

tensorflow不同的训练模型

```shell
npm i @tensorflow/tfjs

npm i @tensorflow-models/mobilenet

npm i @tensorflow-models/hand-pose-detection

npm i @tensorflow-models/pose-detection

npm i @tensorflow-models/coco-ssd

npm i @tensorflow-models/deeplab

npm i @tensorflow-models/face-landmarks-detection

npm i @tensorflow-models/speech-commands

npm i @tensorflow-models/universal-sentence-encoder

npm i @tensorflow-models/toxicity

npm i @tensorflow-models/depth-estimation

npm i @tensorflow-models/knn-classifier
```

https://github.com/tensorflow/tfjs-models



## 推荐算法



## gradio

部署ai 程序 

https://www.gradio.app/



## 理论

### 大模型理论

https://github.com/ZJU-LLMs/Foundations-of-LLMs/blob/main/%E3%80%8A%E5%A4%A7%E6%A8%A1%E5%9E%8B%E5%9F%BA%E7%A1%80%E3%80%8B%E6%95%99%E6%9D%90/%E3%80%8A%E5%A4%A7%E6%A8%A1%E5%9E%8B%E5%9F%BA%E7%A1%80%E3%80%8B%E5%88%86%E7%AB%A0%E8%8A%82%E5%86%85%E5%AE%B9/%E7%AC%AC1%E7%AB%A0%20%E8%AF%AD%E8%A8%80%E6%A8%A1%E5%9E%8B%E5%9F%BA%E7%A1%80.pdf



## AI 工具

### kimi

#### ppt



### librachat

https://www.librechat.ai/docs



### claude

Claude 3.7 Sonnet

#### Claude code

Claude Code 是 Anthropic 官方推出的命令行 AI 编程助手，它不是传统的 AI IDE，而是一个革命性的智能编程工具。与 Cursor 等 AI 编程工具不同，Claude Code 采用代理式开发模式，让 AI 从"辅助工具"升级为"开发团队"。

#### 核心优势

- **代理式开发**：AI 自主完成规划和执行，你扮演项目经理角色
- **原生 Claude 模型**：直接使用最强大的 Claude 4 Sonnet/Opus 模型（也可以替换为国产大模型，例如GLM4.5或者KIMI K2等）
- **无限工具调用**：复杂任务不会被强制中断
- **超大上下文**：200K+ 上下文窗口，深入理解大型项目
- **完全自主**：真正的 Agent 能力，可独立完成复杂任务

安装

```Bash
npm install -g @anthropic-ai/claude-code
```

#### 启动 Claude Code

bash

```Bash
# 在项目目录中启动cd your-project
claude

# 直接提问
claude "帮我分析这个项目"# 单次执行模式
claude -p "运行测试并生成报告"# 继续上次对话
claude -c# 恢复特定会话
claude -r "session-id"
```



#### claude-code-spec-workflow

https://github.com/pimzino/claude-code-spec-workflow#readme



#### claude code Router

https://github.com/musistudio/claude-code-router?tab=readme-ov-file

安装

```shell
npm install -g @anthropic-ai/claude-code

npm install -g @musistudio/claude-code-router
```

`config.json` 文件有几个关键部分：

- **`PROXY_URL`** (可选): 您可以为 API 请求设置代理，例如：`"PROXY_URL": "http://127.0.0.1:7890"`。

- **`LOG`** (可选): 您可以通过将其设置为 `true` 来启用日志记录。当设置为 `false` 时，将不会创建日志文件。默认值为 `true`。

- **`LOG_LEVEL`** (可选): 设置日志级别。可用选项包括：`"fatal"`、`"error"`、`"warn"`、`"info"`、`"debug"`、`"trace"`。默认值为 `"debug"`。

- 日志系统

  : Claude Code Router 使用两个独立的日志系统：

  - **服务器级别日志**: HTTP 请求、API 调用和服务器事件使用 pino 记录在 `~/.claude-code-router/logs/` 目录中，文件名类似于 `ccr-*.log`
  - **应用程序级别日志**: 路由决策和业务逻辑事件记录在 `~/.claude-code-router/claude-code-router.log` 文件中

- **`APIKEY`** (可选): 您可以设置一个密钥来进行身份验证。设置后，客户端请求必须在 `Authorization` 请求头 (例如, `Bearer your-secret-key`) 或 `x-api-key` 请求头中提供此密钥。例如：`"APIKEY": "your-secret-key"`。

- **`HOST`** (可选): 您可以设置服务的主机地址。如果未设置 `APIKEY`，出于安全考虑，主机地址将强制设置为 `127.0.0.1`，以防止未经授权的访问。例如：`"HOST": "0.0.0.0"`。

- **`NON_INTERACTIVE_MODE`** (可选): 当设置为 `true` 时，启用与非交互式环境（如 GitHub Actions、Docker 容器或其他 CI/CD 系统）的兼容性。这会设置适当的环境变量（`CI=true`、`FORCE_COLOR=0` 等）并配置 stdin 处理，以防止进程在自动化环境中挂起。例如：`"NON_INTERACTIVE_MODE": true`。

- **`Providers`**: 用于配置不同的模型提供商。

- **`Router`**: 用于设置路由规则。`default` 指定默认模型，如果未配置其他路由，则该模型将用于所有请求。

- **`API_TIMEOUT_MS`**: API 请求超时时间，单位为毫秒。



#### skills

简单来说，技能就是怎么做；MCP 是有什么工具、有什么功能。

技能的话，主要是经验、最佳实践、流程的封装，而 MCP 是连接与交互的协议，主要是 API 调用、数据读写和工具等。

Skills主要是 Markdown 文件和一些脚本文件，优势在于渐进式加载，不需要服务器资源，适用性好；MCP 主要是客户端和服务端的架构，启动时加载所有工具定义，集成外部功能，Tokens 消耗更高，使用起来更复杂。

两者是互补的关系，Agent 可以通过 Skills 获取知识，通过 MCP 拓展功能

https://github.com/anthropics/skills

react skill https://github.com/vercel-labs/agent-skills/tree/main

open skills https://github.com/numman-ali/openskills

### cursor

Cursor 能解决非常多基础问题，包括我自己也已经习惯了使用 Cursor 替代 VS Code 完成日常工作。它很好用，但并不神秘，**本质上就是在传统 IDE 基础上，搭配足够好的交互与足够好的 LLM**，从而超越传统 IDE。交互方面，它在 VS Code 基础上，补充提供了：

- 更适配 LLM 场景的上下文引用能力(即 `@codebase/@files` 等 symbol 指令)；
- 适合复杂编程的 Composer 面板，可以在此与 LLM 保持一个较长时间的回话，并且支持多文件编辑，可以在这里持续沟通，持续生成代码，完成复杂任务；
- 提供了几乎毫无门槛的代码自动补全能力，并且支持多行编辑，这在一些场景，如修改变量名时，非常好用；
- 甚至还贴心的支持在 Terminal 中唤醒 LLM 交交互面板，实现命令生成、命令行错误处理等能力

#### composer

Cursor Chat 帮助你搜索和理解代码。使用它来探索你的代码库、提问和获取解释。你可以使用 `⌘⏎` 搜索代码。

Composer 帮助你编写和编辑代码。它提供了一个工作区，你可以在其中生成新代码并直接将更改应用到你的文件中。



#### cursor-help

重置cursor试用期，一个go脚本

https://github.com/yuaotian/go-cursor-help?tab=readme-ov-file#-chinese



#### cursor_machine_id

一个用于修改 Cursor 编辑器设备 ID 的跨平台工具集。当遇到设备 ID 锁定问题时，可用于重置设备标识。

https://github.com/fly8888/cursor_machine_id

一个python脚本

#### cursor使用指南

https://cursor.directory/

https://cursor.document.top/tips/usage/set-rules/



### bolt



### cline

Cline 是一款开源的 AI 助手插件，深度集成在 VS Code 中。它借助 Claude 3.5 Sonnet 等模型的能力，实现复杂的软件开发任务。**Cline 支持多种 API 提供商和模型，包括 OpenRouter、Anthropic、Google Gemini、DeepSeek V3 等，开发者可自由选择调用远程或本地模型，具有高度的灵活性。**其独特的「人类监督」机制确保了代码和命令的安全性，为开发者提供了可靠的编程环境

**代码创建与编辑功能强大：**通过深入分析项目的文件结构和语法树（AST），Cline 能够快速理解项目并提供高效的代码补全建议，同时还能自动修复语法错误，大大提高了编码效率。例如，在编写 Python 脚本时，它可以根据上下文准确地补全函数名、变量名等，减少手动输入的错误。

**命令行集成高效便捷：**Cline 获得用户授权后，可直接在终端执行命令，实时监控输出并据此灵活调整操作。比如在安装项目依赖包时，它能自动执行pip install命令，并实时反馈安装进度和结果，让开发者无需手动在终端输入命令，节省时间和精力。

**浏览器操作功能实用：**能够启动无头浏览器，模拟用户在浏览器中的操作，如点击、输入、滚动等，并可以捕获页面截图和控制台日志。这在 Web 开发中非常有用，例如可以帮助开发者快速定位和修复页面布局问题、JavaScript 错误等。

**支持 MCP 协议扩展能力：**借助 Model Context Protocol（MCP），Cline 能够动态扩展自身功能，创建各种自定义工具。例如，开发者可以通过简单的指令让 Cline 创建一个用于获取特定网站数据的工具，然后将其集成到工作流程中，满足个性化的开发需求。

**多模型支持灵活多样**：除了 Claude 3.5 Sonnet 模型外，Cline 还支持 Google Gemini 2.0 等多种模型。开发者可以根据不同的项目需求和个人偏好选择合适的模型，以获得最佳的编程辅助效果。例如，在处理多模态任务时，可选择支持多模态输入 / 输出的 Gemini 2.0 模型。

**上下文管理精准有效：**Cline 可以通过精心管理添加到上下文中的信息，为大型复杂项目提供有价值的帮助，同时避免因信息过多而导致的性能问题。在处理包含多个文件和模块的项目时，它能准确理解项目结构和代码逻辑，提供针对性的建议和解决方案。

**安全机制保障可靠：**「人类监督」机制确保每一步操作都在用户的掌控之中，用户可以批准或拒绝 Cline 的操作，有效避免潜在的安全风险，保护代码和数据的安全。例如，在执行可能修改重要文件的操作时，用户可以先审查 Cline 的操作计划，确保无误后再批准执行

适用场景

**Web 开发项目：**在创建一个基于 React + Vite + Tailwind CSS 的 Patron 搜索界面时，Cline 可根据设计图或文字描述快速生成符合要求的组件代码，包括搜索栏、按钮、信息折叠面板等，并实现交互功能，如点击搜索按钮执行搜索操作、点击折叠面板展开或隐藏内容等，还能确保界面的响应式设计，使其适应不同屏幕尺寸。

**数据处理与分析任务：**开发者需要编写 Python 脚本来批量处理 Excel 文件并生成数据报表时，Cline 能够理解需求，自动创建脚本文件，读取指定文件夹下的所有 Excel 文件，提取销售数据并进行月度统计，最后生成汇总报表并实现可视化，如绘制柱状图展示销售额趋势等，整个过程无需开发者手动编写大量代码。

**自动化脚本编写工作：**当需要编写一个自动化脚本，如自动备份数据库、定期清理文件等，Cline 可以协助开发者完成脚本的基本框架搭建，根据具体需求生成相应的命令和逻辑，如设置备份路径、定时任务等，同时确保脚本的准确性和高效性，减少人工编写可能出现的错误。

**代码调试与错误修复环节：**在开发过程中遇到代码错误或运行时问题时，Cline 可以通过分析错误信息、查看相关代码文件，提出可能的解决方案，如修复语法错误、调整函数参数等。对于复杂的调试场景，它还可以启动无头浏览器模拟运行环境，捕获控制台日志，帮助开发者快速定位问题所在。

**项目管理与协作场景：**在团队协作的项目中，Cline 可以与 GitHub 等版本控制系统集成，自动获取最新代码，更新 Jira tickets，保持团队成员之间的信息同步。例如，当开发者完成一个功能模块的开发并提交代码后，Cline 可以自动更新相关的 Jira 任务状态，方便团队成员及时了解项目进展。

#### Roo code

**Roo Code** 是一个 AI 驱动的**自主编码代理**，它存在于您的编辑器中。它可以：

- 用自然语言沟通
- 直接在您的工作区读写文件
- 运行终端命令
- 自动化浏览器操作
- 与任何 OpenAI 兼容或自定义的 API/模型集成
- 通过**自定义模式**调整其"个性"和能力

无论您是寻找灵活的编码伙伴、系统架构师，还是像 QA 工程师或产品经理这样的专业角色，Roo Code 都可以帮助您更高效地构建软件

https://github.com/RooVetGit/Roo-Code

Roo Code 配备了强大的[工具](https://docs.roocode.com/basic-usage/how-tools-work)，可以：

- 读写项目中的文件
- 在 VS Code 终端中执行命令
- 控制网络浏览器
- 通过 [MCP（模型上下文协议）](https://docs.roocode.com/advanced-usage/mcp)使用外部工具

MCP 通过允许您添加无限自定义工具来扩展 Roo Code 的能力。与外部 API 集成、连接数据库或创建专业开发工具 - MCP 提供了扩展 Roo Code 功能以满足您特定需求的框架。



### windsurf

Windsurf不仅可以深度理解代码库，还配备了强大的工具集，并能实时感知用户的操作

#### devin.cursorrules

https://github.com/grapeot/devin.cursorrules





### tabby

https://tabby.tabbyml.com/docs/quick-start/installation/docker/



### huggingface

Ai 社区

https://huggingface.co/



#### gradio

https://www.gradio.app/guides/creating-plots



### trae

https://www.trae.ai/ ai ide



### astrbot

聊天机器人

https://github.com/Soulter/AstrBot



### deepseek



### **repomix**

https://repomix.com/

https://zhuanlan.zhihu.com/p/22711061038



### augment code

https://www.augmentcode.com/install/vscode



### open-lovable

https://github.com/firecrawl/open-lovable?tab=readme-ov-file

## 视频生成

### 万相2.6



## 终端

### aider

https://aider.chat/docs/llms.html



### shell

https://github.com/BuilderIO/ai-shell



## minio

存放ai数据集的平台

https://github.com/minio/minio



## 自动化工具

### midsence

字节开发的ai自动化工具

集成playwright

```javascript
import { expect } from "@playwright/test";
import { test } from "./fixture";

test.beforeEach(async ({ page }) => {
  page.setViewportSize({ width: 400, height: 905 });
  await page.goto("https://www.ebay.com");
  await page.waitForLoadState("networkidle");
});

test("search headphone on ebay", async ({ 
  ai, 
  aiQuery, 
  aiAssert,
  aiInput,
  aiTap,
  aiScroll 
}) => {
  // 使用 aiInput 输入搜索关键词
  await aiInput('Headphones', '搜索框');
  
  // 使用 aiTap 点击搜索按钮
  await aiTap('搜索按钮');
  
  // 等待搜索结果加载
  await aiWaitFor('搜索结果列表已加载', { timeoutMs: 5000 });
  
  // 使用 aiScroll 滚动到页面底部
  await aiScroll(
    { 
      direction: 'down',
      scrollType: 'untilBottom'
    },
    '搜索结果列表'
  );
  
  // 使用 aiQuery 获取商品信息
  const items = await aiQuery<Array<{title: string, price: number}>>(
    '获取搜索结果中的商品标题和价格'
  );
  
  console.log("headphones in stock", items);
  expect(items?.length).toBeGreaterThan(0);
  
  // 使用 aiAssert 验证筛选功能
  await aiAssert("界面左侧有类目筛选功能");
});
```



### browseruse

通过简单的几行代码，就能让 AI 模型像真人一样与网页互动，完成各种复杂任务，比如自动投简历、查询航班信息，甚至筛选模型。

底层也借助了微软的自动化框架 playwright 进行功能延展

#### 主要功能

- **自动化操作浏览器**：可以自动操作浏览器，与网页交互，比如点击、填写表单、下载文件等，就像真人一样流畅。
-  **视觉识别与HTML提取**：内置的视觉识别功能和 HTML 元素解析工具，能帮助 AI 识别网页中的动态内容并作出准确判断。
- **自动多标签管理**：支持自动管理多个浏览器标签页，智能调度任务。还可以并行多个 AI 智能体，分别处理不同任务，提高效率。
- **支持主流 LLM 模型**：兼容 GPT-4o、Claude 3.5 Sonnet 等主流大模型，充分发挥 AI 的理解与决策能力。
- **自定义操作**：允许用户定义个性化的任务流程，让工具更加适配具体需求

安装

```shell
pip install browser-use
```

使用

```python
from langchain_openai import ChatOpenAI
from browser_use import Agent
import asyncio

async def main():
    agent = Agent(
        task="Find a one-way flight from Bali to Oman on 12 January 2025 on Google Flights. Return me the cheapest option.",
        llm=ChatOpenAI(model="gpt-4o"),
    )
    result = await agent.run()
    print(result)

asyncio.run(main())
```



### shortest

基于claude

### stagehand

LLM+vision

https://github.com/browserbase/stagehand



### bugster

nextjs公司用的ai测试工具

https://docs.bugster.dev/



## 文档工具/RAG

检索增强生成

**检索增强生成 (RAG) 是一种使用来自私有或专有数据源的信息来辅助文本生成的技术。**它将检索模型（设计用于搜索大型数据集或知识库）和生成模型（例如[大型语言模型 (LLM)](https://www.elastic.co/cn/what-is/large-language-models)，此类模型会使用检索到的信息生成可供阅读的文本回复）结合在一起。

通过从更多数据源添加背景信息，以及通过训练来补充 LLM 的原始知识库，检索增强生成能够提高搜索体验的相关性。这能够改善大型语言模型的输出，但又无需重新训练模型。额外信息源的范围很广，从训练 LLM 时并未用到的互联网上的新信息，到专有商业背景信息，或者属于企业的机密内部文档，都会包含在内。

RAG 对于诸如回答问题和内容生成等任务，具有极大价值，因为它能支持[生成式 AI](https://www.elastic.co/cn/what-is/generative-ai) 系统使用外部信息源生成更准确且更符合语境的回答。它会实施搜索检索方法（通常是[语义搜索](https://www.elastic.co/cn/what-is/semantic-search)或混合搜索）来回应用户的意图并提供更相关的结果。

检索增强生成是一个多步式流程，始于检索，然后推进到生成。下面介绍了它的运作方式：

**检索**

- RAG 从输入查询开始。这可以是用户的问题，或者需要详细回复的任意一段文本。
- 检索模型会从知识库、数据库或外部来源（或者同时从多个来源）抓取相关信息。模型在何处搜索取决于输入查询所询问的内容。检索到的这一信息现在可以作为模型所需要的任何事实或背景信息的参考来源。
- 检索到的信息会被转化为高维度空间中的矢量。这些知识矢量会被存储在[矢量数据库](https://www.elastic.co/cn/elasticsearch/vector-database)中。
- 矢量模型会基于与输入查询的相关性，对检索到的信息进行排序。分数最高的文档或段落会被选中，以进行进一步的处理。

**生成**

- 接下来，生成模型（例如 LLM）会使用检索到的信息来生成文本回复。
- 生成的文本可能会经过额外的后处理步骤，以确保其语法正确，语义连贯。
- 整体而言，这些回复更加准确，也更符合语境，因为这些回复使用的是检索模型所提供的补充信息。在缺少公共互联网数据的专业领域，这一功能尤其重要。

相比于单独运行的语言模型，检索增强生成有数项优势。下面列举了它可以从哪些方面改进文本生成和回复：

- RAG 会确保您的模型能够访问最新、最及时的事实和相关信息，因为它能定期更新外部参考信息。这能确保：它所生成的回复会纳入可能与提出查询的用户相关的最新信息。您还可以实施[文档级安全性](https://www.elastic.co/guide/en/elasticsearch/reference/current/field-and-document-access-control.html)，来控制数据流中数据的访问权限，并限制特定文档的安全许可。
- RAG 是更具有成本效益的选项，因为它需要的计算和存储都更少，这意味着您无需拥有自己的 LLM，也无需花费时间和资金对您的模型进行微调。
- 声称数据准确固然很简答，但要证明数据准确却不简单。RAG 可以引用外部来源并将其提供给用户，以便用户为其回复提供支持性信息。如果愿意的话，用户还可以评估来源，以确认他们所收到的回复是否准确。
- 虽然由 LLM 提供支持的聊天机器人可以提供比之前的脚本式回复更加个性化的答案，但 RAG 可以提供更加量身定制的答案。这是因为，RAG 在通过衡量意图来生成答案时，能够使用搜索检索方法（通常是语义搜索）来参考一系列基于背景信息得出的要点。
- 当遇到训练时未出现过的复杂查询时，LLM 有时候会“出现幻觉”，提供不准确的回复。对于模糊性查询，RAG 可以更准确地进行回复，因为它的答案基于来自相关数据源的更多参考资料。
- RAG 模型用途多样，可用于执行各种各样的自然语言处理任务，包括对话系统、内容生成，以及信息检索。
- 在任何人造 AI 中，偏见都会是一个问题。RAG 在回答时可以帮助减少偏见，因为它依赖的是经过筛查的外部来源。

https://www.elastic.co/cn/what-is/retrieval-augmented-generation

### docling

**Docling** 是一个开源的以AI驱动的文档解析和处理工具。

它能够轻松读取各种流行的文档格式，包括 `PDF、DOCX、PPTX、LSX、图片、HTML、AsciiDoc 和 Markdown`，并将其转换为 HTML、Markdown 和 JSON 格式。

主打 **快速解析文档并导出为所需格式**，为构建 AI 驱动的 RAG/QA 应用提供了高效、便捷的解决方案。

#### 主要功能

- • **多格式支持**：支持读取和转换多种文档格式，让你无需担心不同格式的兼容性问题。
- • **PDF 文档理解**：不仅能够提取 PDF 文档的文字内容，还能解析页面布局、阅读顺序和表格结构，提供深入的信息，完美还原数据。
- • **统一格式**：使用统一的 DoclingDocument 格式来表示文档内容，方便后续处理和分析。
- • **RAG / QA 应用**：可以轻松集成 LlamaIndex 和 LangChain 等工具，实现强大的 RAG / QA 应用。
- • **OCR 支持**：支持 OCR 技术，可以处理扫描的 PDF 文件。
- • **简单易用**：提供简单的 CLI 命令行界面，用户快速使用。

使用

```python
from docling.document_converter import DocumentConverter

source = "https://arxiv.org/pdf/2408.09869"  # document per local path or URL
converter = DocumentConverter()
result = converter.convert(source)
print(result.document.export_to_markdown())  # output: "## Docling Technical Report[...]"
```

https://github.com/DS4SD/docling



### KAG

https://github.com/OpenSPG/KAG

KAG 是基于 [OpenSPG](https://github.com/OpenSPG/openspg) 引擎和大型语言模型的逻辑推理问答框架，用于构建垂直领域知识库的逻辑推理问答解决方案。KAG 可以有效克服传统 RAG 向量相似度计算的歧义性和 OpenIE 引入的 GraphRAG 的噪声问题。KAG 支持逻辑推理、多跳事实问答等，并且明显优于目前的 SOTA 方法。

KAG 的目标是在专业领域构建知识增强的 LLM 服务框架，支持逻辑推理、事实问答等。KAG 充分融合了 KG 的逻辑性和事实性特点，其核心功能包括：

- 知识与 Chunk 互索引结构，以整合更丰富的上下文文本信息
- 利用概念语义推理进行知识对齐，缓解 OpenIE 引入的噪音问题
- 支持 Schema-Constraint 知识构建，支持领域专家知识的表示与构建
- 逻辑符号引导的混合推理与检索，实现逻辑推理和多跳推理问答

私域知识库场景，非结构化数据、结构化信息、业务专家经验 往往三者共存，KAG 提出了一种对大型语言模型（LLM）友好的知识表示框架，在 DIKW（数据、信息、知识和智慧）的层次结构基础上，将 SPG 升级为对 LLM 友好的版本，命名为 LLMFriSPG。

这使得它能够在同一知识类型（如实体类型、事件类型）上兼容无 schema 约束的信息提取和有 schema 约束的专业知识构建，并支持图结构与原始文本块之间的互索引表示。

### RAGFlow

https://github.com/infiniflow/ragflow

传统的文档管理系统往往存在以下痛点：

- 检索效率低下，找资料耗时费力
- 文档格式复杂，难以统一处理
- • 知识提取不准确，答案缺乏可靠性
- • 系统部署复杂，维护成本高

RAGFlow通过深度文档理解技术，结合先进的大语言模型，完美解决了这些问题。它能够智能处理各类文档，提供准确的问答服务，让企业知识管理更轻松、更高效

通过先进的文档分析技术，RAGFlow能够精准识别和提取文档中的结构化信息，自动建立知识关联，让文档内容真正发挥价值，而不是仅仅停留在简单的文本存储层面。

- 智能识别文档结构和语义
- 自动提取关键信息
- 支持跨文档知识关联



### vikingdb

使用

```typescript
import { Signer } from '@volcengine/openapi';
import type { RequestObj } from '@volcengine/openapi/lib/base/types';

interface Options {
  pathname: string
  method: 'GET' | 'POST'
  body?: string // json 字符串，当且仅当 post 请求
  region: 'cn-beijing' | 'cn-shanghai'
  params?: Record<string, any> // 当且仅当 get 请求
}

function signer({ pathname, method, body, region, params }: Options) {
  const requestObj: RequestObj = {
   region, 
   headers: {
     Accept: 'application/json',
     'Content-Type': 'application/json'
   },
   method,
   body,
   pathname,
   params,
 }

  const signer = new Signer(requestObj, 'air')
    signer.addAuthorization({
      accessKeyId: 'ak', // 替换为用户 ak
      secretKey: 'sk', // 替换为用户 sk
 })
 return requestObj.headers
}

import { Injectable } from '@nestjs/common';
import { vikingdb } from '@volcengine/openapi';

@Injectable()
export class VolcengineService {
  public service = new vikingdb.VikingdbService({
    ak: 'xxx',
    sk: 'xxx',
    region: 'cn-shanghai',
  });
}
```





## 协同工具

### Vibe-kanban

https://github.com/BloopAI/vibe-kanban



## 设计工具

### uizard

原型设计工具

https://app.uizard.io/prototypes/vmleq8oW4jc5b5pwJyLg



### builderio

figma插件，由设计稿生成前端代码

https://builder.io/content/3da7ffff103f4c46b211974d66e3099d



## Agent框架

https://github.com/mastra-ai/mastra/pulls

### mastra

```typescript
import { openai } from "@ai-sdk/openai";
import { Agent } from "@mastra/core/agent";
import { weatherTool } from "../tools/weather-tool";
 
export const weatherAgent = new Agent({
  name: "Weather Agent",
  instructions: `You are a helpful weather assistant that provides accurate weather information.
 
Your primary function is to help users get weather details for specific locations. When responding:
- Always ask for a location if none is provided
- Include relevant details like humidity, wind conditions, and precipitation
- Keep responses concise but informative
 
Use the weatherTool to fetch current weather data.`,
  model: openai("gpt-4o-mini"),
  tools: { weatherTool },
});
```

### joyagent-jdgenie

https://github.com/jd-opensource/joyagent-jdgenie

当前相关开源agent主要是SDK或者框架，用户还需基于此做进一步的开发，无法直接做到开箱即用。我们开源的JoyAgent-JDGenie是端到端的多Agent产品，对于输入的query或者任务，可以直接回答或者解决。例如用户query"给我做一个最近美元和黄金的走势分析"，JoyAgent-Genie可以直接给出网页版或者PPT版的报告文档。

JoyAgent-JDGenie是一个通用的多智能体框架，对于用户需要定制的一些新场景功能，只需将相关的子智能体或者工具挂载到JoyAgent-Genie即可。为了验证JoyAgent-JDGenie的通用性，在GAIA榜单Validation集准确率**75.15%、Test集65.12%**，已超越OWL（CAMEL）、Smolagent（Huggingface）、LRC-Huawei（Huawei）、xManus（OpenManus）、AutoAgent（香港大学）等行业知名产品。

### dify

https://github.com/langgenius/dify/





## TTS

语言转文本

https://github.com/coqui-ai/TTS?tab=readme-ov-file



### buzz

https://github.com/chidiwilliams/buzz?tab=readme-ov-file



### pyvideotrans

https://github.com/jianchang512/pyvideotrans



## 前端AI组件

### prochat

https://pro-chat.antdigital.dev/



### ant-x

https://x.ant.design/index-cn



### chatbotui

https://github.com/mckaywrigley/chatbot-ui

## LLM

开源大模型

### THUDM

清华大模型

https://github.com/THUDM

### OPENEMMA

自动驾驶大模型

https://github.com/taco-group/openemma



## 爬虫

Crawl4AI 是一个专注于 **LLM（大语言模型）友好** 的开源网络爬虫和抓取工具。它的目标是帮助开发者、研究人员和普通用户高效地从网页中提取有价值的信息，同时支持多种灵活的配置和扩展。

**它的核心功能包括：**

1. 生成干净的 Markdown

   直接输出结构化的文本内容，方便后续处理或导入到 AI 模型中。

2. 结构化提取

   支持 CSS、XPath 等传统方法，也可以结合 LLM 进行智能提取。

3. 高级浏览器控制

   支持无头浏览器、代理、隐身模式、会话管理等功能，帮助你模拟真实用户行为。

4. 高性能

   支持多线程和异步操作，适合处理大规模数据或实时场景。

5. 完全开源

   没有任何 API 限制或付费墙，完全自由使用和修改。

对于很多开发者来说，网络爬虫工具的选择往往需要在功能、性能和易用性之间权衡。而 Crawl4AI 则在这些方面都表现得非常出色：

1. **功能全面，配置灵活**
   Crawl4AI 提供了丰富的配置选项，无论是简单的网页抓取，还是复杂的动态页面处理，都能轻松应对。它还支持多种浏览器（如 Chromium、Firefox 等），满足不同场景的需求。
2. **LLM 友好，助力 AI 开发**
   作为专为 LLM 设计的工具，Crawl4AI 输出的内容格式非常适合用于 RAG（检索增强生成）或其他 AI 流程。你可以直接将抓取的数据喂给大模型，而无需额外的格式转换。
3. **社区活跃，持续更新**
   Crawl4AI 是一个活跃的开源项目，拥有庞大的开发者社区。定期的版本更新和功能迭代，确保了工具的稳定性和前沿性。
4. **完全免费，无后顾之忧**
   与其他商业工具不同，Crawl4AI 完全开源且免费，没有任何使用限制。你可以根据需求自由修改和扩展。

```python
from crawl4ai import AsyncWebCrawler, BrowserConfig

async def simple_crawl():
    browser_config = BrowserConfig(headless=True, java_script_enabled=True)
    async with AsyncWebCrawler(config=browser_config) as crawler:
        result = await crawler.arun(url="https://example.com")
        print(result.markdown_content)

if __name__ == "__main__":
    import asyncio
    asyncio.run(simple_crawl())
```

https://github.com/unclecode/crawl4ai



## 资源

千问：https://qwenlm.github.io/blog/page/3/

