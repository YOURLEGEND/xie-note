**目录**

[共享文件的设置](#t0 "共享文件的设置")

[默认共享](#t1 "默认共享")

[关闭默认共享](#t2 "关闭默认共享")

[关闭共享服务](#t3 "关闭共享服务 ") 

[共享文件夹权限](#t4 "共享文件夹权限")

[文件服务器资源管理器的搭建](#t5 "文件服务器资源管理器的搭建")

* * *

**文件共享**是指主动地在网络上共享自己的计算机文件。一般文件共享使用P2P模式，文件本身存在用户本人的个人电脑上。大多数参加文件共享的人也同时下载其他用户提供的共享文件。有时这两个行动是连在一起的。在很早期的Windows中(Windows2000以下)，文件共享服务是利用TCP的139端口实现的，服务名是SMB。后来，微软又把SMB改名为CIFS，并且使用的是TCP的445端口

相关文章：[Linux文件共享服务之Samba](https://blog.csdn.net/qq_36119192/article/details/82733374 "Linux文件共享服务之Samba")

哪些用户能够共享文件夹

*   普通用户是没有权限共享文件夹
*   Administrators 组的账号可以设置共享文件夹
*   Power user 组的账号也可以设置共享文件夹

### 共享文件的设置

**共享文件服务端的设置**

1、文件共享服务器需要打开网卡上的 Microsoft 网络的文件和打印共享功能(这个功能默认是开启的)

![](https://img-blog.csdn.net/20181017074046115?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2、然后打开高级共享设置，启用网络发现和启用文件、打印机共享(实际上就是打开了TCP的445端口)

![](https://img-blog.csdn.net/20181017075520325?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3、选择要共享的文件，右键-->属性-->共享-->然后选择一个用户名去共享，然后就会有一个访问共享文件夹的路径

![](https://img-blog.csdn.net/2018101708103021?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**隐藏共享文件的设置**

共享文件的时候选择高级共享，然后共享文件名后加一个 $，这样客户端默认看不到这个共享的文件夹，除非访问的时候加上共享文件名和$：  \\\\WIN-7\\file$

![](https://img-blog.csdn.net/20181017084303241?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdnimg.cn/20190904004626833.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**共享文件客户端的访问**

方法一：客户端运行窗口输入：\\\\WIN-7  或者  \\\\ip地址，然后用服务器的用户名和密码登录即可访问共享文件夹(看不到隐藏的共享文件夹)。

![](https://img-blog.csdn.net/20181017081251959?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方法二：客户端在文件夹窗口输入： \\\\WIN-7 或者  \\\\ip地址   ，然后用服务器的用户名和密码登录即可访问共享文件夹(看不到隐藏的共享文件夹)。

![](https://img-blog.csdn.net/20181017083526458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方法三：打开文件夹窗口，点击我的电脑-->计算机-->映射网络驱动器，然后输入： \\\\WIN-7\\file ，然后用服务器的用户名和密码登录即可访问共享文件夹。 （这种方法连接的时候要输入具体共享的文件名）

![](https://img-blog.csdn.net/20181017083657215?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

客户端使用任意端口访问文件服务器的TCP 445 端口的共享资源

![](https://img-blog.csdn.net/2018101708060062?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方法四：在命令行中输入  net  use \\\\ip\\c$  /u:administrator root 。然后在文件夹中打开即可

![](https://img-blog.csdnimg.cn/20190904004026560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 默认共享

默认共享是为管理员管理服务器的方便而设，其权限不能更改，只要知道服务器的管理员账号和密码，不管其是否明确共享了文件夹，都可以访问其所有的分区。 查看默认共享：net share 

![](https://img-blog.csdn.net/20181017103624467?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如图，那么黑客只要知道了服务器 Administrator 用户的密码就可以访问其默认共享文件夹了

![](https://img-blog.csdn.net/20181017105659618?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

黑客还可以通过命令行把你的默认共享的盘符挂载到本地：net use k:  \\\\10.96.10.59\\c$    把10.96.10.59计算机上的C盘挂载到本地的k盘，这里的认证用户只能是 administrator  
![](https://img-blog.csdn.net/2018101810292194?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后你可以看到本地多了一个k盘，这正是远程主机上的C盘

![](https://img-blog.csdn.net/20181018103027479?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

net use 查看建立的文件共享

![](https://img-blog.csdnimg.cn/2019090400213270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

要删除此共享的话：net  use k: /del

![](https://img-blog.csdnimg.cn/20190904001335517.png)

###  关闭默认共享

**net share命令关闭默认共享**

 很明显，这是非常危险的。所以我们一般都会关闭默认共享

*   net share ipc$    /delete              关闭ipc默认共享
*   net share admin$  /delete          关闭admin账户目录共享
*   net share  c$ /delete                  关闭c盘默认共享
*   net share  e$ /delete                关闭e盘默认共享

用net share命令来删除，但是机器重启后共享会自动出现。所以我们可以将命令保存为.bat批处理文件，然后把这个批处理文件拖到“程序”→“启动”项，这样每次开机就会运行它，也就是通过net命令关闭共享。

**注册表关闭默认共享**

禁止C$、D$管理共享(如果没有则新建)： 

*   HKEY\_LOCAL\_MACHINE/SYSTEM/CurrentControlSet/Services/lanmanserver/parameters 
*   Name：AutoShareServer                
*   Type：DWORD              
*   Value：0    

![](https://img-blog.csdn.net/20181018195915375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

禁止ADMIN$缺省共享： 

*   HKEY\_LOCAL\_MACHINE/SYSTEM/CurrentControlSet/Services/lanmanserver/parameters 
*   Name：AutoShareWks   
*   Type：REG\_DWORD             
*   Value：0x0

限制IPC$缺省共享：

*   HKEY\_LOCAL\_MACHINE/SYSTEM/CurrentControlSet/Control/Lsa 
*   Name：restrictanonymous 
*   Type：REG\_DWORD 
*   Value：0x0(缺省)          0x1 匿名用户无法列举本机用户列表             0x2 匿名用户无法连接本机IPC$共享        说明:不建议使用2，否则可能会造成你的一些服务无法启动，如SQL Server。

![](https://img-blog.csdn.net/20181018200115949?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 关闭共享服务 

更彻底点的，文件打印共享和共享文件，很多人用不到，可以直接停掉该服务。

1.  直接关闭文件网卡上的 Microsoft 网络的文件和打印共享功能
2.  关闭服务管理里面的Server服务

![](https://img-blog.csdn.net/20181017153657339?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **共享文件夹权限**

用户必须拥有适当的共享权限才可以访问共享文件夹。共享权限的种类与其所具有的访问能力如下 

![](https://img-blog.csdn.net/2018101708144633?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**共享权限和NTFS权限的联系和区别**

*   共享权限是基于文件夹的；NTFS 权限是基于文件的
*   共享权限只有当用户通过网络访问共享文件夹时才起作用；NTFS 权限无论用户是通过网络还是本地登录使用文件都会起作用 
*   共享权限与文件操作系统无关，只要设置共享就能够应用共享权限；NTFS 权限必须是NTFS 文件系统，否则不起作用 
*   共享权限只有几种：读者，参与者，所有者；NTFS 权限有许多种，如读，写，执行，修改，完全控制等，我们可以进行非常细致的设置

**共享权限和 NTFS  权限的特点**

*   不管是共享的权限还是 NTFS 权限都有累加性。
*   不管是共享权限还是 NTFS 权限都遵循“拒绝”权限超越其他权限。
*   当一个账户通过网络访问一个共享文件夹，而这个文件夹又在一个 NTFS 分区上，那么用户最终的权限是它对该文件夹的共享权限与 NTFS 权限中最为严格的权限

### 文件服务器资源管理器的搭建

使用文件服务器[资源管理器](https://so.csdn.net/so/search?q=%E8%B5%84%E6%BA%90%E7%AE%A1%E7%90%86%E5%99%A8&spm=1001.2101.3001.7020)高级工具功能

*   文件夹和卷设置配额
*   主动屏蔽文件，并生成全面的存储报告

角色，添加角色，选择文件服务，然后再选择文件服务器资源管理器，接着选择磁盘安装即可

![](https://img-blog.csdn.net/20181017161225349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181017161304362?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

配额管理：配额管理在文件服务器资源管理器内是以磁盘或文件夹为单位，不论用户是谁，设置配的文件夹总量不允许超过目录配额的限制。

文件屏蔽：可以限制文件夹存放的文件类型 可以指定某个文件夹的能够存放的文件类型，是基于文件的扩展名来控制的。 通过创建文件屏蔽来控制用户可以保存的文件类型以及在用户尝试保存未经授权的文件时生成通知。 定义可以应用于新的卷或文件夹以及可以在整个组织中使用的文件屏蔽模板。 创建增强文件屏蔽规则灵活性的文件屏蔽例外。

![](https://img-blog.csdn.net/20181017161506136?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg) 

相关文章：[135、137、138、139和445端口](https://blog.csdn.net/qq_36119192/article/details/83279527 "135、137、138、139和445端口")

                  [IPC$共享和其他共享(C$、D$、Admin$)](https://blog.csdn.net/qq_36119192/article/details/83143354 "IPC$共享和其他共享(C$、D$、Admin$)")