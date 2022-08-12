**目录**

[获取用户密码](#t0 "获取用户密码")

[抓取自动登录的密码](#t1 "抓取自动登录的密码")

[导出密码哈希](#t2 "导出密码哈希")

[上传mimikatz程序](#t3 "上传mimikatz程序")

[加载kiwi模块](#t4 "加载kiwi模块")

[加载mimikatz模块](#t5 "加载mimikatz模块")

* * *

获取用户密码
------

![](https://img-blog.csdnimg.cn/20191215140019617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 抓取自动登录的密码

**1：**很多用户习惯将计算机设置自动登录，可以使用  `run windows/gather/credentials/windows_autologin`  抓取自动登录的用户名和密码

![](https://img-blog.csdnimg.cn/20190613152001294.png)

### 导出密码哈希

**2：**hashdump 模块可以从SAM数据库中导出本地用户账号，执行：run hashdump ，该命令的使用需要**系统权限**

**![](https://img-blog.csdnimg.cn/20181201225502183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

用户[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)数据的输出格式为：

```
用户名：SID：LM哈希：NTLM哈希:::
```


所以我们得到了三个用户账号，分别为Administrator、Guest和小谢。

其中Administrator和Guest的[LM](https://en.wikipedia.org/wiki/LAN_Manager "LM")哈希（aad3b435b51404eeaad3b435b51404ee）和[NTLM](https://en.wikipedia.org/wiki/NT_LAN_Manager "NTLM")哈希（31d6cfe0d16ae931b73c59d7e0c089c0）对应的是一个空密码。所以，只有小谢的哈希有效。

接下来要处理的就是用户 小谢 的密码（ a86d277d2bcd8c8184b01ac21b6985f6 ）了。我们可以使用类似John这样的工具来破解密码：[John破解Windows系统密码](https://xie1997.blog.csdn.net/article/details/83373895#%E7%A0%B4%E8%A7%A3Windows%E7%B3%BB%E7%BB%9F%E5%AF%86%E7%A0%81 "John破解Windows系统密码")，或者使用在线网站解密：[md5在线解密破解,md5解密加密](https://www.cmd5.com/default.aspx "md5在线解密破解,md5解密加密")

![](https://img-blog.csdnimg.cn/20200927210242966.png)

还可以使用命令：run windows/gather/smart\_hashdump  ，该命令的使用需要**系统权限。**该功能更强大，如果当前用户是域管理员用户，则可以导出域内所有用户的[hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020)

![](https://img-blog.csdnimg.cn/20200927000439328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 上传mimikatz程序

**3：**我们还可以通过上传mimikatz程序，然后执行mimikatz程序来获取明文密码。

执行mimikatz必须**System权限**。

我们先getsystem提权至系统权限，然后执行  execute  -i  -f  mimikatz.exe ，进入mimikatz的交互界面。然后执行：

*   privilege::debug
*   sekurlsa::logonpasswords

![](https://img-blog.csdnimg.cn/20190613144629360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 加载kiwi模块

**4：**加载kiwi模块，该模块的使用需要**System权限**。关于该模块的用法：[MSF中kiwi模块的使用](https://xie1997.blog.csdn.net/article/details/104802921 "MSF中kiwi模块的使用")

### 加载mimikatz模块

**5：**或者运行MSF里面自带的 mimikatz 模块 ，该模块的使用需要**System权限**。传送门：[MSF中mimikatz模块的使用](https://blog.csdn.net/qq_36119192/article/details/103002480 "MSF中mimikatz模块的使用")。目前该模块已经被kiwi模块代替了。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)