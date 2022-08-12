**目录**

[远程连接机器](#t0 "远程连接机器")

[LocalAccountTokenFilterPolicy](#t1 "LocalAccountTokenFilterPolicy")

[实验](#t2 "实验 ") 

* * *

远程连接机器
------

当我们获取到远程 Windows 系统的账号密码(明文或哈希)后，我们有哪些方式可以利用该账号密码进行[远程连接](https://so.csdn.net/so/search?q=%E8%BF%9C%E7%A8%8B%E8%BF%9E%E6%8E%A5&spm=1001.2101.3001.7020)呢？

*   RDP远程连接：普通账号即可，但是动静大，容易被发现，不到万不得已最好不要远程连接。
*   IPC$远程连接：对方主机需开启IPC$共享，连接成功后执行at、tasklist、schtasks等命令可获取权限。传送门：[IPC$共享和其他共享(C$、D$、Admin$)](https://blog.csdn.net/qq_36119192/article/details/83143354 "IPC$共享和其他共享(C$、D$、Admin$)")
*   [psexec工具使用浅析(admin$ or c$)](https://xie1997.blog.csdn.net/article/details/103614211 "psexec工具使用浅析(admin$ or c$)")
*   WMIC远程连接：对方主机需开启admin$共享，传送门：[wmiexec.exe(admin$)](https://xie1997.blog.csdn.net/article/details/102984779#wmiexec.exe "wmiexec.exe(admin$)")
*   smbexec远程连接：对方主机需开启C$共享，传送门：[smbexec的使用(c$共享)](https://blog.csdn.net/qq_36119192/article/details/104147068 "smbexec的使用(c$共享)")
*   WinRM远程连接：对方主机需开启WinRM服务，传送门：[WinRM远程管理工具的使用](https://xie1997.blog.csdn.net/article/details/105122945 "WinRM远程管理工具的使用")
*   DCOM远程连接：需管理员组内账号，传送门：[利用DCOM执行系统命令](https://blog.csdn.net/qq_36119192/article/details/104148658 "利用DCOM执行系统命令")
*   哈希传递攻击：[哈希传递攻击(Pass-the-Hash,PtH)](https://xie1997.blog.csdn.net/article/details/103941590 "哈希传递攻击(Pass-the-Hash,PtH)")

而在远程连接时，哪些类型的账号允许远程连接，哪些类型的账号不允许远程连接，如下：

**工作组环境：**

工作组环境连接，涉及到了 LocalAccountTokenFilterPolicy 值，后面会详细讲述这个参数。

*   Windows Vista之前的系统，比如Windows Server2003、Windows XP，只要使用本地管理员组内账号即可。
*   Windows Vista之后的系统，比如Windows Server2008、Windows 7，默认只能使用 administrator 账号进行连接。

**域环境：**

*   连接普通域成员主机，只需使用普通域账号连接即可。
*   连接域控，需域管理员组内账号连接。

LocalAccountTokenFilterPolicy
-----------------------------

而在远程连接时，涉及到 **LocalAccountTokenFilterPolicy** 值。

![](https://img-blog.csdnimg.cn/20200406101917122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
在 Windows Vista 以后的操作系统中，LocalAccountTokenFilterPolicy 的默认值为0，这种情况下内置账户 administrator 进行远程连接时会直接得到具有管理员凭证的令牌，而非 administrator 的本地管理员账户进行远程连接时，会得到一个删除了管理员凭证的令牌。而域用户不受此影响。也就是说，在工作组环境中，只有 administrator 账号才能建立具备管理员权限的远程连接，其他本地管理员账户建立需要管理员权限的远程连接时则会提示权限不足。而在域环境中，只要是域管理员都可以建立具备管理员权限的远程连接。所以，在域环境中，远程连接域控可以使用域管理员，远程连接普通域主机可以使用普通域成员。  
而如何修改 **LocalAccountTokenFilterPolicy** 的值呢？可以通过以下方法修改远程系统上 LocalAccountTokenFilterPolicy  的值，使得非 administrator 的本地管理员建立连接时也可以得到具有管理员凭证的令牌，即可正常通过各种方式远程执行命令。

**修改 LocalAccountTokenFilterPolicy 为1**

```
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
```


**恢复 LocalAccountTokenFilterPolicy 为0 (删除后需要重启 explorer.exe 才能使修改生效)**

```
reg delete HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system /v LocalAccountTokenFilterPolicy /f
```


### 实验 

Windows Server 2003：192.168.10.15

Windows Server 2008：192.168.10.20

本地管理员账号：

*   administrator
*   xie

普通账号：

*   hack

默认情况下，使用本地管理员账号即可连接Windows Server2003，而Windows Server2008则只能 administrator 账号才可以连接。

![](https://img-blog.csdnimg.cn/2020040523400838.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
![](https://img-blog.csdnimg.cn/20200405234340400.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改Windows Server2008的 **LocalAccountTokenFilterPolicy** 为1后，使用本地管理员账号 xie 也可远程连接了。

![](https://img-blog.csdnimg.cn/20200405234519420.png)

![](https://img-blog.csdnimg.cn/20200405234438548.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11176 人正在系统学习中