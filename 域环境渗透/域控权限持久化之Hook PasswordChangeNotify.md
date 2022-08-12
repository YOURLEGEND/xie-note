**目录**

[Hook PasswordChangeNotify](#t0 "Hook PasswordChangeNotify")

[使用Invoke-ReflectivePEInjection.ps1脚本获取修改密码后的明文密码](#t1 "使用Invoke-ReflectivePEInjection.ps1脚本获取修改密码后的明文密码")

[Hook PasswordChangeNotify的防御措施](#t2 "Hook PasswordChangeNotify的防御措施")

* * *

Hook PasswordChangeNotify
-------------------------

Hook PasswordChangeNotify的作用是当用户修改密码后在系统中进行同步。攻击者可以利用该功能获取用户修改密码时输入的密码明文。在修改密码时，用户输入新密码后，LSA会调用PasswordFileter来检查该密码是否符合复杂性要求。如果密码符合复杂性要求，LSA会调用PasswordChangeNotify，在系统中同步密码。

### 使用Invoke-ReflectivePEInjection.ps1脚本获取修改密码后的明文密码

使用Empire下的Invoke-ReflectivePEInjection.ps1，HookPasswordChange.dll需要我们自己准备，地址：[GitHub - clymb3r/Misc-Windows-Hacking: Miscellaneous projects related to attacking Windows.](https://github.com/clymb3r/Misc-Windows-Hacking "GitHub - clymb3r/Misc-Windows-Hacking: Miscellaneous projects related to attacking Windows.")。我们使用如下命令将 HookPasswordChange.dll注入内存。

```
Import-Module .\Invoke-ReflectivePEInjection.ps1      
Invoke-ReflectivePEInjection -PEPath HookPasswordChange.dll -procname lsass
```


![](https://img-blog.csdnimg.cn/2020021821231548.png)

只要修改了用户的密码，修改后的明文密码就会记录在 C:\\Windows\\Temp\\passwords.txt 文件中。

![](https://img-blog.csdnimg.cn/20200218212434600.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200218212501897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Hook PasswordChangeNotify的防御措施
------------------------------

使用Hook PasswordChangeNotify方法不需要重启系统、不会在系统磁盘中留下DLL文件、不需要修改注册表。如果PasswordChangeNotify被攻击者利用，网络管理员是很难检测到的。所以，在日常网络维护工作中，需要对PowerShell进行严格的监视，并启用约束语言模式，对Hook PasswordChangeNotify进行防御。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)