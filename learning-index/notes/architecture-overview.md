# Superpowers 架构总览伴读

这份笔记的目标不是翻译 Superpowers 的 README，而是帮助你理解它的设计：它为什么不是一个普通“技能集合”，而是一套约束 AI 编程代理的完整软件开发方法论。

## 一句话理解

Superpowers 把 AI 编程从“用户给需求，agent 直接写代码”改造成一条有门禁的流水线：先澄清设计，再写计划，再隔离实现，再用 TDD 和审查验证，最后再收尾。

核心入口：

- `../README.md`：整体方法论、安装方式、基本工作流、哲学。
- `../CLAUDE.md`：Claude Code 环境下的贡献约束和质量门禁。
- `../AGENTS.md`：给不同 agent 的共同贡献规则。
- `../skills/`：真正塑造 agent 行为的技能协议。
- `../tests/`：验证技能行为是否真的生效的测试体系。

## 仓库结构怎么读

建议按这个顺序阅读：

1. `README.md`
   - 先看 “How it works” 和 “The Basic Workflow”。
   - 目标：建立整体心智模型，理解它为什么强调设计、计划、TDD、审查。

2. `skills/using-superpowers/SKILL.md`
   - 这是引导 agent 使用技能系统的 bootstrap。
   - 目标：理解为什么技能需要“自动触发”，而不是靠用户手动记住每一步。

3. `skills/brainstorming/SKILL.md`
   - 重点看硬门禁：设计没确认前不能实现。
   - 目标：理解它如何阻止 agent 过早写代码。

4. `skills/writing-plans/SKILL.md`
   - 重点看它怎样把设计转成可执行任务。
   - 目标：理解好计划如何降低执行阶段的自由解释空间。

5. `skills/subagent-driven-development/SKILL.md`
   - 重点看子代理执行、规格符合性审查、代码质量审查。
   - 目标：理解“让多个 agent 干活”不是并发本身，而是隔离上下文和强制复核。

6. `skills/test-driven-development/SKILL.md`
   - 重点看 RED-GREEN-REFACTOR。
   - 目标：理解它如何把“我完成了”变成“有失败再通过的证据”。

7. `skills/systematic-debugging/SKILL.md` 和 `skills/verification-before-completion/SKILL.md`
   - 目标：理解它如何反对猜测式修 bug，以及完成前为什么必须验证。

8. `docs/testing.md` 与 `tests/`
   - 目标：理解技能不是只靠文本漂亮，而是要通过真实 agent 会话 transcript 验证行为。

## 核心流程

```text
User Intent
  ↓
using-superpowers bootstrap
  ↓
brainstorming：澄清目标，形成设计
  ↓
writing-plans：把设计拆成具体任务
  ↓
using-git-worktrees：隔离工作区
  ↓
test-driven-development：红绿重构
  ↓
subagent-driven-development / executing-plans：执行任务
  ↓
requesting-code-review：审查规格和质量
  ↓
verification-before-completion：确认真的完成
  ↓
finishing-a-development-branch：收尾、合并或保留
```

这个流程的重点是：每一步都减少 agent 自由发挥的空间，并增加可检查证据。

## 为什么它强调“human partner”

`CLAUDE.md` 和 `AGENTS.md` 中反复强调保护 human partner，这是一个很重要的设计信号。

它不是礼貌用语，而是在重设 agent 的责任边界：

- agent 不是为了“看起来勤快”而提交 PR；
- agent 要保护人的声誉；
- 如果问题不真实、改动不属于 core、没有证据，就应该阻止行动；
- 低质量自动化不是帮助，而是把风险转嫁给人。

你在自己的项目中也可以迁移这个思想：给 agent 的最高目标不应该只是“完成任务”，而应该是“保护项目和人的长期信誉”。

## 为什么技能是“行为协议”，不是普通文档

Superpowers 的 `SKILL.md` 通常不是简单说明“这个技能是什么”。它会包含：

- 什么时候必须使用；
- 什么行为绝对禁止；
- 具体步骤；
- 红旗和反模式；
- 检查清单；
- 输出格式；
- 何时停止、何时升级给用户。

这说明技能的本质是 agent 行为协议。它像一段“软代码”，通过自然语言约束模型行为。

## 支撑系统

### Harness 适配

Superpowers 支持 Claude Code、Gemini、OpenCode、Codex 等不同环境。值得研究的不是安装命令，而是它如何保持同一套方法论在不同工具中运行。

相关文件：

- `GEMINI.md`
- `docs/README.opencode.md`
- `gemini-extension.json`
- `scripts/sync-to-codex-plugin.sh`

### Hooks

`hooks/` 目录展示了会话启动等生命周期入口。它们让技能体系不是静态文件，而是可以在工具生命周期中被加载和触发。

相关文件：

- `hooks/session-start`
- `hooks/session-start-zh`
- `hooks/hooks.json`
- `hooks/hooks-cursor.json`

### Tests

`docs/testing.md` 说明 Superpowers 如何测试复杂技能：运行真实 Claude Code session，然后验证 transcript。

这点非常值得迁移。因为 AI 行为系统最容易出现的问题是“文档写得很好，但模型实际不照做”。Superpowers 用真实会话测试来缩小这个差距。

## 这套设计最值得学习什么

1. **把流程显式化**
   - 不让 agent 自己决定什么时候设计、什么时候计划、什么时候测试。

2. **把门禁写进技能**
   - 例如设计未批准不能实现，测试未失败不能写生产代码。

3. **把质量责任前置**
   - 不是 PR 后才发现问题，而是在需求、设计、计划、实现阶段都设置检查点。

4. **把用户从“指挥 agent”变成“审批关键决策”**
   - 用户不需要记住所有流程，只需要在关键点确认方向。

5. **把可验证性当成设计目标**
   - 好的 agent 流程应该能被 transcript、测试、diff、审查结果证明。

## 下一步阅读建议

如果你想把它用于自己的项目，下一步不要急着复制目录，而是先回答：

1. 我的项目里，agent 最常犯的错误是什么？
2. 哪些动作必须先问我？
3. 哪些步骤必须有证据才能算完成？
4. 哪些工作可以交给子代理隔离执行？
5. 哪些项目规则应该写进 `CLAUDE.md`，哪些应该写成独立技能？

回答完这些问题，再看 `transfer-patterns.md`，会更容易把 Superpowers 的设计转成你自己的工程体系。
