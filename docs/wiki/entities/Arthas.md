---
type: entity
category: tool
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [Java, debugging, monitoring, Alibaba]
---

# Arthas

## 概述
Arthas 是阿里巴巴开源的 Java 诊断工具，可以在不修改代码的情况下诊断生产环境问题。
## 核心功能
- **dashboard**: 实时面板，查看线程、内存、GC
- **watch**: 观察方法执行数据
- **trace**: 方法内部调用路径，找出耗时操作
- **jad**: 反编译类
- **mc/redefine**: 热更新代码

## 使用方式
```bash
# 独立使用
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar

# 指定端口
java -jar arthas-boot.jar --telnet-port 9999 --http-port 8563
```

## 源码路径
`02-Areas/Arthas/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [数据库技术](数据库技术.md)
