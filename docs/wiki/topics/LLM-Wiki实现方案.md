---
type: topic
category: implementation
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
sources:
  - karpathy-llm-wiki-original-notes
  - karpathy-llm-wiki-obsidian-中文翻译
tags: [LLM, wiki, implementation, tools, comparison]
---

# LLM Wiki 实现方案

## 概述
基于 [Andrej-Karpathy](Andrej-Karpathy.md) 的 [LLM-Wiki](LLM-Wiki.md) 概念，社区已经产生了多种实现方案。

## 主要实现方案

### 1. Wyndo 方案（推荐）
- **工具**: [Obsidian](Obsidian.md) + [Claude-Code](Claude-Code.md) + [Obsidian-Skills](Obsidian-Skills.md)
- **特点**: 最原生、功能完整、有入门套件
- **适合**: 想要完整系统的用户

### 2. claude-obsidian（最热门）
- **GitHub**: AgriciDaniel/claude-obsidian ⭐5301
- **特点**: 知识引擎、Hot Cache、多Agent并行
- **适合**: 深度Claude Code用户

### 3. llm-wiki-skill（中文友好）
- **GitHub**: sdyckjq-lab/llm-wiki-skill ⭐1591
- **特点**: 中文优先、多平台、离线图谱
- **适合**: 中文用户、Hermes Agent用户

### 4. llm-wiki-compiler（工程质量最高）
- **GitHub**: atomicstrata/llm-wiki-compiler ⭐1250
- **特点**: 独立CLI、审批流程、声明溯源
- **适合**: 开发者、需要质量管控

## 方案对比

| 维度 | Wyndo | claude-obsidian | llm-wiki-skill | llm-wiki-compiler |
|------|:-----:|:---------------:|:--------------:|:-----------------:|
| 中文支持 | ❌ | ❌ | ✅✅✅ | `--lang` |
| 多平台 | Claude | Claude | 4+ | MCP |
| 可视化 | ❌ | ❌ | ✅✅✅ | ❌ |
| 审批流程 | ❌ | ❌ | ❌ | ✅✅✅ |
| 安装难度 | 中 | 中 | 低 | 低 |

## 选择建议
- **中文用户**: llm-wiki-skill
- **Claude Code深度用户**: claude-obsidian
- **开发者**: llm-wiki-compiler
- **入门体验**: Wyndo 方案

## 相关页面
- [LLM-Wiki](LLM-Wiki.md) — 核心概念
- [Obsidian](Obsidian.md) — 界面工具
- [Claude-Code](Claude-Code.md) — AI代理
- [知识复利](知识复利.md) — 核心效应
