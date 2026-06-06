---
type: entity
category: tool
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [Java, network, framework, NIO, async]
---

# Netty

## 概述
Netty 是一个异步事件驱动的网络应用框架，用于快速开发可维护的高性能协议服务器和客户端。
## 核心概念
- **NIO 基础**: Channel & Buffer、Selector 三大组件
- **EventLoop**: 事件循环模型，Boss + Worker 线程组
- **ByteBuf**: Netty 的字节缓冲区，比 NIO ByteBuffer 更灵活
- **ChannelHandler**: 处理入站和出站数据的处理器链

## 知识体系（4篇笔记）
1. `Netty01-nio` — NIO 基础：Channel、Buffer、Selector
2. `Netty02-入门` — Netty 概述、第一个项目、组件详解
3. `Netty03-进阶` — 粘包半包、协议设计与编码解码
4. `Netty04-优化与源码` — 序列化优化、参数调优、源码分析

## 关键特性
- 异步非阻塞 I/O
- 高性能、低延迟
- 丰富的协议支持（HTTP、WebSocket、自定义协议）
- 内存池化、零拷贝

## 源码路径
`02-Areas/Netty/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [数据库技术](数据库技术.md)
