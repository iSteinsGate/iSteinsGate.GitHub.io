---
type: concept
category: framework
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
sources:
  - karpathy-llm-wiki-original-notes
  - karpathy-llm-wiki-obsidian-中文翻译
tags: [LLM, wiki, knowledge-base, AI, framework]
---

# LLM Wiki

## 定义
LLM Wiki 是 Andrej Karpathy 提出的一种知识管理模式：用 LLM 构建和维护个人知识库，而非人工维护后偶尔问 AI 问题。

## 核心理念

### 范式转变
传统模式：人维护知识库 → 偶尔问AI问题
LLM Wiki模式：**AI构建和维护整个知识库**

### Karpathy 的比喻
> "Obsidian 是 IDE，LLM 是程序员，wiki 是代码库。"

### 复利效应
每一篇新资料被 LLM 消化后，整个 wiki 就变得更聪明。它变成一个随时间推移越来越密集的网络。

## 三层架构

```
sources/   → 第1层：输入（不可变原始材料）
wiki/      → 第2层：LLM生成的知识页面
CLAUDE.md  → 第3层：管控wiki运作的schema
```

### 第1层：输入（原材料）
- `sources/` 文件夹存放所有阅读材料
- **不可变原则**：一旦保存，不编辑
- 支持：文章、论文、读书笔记、播客要点、PDF

### 第2层：Wiki（知识页面）
- LLM 自动生成摘要页面
- 创建关键人物和概念页面
- 交叉引用所有内容
- 一篇文章可能涉及10-15个页面

### 第3层：Schema（规则）
- `CLAUDE.md` 文件定义 wiki 的运作规则
- 将 Claude 从通用聊天机器人变成纪律严明的 wiki 维护者

## 三大核心操作

| 操作 | 功能 |
|------|------|
| `/ingest-url` | 输入URL，Claude提取文章并编译进wiki |
| `/process-inbox` | 自动分类快速笔记和想法 |
| `/lint-wiki` | 健康检查：断链、孤立页、矛盾内容 |

## 关键优势
1. **自动化维护** — LLM处理所有整理工作
2. **知识复利** — 越用越智能
3. **跨领域连接** — 自动发现不同主题间的联系
4. **减少人工** — 理论美好，实践不再被维护成本杀死

## 相关页面
- [Andrej-Karpathy](Andrej-Karpathy.md) — 提出者
- [AI驱动的第二大脑](AI驱动的第二大脑.md) — 应用场景
- [Obsidian](Obsidian.md) — 推荐界面
- [Claude-Code](Claude-Code.md) — 推荐代理
- [知识复利](知识复利.md) — 核心效应

## 参考资料
- [Karpathy 原始推文](https://x.com/karpathy/status/2039805659525644595)
- [The AI Maker 完整蓝图](https://aimaker.substack.com/p/llm-wiki-obsidian-knowledge-base-andrej-karphaty)
