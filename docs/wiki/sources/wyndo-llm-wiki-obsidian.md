---
type: source
source_type: article
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
author: Wyndo
date: 2026-04-16
platform: The AI Maker (Substack)
url: https://aimaker.substack.com/p/llm-wiki-obsidian-knowledge-base-andrej-karphaty
tags: [LLM, wiki, obsidian, claude-code, second-brain]
---

# Wyndo: 用 Karpathy 的 LLM Wiki 构建 AI 第二大脑

## 基本信息
- **作者**: [Wyndo](Wyndo.md)
- **日期**: 2026年4月16日
- **来源**: The AI Maker (Substack)
- **类型**: 付费文章（部分免费）
- **字数**: 6802词

## 文章定位
这是将 [Andrej-Karpathy](Andrej-Karpathy.md) 的 [LLM-Wiki](LLM-Wiki.md) 概念落地为完整实现方案的蓝图文章。

## 核心内容

### 三层架构
```
sources/   → 第1层：输入（不可变原始材料）
wiki/      → 第2层：LLM生成的知识页面
CLAUDE.md  → 第3层：管控wiki运作的schema
```

### 三大核心操作
1. `/ingest-url` — 输入URL，Claude提取文章并编译进wiki
2. `/process-inbox` — 自动分类快速笔记和想法
3. `/lint-wiki` — 健康检查：断链、孤立页、矛盾内容

### 技术栈
- [Obsidian](Obsidian.md) — 界面
- [Claude-Code](Claude-Code.md) — 代理
- [Obsidian-Skills](Obsidian-Skills.md) — LLM操作技能
- CLI — 终端运行

### 关键设计决策
1. `raw/` 文件夹**不可变** — AI不修改源文件
2. Wiki 合并同主题文章；新概念创建新页面
3. 通过 "See Also" 部分交叉引用
4. 事实冲突标注来源归属
5. `index.md` 和 `log.md` 是必须维护的文件

## 文章结构
1. **背景** — 笔记系统的痛点
2. **灵感** — Karpathy 的 LLM Wiki
3. **技术栈** — Obsidian + Claude Code + Skills
4. **三层架构详解** — sources / wiki / schema
5. **三大操作详解** — ingest / process-inbox / lint
6. **实际示例** — Paul Graham 文章处理
7. **入门套件** — 可下载的完整系统

## 关键洞察
1. 笔记系统的致命弱点是**维护成本**
2. LLM Wiki 翻转了范式：AI维护知识库
3. 知识具有**复利效应**
4. Obsidian Skills 是关键：教LLM用原生语言操作
5. 三个命令就能运行

## 相关页面
- [LLM-Wiki](LLM-Wiki.md) — 核心框架
- [AI驱动的第二大脑](AI驱动的第二大脑.md) — 应用场景
- [Wyndo](Wyndo.md) — 作者
- [Obsidian](Obsidian.md) — 推荐界面
- [Claude-Code](Claude-Code.md) — 推荐代理

## 参考资料
- [原文](https://aimaker.substack.com/p/llm-wiki-obsidian-knowledge-base-andrej-karphaty)
- [中文翻译](../raw/articles/karpathy-llm-wiki-obsidian-中文翻译.md)
