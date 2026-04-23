---
name: writing-skills
description: 在创建新技能、编辑现有技能或部署前验证技能时使用
---

# 编写技能

## 概述

**编写技能就是将测试驱动开发（TDD）应用于流程文档。**

**个人技能存放在特定于代理的目录中（Claude Code 为 `~/.claude/skills`，Codex 为 `~/.agents/skills/`）**

你编写测试用例（使用子代理的压力场景），观察它们失败（基线行为），编写技能（文档），观察测试通过（代理遵从），然后重构（堵住漏洞）。

**核心原则：** 如果你没有观察过代理在没有该技能的情况下失败，你就不知道该技能是否教授了正确的内容。

**前置要求：** 在使用此技能之前，你必须理解 superpowers:test-driven-development。该技能定义了基本的 RED-GREEN-REFACTOR 循环。本技能将 TDD 适配到文档编写中。

**官方指南：** 关于 Anthropic 官方的技能编写最佳实践，请参阅 anthropic-best-practices.md。本文档提供了额外的模式和指南，与本技能中 TDD 的方法互为补充。

## 什么是技能？

**技能**是经过验证的技术、模式或工具的参考指南。技能帮助未来的 Claude 实例找到并应用有效的方法。

**技能是：** 可复用的技术、模式、工具、参考指南

**技能不是：** 关于你如何解决某个问题的叙事性描述

## 技能的 TDD 映射

| TDD 概念 | 技能创建 |
|----------|----------|
| **测试用例** | 使用子代理的压力场景 |
| **生产代码** | 技能文档 (SKILL.md) |
| **测试失败 (RED)** | 代理在没有技能时违反规则（基线） |
| **测试通过 (GREEN)** | 代理在有技能时遵从规则 |
| **重构** | 在保持遵从的同时堵住漏洞 |
| **先写测试** | 在编写技能之前运行基线场景 |
| **观察失败** | 记录代理使用的确切合理化借口 |
| **最小代码** | 编写针对这些具体违规的技能 |
| **观察通过** | 验证代理现在遵从规则 |
| **重构循环** | 发现新的合理化借口 → 堵住 → 重新验证 |

整个技能创建过程遵循 RED-GREEN-REFACTOR。

## 何时创建技能

**适合创建的情况：**
- 该技术对你来说并非显而易见
- 你会在多个项目中反复参考
- 该模式具有广泛适用性（非项目特定）
- 其他人也能受益

**不适合创建的情况：**
- 一次性解决方案
- 在其他地方已有完善文档的标准实践
- 项目特定的约定（应放在 CLAUDE.md 中）
- 机械性约束（如果可以用正则/验证来强制执行，就自动化它——把文档留给需要判断力的场景）

## 技能类型

### 技术型
具有可遵循步骤的具体方法（如 condition-based-waiting、root-cause-tracing）

### 模式型
思考问题的方式（如 flatten-with-flags、test-invariants）

### 参考型
API 文档、语法指南、工具文档（如 office 文档）

## 目录结构

```
skills/
  skill-name/
    SKILL.md              # 主参考文档（必需）
    supporting-file.*     # 仅在需要时
```

**扁平命名空间** — 所有技能在同一个可搜索的命名空间中

**需要独立文件的场景：**
1. **大型参考文档**（100+ 行）— API 文档、全面的语法说明
2. **可复用工具** — 脚本、实用程序、模板

**保持内联的内容：**
- 原则和概念
- 代码模式（< 50 行）
- 其他所有内容

## SKILL.md 结构

**前置元数据 (YAML)：**
- 两个必需字段：`name` 和 `description`（所有支持的字段见 [agentskills.io/specification](https://agentskills.io/specification)）
- 总计最多 1024 个字符
- `name`：仅使用字母、数字和连字符（无括号、特殊字符）
- `description`：第三人称，仅描述何时使用（而非做什么）
  - 以 "Use when..." 开头，聚焦于触发条件
  - 包含具体的症状、情境和上下文
  - **切勿总结技能的流程或工作方式**（原因见 CSO 部分）
  - 尽量控制在 500 字符以内

```markdown
---
name: Skill-Name-With-Hyphens
description: Use when [具体的触发条件和症状]
---

# 技能名称

## 概述
这是什么？用 1-2 句话说明核心原则。

## 何时使用
[如果决策不明显，使用小型内联流程图]

包含症状和用例的要点列表
何时不适用

## 核心模式（适用于技术型/模式型）
修改前/修改后的代码对比

## 快速参考
用于快速查阅常见操作的表格或要点

## 实现方式
简单模式使用内联代码
大型参考或可复用工具使用文件链接

## 常见错误
出错的情况 + 修复方法

## 实际效果（可选）
具体的结果
```


## Claude 搜索优化 (CSO)

**对发现技能至关重要：** 未来的 Claude 需要能找到你的技能

### 1. 丰富的描述字段

**目的：** Claude 通过阅读 description 来决定为给定任务加载哪些技能。让它回答："我现在应该阅读这个技能吗？"

**格式：** 以 "Use when..." 开头，聚焦于触发条件

**关键：描述 = 何时使用，而非技能做什么**

描述应仅描述触发条件。切勿在描述中总结技能的流程或工作方式。

**为什么这很重要：** 测试表明，当描述总结了技能的工作流程时，Claude 可能会遵循描述而非阅读完整的技能内容。一个描述为"任务间进行代码审查"的技能导致 Claude 只做了一次审查，尽管该技能的流程图清楚地显示需要两次审查（规格合规性审查和代码质量审查）。

当描述改为"Use when executing implementation plans with independent tasks"（无工作流总结）后，Claude 正确地阅读了流程图并遵循了两阶段审查流程。

**陷阱：** 总结工作流的描述会创建一条 Claude 会走的捷径。技能正文变成了 Claude 会跳过的文档。

```yaml
# ❌ 错误：总结了工作流 - Claude 可能遵循此描述而非阅读技能
description: Use when executing plans - dispatches subagent per task with code review between tasks

# ❌ 错误：过多流程细节
description: Use for TDD - write test first, watch it fail, write minimal code, refactor

# ✅ 正确：仅触发条件，无工作流总结
description: Use when executing implementation plans with independent tasks in the current session

# ✅ 正确：仅触发条件
description: Use when implementing any feature or bugfix, before writing implementation code
```

**内容要求：**
- 使用具体的触发条件、症状和情境来表明此技能适用
- 描述*问题*（竞态条件、不一致行为）而非*特定语言的症状*（setTimeout、sleep）
- 保持触发条件与技术无关，除非技能本身是技术特定的
- 如果技能是技术特定的，在触发条件中明确说明
- 使用第三人称（会被注入系统提示词）
- **切勿总结技能的流程或工作方式**

```yaml
# ❌ 错误：过于抽象、模糊，不包含何时使用
description: For async testing

# ❌ 错误：第一人称
description: I can help you with async tests when they're flaky

# ❌ 错误：提及技术但技能并非针对该技术
description: Use when tests use setTimeout/sleep and are flaky

# ✅ 正确：以 "Use when" 开头，描述问题，无工作流
description: Use when tests have race conditions, timing dependencies, or pass/fail inconsistently

# ✅ 正确：技术特定技能，触发条件明确
description: Use when using React Router and handling authentication redirects
```

### 2. 关键词覆盖

使用 Claude 会搜索的词语：
- 错误信息："Hook timed out"、"ENOTEMPTY"、"race condition"
- 症状："flaky"、"hanging"、"zombie"、"pollution"
- 同义词："timeout/hang/freeze"、"cleanup/teardown/afterEach"
- 工具：实际的命令、库名、文件类型

### 3. 描述性命名

**使用主动语态，动词优先：**
- ✅ `creating-skills` 而非 `skill-creation`
- ✅ `condition-based-waiting` 而非 `async-test-helpers`

### 4. Token 效率（关键）

**问题：** getting-started 和频繁引用的技能会加载到每次对话中。每个 token 都很宝贵。

**目标字数：**
- getting-started 工作流：每个 < 150 词
- 频繁加载的技能：总计 < 200 词
- 其他技能：< 500 词（同样要简洁）

**技巧：**

**将细节移到工具帮助中：**
```bash
# ❌ 错误：在 SKILL.md 中记录所有标志
search-conversations 支持 --text, --both, --after DATE, --before DATE, --limit N

# ✅ 正确：引用 --help
search-conversations 支持多种模式和过滤器。运行 --help 查看详情。
```

**使用交叉引用：**
```markdown
# ❌ 错误：重复工作流细节
When searching, dispatch subagent with template...
[20 行重复的指令]

# ✅ 正确：引用其他技能
始终使用子代理（节省 50-100 倍上下文）。必需：使用 [other-skill-name] 进行工作流操作。
```

**压缩示例：**
```markdown
# ❌ 错误：冗长的示例（42 词）
your human partner: "How did we handle authentication errors in React Router before?"
You: I'll search past conversations for React Router authentication patterns.
[Dispatch subagent with search query: "React Router authentication error handling 401"]

# ✅ 正确：精简的示例（20 词）
Partner: "How did we handle auth errors in React Router?"
You: Searching...
[Dispatch subagent → synthesis]
```

**消除冗余：**
- 不要重复交叉引用技能中已有的内容
- 不要解释从命令中显而易见的内容
- 不要包含同一模式的多个示例

**验证：**
```bash
wc -w skills/path/SKILL.md
# getting-started 工作流：目标每个 < 150
# 其他频繁加载的：目标总计 < 200
```

**以你做的事或核心洞察来命名：**
- ✅ `condition-based-waiting` > `async-test-helpers`
- ✅ `using-skills` 而非 `skill-usage`
- ✅ `flatten-with-flags` > `data-structure-refactoring`
- ✅ `root-cause-tracing` > `debugging-techniques`

**动名词 (-ing) 适合描述流程：**
- `creating-skills`、`testing-skills`、`debugging-with-logs`
- 主动的，描述你正在执行的操作

### 5. 交叉引用其他技能

**在编写引用其他技能的文档时：**

仅使用技能名称，并带有明确的要求标记：
- ✅ 正确：`**REQUIRED SUB-SKILL:** Use superpowers:test-driven-development`
- ✅ 正确：`**REQUIRED BACKGROUND:** You MUST understand superpowers:systematic-debugging`
- ❌ 错误：`See skills/testing/test-driven-development`（不清楚是否必需）
- ❌ 错误：`@skills/testing/test-driven-development/SKILL.md`（强制加载，消耗上下文）

**为什么不用 @ 链接：** `@` 语法会立即强制加载文件，在你需要之前就消耗 200k+ 上下文。

## 流程图使用

```dot
digraph when_flowchart {
    "Need to show information?" [shape=diamond];
    "Decision where I might go wrong?" [shape=diamond];
    "Use markdown" [shape=box];
    "Small inline flowchart" [shape=box];

    "Need to show information?" -> "Decision where I might go wrong?" [label="yes"];
    "Decision where I might go wrong?" -> "Small inline flowchart" [label="yes"];
    "Decision where I might go wrong?" -> "Use markdown" [label="no"];
}
```

**仅在以下情况使用流程图：**
- 不明显的决策点
- 可能过早停止的流程循环
- "何时使用 A vs B" 的决策

**切勿在以下情况使用流程图：**
- 参考材料 → 使用表格、列表
- 代码示例 → 使用 Markdown 代码块
- 线性指令 → 使用编号列表
- 没有语义含义的标签（step1、helper2）

请参阅 @graphviz-conventions.dot 了解 graphviz 样式规则。

**为你的伙伴可视化：** 使用本目录中的 `render-graphs.js` 将技能的流程图渲染为 SVG：
```bash
./render-graphs.js ../some-skill           # 每个图表单独渲染
./render-graphs.js ../some-skill --combine # 所有图表合并为一个 SVG
```

## 代码示例

**一个优秀的示例胜过多个平庸的示例**

选择最相关的语言：
- 测试技术 → TypeScript/JavaScript
- 系统调试 → Shell/Python
- 数据处理 → Python

**好的示例：**
- 完整且可运行
- 有良好的注释解释原因
- 来自真实场景
- 清晰地展示模式
- 可以直接改编（非通用模板）

**不要：**
- 用 5+ 种语言实现
- 创建填空模板
- 编写生造的示例

你擅长代码移植 — 一个优秀的示例就足够了。

## 文件组织

### 自包含技能
```
defense-in-depth/
  SKILL.md    # 所有内容内联
```
适用场景：所有内容都放得下，不需要大型参考文档

### 带可复用工具的技能
```
condition-based-waiting/
  SKILL.md    # 概述 + 模式
  example.ts  # 可改编的可用辅助工具
```
适用场景：工具是可复用代码，而非叙事性描述

### 带大型参考的技能
```
pptx/
  SKILL.md       # 概述 + 工作流
  pptxgenjs.md   # 600 行 API 参考
  ooxml.md       # 500 行 XML 结构
  scripts/       # 可执行工具
```
适用场景：参考材料太大，无法内联

## 铁律（与 TDD 相同）

```
没有失败的测试，就不写技能
```

这适用于新技能和对现有技能的编辑。

先写技能再测试？删掉它。重新开始。
编辑技能不做测试？同样是违规。

**没有例外：**
- 不适用于"简单的补充"
- 不适用于"只是加一个章节"
- 不适用于"文档更新"
- 不要保留未测试的更改作为"参考"
- 不要在运行测试时"顺便调整"
- 删除就是删除

**前置要求：** superpowers:test-driven-development 技能解释了为什么这很重要。同样的原则适用于文档。

## 测试所有技能类型

不同的技能类型需要不同的测试方法：

### 纪律执行型技能（规则/要求）

**示例：** TDD、完成前验证、编码前设计

**测试方法：**
- 学术性问题：它们是否理解规则？
- 压力场景：它们在压力下是否遵从？
- 多重压力组合：时间 + 沉没成本 + 疲劳
- 识别合理化借口并添加明确的反驳

**成功标准：** 代理在最大压力下遵循规则

### 技术型技能（操作指南）

**示例：** condition-based-waiting、root-cause-tracing、defensive-programming

**测试方法：**
- 应用场景：它们能否正确应用该技术？
- 变体场景：它们能否处理边界情况？
- 信息缺失测试：指令是否有遗漏？

**成功标准：** 代理成功将技术应用于新场景

### 模式型技能（心智模型）

**示例：** reducing-complexity、信息隐藏概念

**测试方法：**
- 识别场景：它们能否识别模式何时适用？
- 应用场景：它们能否使用该心智模型？
- 反例：它们是否知道何时不适用？

**成功标准：** 代理正确识别何时/如何应用模式

### 参考型技能（文档/API）

**示例：** API 文档、命令参考、库指南

**测试方法：**
- 检索场景：它们能否找到正确的信息？
- 应用场景：它们能否正确使用找到的信息？
- 缺口测试：常见用例是否都被覆盖？

**成功标准：** 代理找到并正确应用参考信息

## 跳过测试的常见合理化借口

| 借口 | 现实 |
|------|------|
| "技能显然很清楚" | 对你清楚 ≠ 对其他代理清楚。去测试。 |
| "这只是参考" | 参考文档也可能有缺口、不清楚的部分。测试检索。 |
| "测试是小题大做" | 未测试的技能一定有问题。15 分钟测试节省数小时。 |
| "有问题再测" | 问题 = 代理无法使用技能。部署前测试。 |
| "测试太繁琐" | 测试比在生产中调试糟糕的技能要轻松得多。 |
| "我有信心它没问题" | 过度自信保证出问题。反正都要测试。 |
| "学术审查就够了" | 阅读 ≠ 使用。测试应用场景。 |
| "没时间测试" | 部署未测试的技能浪费更多时间来修复。 |

**所有这些都意味着：部署前测试。没有例外。**

## 抵御合理化借口的技能加固

执行纪律的技能（如 TDD）需要抵抗合理化。代理很聪明，在压力下会找到漏洞。

**心理学说明：** 理解说服技术为什么有效有助于你系统地应用它们。请参阅 persuasion-principles.md 了解研究基础（Cialdini, 2021; Meincke et al., 2025）关于权威、承诺、稀缺、社会认同和统一原则。

### 明确堵住每个漏洞

不要只陈述规则 — 还要禁止特定的变通方法：

<Bad>
```markdown
Write code before test? Delete it.
```
</Bad>

<Good>
```markdown
Write code before test? Delete it. Start over.

**No exceptions:**
- Don't keep it as "reference"
- Don't "adapt" it while writing tests
- Don't look at it
- Delete means delete
```
</Good>

### 应对"精神 vs 字面"的论点

在早期添加基础原则：

```markdown
**Violating the letter of the rules is violating the spirit of the rules.**
```

这切断了一整类"我在遵循精神"的合理化借口。

### 构建合理化借口表

从基线测试中收集合理化借口（见下方测试部分）。代理的每个借口都要放入表中：

```markdown
| Excuse | Reality |
|--------|---------|
| "Too simple to test" | Simple code breaks. Test takes 30 seconds. |
| "I'll test after" | Tests passing immediately prove nothing. |
| "Tests after achieve same goals" | Tests-after = "what does this do?" Tests-first = "what should this do?" |
```

### 创建红旗清单

让代理在合理化时容易自我检查：

```markdown
## Red Flags - STOP and Start Over

- Code before test
- "I already manually tested it"
- "Tests after achieve the same purpose"
- "It's about spirit not ritual"
- "This is different because..."

**All of these mean: Delete code. Start over with TDD.**
```

### 更新 CSO 以包含违规症状

在描述中添加：你即将违反规则时的症状：

```yaml
description: use when implementing any feature or bugfix, before writing implementation code
```

## 技能的 RED-GREEN-REFACTOR

遵循 TDD 循环：

### RED：编写失败测试（基线）

在没有技能的情况下使用子代理运行压力场景。记录确切行为：
- 它们做了什么选择？
- 它们使用了什么合理化借口（逐字记录）？
- 哪些压力触发了违规？

这就是"观察测试失败" — 在编写技能之前，你必须看到代理自然会做什么。

### GREEN：编写最小技能

编写针对那些具体合理化借口的技能。不要为假设情况添加额外内容。

在有技能的情况下运行相同场景。代理现在应该遵从。

### REFACTOR：堵住漏洞

代理找到了新的合理化借口？添加明确的反驳。重新测试直到无懈可击。

**测试方法：** 请参阅 @testing-skills-with-subagents.md 了解完整的测试方法：
- 如何编写压力场景
- 压力类型（时间、沉没成本、权威、疲劳）
- 系统性地堵住漏洞
- 元测试技术

## 反模式

### ❌ 叙事性示例
"在 2025-10-03 的会话中，我们发现空的 projectDir 导致了..."
**为什么不好：** 过于具体，不可复用

### ❌ 多语言稀释
example-js.js、example-py.py、example-go.go
**为什么不好：** 质量平庸，维护负担大

### ❌ 流程图中的代码
```dot
step1 [label="import fs"];
step2 [label="read file"];
```
**为什么不好：** 无法复制粘贴，难以阅读

### ❌ 泛泛的标签
helper1、helper2、step3、pattern4
**为什么不好：** 标签应该有语义含义

## 停：在进入下一个技能之前

**在编写任何技能之后，你必须停下来完成部署流程。**

**不要：**
- 批量创建多个技能而不逐个测试
- 在当前技能验证之前进入下一个技能
- 因为"批量处理更高效"而跳过测试

**下方的部署清单对每个技能都是强制性的。**

部署未测试的技能 = 部署未测试的代码。这是违反质量标准的。

## 技能创建清单（TDD 适配版）

**重要：使用 TodoWrite 为下方每个清单项创建待办事项。**

**RED 阶段 - 编写失败测试：**
- [ ] 创建压力场景（纪律型技能需要 3+ 个组合压力）
- [ ] 在没有技能的情况下运行场景 — 逐字记录基线行为
- [ ] 识别合理化借口/失败中的模式

**GREEN 阶段 - 编写最小技能：**
- [ ] 名称仅使用字母、数字、连字符（无括号/特殊字符）
- [ ] YAML 前置元数据包含必需的 `name` 和 `description` 字段（最多 1024 字符；见 [规范](https://agentskills.io/specification)）
- [ ] 描述以 "Use when..." 开头并包含具体的触发条件/症状
- [ ] 描述使用第三人称编写
- [ ] 全文包含搜索关键词（错误、症状、工具）
- [ ] 清晰的概述和核心原则
- [ ] 针对在 RED 阶段识别的具体基线失败进行处理
- [ ] 代码内联或链接到独立文件
- [ ] 一个优秀的示例（非多语言）
- [ ] 在有技能的情况下运行场景 — 验证代理现在遵从

**REFACTOR 阶段 - 堵住漏洞：**
- [ ] 识别测试中新的合理化借口
- [ ] 添加明确的反驳（如果是纪律型技能）
- [ ] 从所有测试迭代中构建合理化借口表
- [ ] 创建红旗清单
- [ ] 重新测试直到无懈可击

**质量检查：**
- [ ] 仅在决策不明显时使用小型流程图
- [ ] 快速参考表格
- [ ] 常见错误章节
- [ ] 无叙事性描述
- [ ] 仅在需要工具或大型参考时使用辅助文件

**部署：**
- [ ] 将技能提交到 git 并推送到你的 fork（如果已配置）
- [ ] 考虑通过 PR 贡献回来（如果具有广泛用途）

## 发现流程

未来的 Claude 如何找到你的技能：

1. **遇到问题**（"测试不稳定"）
3. **找到技能**（描述匹配）
4. **扫描概述**（这相关吗？）
5. **阅读模式**（快速参考表格）
6. **加载示例**（仅在实现时）

**优化这个流程** — 尽早且频繁地放置可搜索的术语。

## 总结

**创建技能就是将 TDD 应用于流程文档。**

同样的铁律：没有失败的测试就不写技能。
同样的循环：RED（基线）→ GREEN（编写技能）→ REFACTOR（堵住漏洞）。
同样的好处：更好的质量，更少的意外，无懈可击的结果。

如果你在编写代码时遵循 TDD，在编写技能时也应遵循。这是将同样的纪律应用于文档。
