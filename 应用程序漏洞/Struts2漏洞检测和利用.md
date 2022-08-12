**目录**

[Struts2](#t0 "Struts2")

[Struts2爆发过的RCE漏洞](#t1 "Struts2爆发过的RCE漏洞")

[判断网站是否使用了struts2框架](#t2 "判断网站是否使用了struts2框架")

[判断网站是否存在struts2漏洞](#t3 "判断网站是否存在struts2漏洞")

[对struts2漏洞进行利用](#t4 "对struts2漏洞进行利用")

* * *

由于本人并非JAVA程序员，所以对JAVA方面的知识不是很懂，仅仅是能看懂而已，对struts2[框架](https://so.csdn.net/so/search?q=%E6%A1%86%E6%9E%B6&spm=1001.2101.3001.7020)更是不懂。本文参考其他对struts2漏洞讲解的文章进行归纳总结，以后有时间去学习JAVA WEB开发方面的知识，后期再看了解原理。

Struts2
=======

[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) Struts2 作为世界上最流行的 Java Web 服务器框架之一，广泛应用于各种大型网站中。

[Struts](https://so.csdn.net/so/search?q=Struts&spm=1001.2101.3001.7020) 2是Struts的下一代产品，是在 Struts 1和WebWork的技术基础上进行了合并的全新的Struts 2框架。其全新的Struts 2的体系结构与Struts 1的体系结构差别巨大。Struts 2以WebWork为核心，采用拦截器的机制来处理用户的请求，这样的设计也使得业务逻辑控制器能够与 ServletAPI完全脱离开，所以Struts 2可以理解为WebWork的更新产品。虽然从Struts 1到Struts 2有着太大的变化，但是相对于WebWork，Struts 2的变化很小。

自Struts 2诞生以上，有关于这个框架的漏洞就没有停止过。从2007年7月23日发布的第一个Struts2漏洞S2-001到2020年12月8日发布的最新漏洞S2-061，跨度足足有十几年，而漏洞的个数也升至数十个。分析了Struts2的这些漏洞发现，基本上是RCE(远程代码执行)、XSS、CSRF、DOS、目录遍历和其他功能缺陷漏洞等等，而本文主要关注的是Struts2的RCE漏洞。Struts2会对某些标签属性（比如 id）的属性值进行二次表达式解析，因此在某些场景下将可能导致远程代码执行。

使用Struts2框架的页面一般都是以.action结尾的，所以如果我们看到这种URL，我们首先应该想到的就是Struts2漏洞。

Struts2爆发过的RCE漏洞
----------------

1.  S2-001  影响版本：Struts 2.0.0 - 2.0.8，相关文章：[Struts2漏洞系列之【S2-001】利用表单错误进行远程代码执行](https://mp.weixin.qq.com/s/Iehfk4JEAZ0-BjpDYpkXMg "Struts2漏洞系列之【S2-001】利用表单错误进行远程代码执行")
2.  S2-005  影响版本：Struts 2.0.0 - 2.1.8.1 ，相关文章：[Struts2漏洞系列之【S2-005】 参数名过滤不严谨导致代码执行](https://mp.weixin.qq.com/s/xaVxdYPRIhlxiFLy9WhUHA "Struts2漏洞系列之【S2-005】 参数名过滤不严谨导致代码执行")
3.  S2-007  影响版本：Struts 2.0.0 - 2.2.3，相关文章：[Struts2漏洞系列之【S2-007】 当类型转换出错时,用户输入被当作OGNL执行](https://mp.weixin.qq.com/s/EixluN3d1GcglXfcYeO7wg "Struts2漏洞系列之【S2-007】 当类型转换出错时,用户输入被当作OGNL执行")
4.  S2-008  影响版本：Struts 2.1.0 - 2.3.1
5.  S2-009  影响版本：Struts 2.1.0 - 2.3.1.1，相关文章：[Struts2漏洞系列之【S2-009】 远程代码执行漏洞](https://mp.weixin.qq.com/s/W0ZWPNlijQTuebLBxNxgfw "Struts2漏洞系列之【S2-009】 远程代码执行漏洞")
6.  S2-012  影响版本：Struts 2.1.0 - 2.3.13，相关文章：[Struts2漏洞系列之【S2-012】重定向导致的OGNL表达式执行](https://mp.weixin.qq.com/s/S5TGRtCVI9heT7bopGLJhA "Struts2漏洞系列之【S2-012】重定向导致的OGNL表达式执行")
7.  S2-013  影响版本：Struts 2.0.0 - 2.3.14 
8.  S2-015  影响版本：Struts 2.0.0 - 2.3.14.2，相关文章：[Struts2漏洞系列之【S2-015】ActionName 配置了通配符时导致的OGNL表达式执行](https://mp.weixin.qq.com/s/npm87uV1eIBHr0OexQFs1w "Struts2漏洞系列之【S2-015】ActionName 配置了通配符时导致的OGNL表达式执行")
9.  S2-016  影响版本：Struts 2.0.0 – 2.3.15，相关文章：[Struts2漏洞系列之【S2-016】特殊参数名前缀导致的OGNL表达式执行](https://mp.weixin.qq.com/s/g1g7OqLyUg9Cd0tH2bvFpg "Struts2漏洞系列之【S2-016】特殊参数名前缀导致的OGNL表达式执行")
10.  S2-019  影响版本：Struts 2.0.0 - 2.3.15.1
11.  S2-devMode 影响版本：当Struts开启devMode时，该漏洞将影响Struts 2.1.0–2.5.1，通杀Struts2所有版本
12.  S2-032  影响版本：Struts 2.3.20 – 2.3.28（2.3.20.3和2.3.24.3除外），相关文章：[Struts2漏洞系列之【S2-032】特殊参数名前缀 \`method:\` 导致的OGNL表达式执行](https://mp.weixin.qq.com/s/FX05X734SoIbev1zSSR6MA "Struts2漏洞系列之【S2-032】特殊参数名前缀 `method:` 导致的OGNL表达式执行 ")
13.  S2-045  影响版本：Struts 2.3.5 – 2.3.31   Struts2.5 – 2.5.10，相关文章：[Struts2漏洞系列之【S2-045】上传数据异常导致的OGNL表达式执行](https://mp.weixin.qq.com/s/0uY7luyJIj1sZMclrNBcoA "Struts2漏洞系列之【S2-045】上传数据异常导致的OGNL表达式执行")
14.  S2-048  影响版本：Struts 2.3.x 系列中启用了 struts2-struts1-plugin 插件的版本
15.  S2-052  影响版本：Struts 2.1.2 - Struts 2.3.33   Struts 2.5 - Struts 2.5.12，相关文章：[Struts2漏洞系列之【S2-052】REST插件远程执行命令漏洞](https://mp.weixin.qq.com/s/Yd2NZIkzhMEaM6UiU_M9_Q "Struts2漏洞系列之【S2-052】REST插件远程执行命令漏洞")
16.  S2-053  影响版本：Struts 2.0.1-2.3.33   Struts 2.5-2.5.10
17.  S2-057  影响版本：Struts 2.3–2.3.34    Struts2.5–2.5.16，相关文章：[Struts2漏洞系列之【S2-057】namespace可控导致的OGNL表达式执行](https://mp.weixin.qq.com/s/3W4YqSii3a2IVHOsP4tqlw "Struts2漏洞系列之【S2-057】namespace可控导致的OGNL表达式执行")
18.  S2-059  影响版本：Struts 2.0.0 - Struts 2.5.20  ，相关文章：[Struts2 S2-059 漏洞分析](https://mp.weixin.qq.com/s?__biz=MzUyMDEyNTkwNA==&mid=2247484472&idx=1&sn=35c122ea5f7dbbcc302f0a88cea70ac6&scene=21#wechat_redirect "Struts2 S2-059 漏洞分析")
19.  S2-061  影响版本：Struts 2.0.0 - Struts 2.5.25  ，相关文章：[Struts2 S2-061漏洞分析(CVE-2020-17530)](https://mp.weixin.qq.com/s/RD2HTMn-jFxDIs4-X95u6g "Struts2 S2-061漏洞分析(CVE-2020-17530)")

判断网站是否使用了struts2框架
------------------

评价：准确性不是很高，第一个版本误报特别多，v2版本也还是存在很多误报，但是比第一个版本好多了。这里我们只用v2版本测试。

单个URL检测：python2  struts2\_hunt\_v2.py  http://www.demo.com/

![](https://img-blog.csdnimg.cn/2020022316265134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

批量URL检测：python2  POC-T.py  -s  struts2\_hunt\_v2  -iF  url.txt

这里批量检测利用到了POC-T脚本调用框架

![](https://img-blog.csdnimg.cn/20200223162703515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

判断网站是否存在struts2漏洞
-----------------

这里判断网站是否存在struts2漏洞，有挺多工具的。

**安恒出品的一款struts2漏洞工具**

单个URL检测

![](https://img-blog.csdnimg.cn/20200223163302381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

批量URL检测

![](https://img-blog.csdnimg.cn/2020022316315741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Python脚本进行检测**

两个python脚本都可以检测

**![](https://img-blog.csdnimg.cn/20200223172653220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

**![](https://img-blog.csdnimg.cn/20200223172717161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

对struts2漏洞进行利用
--------------

安恒工具可以可视化利用，脚本也可以直接利用

**命令执行**

![](https://img-blog.csdnimg.cn/2020022317285186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**命令执行**

![](https://img-blog.csdnimg.cn/20200223172918422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**得到网站绝对路径**

![](https://img-blog.csdnimg.cn/2020022317295881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**上传文件**

![](https://img-blog.csdnimg.cn/20200223172939198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[史上最全Struts2 漏洞复现合集（上）](https://zhuanlan.zhihu.com/p/183876381 "史上最全Struts2 漏洞复现合集（上）")

                 [史上最全Struts 2 漏洞复现合集（下）](https://www.freebuf.com/vuls/246969.html "史上最全Struts 2 漏洞复现合集（下）")

              [【开篇】Struts2历史漏洞系列分析文章](https://mp.weixin.qq.com/s/VNlZtOJ6KU2QGrZEclUAEA "【开篇】Struts2历史漏洞系列分析文章")