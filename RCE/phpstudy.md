# phpstudy后门

- **phpstudy后门影响版本**
> 
phpstudy 2016版php-5.4
phpstudy 2018版php-5.2.17
phpstudy 2018版php-5.4.45


<!--more-->


- **后门位置**

后门代码存在于\ext\php_xmlrpc.dll模块中
- phpStudy2016路径
php\php-5.2.17\ext\php_xmlrpc.dll
php\php-5.4.45\ext\php_xmlrpc.dll

- [phpStudy2018](http://www.pc6.com/softview/SoftView_48660.html)
\php\php-5.2.17\ext\php_xmlrpc.dll
\php\php-5.4.45\ext\php_xmlrpc.dll

# 漏洞证明：
使用notepod打开上述后门模块地址 打开此文件查找@eval，文件存在@eval(%s(‘%s’))证明漏洞存在
