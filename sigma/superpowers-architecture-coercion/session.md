# 学习会话：Superpowers 架构强制力与工作流约束哲学

## 学习者画像
- 水平：中高级（能编写独立 Skill，理解 Prompt 约束，但在大规模任务上遇到"管不住模型"瓶颈）
- 语言：中文（技术术语保留英文）
- 开始时间：2026-04-19
- 目标：从 superpowers 项目中提取"工作流强制力"模式，构建可执行的 AI 研发流水线

## 概念地图

### 维度一：强制约束与心理学提示词（Coercion & Persuasion）
| # | 概念 | 对应源码 | 前置依赖 | 状态 | 掌握度 | 最近复习 | 复习间隔 |
|---|------|----------|----------|------|--------|----------|----------|
| 1 | 会话启动劫持：Hook 作为"第一印象"攻击向量 | hooks/session-start, using-superpowers/SKILL.md | - | 已掌握 | 80% | 2026-04-19 | 1d |
| 2 | Prompt 中的说服心理学：权威、承诺、稀缺性 | writing-skills/persuasion-principles.md | 1 | 已掌握 | 80% | 2026-04-19 | 1d |
| 3 | 反合理化表格与红旗检测：预判模型的借口 | systematic-debugging 红旗表, TDD 红旗表, verification-before-completion 合理化预防表 | 2 | 已掌握 | 80% | 2026-04-19 | 1d |

### 维度二：工程流转与状态交接（Agentic SDLC）
| # | 概念 | 对应源码 | 前置依赖 | 状态 | 掌握度 | 最近复习 | 复习间隔 |
|---|------|----------|----------|------|--------|----------|----------|
| 4 | 头脑风暴：brainstorming 如何在写代码前强制收敛需求 | skills/brainstorming/SKILL.md | 1, 2 | 已掌握 | 80% | 2026-04-19 | 1d |
| 5 | 计划即契约：writing-plans 如何创建不可违背的执行规格 | skills/writing-plans/SKILL.md | 3, 4 | 已掌握 | 80% | 2026-04-19 | 1d |
| 6 | 计划执行交接：executing-plans 与 subagent-driven-development 的双模式 | skills/executing-plans/SKILL.md, skills/subagent-driven-development/SKILL.md | 5 | 已掌握 | 75% | 2026-04-19 | 1d |
| 7 | Git Worktree 隔离：using-git-worktrees 如何实现分支物理隔离 | skills/using-git-worktrees/SKILL.md | 4 | 已掌握 | 75% | 2026-04-19 | 1d |

### 维度三：闭环验证防御（Verification-Before-Completion）
| # | 概念 | 对应源码 | 前置依赖 | 状态 | 掌握度 | 最近复习 | 复习间隔 |
|---|------|----------|----------|------|--------|----------|----------|
| 8 | TDD 铁律：红-绿-重构作为强制门控 | skills/test-driven-development/SKILL.md | 2, 3 | 已掌握 | 85% | 2026-04-19 | 1d |
| 9 | 完成前验证："禁止虚假成功"防御与 Gate Function | skills/verification-before-completion/SKILL.md | 3, 8 | 已掌握 | 80% | 2026-04-19 | 1d |
| 10 | 系统化调试：四阶段根因分析与架构质疑机制 | skills/systematic-debugging/SKILL.md, defense-in-depth.md, root-cause-tracing.md | 3, 9 | 已掌握 | 80% | 2026-04-19 | 1d |

### 综合
| # | 概念 | 对应源码 | 前置依赖 | 状态 | 掌握度 | 最近复习 | 复习间隔 |
|---|------|----------|----------|------|--------|----------|----------|
| 11 | 设计你自己的强制系统：全链路综合与实践 | - | 1-10 | 已掌握 | 72% | 2026-04-19 | 1d |

## 误区记录
| # | 概念 | 误区 | 根因 | 状态 | 反例 |
|---|------|------|------|------|------|
| 1 | 反合理化表格 | 误以为找借口的行为源于 Commitment 原则 | 过度套用 Commitment 框架，未理解借口的本质是训练数据中人类逃避行为模式的复现 | 已纠正 | 追问：借口 → 反驳配对预加载到上下文后，模型的自生成文本会触发什么？ |
| 2 | 概念 6+7 共同原则 | 误以为 spec→quality 审查顺序和 worktree 基线验证的共同原则是 Commitment | 再次过度套用 Commitment 框架，将所有机制都归结为承诺/自洽 | 已纠正 | 追问：如果基线就是坏的，后续验证有意义吗？如果代码不符合 spec，代码质量审查有价值吗？ |
| 3 | 分层防御映射 | 倾向于把所有问题归到"流程没走对"（Layer 1/3），忽略模型在不同阶段的失败动机不同 | 未区分跳步（流程问题）vs 找借口（心理问题）vs 假完成（证据问题） | 已纠正 | 跳步→Layer3门控，找借口→Layer2反合理化，假完成→Layer4 Gate Function |

## 会话日志
- [2026-04-19] 会话开始。诊断水平：中高级。构建学习路线图。
- [2026-04-19] 按用户要求重组概念地图，纳入四个核心技能及 using-git-worktrees
- [2026-04-19] 开始概念 1：会话启动劫持
- [2026-04-19] 概念 1 已掌握（80%）——三层架构：Hook程序性注入 + 标签注意力加权 + Authority语言训练模式复现。记录误区：误以为标签名有内在"高权重"，实际是训练数据统计模式。
- [2026-04-19] 概念 2 已掌握（80%）——三原则协同：Authority外部规则 + Commitment自生成上下文自洽压力 + Social Proof规范锚定。关键洞察：Liking的危险在于引入竞争性目标（讨好用户），Commitment的核心是模型自输出成为后续行为的约束上下文。
- [2026-04-19] 概念 3 已掌握（80%）——反合理化设计模式：不阻止偷懒想法，预判它一定出现并预装 When/Then 触发动作。纠正误区：找借口不是 Commitment 驱动，是训练数据中人类逃避模式的复现。关键短语：Simple code breaks. Test takes 30 seconds. / STOP. Return to Phase 1. / Delete it. Start over.
- [2026-04-19] 概念 4 已掌握（80%）——brainstorming 双重门控：口头→书面是转化而非转录，必然产生偏差。两次用户批准：第一次锁定方向，第二次捕获文档化偏差。spec 文档是下游链的契约/根门控。
- [2026-04-19] 概念 5 已掌握（80%）——writing-plans 核心架构原则：链式传递中的"零上下文"原则。每个交接点的接收方都是零上下文启动，因此每个交接输出必须完全自包含。三个 No Placeholders 背后是同一原则：任何依赖隐含上下文的交接都是失败点。
- [2026-04-19] 概念 6+7 已掌握（75%）——共同原则：先建立已知良好基线再构建。spec compliance 先于 code quality（做正确的事 vs 把事做正确），worktree 先验证基线测试通过再开发。纠正误区：不是 Commitment，是顺序门控（Sequential Gating）。记录重复误区：过度套用 Commitment 框架。
- [2026-04-19] 概念 8 已掌握（85%）——TDD Delete it = 消除偏见源，和 worktree 基线、spec→quality 审查是同一模式：干净基线原则。Tests-first 回答"应该做什么"，tests-after 回答"代码做了什么"。
- [2026-04-19] 概念 9 已掌握（80%）——Gate Function 五步：IDENTIFY→RUN→READ→VERIFY→CLAIM。核心原则：Evidence before claims, always。不信任 agent "success" 报告，用 VCS diff 作为 evidence。
- [2026-04-19] 概念 10 已掌握（80%）——3 次修复阈值的本质不是计数而是模式识别：每次修复暴露新的耦合/状态问题 = 架构缺陷而非 bug。最小可辨识模式。
- [2026-04-19] 概念 11 综合实践（72%）——核心差距：能识别单个模式但难以自发组织成分层防御体系。诊断失败动机并选择防御层的能力偏弱。五层防御框架：Layer0注入→Layer1锁定→Layer2反合理化→Layer3顺序门控→Layer4证据验证。后续练习方向：用实际案例做诊断+设计+测试循环。
