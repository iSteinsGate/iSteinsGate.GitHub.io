---
date created: 2023-02-23 14:36
---

#Sql/MsSql

# 表

```
对表结构字段进行修改：

添加列：alter table 表名 add 列名 varchar(55)

删除列：alter table 表名 drop column 列名

改列类型：alter table 表名 alter column 列名 varchar(22)

修改列名称：exec sp_rename '表名.字段名' , '新名', ‘column’

修改表名称：exec sp_rename '旧表名','新表名'

注意：新旧表名为存储过程的参数，需要带着单引号。

添加备注：

EXEC sp_addextendedproperty 'MS_Description', '拆零系数', 'SCHEMA', dbo, 'table', MAS_WH_Inventory_Diff, 'column', split_num;

修改备注

EXEC sp_updateextendedproperty 'MS_Description', '拆零系数', 'SCHEMA', dbo, 'table', MAS_WH_Inventory_Diff, 'column', split_num;
```


## 删除表
```sql
IF EXISTS(SELECT *  
          FROM sys.objects  
          WHERE object_id = OBJECT_ID(N'[dbo].[met_ipm_labor]')  
            AND type in (N'U'))  
DROP TABLE [dbo].[met_ipm_labor];
```

# 字段

## 删除字段
```sql
IF EXISTS(SELECT *  
              FROM sys.columns  
              WHERE name = 'labor_id'  
                AND [object_id] = OBJECT_ID(N'met_ipm_labor_record'))  
    begin  
  
alter table met_ipm_labor_record drop column labor_id;
    end
```

## 添加字段和字段备注

```sql
  
IF NOT EXISTS(SELECT *  
              FROM sys.columns  
              WHERE name = 'batch_id'  
                AND [object_id] = OBJECT_ID(N'msg_messagerecord'))  
    begin  
    ALTER TABLE [dbo].[msg_messagerecord]  
    ADD [batch_id] bigint null  
  
    EXEC sp_addextendedproperty 'MS_Description', '批次', 'SCHEMA', dbo, 'table', msg_messagerecord, 'column',batch_id;  
    end
```

## 修改字段

- 修改字段名

```sql
  
IF EXISTS(SELECT *  
              FROM sys.columns  
              WHERE name = 'mother_inpatient_id'  
                AND [object_id] = OBJECT_ID(N'met_ipm_labor_record'))  
								
EXEC sp_rename '[dbo].[met_ipm_labor_record].[mother_inpatient_id]', 'inpatient_id', 'COLUMN';
```

- 修改字段类型

```sql
alter table met_ipm_baby alter column baby_no nvarchar(10) null ;
```

# 查看死锁

```sql
select   request_session_id   spid,OBJECT_NAME(resource_associated_entity_id) tableName   
from   sys.dm_tran_locks where resource_type='OBJECT'

```

# 截取字符串

```sql
-- 省区
SELECT CHARINDEX( ',', patient_info.region_area_name ) FROM patient_info;
SELECT SUBSTRING( patient_info.region_area_name, 1, 3 ) FROM patient_info;
SELECT
	iif (
		CHARINDEX( ',', patient_info.region_area_name ) > 0,
		( CHARINDEX( ',', patient_info.region_area_name ) - 1 ),
		len( patient_info.region_area_name ) 
	) 
FROM patient_info
```

# 拼接多个字段
```sql
 SELECT 
    NULLIF(CONCAT_WS(', ', NULLIF(NULLIF(TRIM(col1), ''), ''), 
                      NULLIF(NULLIF(TRIM(col2), ''), ''), 
                      NULLIF(NULLIF(TRIM(col3), ''), '')), 
    '') AS concatenated_cols
FROM 
    your_table;
```
# 获取年，月 ， 日

```sql
	select GETDATE() as '当前日期',

                DateName(year,GetDate()) as '年'+

                DateName(month,GetDate()) as '月',

                DateName(day,GetDate()) as '日',

                DateName(dw,GetDate()) as '星期',

                DateName(week,GetDate()) as '周数',

                DateName(hour,GetDate()) as '时',

                DateName(minute,GetDate()) as '分',

                DateName(second,GetDate()) as '秒'
	 
```


```

select CONVERT(nvarchar(100),getDate(), 23)

```