---
type: entity
category: tool
created: 2026-05-21
updated: 2026-05-21
confidence: EXTRACTED
tags: [database, SQL, PostgreSQL]
---

# PostgreSQL

## 概述
PostgreSQL 是一个功能强大的开源关系型数据库。
## 笔记内容
- **PostgreSql json提取** — JSON字段查询和提取
- **Postgresql表字段变更语句** — DDL变更安全写法
- **Sql Server迁移PostgreSql** — 迁移差异对比

## 关键语法
```sql
-- JSON提取
(data->'Document'->>'CreateTime')::timestamp

-- 安全添加字段
DO $$ BEGIN
  IF NOT EXISTS (SELECT 1 FROM pg_attribute
    WHERE attrelid='表名'::regclass AND attname='字段名') THEN
    ALTER TABLE 表名 ADD COLUMN 字段名 类型;
  END IF;
END $$;
```

## SQL Server → PostgreSQL 差异
| SQL Server | PostgreSQL |
|---|---|
| `TOP N` | `LIMIT N` |
| `ISNULL()` | `COALESCE()` |
| `GETDATE()` | `CURRENT_DATE` |
| `IIF()` | `CASE WHEN` |
| `STUFF/STRING_AGG` | `STRING_AGG` |

## 源码路径
`02-Areas/PostgreSql/`

## 相关页面
- [Java后端技术栈](Java后端技术栈.md)
- [数据库技术](数据库技术.md)
