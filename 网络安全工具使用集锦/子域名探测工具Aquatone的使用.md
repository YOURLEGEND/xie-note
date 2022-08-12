**目录**

[Aquatone](#t0 "Aquatone")

[Aquatone的安装](#t1 "Aquatone的安装")

[Aquatone的使用](#t2 "Aquatone的使用")

[子域名爆破](#t3 "子域名爆破")

[端口扫描](#t4 "端口扫描")

[信息收集](#t5 "信息收集")

[子域名劫持](#t6 "子域名劫持")

* * *

Aquatone
========

Aquatone是一款子域名挖掘工具，Aquatone不仅仅只是通过简单的子域爆破，它还会利用各种开放的互联网服务和资源，来协助其完成子域[枚举](https://so.csdn.net/so/search?q=%E6%9E%9A%E4%B8%BE&spm=1001.2101.3001.7020)任务，这也大大提高了子域的爆破率。当发现子域时，我们还可以使用Aauatone来探测主机的公共HTTP端口，并收集响应头，HTML和屏幕截图，并能最终为我们生成一个报告，便于我们后续的分析利用。

Aquatone项目地址：[https://github.com/michenriksen/aquatone](https://github.com/michenriksen/aquatone "https://github.com/michenriksen/aquatone")

Aquatone的安装
-----------

Aquatone作者建议使用Kali安装使用该工具，并且也只针对Kali使用过程中遇到的问题进行修复，所以这里使用最新版Kali进行安装。使用 aquatone-gather 命令需要用到 Nightmare.js，所以需要安装 npm、electron 以及 Nightmare。

**1：安装Aquatone**

```
gem install aquatone
```


![](https://img-blog.csdnimg.cn/2019101020450970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

安装目录为：/var/lib/gems/2.3.0/gems/aquatone-0.5.0/

![](https://img-blog.csdnimg.cn/20191010204808160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**2：安装 npm**

```
apt-get install npm      
npm -v
```


安装完成后使用 npm -v 命令查看是否安装成功

![](https://img-blog.csdnimg.cn/20191011103211630.png)

**3：安装 electron**

安装 electron 和 Nightmare首先需要进入 aquatone 安装目录，也就是 /var/lib/gems/2.3.0/gems/aquatone-0.5.0

（1）直接安装

使用命令：npm install electron

直接安装，缺点是连接国外服务器网络不稳定，下载了好几次都不能完整下载，这个会引发aquatone使用时提示兼容性问题，后面会详细介绍。

（2）使用淘宝镜像安装electron

可以通过以下命令从淘宝npm安装：

```
npm install -g electron --registry=https://registry.npm.taobao.org --unsafe-perm=true
```


![](https://img-blog.csdnimg.cn/20191011221124193.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**4：安装nightmare**

electron 是 nightmare 的依赖环境，所以先安装 electron 然后再开始安装 nightmare

```
npm install -g nightmare --registry=https://registry.npm.taobao.org --unsafe-perm=true
```


Aquatone的使用
-----------

Aquatone按功能分为四个部分：aquatone-discover，aquatone-scan、aquatone-gather 和 aquatone-takeover，具体功能分别如下：

*   aquatone-discover：使用被动收集或字典爆破方式发现子域名
*   aquatone-scan：完成子域名扫描后，可扫描域名开放端口、HTTP header、HTML body、截图等信息并生成报告
*   aquatone-gather：对扫描结果中的IP进行访问请求和网页截图，搜集信息
*   aquatone-takeover：检测域名是否存在子域名劫持风险

四个模块都共用的参数

```
--domain: 要查询的域名      
--threads：查询线程，默认为5      
--fallback-nameservers：手工设置备用查询域名服务器，后跟IP，多个IP用逗号分隔       
以下两个参数可以帮助规避IDS之类的防御：      
--sleep：DNS lookup间隔，单位为秒，但是使用此选项后线程数会被强制设为1      
--jitter：sleep的变化阈值，单位为百分比，可以使sleep时间在一定范围内随机化。此参数只在sleep启用的情况下生效
```


### 子域名爆破

```
aquatone-discover --domain baidu.com  --threads 10
```


![](https://img-blog.csdnimg.cn/20191011160016676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191011160037445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里有一些Error，是因为我们的网络连接不到google.com等网站。还有一些Skipped，是因为我们没有设置这些网站的key值。

**API key**

aquatone-discover在进行子域名挖掘时会用到一些被动收集器，其中如VirusTotal、Censys、PassiveTotal、Shodan这样的服务会要求提供API key方可使用。可以用类似如下指令录入API key：

```
aquatone-discover --set-key shodan o1hyw8pv59vSVjrZU3Qaz6ZQqgM91ihQ
```


 所有的key都保存在以下路径： `/`~`/aquatone/.keys.yml`。

![](https://img-blog.csdnimg.cn/20191011154832803.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**扫描结果**

子域名挖掘过程结束后，工具会在  `/``~/aquatone/域名/`  文件夹下创建 `hosts.txt` 文件，内容为发现的子域名及其对应IP：

此外，工具还会同时生成以 json 格式保存的相同内容的 `hosts.json` 文件，aquatone-scan 和 aquatone-gather 可以直接使用这个文件。

![](https://img-blog.csdnimg.cn/2019101115563626.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 端口扫描

```
aquatone-scan --domain baidu.com --ports 6379,445,80,3389,3306       
--domain 选项会使工具自动寻找子域名挖掘过程中生成的hosts.json 文件。      
aquatone-scan默认扫描的端口为80、443、8000、8080。可以使用--ports参数手工指定：
```


![](https://img-blog.csdnimg.cn/20191011155844689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**扫描结果**

扫描结果会保存在  `/~/aquatone/域名/urls.txt` 文件中，工具同时还会生成 `open_ports.txt` 文件，记录了IP和其对应开放的端口，用逗号分隔。

![](https://img-blog.csdnimg.cn/20191011155935257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 信息收集

该工具能利用之前的扫描结果，获取各种网络服务对应的HTTP响应Header和HTML Body，以及直接对相应网页进行网页截图。网页截图使用 [Nightmare.js](http://www.nightmarejs.org/ "Nightmare.js") Node.js库实现，如果系统中没有安装，该库会被自动安装。

类似aquatone-scan，aquatone-gather自动寻找对应域名的`hosts.json`和`open_ports.txt`文件，对每个域名的每个IP地址都会进行请求和截图，以保证全面覆盖。

```
aquatone-gather --domain baidu.com
```


**结果**

aquatone-gather完成扫描之后，会在  `/~/aquatone/域名/`  文件夹中创建以下文件夹，分别包含搜集到的不同信息文件：

*   `headers/`：包含每个网页HTTP Response Header 的 txt 文件
*   `html/`：包含每个网页的HTML Body的 txt 文件
*   `screenshots/`: 每个网页的PNG格式截图
*   `report/` 根据上述信息生成的HTML格式的简易报告

### 子域名劫持

当一家公司将一个子域名分配给第三方服务提供商，但在停用之后忘了移除DNS配置之时，攻击者可以通过注册相同的服务提供商并获取这一子域名所有权，从而形成劫持。aquatone-takeover 可以在 aquatone-discover 扫描获得的域名列表中寻找可能存在子域名劫持风险的域名：

aquatone-takeover能够检测25种不同服务提供商可能存在的子域名劫持，包括GitHub Pages, Heroku, Amazon S3, Desk和WPEngine。

```
aquatone-takeover --domain baidu.com
```


![](https://img-blog.csdnimg.cn/20191011160454267.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**结果**

工具会在 `/~/aquatone/域名/`  文件夹创建文件 `takeovers.json`，如果有子域名劫持风险的话，该文件中会有描述。

![](https://img-blog.csdnimg.cn/20191011160536310.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[子域名挖掘工具Aquatone安装以及遇到的坑](https://www.jianshu.com/p/418eedb9d9c8 "子域名挖掘工具Aquatone安装以及遇到的坑")

                  [开源子域名挖掘工具Aquatone：介绍和使用](https://www.jianshu.com/p/64f95a4fae5a "开源子域名挖掘工具Aquatone：介绍和使用")