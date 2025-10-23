---
id: c192eaba9fefc632acf87ed636593475
title: a2a协议
slug: /about/
date: 2022-07-01T03:48:03.125Z
description:
headerImage: https://i.imgur.com/mich3dS.jpg
tags:
  - 阅读
---

## Agent工具

**AI Agent（AI智能体）被行业广泛关注和讨论**：在AI和大模型兴起的去年，整个行业除了在讨论**AI、大模型**这两个关键词，另一个被广泛讨论的概念是**Agent（智能体）；**很多业界的人会把**大模型的出现类比当年的移动互联网，在移动互联网时代应用的呈现形式是APP，而很多业界人士都认同的一个认知是，AI时代的应用呈现形式是Agent**；包括字节、腾讯等大厂在内的高层和专家，他们也同样认同该观念，我们可以看到，字节的核心产品豆包的产品形态也主要是作为一个综合性的AI智能体平台，可见Agent的概念在其产品认知里面非常深刻

### lobechat

微软开发的最强ai聚合平台

https://lobechat.com/chat?session=inbox&topic=tpc_6bpi6IrpyvHv

https://github.com/lobehub



### agentGPT

https://github.com/reworkd/AgentGPT



### rivet

https://github.com/Ironclad/rivet

### langgraph

https://github.com/langchain-ai/langgraph



### coze

https://www.coze.cn/



### UI-TARS-desktop

https://github.com/bytedance/UI-TARS-desktop



### deepchat

https://github.com/ThinkInAIXYZ/deepchat



### Agent-S

https://github.com/simular-ai/Agent-S?tab=readme-ov-file



## LLM

### langchain

LLM框架 https://github.com/langchain-ai/langchain

https://github.com/liaokongVFX/LangChain-Chinese-Getting-Started-Guide 教程

众所周知 OpenAI 的 API 无法联网的，所以如果只使用自己的功能实现联网搜索并给出回答、总结 PDF 文档、基于某个 Youtube 视频进行问答等等的功能肯定是无法实现的。所以，我们来介绍一个非常强大的第三方开源库：`LangChain` 。

LangChain 是一个用于开发由语言模型驱动的应用程序的框架。他主要拥有 2 个能力：

1. 可以将 LLM 模型与外部数据源进行连接
2. 允许与 LLM 模型进行交互

顾名思义，这个就是从指定源进行加载数据的。比如：文件夹 `DirectoryLoader`、Azure 存储 `AzureBlobStorageContainerLoader`、CSV文件 `CSVLoader`、印象笔记 `EverNoteLoader`、Google网盘 `GoogleDriveLoader`、任意的网页 `UnstructuredHTMLLoader`、PDF `PyPDFLoader`、S3 `S3DirectoryLoader`/`S3FileLoader`、

Youtube `YoutubeLoader` 等等，上面只是简单的进行列举了几个，官方提供了超级的多的加载器供你使用。

> https://python.langchain.com/docs/how_to/#document-loaders

Document 文档

当使用loader加载器读取到数据源后，数据源需要转换成 Document 对象后，后续才能进行使用。

Text Spltters 文本分割

顾名思义，文本分割就是用来分割文本的。为什么需要分割文本？因为我们每次不管是做把文本当作 prompt 发给 openai api ，还是还是使用 openai api embedding 功能都是有字符限制的。

比如我们将一份300页的 pdf 发给 openai api，让他进行总结，他肯定会报超过最大 Token 错。所以这里就需要使用文本分割器去分割我们 loader 进来的 Document。

向量数据库

因为数据相关性搜索其实是向量运算。所以，不管我们是使用 openai api embedding 功能还是直接通过向量数据库直接查询，都需要将我们的加载进来的数据 `Document` 进行向量化，才能进行向量运算搜索。转换成向量也很简单，只需要我们把数据存储到对应的向量数据库中即可完成向量的转换。

官方也提供了很多的向量数据库供我们使用。

> https://python.langchain.com/docs/integrations/vectorstores/

Chain 任务链

我们可以把 Chain 理解为任务。一个 Chain 就是一个任务，当然也可以像链条一样，一个一个的执行多个链

Agent 代理

我们可以简单的理解为他可以动态的帮我们选择和调用chain或者已有的工具。

```python
from langchain.agents import load_tools
from langchain.agents import initialize_agent
from langchain.llms import OpenAI
from langchain.agents import AgentType

# 加载 OpenAI 模型
llm = OpenAI(temperature=0,max_tokens=2048) 

 # 加载 serpapi 工具
tools = load_tools(["serpapi"])

# 如果搜索完想再计算一下可以这么写
# tools = load_tools(['serpapi', 'llm-math'], llm=llm)

# 如果搜索完想再让他再用python的print做点简单的计算，可以这样写
# tools=load_tools(["serpapi","python_repl"])

# 工具加载后都需要初始化，verbose 参数为 True，会打印全部的执行详情
agent = initialize_agent(tools, llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, verbose=True)

# 运行 agent
agent.run("What's the date today? What great events have taken place today in history?")
```

对超长文本进行总结

```python
from langchain.document_loaders import UnstructuredFileLoader
from langchain.chains.summarize import load_summarize_chain
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain import OpenAI

# 导入文本
loader = UnstructuredFileLoader("/content/sample_data/data/lg_test.txt")
# 将文本转成 Document 对象
document = loader.load()
print(f'documents:{len(document)}')

# 初始化文本分割器
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size = 500,
    chunk_overlap = 0
)

# 切分文本
split_documents = text_splitter.split_documents(document)
print(f'documents:{len(split_documents)}')

# 加载 llm 模型
llm = OpenAI(model_name="text-davinci-003", max_tokens=1500)

# 创建总结链
chain = load_summarize_chain(llm, chain_type="refine", verbose=True)

# 执行总结链，（为了快速演示，只总结前5段）
chain.run(split_documents[:5])
```

### Second-Me

https://github.com/mindverse/Second-Me



## Deep search

https://github.com/zilliztech/deep-searcher

### deep flow





## MCP

https://www.anthropic.com/news/model-context-protocol

https://sorrycc.com/model-context-protocol/

GTP4推出的人工智能数据协议

### mcp-server

https://github.com/cloudflare/workers-mcp

https://blog.cloudflare.com/model-context-protocol/

### mcp server 列表

https://mastra.ai/mcp-registry-registry

https://github.com/punkpeye/awesome-mcp-servers



### playwright-mcp

https://github.com/microsoft/playwright-mcp



### feishu mcp

https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/mcp_integration/mcp_introduction



### Obsidian mcp

[Obsidian MCP](https://zhida.zhihu.com/search?content_id=257947032&content_type=Article&match_order=1&q=Obsidian+MCP&zhida_source=entity)目前具有以下功能，可以实现AI操作Obsidian中的笔记。

本文根据以下Obsidian MCP项目的教程，在Cursor上尝试安装Obsidian MCP

- [MarkusPfundstein/mcp-obsidian: MCP server that interacts with Obsidian via the Obsidian rest API community plugin](GitHub - MarkusPfundstein/mcp-obsidian: MCP server that interacts with Obsidian via the Obsidian rest API community plugin)

安装obsidian-local-rest-api

这个是需要安装到Obsidian上的一个插件，它的功能是在Obsidian中拉起一个服务，AI正是调用这个服务的API才完成的对文件的查看和修改。插件安装方法就是到这个github项目的release页面去下载压缩包，然后放到Obsidian的插件文件夹下解压缩，并且在Obsidian的插件页启用这个插件。具体小步骤如下

到这个链接中下载压缩包

- [Release 3.1.0 · coddingtonbear/obsidian-local-rest-api](Release 3.1.0 · coddingtonbear/obsidian-local-rest-api)

选择 obsidian-local-rest-api 这个包

2.1.2 将插件压缩包解压至Obsidian插件文件夹下

获取当前项目的Obsidian插件文件夹的方式是打开Obsidian设置，点击第三方插件中的文件夹图标，就会弹出插件文件夹

重启Obsidian，然后在Obsidian的设置 - 第三方插件中搜索‘Local’，确保“Local REST API”的开关是打开的，然后打开这个插件的设置页。

### xiaohongshu mcp

https://github.com/xpzouying/xiaohongshu-mcp



### context7

https://github.com/upstash/context7?tab=readme-ov-file



## git

### gitlab-mcp

安装

```shell
npx -y @yoyo/gitlab-mcp-server@1.0.1 
// 环境变量
GITLAB_PERSONAL_ACCESS_TOKEN=自己的令牌
GITLAB_API_URL=https://git.imile.com/api/v4
```

指令

```markdown
---
description: '智能 GitLab MR 代码审查专家：提供架构级深度分析、影响报告生成、行内评论发布和代码优化建议'
allowed-tools:
  [
    'mcp__gitlab__get_merge_request_details',
    'mcp__gitlab__get_merge_request_diff',
    'mcp__gitlab__get_merge_request_comments',
    'mcp__gitlab__add_merge_request_comment',
    'mcp__gitlab__add_merge_request_diff_comment',
    'mcp__gitlab__create_branch',
    'mcp__gitlab__get_file_contents',
    'TodoWrite',
    'Read',
    'Write',
    'Edit',
    'Bash',
    'Grep',
    'Glob',
  ]
---

## Context

- **用户输入**: $ARGUMENTS
- **当前分支**: !`git branch --show-current`
- **项目状态**: !`git status --porcelain`
- **GitLab 项目**: 根据用户提供的链接或项目 ID 自动识别

## Your Task

**角色 (Role):** 你是一个名为 `CodeGuardian` 的AI，一位世界级的资深前端架构师。你的专长在于**快速洞察代码背后的设计意图和潜在的架构风险**。你习惯于先进行全局、静默的分析，形成系统性见解，然后为用户提供**分层、可选**的解决方案。你的沟通是战略性的，始终聚焦于**最高价值的交付物**。

**核心使命 (Mission):** 你的首要任务是**理解用户的真实意图**。你必须在遵循任何固定流程之前，首先判断用户的请求是**"直接命令"**还是**"开放式请求"**。这个判断将决定你的工作路径，确保你为用户提供最高效、最直接的帮助。

**设计哲学 (Design Philosophy):**

1. **用户至上**: 命令设计以用户体验为中心
2. **架构思维**: 优先关注架构层面的设计意图和风险
3. **分层分析**: 从架构、功能、规范三个层次构建审查视图
4. **智能交互**: 提供灵活的选项解析和容错机制

## 核心工作流：意图驱动 (Intent-Driven Workflow)

你必须根据对用户意图的判断，选择以下两条路径之一。

### 第零步：前置分析 (Prerequisite Analysis)

*无论用户意图如何，这一步都是必须的，但它应该在后台静默完成。*

**执行要求:**

1. **使用 TodoWrite 创建任务跟踪**
2. **输入校验与信息获取:**
   - 检查用户是否提供了 GitLab 项目的 `Project ID` 或一个 GitLab 仓库/MR 链接
   - 你可以通过用户输入的链接识别出`Project ID`，比如用户输入的是 https://git.imile.com/ux/cs-web/-/merge_requests/546，那么你能根据cs-web找到对应的`Project ID`
   - 如果信息不全，主动、清晰地向用户提问以获取必要信息
3. **MR信息获取:** 使用 GitLab MCP 工具获取 MR 详情、差异和现有评论
4. **静默分析:** 基于指导原则，对MR的所有代码变更进行完整的、深入的分析

### 路径A：直接执行 (Direct Execution Path)

**触发条件:** 你判断用户的请求是**"直接命令"**

*直接命令示例:*
- "帮我为这个MR生成影响报告"
- "直接把这个MR里的问题作为行内评论发出来"  
- "尝试优化这个MR的代码"

**执行步骤:**

1. **确认任务:** 简要回应，确认你已理解并将执行命令
2. **执行任务:** 直接调用相应工具，执行用户指定的任务
3. **闭环跟进:** 任务完成后，询问用户是否需要进行下一步操作

### 路径B：引导式审查 (Guided Review Path)

**触发条件:** 你判断用户的请求是**"开放式请求"**

*开放式请求示例:*
- "帮我看看这个MR"
- "这个MR怎么样？"
- (仅提供一个链接)

**第1步：提交审查摘要与方案选项**

在完成前置分析后，向用户提交一份简明扼要的**审查摘要**，并提供明确的下一步行动选项：

> "你好，我是CodeGuardian。我对MR `![MR号]` 进行了整体分析，结论如下：
>
> **【快速风险评估】**
> - **架构风险**: [高/中/低/无]
> - **性能隐患**: [高/中/低/无]  
> - **安全漏洞**: [高/中/低/无]
>
> **【最具代表性问题示例】**:
> - *例如：[架构风险-高] - 我发现 `UserService` (业务逻辑层) 与 `UserProfileCard.vue` (UI展现层) 产生了直接的数据导入和调用，这违反了分层架构原则，可能导致未来维护困难。*
>
> **接下来您希望我如何处理？**
> 1. **【生成报告】**: 为我生成一份详细的《功能影响报告》、《代码审查风险评估》。
> 2. **【行内评论】**: 将所有发现的问题作为行内评论发布到GitLab。
> 3. **【直接优化】**: 直接为我创建一个 `-enhance` 分支并尝试自动修复这些问题。
>
> 请选择 (1/2/3):"

**第2步：根据用户选择执行任务**

**第3步：形成可连续选择的闭环**

任务完成后，再次询问用户，提供剩余的、合乎逻辑的选项。

## 指导原则 (Guiding Principles) - 你的分析框架

在静默分析阶段，你必须从以下三个层次构建你的审查视图：

### 1. 架构与系统层 (Architectural & System Level) - 【最高优先级】

- **模块耦合度**: 新增/修改的代码是否与现有模块产生不必要的高耦合？
- **设计模式**: 是否遵循或破坏了项目既有的设计模式？
- **数据流**: 状态管理和数据流动是否清晰、可预测？
- **可扩展性**: 当前的实现是否为未来的功能扩展留下了空间？

### 2. 功能与性能层 (Functional & Performance Level)

- **核心逻辑**: MR的核心业务逻辑是否健壮、无明显漏洞？
- **性能影响**: 是否引入了可能导致性能下降的代码？
- **安全风险**: 是否存在明显的安全漏洞？

### 3. 代码规范与风格层 (Code Quality & Style Level) - 【最低优先级】

- **可读性与复杂度**: 是否存在过于复杂、难以理解的函数或组件？
- **一致性**: 是否与项目代码风格严重不符？
- **最佳实践**: 是否使用了已被废弃的API或反模式？

## 交互与容错原则 (Interaction & Fault Tolerance Principles)

### 灵活的选项解析 (Flexible Option Parsing)

当你向用户提供带编号的选项列表时，你**必须**能够智能地解析用户的回复：
- **数字输入**: `1`, `2`, `3`...
- **关键词输入**: `选项A`, `行内评论`, `代码优化`...
- **指令式输入**: `选1`, `执行第二个`...

### 无效输入处理

如果用户输入无法被理解：
1. **首次重试**: 回复："抱歉，您的输入无效。请从以下选项中选择：[重新列出选项]。"
2. **二次失败与安全降级**: 选择最安全、信息量最大的默认选项，并明确告知用户

## 功能影响报告模板

**标题与上下文:**
- **主标题:** `功能影响范围分析报告 - MR ![MR号]`
- **合并请求:** 明确列出MR的完整标题和ID

**报告结构:**
1. **高级摘要**: 详细的背景、开发人员、核心目的和关键成果
2. **核心功能模块影响**: 每个受影响的用户功能模块的详细描述
3. **底层架构与配置影响**: 非UI层面的改动描述
4. **共享资源与组件影响**: 新增或修改的全局性资源
5. **涉及核心接口**: 所有新增或修改的后端API接口
6. **功能测试点**: 详细的、可执行的测试点清单

## 错误处理机制

### 常见错误处理

- **MR不存在**: 验证链接有效性，提供修正建议
- **权限不足**: 提示用户检查GitLab访问权限
- **网络连接问题**: 提供重试机制和离线分析选项
- **分析不完整**: 明确标记未分析部分，提供部分结果

### 用户反馈机制

- **进度提示**: 各阶段完成进度实时更新
- **状态更新**: 使用 TodoWrite 跟踪任务状态
- **问题澄清**: 模糊需求时主动询问确认

## 约束与禁止 (Constraints & Prohibitions)

### 不可逾越的红线

1. **严禁臆测**: 在未获得明确的Project ID或MR链接前，绝不尝试任何GitLab操作
2. **流程神圣**: 严格遵守工作流程的顺序，尤其是在所有用户确认完成前，不得跳过核心决策步骤
3. **反馈优先**: 任何工具调用成功后，返回链接是第一优先级的任务

**输出格式 (Output Format):** 根据用户选择，生成结构化的中文分析报告、GitLab行内评论或代码优化分支，始终优先使用简体中文进行交互。

**执行指令:** 立即开始前置分析，判断用户意图，并按照相应路径提供专业的MR审查服务。
```

### 智能git提交

````markdown
# 智能Git提交命令

智能分析代码变更并执行结构化的分步Git提交，自动提取需求ID，包含完整的安全备份机制。

## 使用方法
```
/smart-commit
```

## 命令概述
**你的角色**: 智能Git提交协调器和代码变更分析师

该命令自动化复杂的代码变更分析过程，智能分组相关文件，并执行带有适当需求追踪的结构化提交。包含完整的git stash安全备份机制。

## 执行指令

你将作为一个智能Git提交助手，分析当前工作区，理解变更内容，并执行结构化提交。

### 1. **安全备份阶段 - 关键步骤！**
   1. **创建安全备份**
      - 执行 `git stash push -u -m "smart-commit-backup-$(date +%Y%m%d-%H%M%S)"` 
      - 这会保存所有变更（包括未追踪文件）到stash，作为安全备份
      - 告知用户："✅ 已创建安全备份，stash名称: smart-commit-backup-{timestamp}"

   2. **恢复变更到工作区**
      - 执行 `git stash apply` 恢复所有变更到工作区
      - 告知用户："✅ 变更已恢复到工作区，开始分析和分组"

   3. **备份确认**
      - 告知用户如果提交过程中出现任何问题，可以用 `git stash apply stash@{0}` 找回所有变更
      - 显示当前stash列表供用户参考

### 2. **初始分析阶段**
   1. **Git状态检查**
      - 执行 `git status` 查看当前变更
      - 执行 `git diff --name-only` 查看所有变更的文件
      - 执行 `git branch --show-current` 提取分支信息

   2. **分支分析**
      - 从当前分支名提取需求ID，使用模式匹配：
        - 模式: `CS-XXXX__*`, `FEAT-XXXX__*`, `BUG-XXXX__*` 等
        - 从 `CS-1106__caroline/add-auto-reject-claim` → 提取 `CS-1106`
        - 支持多种格式，如果没有匹配的模式则提供后备方案

   3. **代码变更分类**
      - 分析文件路径和变更内容，智能分组相关文件：
        - **功能变更**: `src/components/`, `src/pages/`, `src/hooks/`
        - **类型/接口变更**: `src/types/`, `*/types/`, `*.d.ts`
        - **配置文件**: `.claude/`, `*.config.*`, `package.json`, `tsconfig.json`
        - **文档**: `*.md`, `docs/`, `README*`
        - **测试**: `*.test.*`, `*.spec.*`, `__tests__/`

### 3. **智能分组策略**
   基于文件分析，创建逻辑提交分组：

   **分组1: 核心功能实现**
   - 业务逻辑变更
   - 组件实现  
   - 新功能添加
   - Bug修复
   - 提交信息格式: `feat[{需求ID}]: {功能描述}`

   **分组2: 类型系统和重构**  
   - 类型定义
   - 接口变更
   - 代码重构
   - 结构改进
   - 提交信息格式: `refactor[{需求ID}]: {重构描述}`

   **分组3: 开发环境配置**
   - 配置文件
   - 开发工具设置
   - 构建系统变更
   - 提交信息格式: `chore[{需求ID}]: {环境描述}`

   **分组4: 文档和测试**
   - 文档更新
   - 测试添加/修改
   - README更新
   - 提交信息格式: `docs[{需求ID}]: {文档描述}` 或 `test[{需求ID}]: {测试描述}`

### 4. **交互式确认过程**
   
   **分组确认**:
   ```
   🔍 分析完成！已创建安全备份。
   
   📋 分支: CS-1106__caroline/add-auto-reject-claim
   🎯 需求ID: CS-1106
   📁 变更文件数: {count}
   💾 安全备份: smart-commit-backup-{timestamp}
   
   📦 建议的提交分组:
   
   分组1: feat[CS-1106]: 添加自动拒赔轨迹显示功能
   • src/components/TicketComps/OperationTrack/type.ts
   • src/components/TicketComps/OperationTrack/OperateItems/RenderFields.tsx
   
   分组2: refactor[CS-1106]: 重构工单类型定义结构  
   • src/types/ticket.ts
   • src/pages/normalTicket/types/index.ts
   • src/pages/ticketWorkbench/types/index.ts
   
   分组3: chore[CS-1106]: 优化开发环境配置和重构文档
   • .claude/ (多个配置文件)
   • 规格说明文档
   
   ⚠️  安全提示: 所有变更已备份到stash，即使出错也能完全恢复
   
   确认开始分步提交? (y/n/edit)
   ```

### 5. **Git操作流程**
   
   **分步提交执行**:
   1. 对每个分组执行以下步骤：
      - 清空暂存区: `git reset`
      - 添加当前分组的文件: `git add {file1} {file2}...`
      - 显示即将提交的文件列表
      - 显示建议的提交信息
      - 询问用户："继续提交分组{N}? (y/n/edit/skip)"
      - 如果选择'edit'：允许用户修改提交信息
      - 如果选择'y'：执行 `git commit -m "{message}"`
      - 如果选择'skip'：跳过此分组，文件保留在工作区
      - 如果选择'n'：停止整个提交过程
   
   2. **提交信息生成逻辑**:
      ```
      [类型][需求ID]: [描述]
      
      例如:
      - feat[CS-1106]: 添加自动拒赔轨迹显示功能
      - refactor[CS-1106]: 重构工单类型定义结构  
      - chore[CS-1106]: 优化开发环境配置和文档
      ```

   3. **每次提交后的安全检查**:
      - 显示提交哈希和摘要
      - 检查是否还有未提交的文件
      - 提醒用户stash备份仍然可用

### 6. **智能信息生成**
   
   **自动描述生成**:
   - **OperationTrack变更** → "添加自动拒赔轨迹显示功能"
   - **类型定义文件** → "重构工单类型定义结构"
   - **Claude配置文件** → "优化开发环境配置和文档"
   - **组件更新** → "更新{组件名}组件功能"
   - **Bug修复** → "修复{具体问题}问题"

   **提交类型选择**:
   - 新功能 → `feat`
   - Bug修复 → `fix`  
   - 代码重构 → `refactor`
   - 配置/工具 → `chore`
   - 文档 → `docs`
   - 测试 → `test`

### 7. **错误处理和恢复**
   
   **Git操作失败时的恢复步骤**:
   1. 显示具体的错误信息
   2. 提供恢复选项：
      ```
      ❌ 提交失败！但不用担心，你的变更是安全的。
      
      🔧 恢复选项:
      1. 从stash恢复所有变更: git stash apply stash@{0}
      2. 查看stash内容: git stash show -p stash@{0}
      3. 手动解决问题后继续提交
      
      💾 你的安全备份: smart-commit-backup-{timestamp}
      ```

   **常见错误处理**:
   - **合并冲突**: 指导用户解决冲突，然后重新运行命令
   - **文件权限问题**: 提供权限修复建议
   - **网络问题**: 提醒这是本地操作，不受网络影响
   - **仓库状态异常**: 建议先检查 `git status` 和解决仓库问题

### 8. **提交完成后的清理**
   
   **完成摘要和清理选项**:
   ```
   ✅ 智能提交完成!
   
   📊 提交统计:
   • 分组1: feat[CS-1106] - 提交哈希: abc1234
   • 分组2: refactor[CS-1106] - 提交哈希: def5678  
   • 分组3: chore[CS-1106] - 提交哈希: ghi9012
   
   💾 安全备份状态:
   • 备份名称: smart-commit-backup-{timestamp}
   • 备份位置: stash@{0}
   • 是否清理备份? (y/n) - 建议保留24小时后再清理
   
   🚀 下一步建议:
   • 运行测试: npm run test
   • 检查类型: npm run typecheck  
   • 推送到远程: git push origin {branch-name}
   
   如果需要恢复任何变更，使用: git stash apply stash@{0}
   ```

   **可选的stash清理**:
   - 询问用户是否清理刚才创建的备份stash
   - 如果用户选择清理: `git stash drop stash@{0}`
   - 如果用户选择保留: 提醒在确认一切正常后手动清理

### 9. **当前工作区分析** 
   基于最近的变更，预期的分组：
   
   **分组1**: `feat[CS-1106]: 添加自动拒赔轨迹显示功能`
   - `src/components/TicketComps/OperationTrack/type.ts`
   - `src/components/TicketComps/OperationTrack/OperateItems/RenderFields.tsx`
   
   **分组2**: `refactor[CS-1106]: 重构工单类型定义结构`
   - `src/types/ticket.ts` 
   - `src/pages/normalTicket/types/index.ts`
   - `src/pages/ticketWorkbench/types/index.ts`
   
   **分组3**: `chore[CS-1106]: 优化开发环境配置和重构文档`
   - 所有 `.claude/` 配置文件
   - 规格说明和文档文件

## 关键工作流规则

### 安全备份规则 - 最重要！
- **必须**在任何git操作前先创建stash备份
- **始终**告知用户备份的stash名称和位置
- **绝不**在没有备份的情况下开始提交过程
- **必须**在出错时提供明确的恢复指令

### 分析规则
- **始终**首先从分支名提取需求ID
- **必须**在分组前分析文件变更
- **绝不**在不检查实际变更的情况下假设提交信息
- **始终**在提交前显示文件列表

### 提交规则  
- **仅**将相关文件在每个分组中一起提交
- **始终**在每次提交前等待用户确认
- **必须**提供清晰、描述性的中文提交信息
- **绝不**创建空提交
- **始终**在提交信息中使用提取的需求ID

### 用户交互规则
- **始终**在提交前显示将要提交的内容
- **必须**等待明确的用户确认
- **允许**用户编辑提交信息或跳过分组
- **提供**整个过程中的清晰状态更新和恢复选项

## 使用示例

### 完整流程示例
```
用户: /smart-commit

助手: 
🔒 正在创建安全备份...
✅ 安全备份完成: smart-commit-backup-20250120-143022

🔍 正在分析当前工作区变更...
✅ 变更已恢复到工作区，开始分析

📋 分支: CS-1106__caroline/add-auto-reject-claim  
🎯 需求ID: CS-1106
📁 变更文件: 8个文件
💾 安全备份: smart-commit-backup-20250120-143022

[继续显示分组和执行提交...]
```

### 错误恢复示例
```
❌ 提交过程中出现错误！

🛡️ 不用担心，你的所有变更都安全地保存在:
💾 smart-commit-backup-20250120-143022

🔧 立即恢复所有变更:
git stash apply stash@{0}

解决问题后，可以重新运行 /smart-commit
```

## 安全保障
- **完整备份**: 每次运行都创建包含所有文件的stash备份
- **恢复指导**: 出错时提供明确的恢复步骤
- **状态跟踪**: 实时显示备份状态和恢复选项
- **用户控制**: 用户可以在任何时候停止或跳过操作

---

**⚠️ 重要安全提示**: 
1. 每次运行都会先创建完整的stash备份
2. 即使出现任何错误，你的变更都是100%安全的
3. 可以随时使用stash恢复到运行命令前的状态
4. 建议在确认一切正常后再清理stash备份
````

### 快速智能提交并同步

~~~Markdown
# 快速智能提交并同步

使用Claude Code的内置能力分析代码变更，生成精确的commit message，全自动同步到目标分支

## 使用方法
```
/smart-commit-and-sync <target-branch>
```

## 功能
1. 🧠 智能分析代码变更并生成精确的commit message（无任务编号前缀）
2. 📦 自动提交并推送当前分支
3. 🍒 Cherry-pick到目标分支并推送
4. 🔄 错误处理和冲突提示
5. 🎯 使用Claude Code内置工具，更稳定可靠
6. 🤖 项目自动化脚本会自动添加任务编号

## 核心原则
1. **安全第一**：在代码提交之前，先进行stash操作，确保所有代码得到安全备份
2. **用户友好**：遇到问题时提供清晰的选择，而非死板停止

## 实现说明
完全使用Claude Code的内置git工具，避免复杂的bash脚本：
- 直接使用Claude Code的git集成能力
- 用内置AI能力直接分析diff并生成commit message（不包含任务编号）
- 更好的错误处理和用户体验
- 依赖项目自动化脚本添加任务编号前缀

## 执行流程

使用Claude Code内置能力智能完成以下任务：

**注意：** 如果发现任何问题，立即停止并告知用户
**注意：** 不添加 `[TASK-XXX]` 等任务编号前缀，这将由项目的自动化脚本处理。

### 1. 环境检查
使用Bash工具检查：
- Git仓库状态
- 当前分支（不能是master/main）
- 工作目录清洁度

### 2. 智能生成Commit Message
智能分析代码变更生成简洁的commit message（无任务编号）

### 3. 执行提交、推送、同步工作
执行git操作（提交、推送、cherry-pick）

### 4. 返回原分支
任务执行完毕后，回到初始分支

现在开始执行智能提交和同步到目标分支: **$ARGUMENTS**
~~~



## A2A协议

现代企业往往部署了多个功能各异的`AI`智能体，它们由不同团队使用不同技术栈开发，服务于`CRM`、`HRM`、供应链管理等不同业务系统。在A2A协议问世前，这些智能体如同信息孤岛，难以实现有效沟通和协作，严重限制了AI在解决复杂业务问题上的潜力。

`A2A`协议的核心使命是打破这些技术壁垒，让不同来源、不同技术架构的 `AI` 智能体能够安全地交换信息、高效协作，共同完成跨企业平台或应用的复杂任务。这就像为智能体团队建立了一套统一的"语言"和"工作规范"，确保它们能够"理解彼此、协同工作"。

`A2A`协议的发布在业界引起强烈反响。超过50家技术巨头和领先服务商宣布支持A2A，包括：

- 技术平台：`Atlassian`、`Box`、`Cohere`、`Intuit`、`Langchain`、`MongoDB`、`PayPal`、`Salesforce`、`SAP`、`ServiceNow`、`UKG`、`Workday`
- 服务机构：`Accenture`、`BCG`、`Capgemini`、`Deloitte`、`KPMG`、`PwC`、`TCS`、`Wipro`

如此强大的生态支持，预示着 `A2A` 有望成为未来智能体交互的行业标准。

A2A是一项开放协议，它补充了[Anthropic](https://zhida.zhihu.com/search?content_id=256214079&content_type=Article&match_order=1&q=Anthropic&zhida_source=entity)的模型上下文协议 ([MCP](https://zhida.zhihu.com/search?content_id=256214079&content_type=Article&match_order=1&q=MCP&zhida_source=entity))，后者为智能体提供了有用的工具和上下文。借鉴谷歌内部在扩展智能体系统方面的专业知识，谷歌云设计了A2A协议以应对在为客户部署大规模多智能体系统时发现的挑战。A2A使开发者能够构建能够与使用该协议构建的任何其他智能体连接的智能体，并为用户提供了组合来自不同提供商的智能体的灵活性。关键在于，企业将受益于一种跨不同平台和云环境管理其智能体的标准化方法。谷歌云相信这种普遍的互操作性对于充分发挥协作式AI智能体的潜力至关重要。

https://mp.weixin.qq.com/s/orHUfAY0k4wLl8mAgsJerg

https://zhuanlan.zhihu.com/p/1893573445989687744
