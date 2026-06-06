---
type: entity
category: tool
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [version-control, git, tools]
---

# Git

## 概述
Git 分布式版本控制系统。
## 笔记内容（3篇）
- **Git stash 命名** — stash 管理技巧
- **Git merge --no-ff** — 合并策略详解
- **Git 常用命令** — 查看commit所属分支、移除版本关联

## 关键命令
```bash
# 查看commit属于哪个分支
git branch -r --contains COMMIT_ID

# stash管理
git stash save "message"
git stash list
git stash pop

# 合并策略
git merge --no-ff feature  # 保留分支历史
```

## 源码路径
`02-Areas/Git/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [数据库技术](数据库技术.md)
