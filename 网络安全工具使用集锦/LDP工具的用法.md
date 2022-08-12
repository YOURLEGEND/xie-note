**目录**

[LDP的使用](#t0 "LDP的使用")

* * *

LDP是微软自带的一款域内信息查询工具，在域控的 [cmd](https://so.csdn.net/so/search?q=cmd&spm=1001.2101.3001.7020) 窗口执行 ldp 即可打开LDP。普通域成员主机默认是没有LDP的，可以自行上传ldp.exe工具上去

域控打开，直接cmd窗口执行ldp即可。

![](https://img-blog.csdnimg.cn/20200727141544365.png)

普通域主机打开LDP，自行上传ldp.exe

![](https://img-blog.csdnimg.cn/20200727141501160.png)

### LDP的使用

在没有加入域的主机上也可以使用LDP。

![](https://img-blog.csdnimg.cn/20200930162740774.png)

然后输入域控的ip和默认的389端口

![](https://img-blog.csdnimg.cn/20200930162752989.png)

然后再输入账号密码进行认证，这里可以是普通的域账号密码

![](https://img-blog.csdnimg.cn/20200930162851129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200930162940283.png)

![](https://img-blog.csdnimg.cn/2020093016295458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后再点击View——>Tree

![](https://img-blog.csdnimg.cn/20200930163021376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

输入一个BaseDN就行，这里的BaseDN可以参照如下，不限于如下

```
CN=Builtin,DC=xie,DC=com      
CN=Computers,DC=xie,DC=com      
OU=Domain Controllers,DC=xie,DC=com      
CN=ForeignSecurityPrincipals,DC=xie,DC=com      
CN=Managed Service Accounts,DC=xie,DC=com      
CN=Users,DC=xie,DC=com      
CN=Configuration,DC=xie,DC=com      
CN=Schema,CN=Configuration,DC=xie,DC=com      
DC=DomainDnsZones,DC=xie,DC=com      1
DC=ForestDnsZones,DC=xie,DC=com
```


![](https://img-blog.csdnimg.cn/20200930163054875.png)

![](https://img-blog.csdnimg.cn/20200930163112208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果这里的DN填的是DC=xie,DC=com的话，则会把整个ad的信息都给查询出来(比较慢)

![](https://img-blog.csdnimg.cn/20200930165554333.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想看某个子项的具体信息，右键——>Search

![](https://img-blog.csdnimg.cn/20200930163205951.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在弹出的框中Scope选择Subtre，点击Run。我们就可以在右下角查看到查找的信息了

![](https://img-blog.csdnimg.cn/20200930163251964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)