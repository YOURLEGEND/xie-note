**目录**

[Set](#t0 "Set")

[钓鱼攻击](#t1 "钓鱼攻击")

[网站克隆](#t2 "网站克隆 ") 

* * *

Set
---

Set(Social engineering tookit)是一款社会工程学工具，该工具用的最多的就是用来制作钓鱼网站。

[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020)中自带了该工具。

### 钓鱼攻击

在应用程序中的漏洞利用工具集里面。

![](https://img-blog.csdnimg.cn/20181121215833769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开之后，我们看到了如下的界面。

![](https://img-blog.csdnimg.cn/20181121215935134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
1) 社会工程学攻击      
2) 快速追踪测试      
3) 第三方模块      
4) 升级软件      
5) 升级配置      
6) 帮助      
99)退出
```


 我们利用它来制作钓鱼网站，选择 1 社会工程学攻击 ，然后又跳出了下面的选择

![](https://img-blog.csdnimg.cn/2018112122013240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
1)鱼叉式网络钓鱼攻击      
2)网页攻击      
3)传染媒介式（俗称木马）      
4)建立payloaad和listener      
5)邮件群发攻击（夹杂木马啊payload的玩意发给你）      
6)Arduino基础攻击      
7)无线接入点攻击      
8)二维码攻击      
9)Powershell攻击      1
10)第三反模块      1
99)返回上级
```


我们选择 2 网页攻击 ，然后又跳出了下面的选择

![](https://img-blog.csdnimg.cn/20181121220552275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
1)java applet攻击（网页弹窗那种）      
2)Metasploit 浏览器漏洞攻击      
3)钓鱼网站攻击      
4)标签钓鱼攻击      
5)网站jacking攻击（这个真心不明白，好像也和java的攻击方式有些相同）      
6)多种网站攻击方式      
7)全屏幕攻击（不明所以的玩意，只能够对谷歌邮箱和脸书用）      
8)HTA攻击方式       1
99)返回主按钮
```


 我们选择 3 钓鱼网站攻击，然后跳出来下面的选择

![](https://img-blog.csdnimg.cn/20181121220813968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
1）网站模版      
2）设置克隆网站      
3）导入自己的网站       
99）返回到上一级
```


如果我们选择1网站模板的话，会提示我们输入POST返回的地址，我们输入自己主机的地址，然后会叫我们选择网站的模板，我们选择2 google

![](https://img-blog.csdnimg.cn/20181121225215397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们访问该主机，和谷歌的页面一模一样，如果我们输入用户名和密码登录的话， 

![](https://img-blog.csdnimg.cn/20181121225339528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们就会收到用户输入的用户名和密码，这样就完成了一次钓鱼网站攻击了

![](https://img-blog.csdnimg.cn/20181121225454839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 网站克隆 

网站模板只有几个网站的模板，不能满足我们的需求。于是，在上一步我们可以选择克隆网站。这个克隆网站的要求就是最好是静态页面而且有POST返回的登录界面，现在的百度、QQ、163对于都克隆没用了。

![](https://img-blog.csdnimg.cn/20181121225928483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问网站，和百度页面一模一样，不过这并不能获取到用户输入的用户名和密码，只是外观一模一样

![](https://img-blog.csdnimg.cn/20181121230129277.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)