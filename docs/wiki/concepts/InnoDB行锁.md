---
type: concept
category: methodology
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [MySQL, database, lock, InnoDB]
---

# InnoDB行锁

## 概述
InnoDB 存储引擎的行锁机制是数据库并发控制的核心。
## 三种行锁
1. **Record Lock** — 记录锁，锁定单行
2. **Gap Lock** — 间隙锁，锁定范围（不包含记录本身）
3. **Next-Key Lock** — 临键锁，Record + Gap

## 加锁规则
1. 等值查询唯一索引 → 命中则Record Lock，未命中则Gap Lock
2. 等值查询非唯一索引 → 左开右闭区间
3. 范围查询 → 所有扫描到的记录加Next-Key Lock

## 美团面试高频
手写SQL → 分析加了哪些锁 → 判断是否死锁

## 源码路径
`02-Areas/MySql/行锁.md`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [知识管理方法论](知识管理方法论.md)
