


## 查找traceId

```text
2023-11-01 08:28:20.884  INFO [tsinghui-biz,traceId,spanId] 31170 --- [io-20100-exec-6] c.g.c.i.e.controller.TestController   : 登陆参数:{}
```
- 需求1：通过"登陆参数"找到同行的traceId的值
```shell
grep "登陆参数" your_log_file.log | awk -F',' '{print $2}'
```
awk -F',' '{print $2}'：使用逗号作为分隔符，提取登陆参数行中的第二个字段（在这种情况下，就是 "traceId"）。

- 需求2：将找到的traceId作为参数传递给grep继续查询该文件
```shell
grep "登陆参数" your_log_file.log | awk -F',' '{print $2}'| xargs -I '{}' grep '{}' your_log_file.log
```
xargs -I '{}' grep '{}' your_log_file.log：使用 xargs 命令将前一步提取的 "traceId" 的值传递给 grep 命令，然后在同一文件中查找与该值相关的信息