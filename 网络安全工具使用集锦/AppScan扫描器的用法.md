**目录**

[AppScan](#t0 "AppScan")

[软件功能](#t1 "软件功能")

[建立一次基础的扫描](#t2 "建立一次基础的扫描")

* * *

AppScan
-------

**AppScan**是一款非常好用且功能强大的Web 应用[安全测试](https://so.csdn.net/so/search?q=%E5%AE%89%E5%85%A8%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)工具，曾以 Watchfire AppScan 的名称享誉业界，AppScan 可自动化 Web 应用的安全漏洞评估工作，能扫描和检测所有常见的 Web 应用安全漏洞，例如 SQL 注入（SQL-injection）、跨站脚本攻击（cross-site scripting）、缓冲区溢出（buffer overflow）和最新的 Flash/Flex 应用以及 Web 2.0 应用曝露等方面安全漏洞的扫描

Appscan工作原理小结如下：

*   通过爬行发现整个web应用结构
*   根据分析，发送修改的HTTP Request进行攻击尝试(扫描规则库)
*   通过对Response 的分析验证是否存在安全漏洞

### 软件功能

AppScan 采用三种彼此互补和增强的不同测试方法：

*   动态分析（“黑盒扫描”）：这是主要方法，用于测试和评估运行时的应用程序响应。
*   静态分析（“白盒扫描”）：这是用于在完整 Web 页面上下文中分析 JavaScript 代码的独特技术。
*   交互分析（“glass box 扫描”）：动态测试引擎可与驻留在 Web 服务器本身上的专用 glass-box 代理程序交互，从而使 AppScan 能够比仅通过传统动态测试时识别更多问题并具有更高准确性。

AppScan 的高级功能包括：常规和法规一致性报告，并提供超过 40 个不同的开箱即用模板通过 AppScan eXtension Framework 或通过使用 AppScan SDK 直接集成到现有系统内来实现的定制和可扩展性

链接分类功能：超越应用程序安全性以确认由指向恶意或其他不需要的站点的链接向用户带来的风险  
AppScan S可帮助您在站点部署之前为生产阶段的进行中风险评估来降低 Web 应用程序攻击和数据违规的风险。

  
软件特色  
“AppScan全面扫描”包含两个阶段：探索和测试。 尽管扫描过程的绝大部分对于用户来说实际上是无缝的，并且直到扫描完成几乎不需要用户输入，但理解其后的原则仍然很有帮助。  
探索阶段  
在第一个阶段中，AppScan 通过模拟 Web 用户单击链接和填写表单字段来探索站点（Web 应用程序或 Web 服务）。这就是“探索”阶段  
AppScan 将分析它所发送的每个请求的响应，查找潜在漏洞的任何指示信息。 AppScan 接收到可能指示有安全漏洞的响应时，它将自动基于响应创建测试，并通知所需验证规则，同时考虑在确定哪些结果构成漏洞以及所涉及到安全风险的级别时所需的验证规则。  
在发送所创建的特定于站点的测试之前，AppScan 将向应用程序发送若干格式不正确的请求，以确定其生成错误响应的方式。 之后，此信息将用于增加 AppScan 的自动测试验证过程的精确性。  
测试阶段  
在第二个阶段，AppScan 将发送它在探索阶段创建的数千个定制测试请求。 它使用定制验证规则记录和分析应用程序对每个测试的响应。 这些规则既可识别应用程序内的安全问题，又可排列其安全风险级别。  
无 Web 服务的站点  
如果是没有 Web 服务的站点，那么为 AppScan 提供起始 URL 和登录认证凭证可足以使其能够测试站点。  
如有必要，还可以通过 AppScan 手动搜寻站点，以便能够访问仅通过特定用户输入才能到达的区域。  
Web 服务  
为了能够有效扫描 Web Service，AppScan 安装包含一项工具，用户通过它可查看 Web 服务中整合的各种方法，处理输入数据以及检查来自服务的反馈。  
您首先需要为 AppScan 提供服务的 URL。 集成的“通用服务客户机 (GSC)”使用服务的 WSDL 文件以树格式显示可用的单独方法，并且会创建用于向服务发送请求的用户友好 GUI。您可以使用此界面输入参数和查看结果。此过程由 AppScan 进行“记录”，并且用于在 AppScan 扫描站点时创建针对服务的测试。

建立一次基础的扫描
---------

  
点击左上角的文件--.>新建-->常规扫描，然后进入了下面的页面，选择下一步即可

![](https://img-blog.csdn.net/2018101920114991?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里的话，就填写你要扫描网站的域名，如果你想通过代理进行扫描，可以勾选下面的其他连接设置。然后下一步

![](https://img-blog.csdn.net/20181019201431216?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果你要扫描的网站需要经过登录的网站的话，这里勾选记录，然后你模拟登录一次网站，那么APPscan就能记录你的登录信息了。如果不需要登录的话，勾选无然后下一步

![](https://img-blog.csdn.net/20181019201640296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里缺省值的话直接下一步就可以了

![](https://img-blog.csdn.net/20181019201750880?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里直接点击完成就可以开始一次扫描了

![](https://img-blog.csdn.net/20181019201824310?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当扫描完成后，这里查看扫描结果，AppScan扫描误报率有点高啊

![](https://img-blog.csdnimg.cn/20190614142850662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)