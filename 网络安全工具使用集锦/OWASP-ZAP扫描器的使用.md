**目录**

[OWASP-ZAP](#t0 "OWASP-ZAP")

[更新](#t1 "更新")

[代理](#t2 "代理")

[目录扫描](#t3 "目录扫描")

[主动扫描(Active  Scan)](#t4 "主动扫描(Active  Scan)")

[扫描结果](#t5 "扫描结果")

[生成报告](#t6 "生成报告 ") 

* * *

OWASP-ZAP
---------

OWASP Zed攻击代理（ZAP）是世界上最受欢迎的免费安全审计工具之一，由数百名国际志愿者积极维护。它可以帮助你在开发和测试应用程序时自动查找Web应用程序中的安全漏洞。

也可以说ZAP是一个中间人代理。它能够获取你对Web应用程序发出的所有请求以及你从中收到的所有响应。

它即可以用于安全专家、开发人员、功能测试人员，甚至是渗透测试入门人员。它也是经验丰富的测试人员用于手动安全测试的绝佳工具。

OWASP-ZAP可以在windows、linux和mac下运行，Kali中自带OWASP-ZAP。给大家分享在windows下的OWASP-ZAP。

链接: [百度网盘 请输入提取码](https://pan.baidu.com/s/1EsOcaux2IxOIAqmEluHRJQ "百度网盘 请输入提取码")   提取码: q9hg 

OWASP-ZAP主要拥有以下重要功能：

*   本地代理
*   主动扫描
*   被动扫描
*   Fuzzy
*   暴力破解

虽然OWASP-ZAP拥有很多的功能，但是他最强大的功能还是主动扫描，可以自动对目标网站发起渗透测试，可以检测的缺陷包括路径遍历、文件包含、跨站脚本、sql注入等等。

### 更新

由于owasp zap 官方不定期的会更新zap插件和zap版本，所以我们也可以选择更新。只要更新栏中显示更新，我们就可以勾选，然后进行更新了。

![](https://img-blog.csdnimg.cn/20181115215422281.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击 Marketplace，这里会有三种可更新的插件。

*   release：为经过长期验证比较成熟的插件
*   beta：为正在测试测试中的插件，可能会出现问题
*   alpha：比beta更加低的测试版插件

![](https://img-blog.csdnimg.cn/20181115194752370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 代理

OWASP-ZAP 默认使用 8080 端口开启http代理，如果我们想修改其默认代理，在 工具-->选项--> 本地代理 进行设置。

![](https://img-blog.csdnimg.cn/20181115215628151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 目录扫描

我们配置浏览器设置代理端口为本地的 8080，然后我们访问任意的网站，都可以截取到访问的网址，从而实现攻击。

当我们在浏览器访问网站 后，OWASP-ZAP就会截取到我们访问的网站。

![](https://img-blog.csdnimg.cn/20181115195746672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

选择该站点进行 owsap-zap 的强制浏览网站、强制浏览目录，owasp zap的强制目录浏览选择使用owasp zap自带的directory-list-1.0.txt 目录字典进行尝试爬取（当然我们也可以自定义字典）

![](https://img-blog.csdnimg.cn/20181115201459476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181115220304684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

以上的目的是尽量的爬行出网站的所有链接页面。做完以上的工作之后，我们就可以选择该站点进行 active scan (主动扫描)了

### 主动扫描(Active  Scan)

主动扫描是OWASP-ZAP最强大的功能了，我们可以通过站点，右键然后Active Scan进行主动扫描。

![](https://img-blog.csdnimg.cn/20181115221110930.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们也可以快速开始这里填入域名直接进行攻击。

![](https://img-blog.csdnimg.cn/20181115221316820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 扫描结果

等主动扫描完后，在警报栏这里就会显示扫描结果了。左边是存在的漏洞，右边是漏洞的详情

![](https://img-blog.csdnimg.cn/20181115221527149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 生成报告 

![](https://img-blog.csdnimg.cn/20181115221831353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)