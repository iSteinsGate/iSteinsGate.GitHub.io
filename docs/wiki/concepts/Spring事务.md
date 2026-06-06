---
type: concept
category: methodology
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [Java, Spring, transaction, AOP]
---

# Spring事务

## 概述
Spring 事务管理通过 @Transactional 注解实现声明式事务。
## 事务传播机制
| 传播机制 | 说明 |
|---|---|
| REQUIRED（默认） | 有事务加入，没有新建 |
| SUPPORTS | 有事务加入，没有非事务运行 |
| MANDATORY | 必须在事务中，否则抛异常 |
| REQUIRES_NEW | 总是新建事务，挂起当前 |
| NOT_SUPPORTED | 非事务运行，挂起当前 |
| NEVER | 非事务运行，有事务抛异常 |
| NESTED | 嵌套事务 |

## 注意事项
- 默认只对 RuntimeException 回滚
- 同类方法调用不走代理，事务失效
- public 方法才生效

## 源码路径
`02-Areas/Java/Spring/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [知识管理方法论](知识管理方法论.md)
