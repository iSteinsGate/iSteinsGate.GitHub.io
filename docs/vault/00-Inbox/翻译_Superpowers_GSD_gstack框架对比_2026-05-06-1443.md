# Superpowers、GSD 和 gstack：每个 Claude Code 框架实际上约束了什么

> 原文链接：https://medium.com/@tentenco/superpowers-gsd-and-gstack-what-each-claude-code-framework-actually-constrains-12a1560960ad
> 作者：Ewan Mak | 2026年4月
> 翻译时间：2026-05-06

---

Superpowers、GSD 和 gstack 是 2026 年增长最快的三个 Claude Code 框架，截至 4 月分别拥有约 94,000、35,000 和 50,000 个 GitHub Stars。它们从三个完全不同的角度解决同一个根本问题——AI 生成的代码速度快但不可靠。Superpowers 约束开发流程，GSD 约束执行环境，gstack 约束决策视角。本文将拆解它们的源代码和架构，梳理各自的设计假设，找出它们互补的地方，并探讨第四个模式——Andrej Karpathy 的 AutoResearch——它填补了前三者都未触及的空白。

## AI 代码的信任问题

2024 到 2025 年间，AI 编程工具在速度上竞争。到 2026 年，讨论转向了质量和可维护性。根据 ByteIota 的分析，Claude Code 以 46% 的份额领先开发者偏好调查，Cursor 以 19% 位居第二，GitHub Copilot 以 9% 位居第三。但原生 Claude Code 仍然会跳过测试、构建混乱的架构，并在输出中埋下安全隐患。

模型的能力不是瓶颈，纪律才是。你让它添加一个功能，它会交付一些东西——可能有测试，也可能没有；可能符合你讨论的规格，也可能不符合。它就像一个编码飞快但从不开 Pull Request Review 的团队成员。

三个开源框架应运而生，从不同维度对 Agent 行为施加约束。

## Superpowers：通过七阶段流水线实现流程纪律

Jesse Vincent 构建了 Superpowers。他是 Perl 社区的老手——Request Tracker 的创建者（被数千个组织使用）、前 Perl 5 pumpking、Keyboardio 的联合创始人。他于 2025 年 10 月 9 日发布了第一个版本，与 Anthropic 推出 Claude Code 插件系统是同一天。

Anthropic 于 2026 年 1 月 15 日将 Superpowers 纳入其官方插件市场。到 3 月，它已突破 94,000 个 GitHub Stars，巅峰时期每天增长近 2,000 个 Stars。它是今年增长最快的开源项目之一。

**核心假设：AI 不是缺乏能力，而是缺乏结构。施加严格的开发方法论，输出质量就会稳定。**

Superpowers 强制执行七阶段工作流：**头脑风暴 → 规格说明 → 计划 → TDD → 子代理开发 → 审查 → 完成**。有趣的设计选择是强制测试驱动开发。其他框架建议测试，而 Superpowers 会在测试存在之前删除已编写的代码，并强制从测试重新开始。chardet 库使用此方法论发布了 7.0.0 版本——性能提升 41 倍，准确率从 94.5% 提高到 96.8%。

安装只需两条命令：

```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

它也兼容 Cursor、Codex、OpenCode 和 Gemini CLI。

该流水线使长达数小时的自主会话成为可能。Claude 处理每个任务，根据计划和编码标准审查自己的输出，然后继续推进。两小时不偏离的自主工作很常见。

## GSD：上下文隔离作为质量保障

GSD（Get Shit Done）由 Lex Christopherson 构建，他以 TÂCHES 为名。他的定位很直接："我是一个独立开发者。我不写代码——Claude Code 写。"

GSD 解决的是另一个问题：**上下文腐烂**——随着上下文窗口填满，Claude 输出质量下降的现象。社区测试显示，上下文使用超过 50% 后质量开始下降；超过 70%，幻觉急剧增加。项目越大、对话越长，问题越严重。

GSD 的解决方案：**完全上下文隔离**。它将工作拆分为原子任务。每个任务获得一个全新的 Claude 实例和干净的 200K token 上下文窗口。主对话只负责调度——其负载保持在 30-40%。所有项目状态以文本文件形式持久化到磁盘，新会话可以从上次中断的地方继续。

GSD 最初是 Markdown 提示词（v1），后来用 TypeScript 重写（v2）。v2 的重写是必要的，因为 v1 依赖 LLM 阅读指令并配合执行。v2 在 TypeScript 层面控制 Agent 会话——清空上下文、注入文件、管理 Git 分支、跟踪成本和 token、检测死循环、从崩溃中恢复。

质量门控内置其中。Schema 漂移检测会标记没有配套迁移的 ORM 变更。范围缩减检测会捕获计划悄悄删减需求的情况。每个提交都是原子的，可以独立回滚。

## gstack：角色治理取代通用辅助

gstack 由 Y Combinator CEO Garry Tan 创建。在 YC 之前，他是 Palantir 的首批工程师之一，联合创办了 Posterous（后被 Twitter 收购），并构建了 YC 的内部社交网络 Bookface。他于 2026 年 3 月 12 日开源了 gstack，16 天内达到 50,000 个 GitHub Stars。TechCrunch 进行了报道。评价两极分化——有人称之为突破，也有人质疑为什么配置文件能获得如此多关注。

gstack 的设计假设与 Superpowers 和 GSD 处于不同的轴线上。它不关心你的开发流程或上下文窗口健康状况。它治理的是**谁做什么决策**。

该框架目前有 23+ 个斜杠命令（称为"技能"），每个映射到一个特定角色。其中几个值得关注：

- `/office-hours`：以产品顾问身份运行，质疑功能需求背后的用户问题
- `/plan-ceo-review`：以 CEO 身份审查计划，关注用户价值而非实现细节
- `/plan-eng-review`：以工程负责人身份审查技术可行性和架构风险
- `/review`：以高级工程师身份进行代码审查，关注可维护性和框架选择
- `/ship`：以发布经理身份运行，检查发布清单和回滚计划

在表面之下，gstack 做了五件重要的事情。

**第一，角色聚焦。** 当 Claude 以工程经理身份审查代码时，它会忽略关于 UI 颜色的反馈，专注于框架选择和可维护性。当它以 CEO 身份运行时，它会追问功能请求背后的用户问题，而不是急于实现。

**第二，数据编排。** `/office-hours` 的输出流入 `/plan-ceo-review`，再流入 `/plan-eng-review`。每一步的输出成为下一步的输入——一个结构化的决策链。

**第三，质量跟踪。** Review Readiness Dashboard 显示哪些审查已完成、哪些缺失、以及你是否可以发布。工程审查是唯一的硬性门控。CEO 和设计审查是推荐的。

**第四，决策哲学。** gstack 嵌入了一个叫做"煮湖"（Boil the Lake）的概念。湖可以被煮沸——比如让一个模块达到 100% 测试覆盖率。海洋不能——比如从头重写整个系统。这个原则贯穿于每个技能中。

**第五，认知负载适应。** 当 gstack 检测到多个并行对话时，技能进入 ELI16 模式（像解释给 16 岁的人听），重新建立完整上下文以防止碎片化。

## 它们如何互补

三个框架约束的维度互不重叠：

| 维度 | Superpowers | GSD | gstack |
|------|-------------|-----|--------|
| 约束对象 | 开发流程 | 执行环境 | 决策视角 |
| 核心机制 | 七阶段流水线 + 强制 TDD | 上下文隔离 + 原子提交 | 角色系统 + 决策链 |
| 最佳场景 | 缺乏工程纪律的项目 | 超出单个上下文窗口的复杂项目 | 需要产品思维的创始人工程师 |

gstack 的结构缺口很有启发性。它的工作流是：思考（`/office-hours`）→ 计划（`/plan-*-review`）→ 构建 → 审查（`/review`）→ 发布（`/ship`）。但构建阶段没有对应的技能。Claude Code 在此期间恢复为默认模式，直到你手动运行 `/review`。而构建阶段恰恰是 Superpowers（TDD + 子任务调度）和 GSD（上下文隔离 + 原子提交）最强的地方。

理论上可以组合使用。实际上存在技术障碍：Superpowers 在构建阶段的交互式问答提示会阻塞 Claude Code 的输入流。GSD v2 是 TypeScript 应用而非 Markdown 提示词，增加了集成复杂度。

## AutoResearch：无人值守的持续优化

Andrej Karpathy 于 2026 年 3 月 7 日发布了 AutoResearch。一个月内超过 65,000 个 GitHub Stars。

模式很直接：Agent 修改代码，在固定时间预算内（默认 5 分钟）运行实验，衡量单一指标，如果指标改善则保留更改，否则回滚。无限重复。Karpathy 在两天内运行了约 700 次实验，发现了约 20 个真正的改进，从他认为已经完全优化的 GPT-2 训练脚本中榨取了 11% 的效率提升。

Shopify CEO Tobi Lütke 将此模式应用于 Shopify 的模板引擎。93 次自动提交带来了 53% 的渲染性能提升。

移植到软件工程中，此模式填补了 gstack 的 Ship 阶段之后的空白。一个 `/optimize` 技能可以自动修改代码、运行测试、衡量覆盖率和 Lighthouse 分数、提交改进、回滚退化——这是三个框架目前都未提供的无人值守持续优化。
[]()
Karpathy 在思考更大的方向。他在 X 上发帖说 AutoResearch 应该朝异步多 Agent 协作发展，一个 SETI@home 式的分布式研究社区。GitHub 的分支模型（master + PR 合并回来）对此并不理想，但方向是明确的。

## 并行开发：git worktree 优于 Conductor

gstack 的 README 推荐使用 Conductor 进行并行开发，但没有提供官方实现。底层机制是 git worktree。Claude Code 原生支持 `-w` 参数指定 worktree。几个 tmux 窗口就能实现相同的效果：

```bash
git worktree add ../feature-auth feature-auth
claude -w ../feature-auth
```

社区成员已经提交了 PR，希望将其作为 gstack 的内置功能。对于同时运行多个开发线程的团队来说，这比等待一个尚不存在的 Conductor 方案更实际。

## 如何选择

"框架战争"的叙事具有误导性。这些工具并非争夺同一批用户。Superpowers 面向缺乏工程纪律的独立开发者。GSD 面向复杂度超出单个上下文窗口承载能力的项目。gstack 面向需要产品思维与代码输出并重的创始人工程师。它们的使用场景几乎不重叠。真正的问题是如何组合它们。

### Superpowers 和 GSD 的实际区别是什么？

Superpowers 约束 AI 如何编写代码——七阶段流水线加强制 TDD 和子任务委派。GSD 约束 AI 编写代码的条件——每个任务干净的 200K 上下文窗口，主对话负载保持在 30-40%。Superpowers 确保流程质量，GSD 确保环境质量。理论上可以叠加使用。

### gstack 为谁而建？

gstack 适合同时担任 CEO、工程师、设计师和 QA 的创始人工程师。它的角色系统构建了"从什么视角思考"。如果你只需要 AI 编写一个库函数而不需要产品层面的决策，Superpowers 或 GSD 更直接。

### 能同时运行三个框架吗？

理论上可以。gstack 负责思考和审查，Superpowers 的 TDD 原则应用于构建阶段，GSD 的上下文隔离防止长时间会话的质量退化。实际上没有开箱即用的集成方案——需要手动配置。主要技术障碍是 Superpowers 的交互式提示在构建期间阻塞 Claude Code 的输入。

### AutoResearch 模式对软件工程可行吗？

可行，但有限制。它适用于有明确数值指标的维度——测试覆盖率、Lighthouse 分数、响应时间。对于主观维度如 UI 美观度或代码可读性效果不佳。Shopify 的 CEO 在内部工具上验证了该模式：93 次自动提交，53% 的渲染提升。

### Claude Code 的原生功能会取代这些框架吗？

Anthropic 于 2026 年 1 月将 Superpowers 纳入其官方市场，表明更倾向于集成优秀的社区方案而非内部构建一切。Claude Code 已原生支持子代理和 worktree。更高层的抽象如工作流编排和角色治理短期内不太可能成为原生功能。

## 来源

- GitHub — obra/superpowers（官方仓库）
- GitHub — gsd-build/get-shit-done（官方仓库）
- GitHub — garrytan/gstack（官方仓库）
- GitHub — karpathy/autoresearch（官方仓库）

## 作者见解

我们的团队在 2025 年末从 Cursor 迁移到 Claude Code，并在客户项目中测试了 Superpowers 和 GSD 的不同配置。最显著的差异：Superpowers 的强制 TDD 明显减少了回归 Bug，但构建速度有所下降，因为测试优先的工作流消耗更多 token 和时间。GSD 的上下文隔离在 50+ 文件的项目中产生了可衡量的差异——Claude 在第三小时仍保持第一小时的质量。gstack 的 `/plan-ceo-review` 是三者中最被低估的功能。它迫使你在写代码之前回答"这个功能到底解决什么问题？"，消除了大量构建中途的方向变更。

对大多数团队来说，我建议选择与你最痛的痛点匹配的框架，运行两周，然后决定是否叠加第二个。同时运行三个框架会产生真实的管理开销，除非你已经熟悉每一个。
