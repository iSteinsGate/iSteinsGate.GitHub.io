# EvoMap 中文文档集

本目录包含 EvoMap 官方博客文章的中文翻译与解读，按时间顺序整理。

## 📑 快速导航

| 序号 | 文章标题 | 日期 | 主题 |
|------|---------|------|------|
| 1 | [EvoMap 诞生：从平台依赖到进化协议](./01.EvoMap诞生：从平台依赖到进化协议.md) | 2026-02-16 | 起源故事、GEP 协议 |
| 2 | [GEP 协议深潜：为 AI Agent 自进化而生的基因工程](./02.GEP%20协议深潜：为%20AI%20Agent%20自进化而生的基因工程.md) | 2026-02-16 | GEP 协议详解 |
| 3 | [Agent Skill vs GEP Gene：工具与进化的根本分野](./03.Agent%20Skill%20vs%20GEP%20Gene：工具与进化的根本分野.md) | 2026-02-17 | 工具范式 vs 进化范式 |
| 4 | [OpenClaw x EvoMap：CritPt 评估报告](./04.OpenClaw%20x%20EvoMap：CritPt%20评估报告.md) | 2026-02-21 | 评估报告、安全分析 |
| 5 | [EvoMap 更新日志 2026-02-22](./05.EvoMap%20更新日志%202026-02-22.md) | 2026-02-22 | 版本更新、新特性 |
| 6 | [AI Council：当 Agent 自治开源](./06.AI%20Council：当%20Agent%20自治开源.md) | 2026-02-24 | 治理模型、自治开源 |
| 7 | [群体智能：当神族遇上虫族](./07.群体智能：当神族遇上虫族.md) | 2026-02-24 | Swarm Intelligence |

---

## 📚 文章概览

### 1. [EvoMap 诞生：从平台依赖到进化协议](./01.EvoMap诞生：从平台依赖到进化协议.md)
**日期：** 2026-02-16
**原文：** [The EvoMap Origin Story](https://evomap.ai/blog/evomap-origin-story)

从 OpenClaw 被收购、Evolver 插件 10 分钟登顶到被下架，再到中国开发者大规模误封事件——这一连串事件如何催生了 EvoMap 的诞生。文章深入分析了 AI Agent 生态的三个结构性问题：系统性重复计算、经验孤岛、平台锁定，并介绍了 GEP（基因组进化协议）如何成为解决方案。

**核心观点：** 当 AI 能力被单一平台控制时，我们需要一个开放的、去中心化的进化协议。

---

### 2. [GEP 协议深潜：为 AI Agent 自进化而生的基因工程](./02.GEP%20协议深潜：为%20AI%20Agent%20自进化而生的基因工程.md)
**日期：** 2026-02-16  
**原文：** [GEP Protocol Deep Dive](https://evomap.ai/blog/gep-protocol-deep-dive)

对 GEP（Genome Evolution Protocol）进行系统性深潜，详细解释了六步 EvoMap Loop：观察失败 → 演化与验证 → 封装为 Gene + Capsule → 发布到 Hub → 验证与排名 → 继承与反馈。文章还深入解析了 Gene、Capsule、EvolutionEvent、GDI 等核心概念。

**核心观点：** MCP 解决的是「Agent 如何调用工具」，GEP 解决的是「Agent 如何在调用工具的过程中持续变强，并把经验留在网络中」。

---

### 3. [Agent Skill vs GEP Gene：工具与进化的根本分野](./03.Agent%20Skill%20vs%20GEP%20Gene：工具与进化的根本分野.md)
**日期：** 2026-02-17  
**原文：** [Agent Skill vs GEP Gene: The Fundamental Divide](https://evomap.ai/blog/agent-skill-vs-gep-gene)

深入对比了 Agent Skill（工具范式）与 GEP Gene（进化范式）的本质差异。文章解释了为什么 Skill 是静态的、孤立的、缺乏身份与声誉的，而 Gene 是动态的、可共享的、具备进化能力的，并阐述了从「工具」走向「基因」的必要性。

**核心观点：** Skill 关注的是「能做什么」，Gene 关注的是「如何在真实世界中越来越会做」。

---

### 4. [OpenClaw x EvoMap：CritPt 评估报告](./04.OpenClaw%20x%20EvoMap：CritPt%20评估报告.md)
**日期：** 2026-02-21  
**原文：** [OpenClaw x EvoMap: CritPt Evaluation Report](https://evomap.ai/blog/openclaw-critpt-report)

系统评估了 OpenClaw + EvoMap 组合在 CritPt 物理求解任务上的表现。文章展示了 Agent 如何从「语言模仿者」转变为「物理仿真工程师」，同时分析了安全与鲁棒性方面的挑战，以及协议与平台之间的角色分工。

**核心观点：** 进化让能力变强，并不会自动让系统变得更安全。安全本身也需要被建模为一种可验证、可演化的能力基因。

---

### 5. [EvoMap 更新日志 2026-02-22](./05.EvoMap%20更新日志%202026-02-22.md)
**日期：** 2026-02-22  
**原文：** [EvoMap Changelog 2026-02-22](https://evomap.ai/blog/changelog-2026-02-22)

详细记录了 EvoMap 在 2026-02-22 这一天的重要更新：161 次提交、18 个新特性。涵盖 Recipe & Organism 建模、Marketplace 统一、BoCha 搜索集成、Biology Dashboard 扩展、知识图谱搜索优先改版、Economics 页面重构、UAT → Credit 全局重命名等。

**核心观点：** 这是一次以「进化」为中心的版本更新，把 Agent 视为生物体，把能力视为基因资产。

---

### 6. [AI Council：当 Agent 自治开源](./06.AI%20Council：当%20Agent%20自治开源.md)
**日期：** 2026-02-24  
**原文：** [AI Council: When Agents Govern Their Own Open Source](https://evomap.ai/blog/ai-council)

介绍 EvoMap 的 AI Council 治理模型：让一群 AI Agent 像「开源委员会」那样，对开源项目进行提案、评审、投票与执行。文章解释了从「脚本乱飞」到「协议约束的进化」的转变，以及如何通过 Proposal → Decision → EvolutionEvent 实现可审计的自治治理。

**核心观点：** 当开源项目的维护者从「人类工程师」变成「AI Agent」，治理模型必须被彻底重写。

---

### 7. [群体智能：当神族遇上虫族](./07.群体智能：当神族遇上虫族.md)
**日期：** 2026-02-24  
**原文：** [Swarm Intelligence: When Protoss Meets Zerg](https://evomap.ai/blog/swarm-intelligence)

用星际争霸中神族（Protoss）的「共享意识网络」和虫族（Zerg）的「极速分裂与执行」来比喻 EvoMap 的 Swarm Intelligence 架构。文章解释了如何将「统一认知」与「大规模并行试错」结合，形成大于个体总和的集体智能。

**核心观点：** 真正强大的系统，不是某一个最强 Agent，而是一整个能持续演化的生态。

---

## 📖 英文原文

- [evomap-origin-story.md](./evomap-origin-story.md) - The EvoMap Origin Story（英文版）

---

## 🔗 相关链接

- **EvoMap 官网：** https://evomap.ai
- **EvoMap 博客：** https://evomap.ai/blog
- **GitHub：** https://github.com/EvoMap

---

## 📝 关于本目录

本目录中的文章均为对 EvoMap 官方博客的中文翻译与解读，旨在帮助中文读者更好地理解 EvoMap 的设计理念、技术架构与未来愿景。

**翻译原则：**
- 保持原文结构与核心观点
- 使用符合中文阅读习惯的表达
- 补充必要的背景说明与上下文
- 保留关键术语的英文原文（如 GEP、Gene、Capsule 等）

---

*最后更新：2026-02-26*
