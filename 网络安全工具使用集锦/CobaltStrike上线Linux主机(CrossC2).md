在红蓝对抗中，我们经常会碰到需要对Linux主机进行长期远控的情况。对于Windows主机，我们可以使用CobaltStrike，那么自然我们会想问，CobaltStrike能否对Linux主机进行长期远控呢？

在上一篇文章中我提到了，CobaltStrike自身上线Linux主机的情况，需要知道对方Linux主机的账号密码或[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)秘钥，并且还需要获取一台其他机器权限作为中继。传送门：[CobaltStrike SSH远程登录](https://xie1997.blog.csdn.net/article/details/89489609#%E5%93%88%E5%B8%8C%E4%BC%A0%E9%80%92%E6%94%BB%E5%87%BB%E6%88%96SSH%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95 "CobaltStrike SSH远程登录")   

本文中将讲解如何通过在Linux上执行[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)反弹一个CobaltStrike类型的shell，这得依赖于一个CobaltStrike的插件CrossC2。

CrossC2插件项目地址：[GitHub - gloxec/CrossC2: generate CobaltStrike's cross-platform payload](https://github.com/gloxec/CrossC2 "GitHub - gloxec/CrossC2: generate CobaltStrike's cross-platform payload")

注：CrossC2项目官方声明只支持CobaltStrike3.14版本，本人亲测CobaltStrike4.0也可正常上线，但是无法执行其他操作。

实现CobaltStrike上线Linux主机
-----------------------

1：首先，访问CrossC2项目地址，下载该项目。

将项目src目录下 genCrossC2.Linux 文件上传到CobaltStrike服务端目录下。

![](https://img-blog.csdnimg.cn/20200530213900436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200530213928778.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

2：将 CobaltStrike 服务端的.cobaltstrike.beacon\_keys下载到 CobaltStrike 客户端目录下。

![](https://img-blog.csdnimg.cn/20200530212044840.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
3：由于 Cross C2目前只支持HTTPS Beacon，所以在Listenrs中选择HTTPS进行监听，服务端开启监听 windows/beacon\_https/reverse\_https 类型的beacon

![](https://img-blog.csdnimg.cn/20200530212453228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

4：生成木马

```
./genCrossC2.Linux 监听的IP 监听的端口 null null Linux x64 test
```


![](https://img-blog.csdnimg.cn/20200530212704690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

5：执行木马上线

![](https://img-blog.csdnimg.cn/2020053021380315.png)  
![](https://img-blog.csdnimg.cn/20200530213056277.png)

![](https://img-blog.csdnimg.cn/20200530213142104.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)