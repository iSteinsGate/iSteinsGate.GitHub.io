# 基于表结构Sql生成

## 系统配置
项目根目录: {{PROJECT_ROOT}}
数据库类型: PostgreSQL 11
输出目录: ./generated_sql/views/{{MODULE_NAME}}/

## 当前任务
生成视图SQL文件，基于：
1. 表结构文件目录: {{SCHEMA_DIR}}
2. 字典数据文件: {{DICTIONARY_FILE}}
3. 业务需求文档: {{REQUIREMENT_FILE}}

## 目录结构感知
我理解以下目录结构用于组织文件：{{PROJECT_STRUCTURE}}


## 多文件表结构处理指令
我需要从多个表结构文件中提取相关信息：
1. 首先读取所有表结构文件，构建完整的数据库模型
2. 识别表之间的关系（外键、引用关系）
3. 按模块对表进行分类
4. 特别注意跨模块的表关联

请使用以下步骤处理表结构：

### 步骤1：表结构扫描
扫描目录: {{SCHEMA_DIR}} 及其子目录
发现以下表结构文件：
{{TABLE_FILE_LIST}}

### 步骤2：表关系分析
基于表结构，分析：
1. 主键-外键关系
2. 可能的连接路径
3. 数据流向
4. 性能热点表

### 步骤3：字典数据整合
加载字典文件: {{DICTIONARY_FILE}}
将代码值映射为业务可读值

## 业务需求智能解析

### 需求文件: {{REQUIREMENT_FILE}}
需求文档由第三方提供，具有以下特点：
1. 字段名称可能与数据库字段不完全一致
2. 使用业务术语而非技术术语
3. 可能有模糊或歧义的描述
4. 包含业务规则而非技术规则

### 字段映射策略
采用四级映射策略：

#### 级别1：精确匹配（优先级最高）
- 完全相同的字段名（忽略大小写和特殊字符）
- 例如："用户ID" → "user_id"

#### 级别2：语义匹配
- 使用同义词词典匹配
- 例如："客户姓名" → ["customer_name", "client_name", "full_name"]
- 使用词干提取和相似度算法

#### 级别3：上下文推断
- 根据表关系和业务上下文推断
- 例如："部门负责人" → 需要连接员工表和部门表

#### 级别4：计算字段
- 需要从多个字段计算得出
- 例如："年龄" → "当前日期 - 出生日期"
- 例如："完整地址" → "省份 + 城市 + 详细地址"

## PostgreSQL 11 SQL生成规范

### 语法要求
1. 使用CREATE OR REPLACE VIEW
2. 支持CTE（WITH子句）用于复杂逻辑
3. 使用::进行显式类型转换
4. 使用COALESCE处理NULL值
5. 使用ILIKE进行不区分大小写的匹配
6. 窗口函数必须明确指定OVER()子句
7. 使用STRING_AGG或ARRAY_AGG进行聚合

### 性能优化要求
1. 避免在WHERE子句中使用函数
2. 优先使用EXISTS而不是IN
3. 合理使用索引提示（如果已知）
4. 分页查询使用LIMIT和OFFSET
5. 大数据量考虑分区

### 安全性要求
1. 不要暴露敏感信息
2. 使用参数化查询思想
3. 添加访问权限注释

## 输出文件结构

### 主视图文件
文件名: {{VIEW_NAME}}_202601040911.sql  文件后缀为实际的生成时间(202601040911)
位置: {{OUTPUT_PATH}}/{{MODULE_NAME}}/

文件内容结构:
```sql
-- ============================================
-- 文件: {{VIEW_NAME}}_pg11.sql
-- 生成时间: {{TIMESTAMP}}
-- 需求文档: {{REQUIREMENT_FILE}}
-- 数据库: PostgreSQL 11
-- ============================================

-- 视图概述
-- 名称: {{VIEW_NAME}}
-- 描述: {{VIEW_DESCRIPTION}}
-- 用途: {{BUSINESS_PURPOSE}}

-- 字段映射表
-- 业务字段名      数据库字段/逻辑         映射类型    备注
-- -----------    --------------------    ---------  --------
-- {{FIELD_MAPPING_TABLE}}

-- 依赖的表
-- 1. {{TABLE_1}} - {{TABLE_1_PURPOSE}}
-- 2. {{TABLE_2}} - {{TABLE_2_PURPOSE}}

-- 业务规则
-- 1. {{RULE_1}}
-- 2. {{RULE_2}}

-- ============================================
-- 视图定义开始
-- ============================================

CREATE OR REPLACE VIEW {{SCHEMA_NAME}}.{{VIEW_NAME}} AS
WITH
    -- 预计算CTE（如果需要）
    {{CTE_DEFINITIONS}}
SELECT
    -- 字段列表，每个字段都有详细注释
    {{FIELD_SELECTIONS}}
FROM {{MAIN_TABLE}} AS t1
    {{JOIN_CLAUSES}}
WHERE
    {{WHERE_CONDITIONS}}
    {{GROUP_BY_CLAUSE}}
    {{HAVING_CLAUSE}}
    {{ORDER_BY_CLAUSE}}
    {{LIMIT_CLAUSE}};

-- ============================================
-- 视图定义结束
-- ============================================

-- 索引建议
-- 建议在以下列上创建索引以提升查询性能：
-- 1. {{INDEX_SUGGESTION_1}}
-- 2. {{INDEX_SUGGESTION_2}}

-- 使用示例
-- SELECT * FROM {{SCHEMA_NAME}}.{{VIEW_NAME}} WHERE ...;

-- 性能注意事项
-- 1. {{PERFORMANCE_NOTE_1}}
-- 2. {{PERFORMANCE_NOTE_2}}

-- 维护说明
-- 1. 视图依赖于以下表结构变更：{{DEPENDENCY_LIST}}
-- 2. 当以下表数据变化时需要刷新：{{REFRESH_TRIGGERS}}
-- 3. 预计数据量：{{ESTIMATED_ROWS}} 行

-- 变更历史
-- {{CHANGE_HISTORY}}
