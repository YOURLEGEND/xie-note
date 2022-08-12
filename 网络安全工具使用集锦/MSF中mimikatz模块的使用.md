**目录**

[mimikatz模块的加载](#t0 "mimikatz模块的加载")

[mimikatz模块的使用](#t1 "mimikatz模块的使用")

[mimikatz\_command模块的用法](#t2 "mimikatz_command模块的用法")

* * *

mimikatz模块的加载
-------------

MSF中的mimikatz模块，可以列举出系统中的各种凭据，以及执行一些mimikatz相关的命令。目前，该模块已经更新为功能更全的kiwi模块，传送门：[MSF中kiwi模块的使用](https://xie1997.blog.csdn.net/article/details/104802921 "MSF中kiwi模块的使用")

使用 mimikatz 模块需要System权限，所以我们在使用该模块之前需要将当前MSF中的shell提升为system。提到system有两个方法，一是当前的权限是administrator用户，二是利用其它手段先提权到administrator用户。然后administrator用户可以直接getsystem到system权限。

**提权到system权限**

![](https://img-blog.csdnimg.cn/20200311185018584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**进程迁移**

kiwi模块同时支持32位和64位的系统，但是该模块默认是加载32位的系统，所以如果目标主机是64位系统的话，直接默认加载该模块会导致很多功能无法使用。所以如果目标系统是64位的，则必须先查看系统进程列表，然后将meterpreter进程迁移到一个64位程序的进程中，才能加载kiwi并且查看系统明文。如果目标系统是32位的，则没有这个限制。

![](https://img-blog.csdnimg.cn/20200311185256640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200311185328271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200311185358666.png)

mimikatz模块的使用
-------------

```
加载kiwi模块      
load mimikatz      
查看kiwi模块的使用      
help mimikatz
```


![](https://img-blog.csdnimg.cn/20191110213626332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到mimikatz下有七个命令：

*   kerberos：kerberos相关的模块
*   livessp：尝试检索livessp凭据
*   mimikatz\_command：运行一个定制的命令
*   msv：msv凭证相关的模块，列出目标主机的用户密码哈希
*   ssp：ssp凭证相关的模块
*   tspkg：tspkg凭证相关的模块
*   wdigest：wdigest凭证相关的模块

### mimikatz\_command模块的用法

mimikatz\_command 模块可以让我们使用mimikatz的全部功能。

```
mimikatz_command -f mimikatz的命令
```


例如，使用以下命令查看系统中的明文密码

```
mimikatz_command -f sekurlsa::searchPasswords
```


![](https://img-blog.csdnimg.cn/20191111114428923.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)