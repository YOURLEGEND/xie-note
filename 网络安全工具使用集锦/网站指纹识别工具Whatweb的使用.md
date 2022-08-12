**目录**

[whatweb](#t0 "whatweb")

[一些常见的Whatweb的扫描](#t1 "一些常见的Whatweb的扫描")

[常规扫描](#t2 "常规扫描")

[批量扫描](#t3 "批量扫描")

[详细回显扫描](#t4 "详细回显扫描")

[扫描强度等级控制](#t5 "扫描强度等级控制")

[快速本地扫描(扫描内网的主机)](#t6 "快速本地扫描(扫描内网的主机)")

[将扫描结果导出至文件内](#t7 "将扫描结果导出至文件内")

* * *

whatweb
=======

whatweb 是[kali](https://so.csdn.net/so/search?q=kali&spm=1001.2101.3001.7020)中网站指纹识别的工具，使用Ruby语言开发。whatweb可识别web技术，包括内容管理系统(CMS)、博客平台、统计/分析包、JavaScript库，Web服务器和嵌入式设备等。它有超过900个插件，每个插件都能识别不同的东西。Whatweb还可以识别版本号，电子邮件地址、账户ID、Web框架模块，SQL错误等。

WhatWeb可以隐秘、快速、彻底或缓慢扫描。WhatWeb支持攻击级别来控制速度和可靠性之间的权衡。当在浏览器中访问网站时，该交易包含许多关于Web技术为该网站提供支持的提示。有时，单个网页访问包含足够的信息来识别网站，但如果没有，WhatWeb可以进一步询问网站。默认的攻击级别称为“被动”，速度最快，只需要一个网站的HTTP请求。这适用于扫描公共网站。在渗透测试中开发了更积极的模式。

用法： **weatweb  域名**

*   \-i  指定要扫描的文件
*   \-v 详细显示扫描的结果
*   \-a  指定运行级别

一些常见的Whatweb的扫描
---------------

### 常规扫描

**whatweb   域名**

![](https://img-blog.csdnimg.cn/20181114105949714.png)

### 批量扫描

我们可以通过将很多要扫描的网站域名写入文件内，然后扫描时指定该文件即可。

比如，我们现在在 root 目录下有 target.txt 文件，如下。# 号表示扫描时不扫描该域名

![](https://img-blog.csdnimg.cn/20181114112624641.png)

使用命令：**whatweb  -i  /root/target.txt**

![](https://img-blog.csdnimg.cn/20181114112934620.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 详细回显扫描

**whatweb  -v  域名**

![](https://img-blog.csdnimg.cn/20181114110107518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 扫描强度等级控制

**whatweb  -a  等级  域名**      可以和-v参数结合使用

![](https://img-blog.csdnimg.cn/20181114110310622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 快速本地扫描(扫描内网的主机)

**whatweb  --no-errors  -t  255   内网网段**   可以和-a和-v参数结合使用 

![](https://img-blog.csdnimg.cn/20181114110833602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 将扫描结果导出至文件内

**whatweb  www.baidu.com  --log-xml=baidu.xml**   将结果导入到baidu.xml文件中

![](https://img-blog.csdnimg.cn/20181114113152846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

其他格式导出文件同理：

*     --log-brief=FILE           简要的记录，每个网站只记录一条返回信息
*     --log-verbose=FILE        详细输出
*    --log-xml=FILE            返回xml格式的日志
*    --log-json=FILE            以json格式记录日志
*    --log-json-verbose=FILE    记录详细的json日志
*    --log-magictree=FILE       xml的树形结构

注：1. Json 格式需要安装 json 依赖 sudo gem install json

       2. Mongo 格式需要安装 mongo 依赖 sudo gem install mongo

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)