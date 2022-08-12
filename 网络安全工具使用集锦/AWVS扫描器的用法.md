**目录**

[AWVS](#t0 "AWVS")

[AWVS功能介绍](#t1 "AWVS功能介绍")

[AWVS如何工作](#t2 "AWVS如何工作")

 [审核漏洞](#t3 " 审核漏洞")

[AWVS11页面介绍](#t4 "AWVS11页面介绍")

[AWVS11中建立扫描](#t5 "AWVS11中建立扫描")

[AWVS10.5中的介绍](#t6 "AWVS10.5中的介绍")

[AWVS11版本启动失败](#t7 "AWVS11版本启动失败")

[利用Burpsuite修改AWVS的数据包标识](#t8 "利用Burpsuite修改AWVS的数据包标识")

* * *

AWVS
----

**WVS(Web Vulnerability Scanner)**是一个自动化的Web应用程序[安全测试](https://so.csdn.net/so/search?q=%E5%AE%89%E5%85%A8%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)工具，它可以扫描任何可通过Web浏览器访问的和遵循HTTP/HTTPS规则的Web站点和Web应用程序。适用于任何中小型和大型企业的内联网、外延网和面向客户、雇员、厂商和其它人员的Web网站。WVS可以通过检查SQL注入攻击漏洞、XSS跨站脚本攻击漏洞等漏洞来审核Web应用程序的安全性。 

### AWVS功能介绍

*   WebScanner ：核心功能，web安全漏洞扫描 (深度，宽度 ，限制20个) 
*   Site Crawler：站点爬行，遍历站点目录结构 
*   Target Finder ：主机发现，找出给定网段中开启了80和443端口的主机
*   Subdomian Scanner ：子域名扫描器，利用DNS查询
*   Blind SQL Injector ：盲注工具 
*   Http Editor http：协议数据包编辑器
*   HTTP Sniffer ： HTTP协议嗅探器 （fiddler，wireshark,bp） 
*   HTTP Fuzzer： 模糊测试工具 (bp)
*   Authentication Tester ：Web认证破解工具

**基础知识：**

*   白盒测试：结构测试，透明盒测试，在知道代码的情况下进行渗透，相当于代码审计 
*   黑盒测试：在测试中，把程序看做一个不能打开的黑盒子，在完全不考虑程序内部结构和内部特性的情况中，在程序接口进行测试扫描，什么都不知道 
*   灰盒测试：介于白盒测试与黑盒测试之间的一种测试，在服务端设置代理agent 从客户端入手 （有权限去访问）

### AWVS如何工作

1.  它会扫描整个网络，通过跟踪站点上的所有链接和robots.txt（如果有的话）而实现扫描。 然后AWVS就会映射出站点的结构并显示每个文件的细节信息。 
2.  在上述的发现阶段或者扫描过程之后，WVS就会自动地对所发现的每一个页面发动一系列的漏洞攻击，这实质上是模拟一个黑客的攻击过程。（自定义的脚本,去探测是否有漏洞） AWVS分析每一个页面中需要输入数据的地方，进而尝试所有的输入组合。这是一个自动扫描阶段 
3.  在它发现漏洞之后，WVS就会在“Alerts Node(警告节点)”中报告这些漏洞，每一个警告都包含着漏洞信息和如何修补漏洞的建议。 
4.  在一次扫描完成之后，它会将结果保存为文件以备日后分析以及与以前的扫描相比较，使用报告工具，就可以创建一个专业的报告来总结这次扫描。

###  审核漏洞

*   版本检查：包括易受攻击的Web服务器，易受攻击的Web服务器技术 
*   CGI测试：包括检查Web服务器问题，主要是决定在服务器上是否启用了危险的HTTP方法。例如put 、trace，delete等等 
*   参数操纵：主要包括跨站脚本攻击（XSS），SQL注入攻击，代码执行，目录遍历攻击，文件入侵，脚本源代码泄露，CRLF注入，PHP代码注入，XPath注入，LDAP注入，Cookie操纵，URL重定向，应用程序错误消息等。 
*   多请求参数操纵：主要是Blind SQL/XPath注入攻击 
*   文件检查：检查备份文件或目录，查找常见的文件（如日志文件，应用程序踪迹等），以及URL中的跨站脚本攻击，还要检查脚本错误等 
*   Web应用程序：检查特定Web应用程序的已知漏洞的大型数据库，例如论坛，Web入口，CMS系统，电子商务应用程序和PHP库等
*   GHDB Google攻击数据库，可以检查数据库中1400多条GHDB搜索项目。 
*   Web服务：主要是参数处理，其中包括SQL注入、Blind SOL注入（盲注），代码执行，XPath注入，应用程序错误消息等。

使用该软件的所提供的手动工具，还可以执行其他的漏洞测试，包括输入合法检查，验证攻击，缓冲区溢出等。

AWVS11页面介绍
----------

AWVS从版本11开始，变成了网页端打开的形式，使用一个自定义的端口进行连接。

在页面的最左边，有6个按钮，分别是Dashboard、Targets、Vulnerabilities、Scans、Reports和settings

![](https://img-blog.csdn.net/20181019203346433?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   Dashboard是仪表盘，你扫描过的网站的一些漏洞信息这里会显示
*   Targets是目标网站，就是你要扫描的目标网站
*   Vulnerabilities就是漏洞，网站的漏洞
*   Scans就是扫描目标站点，从Target里面选择目标站点进行扫描
*   Reports就是漏洞扫描完之后的报告
*   Settings就是软件的一些设置，包括软件更新，代理设置等等

AWVS11中建立扫描
-----------

点击Targets-->Add Target，然后会弹出这个页面，Address就填我们要扫描的网站域名，Description就填写这次扫描的描述信息，可随便填，然后点击Add Target即可

![](https://img-blog.csdn.net/2018101920435750?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后进入了下面这个页面，Scan Speed可以选择扫描的速度，速度越慢扫描的也越仔细。如果站点是需要登录的网站，这里就要勾选上Site Login，如果站点不需要登录的话，这里就不用勾选。这底下还有一个AcuSensor，总的来说就是允许扫描仪从PHP、ASP或JSP应用程序中收集更多信息，从而改进扫描结果并使其可靠性更高。

![](https://img-blog.csdn.net/20181019204709542?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Site Login这里给了我们两个选择，一个是输入用户名和密码。另外一个是使用先前配置好的登录信息

![](https://img-blog.csdn.net/201810192050292?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里是针对不同Web站点的扫描插件

![](https://img-blog.csdn.net/20181019205732757?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 这里还有Crawl、HTTP、Advanced一些功能，我们现在还不必去选。我们就直接点击Scan开始吧。

![](https://img-blog.csdn.net/2018101921014816?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后弹出下面的页面，如果想修改的可以自己修改，修改完后直接点击Create Scan开始扫描

![](https://img-blog.csdn.net/20181019210337893?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

AWVS10.5中的介绍
------------

10.5的版本是客户端版本的，还留有很多其他的功能。但是我们最常用的还是站点扫描，在进行站点扫描中我们需要注意的一点就是，下面这里勾选上。将这里勾选上的话，在扫描的过程中会弹出网站的页面。有些网站需要登录，我们就可以输入登录了。还有就是可以多点点网站的页面，并且在有输入框的地方随便输入。软件会记录这些网页，可以帮助扫描更准确。

![](https://img-blog.csdnimg.cn/20181115230033421.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

AWVS11版本启动失败
------------

需要注意的是，因为自从AWVS11开始，AWVS就变成了B/S架构，所以我们有时候在访问AWVS的时候，需要开启AWVS的服务，否则会报错！

AWVS： A connection to server could not be established.\[Status:-1\]

然后我们开启如下服务即可！

![](https://img-blog.csdnimg.cn/20181116080231192.png)

我们也可以使用脚本来控制启动和关闭AWVS服务

```
@echo off      
mode con lines=30 cols=60      
cls      
echo.-----------------------------------------------------------      
echo.请选择使用：      
echo.      
echo. 1.启动Awvs 11（输入1）      
echo.      
echo. 2.停止Awvs 11（输入2）      1
echo.-----------------------------------------------------------       1
if exist "%SystemRoot%\System32\choice.exe" goto Win7Choice      1
set /p choice=请输入数字并按回车键确认:      1
echo.      1
if %choice%==1 goto StartAwvs      1
if %choice%==2 goto StopAwvs      1
cls      1
"set choice="      1
echo 您输入有误，请重新选择。      2
goto main       2
:Win7Choice      2
choice /c 12 /n /m "请输入相应数字："      2
if errorlevel 2 goto StopAwvs      2
if errorlevel 1 goto StartAwvs      2
cls      2
goto main       2
:StartAwvs      3
cls      3
color 2f      3
net start Acunetix      3
net start "Acunetix Database"      3
echo.-----------------------------------------------------------      3
echo.      3
echo 成功启动 "Acunetix"，"Acunetix Database" 服务！      3
echo.      3
goto end       4
:StopAwvs      4
cls      4
color 2f      4
net stop Acunetix      4
net stop "Acunetix Database"      4
echo.-----------------------------------------------------------      4
echo.      4
echo 成功关闭 "Acunetix"，"Acunetix Database" 服务！      4
echo.      4
goto end       5
:end      5
echo 请按任意键退出。      5
@Pause>nul
```


![](https://img-blog.csdnimg.cn/2018111608063213.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用Burpsuite修改AWVS的数据包标识
-----------------------

在用AWVS扫描的时候，有时候网站有WAF，AWVS就扫不出来任何东西了或者直接就死掉了。因为WAF的规则中有能识别AWVS的标识。所以我们就需要对AWVS的发送的数据包的标识去除。这个我们可以用Burpsuite来做。

在AWVS设置代理： Configuration->Scan Setting->LAN Settings

![](https://img-blog.csdnimg.cn/20190612153821221.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们用BurpSuite随便爬去一个网站，就可以在Burpsuite中抓到该数据包了。

![](https://img-blog.csdnimg.cn/20190613095332662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

通过分析数据包发现特征字段:

*   Acunetix-Aspect: enabled
*   Acunetix-Aspect-Password: 082119f75623eb7abd7bf357698ff66c
*   Acunetix-Aspect-Queries: filelist;aspectalerts

接下里可利用burpsuite正则的功能在每一次发包时都将特征字典替换为空

![](https://img-blog.csdnimg.cn/20190614114034458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

替换完成后将Intercept置为off开启自动代理模式.

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)