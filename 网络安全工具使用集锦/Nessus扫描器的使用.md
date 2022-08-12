**目录**

[Nessus](#t0 "Nessus")

[Scans](#t1 "Scans")

[Settings](#t2 "Settings")

[一个基本扫描的建立](#t3 "一个基本扫描的建立")

[自定义扫描策略](#t4 "自定义扫描策略")

[Nessus的高级扫描方法](#t5 "Nessus的高级扫描方法")

* * *

Nessus
------

**Nessus**号称是世界上最流行的[漏洞扫描](https://so.csdn.net/so/search?q=%E6%BC%8F%E6%B4%9E%E6%89%AB%E6%8F%8F&spm=1001.2101.3001.7020)程序，全世界有超过75000个组织在使用它。该工具提供完整的电脑漏洞扫描服务，并随时更新其漏洞数据库。Nessus不同于传统的漏洞扫描软件，Nessus可同时在本机或远端上遥控，进行系统的漏洞分析扫描。对应渗透测试人员来说，Nessus是必不可少的工具之一。它不仅免费而且更新极快。安全扫描器的功能是对指定网络进行安全检查，找出该网络是否存在有导致黑客攻击的安全漏洞。该系统被设计为client/sever模式，服务器端负责进行安全检查，客户端用来配置管理服务器端。在服务端还采用 了plugin的体系，允许用户加入执行特定功能的插件，这插件可以进行更快速和更复杂的安全检查。在Nessus中还采用了一个共享的信息接口，称为 知识库，其中保存了前面进行检查的结果。检查的结果可以HTML、纯文本、LaTeX（一种文本文件格式）等几种格式保存。

Nessus不仅可以扫描网站，还可以扫描主机。

它由一个执行任务的服务端，和一个分配任务的客户端组成。

它使用8834端口作为后台，你在本地输入 [https://localhost:8834](https://localhost:8834/#/scans/folders/my-scans "https://localhost:8834") 即可转到登录后台页面，然后输入账户名和密码即可登录。

它有两个大的选择按钮：Scans 和 Settings

![](https://img-blog.csdn.net/20181019090249185?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Scans

Scans按钮下面是我们的扫描的一些选项，分别有My Scans、All Scans、Trash、Policies、Plugin Rules和Scanners

![](https://img-blog.csdn.net/20181019090446296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   My Scans就是你的一些扫描的站点
*   All Scans就是你曾经所有的扫描。
*   Trash就是垃圾桶
*   Polices就是策略，策略允许您创建自定义模板，定义在扫描期间执行的操作。创建之后，可以从扫描模板列表中选择它们。从这个页面，您可以查看、创建、导入、下载、编辑和删除策略。
*   Plugin Rules是插件规则，插件规则允许您隐藏或更改任何给定插件的严重性。此外，规则可以限制在特定的主机或特定的时间范围内。从此页面，您可以查看、创建、编辑和删除规则。
*   Scanners扫描，远程扫描仪可以通过升级链接到Nessus。一旦链接，就可以在本地管理它们，并在配置扫描时选择它们。从此页面，您可以查看扫描仪的当前状态，并向下钻取以控制所有正在运行的扫描。

### Settings

Settings按钮下面是软件的一些基本设置，分别有About，Advanced，Proxy Server，SMTP Server，Custom CA，Password Mgmt，My account，Users

![](https://img-blog.csdn.net/20181019191856813?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   About就是一些关于软件的版本等信息
*   Advanced是高级设置，高级设置允许手动配置全局设置。为了使这些设置生效，可能需要重新启动Nessus服务或服务器。注意：在扫描或策略中配置的设置将覆盖这些值。
*   Proxy Server就是代理服务器，如果你要通过代理扫描网站的话，就需要在这里配置信息
*   SMTP Server就是邮件服务器，简单邮件传输协议(SMTP)是收发电子邮件的行业标准。一旦配置为SMTP，扫描结果将通过电子邮件发送到扫描的“电子邮件通知”配置中指定的收件人列表。这些结果可以通过过滤器定制，并需要一个与HTML兼容的电子邮件客户端。
*   Custom CA是自定义的证书颁发机构，在扫描期间，保存自定义证书颁发机构(CA)有助于减少来自插件#51192(SSL证书不能信任)的发现。
*   Password Mgmt是密码管理，密码管理允许您设置密码参数，以及打开登录通知和设置会话超时。登录通知允许用户查看上次成功登录、最后一次失败的登录尝试(日期、时间和IP)，以及自上次成功登录以来是否发生了任何失败的登录尝试。更改将在软重新启动后生效。
*   My Account就是管理员账号的一些信息，通过这里可以修改管理员账户的密码
*   Users是用户，从此页面，您可以查看、创建、编辑和删除用户。一旦创建，用户将配置一个角色，该角色确定用户的扫描权限。此外，每个用户都可以生成一个自定义API密钥来使用RESTAPI进行身份验证。

一个基本扫描的建立
---------

一般我们要扫描一个主机或者网站的话，点击My Scans，然后New Scan新建一个扫描即可。扫描模板的话，如果我们要扫描的是一个网站，我们选择Web Application Tests；如果是要扫描一个主机的话，我们选择Advanced Scan

![](https://img-blog.csdnimg.cn/20181026200230125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

然后进入了下面的页面，Name就是这次扫描的名字，随便写，但是最好不要写中文。Description就是对这次扫描的描述，也可以随便写，Targets就写目标网站的 ip地址或者 域名都可以。然后点击Save保存好。

![](https://img-blog.csdn.net/20181019193248506?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后回到了My Scans页面，点击开始就开始扫描了。

![](https://img-blog.csdn.net/20181019193528861?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

扫描完成的话，这里会有扫描的结构，漏洞分为5种程度，最高级别Critical最低级别info。

![](https://img-blog.csdn.net/20181019193629449?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们可以点进去看每个漏洞的具体描述信息，通过对漏洞的分析，我们可以更好地加固我们的系统

![](https://img-blog.csdn.net/2018101919381969?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

自定义扫描策略
-------

Freebuf：[Nessus自定义扫描策略](http://www.freebuf.com/column/144954.html "Nessus自定义扫描策略")

Nessus的高级扫描方法
-------------

Freebuf：[Nessus的高级扫描方法](http://www.freebuf.com/column/144167.html "Nessus的高级扫描方法")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)