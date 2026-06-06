
-- 清空2025-11-28之前延时任务已经完成 的日志
```sql
delete from xxl_job_info where trigger_status = '0'  and job_type = 'EXPIRED' and add_time  < '2025-11-28'
```
