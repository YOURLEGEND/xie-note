**目录**

[Tomcat的几大高危漏洞](#t0)

[Tomcat安全措施](#t1)

* * *

### Tomcat的几大高危漏洞

1、Tomcat后台弱口令上传war包([Tomcat管理弱口令页面Getshell](https://blog.csdn.net/qq_36119192/article/details/90446112))

2、Tomcat的PUT的上传漏洞(CVE-2017-12615) （[Tomcat PUT方法任意文件上传(CVE-2017-12615)](https://blog.csdn.net/qq_36119192/article/details/100541953)）

3、Tomcat[反序列化](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)漏洞(CVE-2016-8735) ([Tomcat反序列化漏洞(CVE-2016-8735)](https://blog.csdn.net/qq_36119192/article/details/101207424))

4、Tomcat 样例目录session操控漏洞（[Apache Tomcat examples directory vulnerabilities(Apache Tomcat样例目录session操纵漏洞)](https://blog.csdn.net/qq_36119192/article/details/90266372)）

6、Tomcat本地提权漏洞(CVE-2016-1240)（[Tomcat本地提权漏洞分析与防御](https://www.baidu.com/link?url=CBbHY8bVN2V6fYW-ECUPgOvGnitaC7THB8cKSMku3ukyJ36eoMuef8HW7bBiM4gq&wd=&eqid=e8af4dac002068d3000000035d88a90d)）

### Tomcat安全措施

1、删除 webapps 目录中的 docs、examples、host-manager、manager 等正式环境用不着的目录，这一步就可以解决大部分漏洞。 

2、解决“**slow http denial of service attack**”漏洞

slow http denial of service attack漏洞是利用http post的时候，指定一个非常大的content-length，然后以很低的速度发包，比如10-100s发一个字节，让这种连接不断开，这样当客户端连接多了后，占用了webserver的所有可用连接，从而导制DOS，属于一种打拒绝服务攻击。

解决方法：

打开server.xml找到

```
<Connector port="8080" protocol="HTTP/1.1"      
　　connectionTimeout="20000"      
　　redirectPort="8443" />
```


将其中的connectionTimeout="20000"改为1000，其单位为ms。

Tomcat官方关于漏洞修复的说明：[http://tomcat.apache.org/security-7.html](http://tomcat.apache.org/security-7.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-218e8bd352484f8fa11ac90ecbbc905c)[集合](https://edu.csdn.net/skill/java/java-218e8bd352484f8fa11ac90ecbbc905c)[Collection接口](https://edu.csdn.net/skill/java/java-218e8bd352484f8fa11ac90ecbbc905c)44232 人正在系统学习中