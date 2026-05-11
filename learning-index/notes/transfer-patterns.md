# Superpowers 可迁移设计模式

这份笔记关注一个问题：Superpowers 的哪些设计思想可以迁移到你自己的项目？

不要把迁移理解成复制 `skills/` 目录。更有价值的是理解它背后的行为约束、流程门禁和证据体系，然后用适合你项目规模的方式重建。

## 行为塑形模式

### 解决什么问题

AI agent 最大的问题通常不是不会写代码，而是太容易：

- 没澄清需求就实现；
- 没计划就改很多文件；
- 没验证就宣称完成；
- 用猜测代替根因分析；
- 为了显得有帮助而做不该做的事。

### Superpowers 怎么做

Superpowers 用技能来塑造行为，而不是只给建议。

典型做法：

- 使用强制语言：MUST、Do NOT、HARD-GATE。
- 列出反模式和红旗，阻止模型合理化偷懒。
- 把流程拆成必须完成的检查清单。
- 规定何时必须停下来问用户。
- 规定什么时候不能继续。

相关入口：

- `../../skills/using-superpowers/SKILL.md`
- `../../skills/brainstorming/SKILL.md`
- `../../skills/test-driven-development/SKILL.md`

### 你自己的项目怎么借鉴

你可以为自己的项目写类似规则：

```text
当任务涉及数据库迁移时，必须先提出迁移风险和回滚方式。
当任务涉及 UI 改动时，必须用浏览器验证，不允许只跑类型检查。
当任务涉及外部 API 时，必须说明边界、错误处理和测试方式。
当用户要求快速修复时，仍然必须先确认根因。
```

重点不是语气强硬，而是把“常犯错误”提前写成不可跳过的流程。

## 流程链模式

### 解决什么问题

单条提示词很难稳定控制复杂开发。agent 会把需求理解、设计、实现、测试、总结混在一起，最后用户很难知道哪里出了问题。

### Superpowers 怎么做

它把开发拆成一条链：

```text
brainstorming
  → writing-plans
  → using-git-worktrees
  → test-driven-development
  → subagent-driven-development / executing-plans
  → requesting-code-review
  → verification-before-completion
  → finishing-a-development-branch
```

每一步都有明确职责：

- brainstorming：把模糊需求变成设计；
- writing-plans：把设计变成可执行任务；
- worktrees：隔离改动；
- TDD：建立失败到成功的证据链；
- subagents：执行和审查分离；
- code review：发现偏离计划和质量问题；
- verification：防止虚假完成；
- finish branch：做最后决策。

### 你自己的项目怎么借鉴

你不一定需要完整复制这条链。可以先迁移最小版本：

```text
需求澄清
  → 设计确认
  → 小任务计划
  → 实现
  → 验证
  → 用户确认
```

如果你的项目复杂，再加入：

- worktree 隔离；
- TDD 强制；
- 子代理审查；
- PR 前完整检查。

## 证据优先模式

### 解决什么问题

AI 最容易把“我认为完成了”当成“已经完成”。这在工程中很危险。

### Superpowers 怎么做

Superpowers 的哲学中有一条：Evidence over claims。

它要求：

- TDD 中必须看到测试先失败再通过；
- debugging 中必须找到根因；
- review 中必须检查代码，而不是相信实现者报告；
- testing 中通过真实 session transcript 验证技能行为；
- PR 中必须描述真实问题，而不是理论问题。

相关入口：

- `../../docs/testing.md`
- `../../tests/claude-code/README.md`
- `../../skills/verification-before-completion/SKILL.md`
- `../../skills/requesting-code-review/SKILL.md`

### 你自己的项目怎么借鉴

你可以把“完成”的定义写清楚：

```text
完成不是代码已修改，而是：
1. 用户路径已验证；
2. 相关测试已通过；
3. 错误场景已检查；
4. 没有引入无关改动；
5. diff 可以解释；
6. 如果不能验证，必须明确说明。
```

## 贡献保护模式

### 解决什么问题

agent 很容易为了“帮忙”而做出对人的声誉有害的事，比如提交低质量 PR、编造问题、忽略项目规则。

### Superpowers 怎么做

`CLAUDE.md` 和 `AGENTS.md` 把 agent 的责任重新定义为：保护 human partner。

它要求在提交 PR 前：

- 读完整 PR 模板；
- 搜索已有 PR；
- 验证问题真实存在；
- 确认改动属于 core；
- 展示完整 diff 并得到人类明确批准。

它还明确拒绝：

- speculative fixes；
- bulk PR；
- fabricated content；
- domain-specific skills 进入 core；
- 没有 eval evidence 的技能重写。

### 你自己的项目怎么借鉴

在你的项目里，也可以写类似规则：

```text
不要为了显得主动而改无关文件。
不要提交没有真实问题背景的修复。
不要绕过测试或 hook。
不要替用户执行会影响外部系统的动作，除非用户明确批准。
```

这类规则的价值是把 agent 从“执行工具”提升为“风险过滤器”。

## 技能即产品接口模式

### 解决什么问题

如果每个 agent 都靠临场提示词工作，项目经验无法沉淀，用户也必须反复解释同样规则。

### Superpowers 怎么做

它把常见工作方式沉淀成技能：

- 每个技能对应一个明确场景；
- 技能有触发条件；
- 技能写清楚步骤和禁止事项；
- 技能可以在不同 harness 中复用；
- 技能可以被测试。

这让流程从“聊天经验”变成“可复用接口”。

### 你自己的项目怎么借鉴

你可以从最常见的 3 个场景开始写技能：

1. 新功能设计技能；
2. Bug 根因分析技能；
3. PR 完成检查技能。

先不要写太多。每个技能都应该回答：

```text
什么时候用？
必须做什么？
禁止做什么？
用户在哪些点审批？
怎样证明完成？
```

## 跨工具适配模式

### 解决什么问题

团队可能同时使用 Claude Code、Gemini、OpenCode、Codex 等工具。如果流程只存在于某一个工具配置中，就很难迁移。

### Superpowers 怎么做

它把核心方法论放在 skills 中，再针对不同 harness 做适配：

- Claude Code 使用插件和 Skill tool；
- Gemini 通过 `GEMINI.md` 引入 bootstrap；
- OpenCode 通过 plugin 注册技能并映射工具；
- Codex 通过同步脚本和插件结构适配。

相关入口：

- `../../GEMINI.md`
- `../../docs/README.opencode.md`
- `../../scripts/sync-to-codex-plugin.sh`
- `../../gemini-extension.json`

### 你自己的项目怎么借鉴

如果你只是个人项目，不需要一开始就做跨工具适配。

但你可以提前分层：

```text
核心规则：写在 skills 或 reusable docs 中
工具接入：写在 CLAUDE.md / GEMINI.md / AGENTS.md 中
项目约束：写在项目根目录说明中
测试方法：写在 tests 或 docs/testing.md 中
```

这样以后换工具时，不需要重写全部流程。

## 不要照抄什么

### 不要照抄它的复杂度

Superpowers 面向多个 harness 和开源用户，复杂度比个人项目高。你的项目可以先做轻量版本。

### 不要照抄它的语气

Superpowers 的语气很强，是因为它要防止大量低质量 agent PR。你的项目可以保留强约束，但语气可以按团队文化调整。

### 不要照抄它的贡献规则

例如“94% PR rejection rate”是这个项目自己的上下文。你可以迁移“保护人的信誉”这个思想，而不是复制这个数字和表达。

### 不要把所有文档都变成技能

技能应该用于会改变 agent 行为的流程。普通背景知识、架构说明、阅读材料不一定要写成技能。

### 不要在没有评估的情况下改行为塑形内容

Superpowers 明确说技能是会影响 agent 行为的代码。你自己的项目也应该这样看待：一旦规则会改变 agent 行为，就要观察它是否真的改善结果。

## 最小迁移方案

如果你想把 Superpowers 的思想用到自己的项目，可以先做这个最小版本：

```text
.claude/
  skills/
    project-brainstorming/SKILL.md
    project-debugging/SKILL.md
    project-finish-check/SKILL.md
CLAUDE.md
```

`CLAUDE.md` 负责项目级规则：

- 哪些动作需要用户批准；
- 哪些文件不能随便动；
- 测试和验证要求；
- 输出语言和沟通习惯。

技能负责流程：

- 设计前不实现；
- bug 先找根因；
- 完成前必须验证。

等这个最小系统稳定后，再考虑：

- subagent 审查；
- worktree 隔离；
- transcript 测试；
- 跨工具适配；
- 可视化伴读页面。
