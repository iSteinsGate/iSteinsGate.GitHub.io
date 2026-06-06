---
type: concept
category: methodology
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [Java, concurrency, JUC, thread, synchronized]
---

# Java并发编程JUC

## 概述
Java JUC（java.util.concurrent）是 Java 并发编程的核心包。
## 知识体系
1. **理论基础** — 并发安全问题、JMM内存模型
2. **线程基础** — 线程状态转换、创建方式
3. **synchronized** — 锁升级：偏向锁→轻量级锁→重量级锁
4. **ReentrantLock** — 公平锁/非公平锁、条件变量
5. **Unsafe** — 底层内存操作

## synchronized vs ReentrantLock
| 维度 | synchronized | ReentrantLock |
|---|---|---|
| 用法 | 方法/代码块 | 仅代码块 |
| 加锁 | 自动 | 手动lock/unlock |
| 锁类型 | 非公平 | 可选公平/非公平 |
| 可中断 | ❌ | ✅ |
| 条件变量 | 单个 | 多个Condition |

## 源码路径
`02-Areas/Java/JUC/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [知识管理方法论](知识管理方法论.md)
