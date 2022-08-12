WPScan是[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020) Linux默认自带的一款漏洞扫描工具，它采用Ruby编写，能够扫描WordPress网站中的多种安全漏洞，其中包括WordPress本身的漏洞、插件漏洞和主题漏洞。最新版本WPScan的数据库中包含超过18000种插件漏洞和2600种主题漏洞，并且支持最新版本的WordPress。值得注意的是，它不仅能够扫描类似robots.txt这样的敏感文件，而且还能够检测当前已启用的插件和其他功能。

[WordPress](https://so.csdn.net/so/search?q=WordPress&spm=1001.2101.3001.7020)是全球流行的博客网站，全球有上百万人使用它来搭建博客。他使用PHP脚本和Mysql数据库来搭建网站。

### WPScan的使用

由于Kali中自带了WPScan，所以怎么安装就不讲了，直接说说怎么使用。

```
wpscan -h  #查看参数以及意义
```


![](https://img-blog.csdnimg.cn/20181204154739898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

首次打开，我们可以先**更新其漏洞库**

```
wpscan -update  #升级漏洞库
```


 **扫描指定的WordPress站点**

```
wpscan -u http://192.168.10.44   #扫描WordPress站点，可以使用 -u 或者 --url 参数都可
```


它会扫描给定的WordPress站点的一些信息，并且列出可能是漏洞的地方。注意，这里wpscan判断是否有漏洞，是根据wordpress的版本判定的，只要你的版本低于存在漏洞的版本，那么，它就认为存在漏洞，所以，这个没有太多的参考性。

![](https://img-blog.csdnimg.cn/20181204160237184.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

> 注意：以下扫描的结果都是只看进度条之下的

**主题扫描**

```
wpscan -u http://192.168.10.44 --enumerate t  #主题扫描
```


一共扫描了数据库中的411个主题，发现了3个主题

**![](https://img-blog.csdnimg.cn/2018120416260069.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

**扫描主题中存在的漏洞**

```
wpscan -u http://192.168.10.44 --enumerate vt  #主题扫描
```


可以看到，这里没发现主题中存在漏洞的 

![](https://img-blog.csdnimg.cn/2018120416240995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**简单扫描WordPress插件**

```
wpscan -u http://192.168.10.44 --enumerate p  #插件扫描
```


一共扫描了数据库中的1494个插件，其中发现了1个插件，并且又漏洞 

![](https://img-blog.csdnimg.cn/20181204162938763.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**完整扫描WordPress插件** 

```
wpscan -u http://192.168.10.44 --enumerate ap  #扫描插件中的漏洞
```


可以看到，扫描乐乐77803个插件，发现了3个插件，其中一个有漏洞 

![](https://img-blog.csdnimg.cn/20181204162755771.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**枚举WordPress用户名**

```
wpscan -u http://192.168.10.44 --enumerate u  #枚举用户
```


![](https://img-blog.csdnimg.cn/20181204161928805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**暴力破解密码**

```
wpscan –u http://192.168.10.44 --wordlist /root/Desktop/dict.txt --username admin --threads 100 #指定用户名为admin，密码为 /root/Desktop/dict.txt 字典文件中的数据
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)