---
type: entity
category: tool
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [web-server, reverse-proxy, load-balancer]
---

# Nginx

## 概述
Nginx 是高性能的 HTTP 和反向代理服务器。
## 核心配置
- 配置文件: `conf/nginx.conf`
- 核心模块: events、http、server、location

## 常用功能
1. **反向代理** — proxy_pass
2. **负载均衡** — upstream + weight/ip_hash/least_conn
3. **静态文件** — root / alias
4. **HTTPS** — ssl_certificate
5. **限流** — limit_req / limit_conn

## 微信小程序相关
- 订阅消息接口配置

## 源码路径
`02-Areas/Nginx/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [数据库技术](数据库技术.md)
