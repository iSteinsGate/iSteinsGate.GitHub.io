# SQL 执行计划优化案例：MET_IPM_ExecBill 查询优化

**优化日期**: 2026-06-05
**数据库**: PostgreSQL 14.2
**优化结果**: 执行时间从 5533ms 降至 78ms，提升 **71 倍**

---

## 一、原始 SQL

```sql
EXPLAIN (ANALYZE, BUFFERS, VERBOSE)
SELECT item.exec_items_id,
       item.exec_card_id,
       item.exec_bill_id,
       item.bind_type,
       item.order_id,
       item.meditem_id,
       item.item_name,
       item.cacl_method,
       item.batch_id,
       item.real_qty,
       ipmorder.price,
       item.price_unit,
       item.sub_amount,
       item.bind_qty,
       item.book_date,
       item.book_user_id,
       item.book_status,
       book_info.name,
       patient.gender,
       patient.birthday,
       patient.bed_no,
       book_info.age,
       book_info.age_type,
       card.plan_exec_date,
       card.exec_status,
       ipmorder.visit_id      AS inpatient_id,
       ipmorder.ordgroup_code AS is_long,
       ipmorder.exec_dept_id  AS dept_id,
       execOrder.exec_order_id,
       usage.usage_name,
       frequency.name         AS frequency_name,
       frequency.frequency,
       ipmorder.days_treat,
       ipmorder.ref_order_id,
       ipmorder.orditem_name,
       ipmorder.group_no,
       ipmorder.group_label,
       ipmorder.group_label_no,
       ipmorder.dose_unit,
       ipmorder.every_dose,
       ipmorder.calc_every_dispensing,
       goodsname.base_dose,
       goodsname.specs,
       ipmorder.dispensing_mode,
       ipmorder.order_dose,
       ipmorder.drop_speed,
       ipmorder.order_type,
       ipmorder.every_dose_unit,
       ipmorder.first_day_num,
       goodsname.goods_name,
       ipmorder.is_dismount,
       ipmorder.ope_type,
       ipmorder.doctor_entrust,
       ipmorder.exec_frequency,
       ipmorder.skintest_way,
       ipmorder.skintest_way_name,
       ipmorder.is_skintest,
       ipmorder.skintest_status,
       ipmorder.plan_start,
       ipmorder.goods_id,
       ipmorder.exec_dept_id,
       ipmorder.ordgroup_code,
       goodsname.producer,
       ipmorder.create_dept_id,
       create_dept.dept_name  AS create_dept_name
FROM MET_IPM_ExecBill_Items item
         JOIN MET_IPM_ExecBill_Card card
              ON card.exec_card_id = item.exec_card_id AND card.exec_status IN ('0', '1', '2')
         JOIN met_ipm_execBill_order execOrder
              ON execOrder.exec_card_id = item.exec_card_id AND execOrder.order_id = item.order_id
         JOIN MET_IPM_Order ipmorder ON ipmorder.order_id = item.order_id
         JOIN MET_IPM_PATIENT patient ON patient.inpatient_id = ipmorder.visit_id
         JOIN fin_ipm_book_info book_info ON book_info.inpatient_id = ipmorder.visit_id
         LEFT JOIN COM_MET_Frequency frequency ON frequency.frequency_id = ipmorder.frequency_id
         LEFT JOIN COM_MET_Usage usage ON usage.usage_id = ipmorder.usage_id
         LEFT JOIN v_goods_spec_name goodsname ON goodsname.goods_id = ipmorder.goods_id
         LEFT JOIN com_dept create_dept ON create_dept.dept_id = ipmorder.create_dept_id
WHERE execOrder.send_drug = 1
  AND item.goods_id IS NOT NULL
  AND execOrder.send_order_id IS NULL
  AND card.plan_exec_date >= '2026-06-02 00:00:00'
  AND card.plan_exec_date <= '2026-06-03 23:59:59'
  AND execOrder.is_auto_send = 0
  AND card.exec_ward_id = '1671036900502269953'
  AND ipmorder.order_id IN ('2061993540615786498', '2061993674739044353', ...);  -- 53个order_id
```

---

## 二、优化前执行计划分析

### 2.1 整体指标

| 指标 | 值 |
|---|---|
| 总执行时间 | **5533ms** |
| 最终返回行数 | **0 行** |
| 规划时间 | 40ms |
| 共享缓冲区命中 | 32,822 |
| 磁盘读取 | 55,754 buffers (298ms I/O) |
| 并行 Worker 数 | 4 |

### 2.2 执行计划结构概览

```
Nested Loop Left Join (0 rows, 5533ms)
├── Nested Loop Left Join (0 rows)
│   ├── Nested Loop Left Join (0 rows)
│   │   ├── Nested Loop (0 rows)
│   │   │   ├── Nested Loop (0 rows)
│   │   │   │   ├── Gather (8 rows, 5530ms) ← 主要耗时
│   │   │   │   │   └── Parallel Hash Join (24,970 rows × 5 workers)
│   │   │   │   │       ├── Parallel Seq Scan met_ipm_execbill_items ← 瓶颈1
│   │   │   │   │       │   (275,231 rows/worker, 55,754 read buffers)
│   │   │   │   │       └── Parallel Hash
│   │   │   │   │           └── Parallel Seq Scan met_ipm_execbill_order ← 瓶颈2
│   │   │   │   │               (过滤 412,810 行)
│   │   │   │   └── Append (137个分区) ← met_ipm_order 分区表
│   │   │   │       ├── Index Scan met_ipm_order_202308_pkey (rows=0)
│   │   │   │       ├── Index Scan met_ipm_order_202309_pkey (rows=0)
│   │   │   │       ├── ... (共35个有索引的分区)
│   │   │   │       ├── Seq Scan met_ipm_order_202607 (rows=0)
│   │   │   │       └── ... (共102个无数据的空分区)
│   │   │   └── Index Scan fin_ipm_book_info_pkey
│   │   └── Index Scan met_ipm_patient_pkey
│   └── Index Scan com_met_frequency_pkey
├── Index Scan com_met_usage_pkey
└── Index Scan com_dept_pkey
```

### 2.3 瓶颈定位

#### 瓶颈 1：`met_ipm_execbill_items` 全表扫描

```
Parallel Seq Scan on met_ipm_execbill_items
  实际扫描: 275,231 rows × 5 workers ≈ 1,376,155 行
  Filter: goods_id IS NOT NULL
  Rows Removed by Filter: 282,890 (50% 被过滤)
  Buffers: shared read=55,754 (100% 磁盘读取)
  I/O Timings: read=298.660ms
```

**问题**：
- 全表扫描，`goods_id IS NOT NULL` 过滤掉 50% 的行
- 数据全部走磁盘 I/O（`shared read=55754`，命中率 0%）
- 这是最大的时间消耗点

#### 瓶颈 2：`met_ipm_execbill_order` 全表扫描

```
Parallel Seq Scan on met_ipm_execbill_order
  实际扫描: 412,810 + 过滤后 ≈ 537,584 行
  Filter: send_order_id IS NULL AND send_drug = 1 AND is_auto_send = 0
  Rows Removed by Filter: 412,810 (77% 被过滤)
  Buffers: shared hit=31,989
```

**问题**：
- 全表扫描后过滤，77% 的扫描是无用功
- 三个过滤条件无索引支持

#### 瓶颈 3：分区表 Append 开销

```
Append (137个分区)
  有索引的分区: 35个 (202308~202606) → Index Scan
  无数据的空分区: 102个 (202607~203508) → Seq Scan
  总循环: 124,852 次 × 137 分区 = 17,104,724 次扫描尝试
```

**问题**：
- 137 个分区全部被扫描
- 空分区虽快（0.000ms/个），但累积开销可观
- 数据实际集中在少数分区（202606）

#### 瓶颈 4：`card` 过滤导致 0 行

```
Index Scan on met_ipm_execbill_card
  Index Cond: card.exec_card_id = execorder.exec_card_id
  Filter: plan_exec_date >= '2026-06-02' AND plan_exec_date <= '2026-06-03'
          AND exec_ward_id = '1671036900502269953'
          AND exec_status IN ('0','1','2')
  Rows Removed by Filter: 1
```

**问题**：
- 上游 Hash Join 返回 8 行，但 `card` 的 Filter 条件移除了所有行
- 最终 0 行结果，但前面的全表扫描已经白做了

---

## 三、优化措施

### 3.1 创建部分索引

根据瓶颈分析，创建两个部分索引（Partial Index）：

```sql
-- 索引1：met_ipm_execbill_items 表
-- 覆盖 JOIN 条件 (exec_card_id, order_id) 和 WHERE 过滤 (goods_id IS NOT NULL)
CREATE INDEX IF NOT EXISTS idx_execbill_items_order_goods
  ON met_ipm_execbill_items (exec_card_id, order_id)
  WHERE goods_id IS NOT NULL;

-- 索引2：met_ipm_execbill_order 表
-- 覆盖三个 WHERE 过滤条件和 JOIN 条件
CREATE INDEX IF NOT EXISTS idx_execbillorder_card_order
  ON met_ipm_execbill_order (exec_card_id, order_id)
  WHERE send_order_id IS NULL AND send_drug = 1 AND is_auto_send = 0;
```

### 3.2 为什么选择部分索引

| 维度 | 普通索引 | 部分索引（实际选择） |
|---|---|---|
| 索引大小 | 索引全部行 | 仅索引满足条件的行 |
| 写入开销 | 每次写入都维护 | 仅满足条件时维护 |
| 查询匹配 | 任何查询都能用 | WHERE 必须包含索引条件 |
| 适用场景 | 通用 | 本例查询条件固定，完美匹配 |

本例选择部分索引的原因：
1. **查询条件固定**：`send_order_id IS NULL AND send_drug = 1 AND is_auto_send = 0` 是固定的业务过滤条件
2. **过滤比例高**：77% 的行被过滤，部分索引体积仅为普通索引的 1/4
3. **写入性能**：减少索引维护开销，对高写入量的表更友好

---

## 四、优化后执行计划分析

### 4.1 整体指标

| 指标 | 优化前 | 优化后 | 提升倍数 |
|---|---|---|---|
| 总执行时间 | 5533ms | **78ms** | **71x** |
| 磁盘读取 | 55,754 buffers | **482 buffers** | **116x** |
| I/O 耗时 | 298ms | **2.7ms** | **110x** |
| 缓冲区命中 | 32,822 | 18,869 | - |
| 并行 Worker 数 | 4 | 2 | 资源占用更少 |
| 规划时间 | 40ms | 89ms | +49ms |

### 4.2 执行计划结构变化

```
Nested Loop Left Join (0 rows, 78ms) ← 从 5533ms 降至 78ms
├── Nested Loop Left Join (0 rows)
│   ├── Nested Loop Left Join (0 rows)
│   │   ├── Nested Loop (0 rows)
│   │   │   ├── Nested Loop (0 rows)
│   │   │   │   ├── Gather (0 rows, 74ms)
│   │   │   │   │   └── Nested Loop (2 rows × 3 workers)
│   │   │   │   │       ├── Nested Loop (2 rows)
│   │   │   │   │       │   ├── Parallel Hash Join (3 rows) ← 从 24,970 降至 3
│   │   │   │   │       │   │   ├── Index Scan idx_execbillorder_card_order ← 索引生效
│   │   │   │   │       │   │   │   (41,592 rows/worker)
│   │   │   │   │       │   │   └── Parallel Hash
│   │   │   │   │       │   │       └── Parallel Append (53 rows) ← 分区裁剪
│   │   │   │   │       │   │           └── Index Scan met_ipm_order_202606_pkey
│   │   │   │   │       │   └── Index Scan met_ipm_execbill_card
│   │   │   │   │       │       Filter: 0 rows (card 过滤)
│   │   │   │   │       └── Index Scan idx_execbill_items_order_goods ← 索引就绪
│   │   │   │   └── Index Scan met_ipm_patient_pkey
│   │   │   └── Index Scan fin_ipm_book_info_pkey
│   │   └── Index Scan com_met_frequency_pkey
│   └── Index Scan com_met_usage_pkey
└── Index Scan com_dept_pkey
```

### 4.3 关键变化详解

#### 变化 1：`execbill_order` 从 Seq Scan 变为 Index Scan

```
优化前: Parallel Seq Scan (412,810 行过滤)
优化后: Parallel Index Scan using idx_execbillorder_card_order (直接定位)
```

部分索引直接定位到满足 `send_order_id IS NULL AND send_drug = 1 AND is_auto_send = 0` 的行，不再全表扫描。

#### 变化 2：`execbill_items` 索引就绪

```
优化前: Parallel Seq Scan + Filter goods_id IS NOT NULL
优化后: Index Scan using idx_execbill_items_order_goods (never executed)
```

因为 `card` 过滤后返回 0 行，items 索引实际未被触发。但即使有数据时也会走索引，避免全表扫描。

#### 变化 3：分区表裁剪生效

```
优化前: 137 个分区全部扫描 (124,852 循环)
优化后: 仅扫描有数据的分区 (53 rows from met_ipm_order_202606)
```

规划器识别到 `execbill_order` 走索引后返回的 `order_id` 集合很小，自动裁剪了无关分区。

#### 变化 4：规划时间增加

```
优化前: Planning Time = 40ms
优化后: Planning Time = 89ms (+49ms)
```

规划时间翻倍，因为：
- 新增索引让规划器需要评估更多执行路径
- `Buffers: shared hit=28,487 read=212` — 规划阶段访问了大量系统表缓存

---

## 五、优化效果总结

### 5.1 性能提升

```
执行时间:  5533ms  ──→  78ms   (提升 71 倍)
I/O 读取:  55,754  ──→  482    (降低 116 倍)
I/O 耗时:  298ms   ──→  2.7ms  (降低 110 倍)
```

### 5.2 各阶段耗时对比

| 阶段 | 优化前 | 优化后 |
|---|---|---|
| `execbill_items` 扫描 | ~185ms (全表) | ~0ms (索引未触发) |
| `execbill_order` 扫描 | ~76ms (全表) | ~27ms (索引) |
| `met_ipm_order` 分区扫描 | ~350ms (137分区) | ~0.3ms (单分区) |
| `card` 过滤 | ~0ms | ~0ms |
| **总计** | **5533ms** | **78ms** |

### 5.3 剩余可优化点

| 项目 | 状态 | 建议 |
|---|---|---|
| `card` 过滤 0 行 | ⚠️ 业务问题 | 检查数据是否存在或查询条件是否正确 |
| 分区表 Append | ✅ 可忽略 | 仅 ~2ms，无需优化 |
| 规划时间 89ms | ⚠️ 高频场景 | 使用 Prepared Statement 缓存执行计划 |
| `card` 表索引 | 🔵 可选 | 如数据量大可加 `(exec_ward_id, plan_exec_date, exec_status)` 组合索引 |

---

## 六、经验总结

### 6.1 执行计划分析方法论

1. **看最外层 `rows` 和 `actual time`**：0 行但耗时长 = 有问题
2. **看 `actual time` 跳变**：找到时间消耗的转折点
3. **看 `Buffers` hit vs read**：read 多 = I/O 瓶颈
4. **看 `Rows Removed by Filter`**：过滤比例高 = 缺索引
5. **看分区 Append**：分区多且大部分空 = 规划开销
6. **看 `never executed`**：规划了但没执行 = 浪费规划时间

### 6.2 索引设计原则

1. **部分索引优先**：当查询条件固定且过滤比例高时，部分索引比普通索引更优
2. **覆盖 JOIN 条件**：索引列应包含 JOIN 的连接字段
3. **匹配 WHERE 条件**：部分索引的 WHERE 应与查询的 WHERE 一致
4. **评估写入影响**：高写入表用部分索引减少维护开销

### 6.3 分区表优化

1. **分区裁剪**：确保查询条件能触发分区裁剪
2. **清理空分区**：137 个分区中 102 个为空，增加了规划和执行开销
3. **索引覆盖**：有数据的分区应确保有索引

---

## 附录：创建的索引 SQL

```sql
-- 索引1：met_ipm_execbill_items 部分索引
CREATE INDEX IF NOT EXISTS idx_execbill_items_order_goods
  ON met_ipm_execbill_items (exec_card_id, order_id)
  WHERE goods_id IS NOT NULL;

-- 索引2：met_ipm_execbill_order 部分索引
CREATE INDEX IF NOT EXISTS idx_execbillorder_card_order
  ON met_ipm_execbill_order (exec_card_id, order_id)
  WHERE send_order_id IS NULL AND send_drug = 1 AND is_auto_send = 0;
```
