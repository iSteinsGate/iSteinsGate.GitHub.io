

## 添加字段

```sql
DO $$  
BEGIN  
    IF   NOT EXISTS (SELECT 1  FROM pg_attribute  WHERE attrelid = '表名' :: regclass  AND attname = '字段名') THEN  
    ALTER TABLE 表名  ADD COLUMN 字段名 int4; 
        COMMENT ON COLUMN 表名.字段名 IS '备注';  
END  IF;  
END $$;
```

## 设置字段不为空

```sql
update met_ipm_execbill_card set not_print = 0;  
  

ALTER TABLE met_ipm_execbill_card ALTER COLUMN not_print SET NOT NULL;
```