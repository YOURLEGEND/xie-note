**目录**

[注册表](#t0 "注册表")

[注册表结构](#t1 "注册表结构")

[reg](#t2 "reg")

[增](#t3 "增 ") 

[删](#t4 "删")

[改](#t5 "改")

[查](#t6 "查")

* * *

注册表
===

Windows[注册表](https://so.csdn.net/so/search?q=%E6%B3%A8%E5%86%8C%E8%A1%A8&spm=1001.2101.3001.7020)就相当于Windows系统的数据库，系统和软件的配置信息放在注册表里面。如果注册表出现了问题，可能导致系统崩溃。我们平时是使用 regedit.exe 命令来使用图形化界面管理注册表的。而在很多时候，使用图形化界面管理注册表很麻烦。所以今天介绍一种使用纯命令行的工具(reg.exe)来管理注册表。使用 reg.exe 可以对注册表进行添加、删除、修改、查看等操作。

注册表结构
-----

注册表有四个关键术语：键、值、值类型、数据

值的类型有六种，分别为：

*   REG\_BINARY
*   REG\_DWORD
*   REG\_EXPAND\_SZ
*   REG\_MULTI\_SZ
*   REG\_QWORD
*   REG\_SZ

![](https://img-blog.csdnimg.cn/20210104154631430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

reg
---

reg(控制台注册表编辑器)，默认文件路径为：C:\\Windows\\System32\\reg.exe 。执行reg /? 可以查看reg的帮助。如果使用reg对注册表进行增删改查的话，需要管理员权限。

```
QUERY      
ADD      
DELETE      
COPY      
SAVE      
LOAD      
UNLOAD      
RESTORE      
COMPARE      1
EXPORT      1
IMPORT      1
FLAGS
```


![](https://img-blog.csdnimg.cn/20210104151419508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用reg修改注册表，有几个简写

```
HKCR    HKEY_CLASSES_ROOT      
HKCU 	HKEY_CURRENT_USER      
HKLM    HKEY_LOCAL_MACHINE       
HKU     HKEY_USERS      
HKCC    HKEY_CURRENT_CONFIG
```


### 增 

使用如下命令在HKEY\_CURRENT\_USER下新建一个test键，值为hello，值的类型为 REG\_SZ 。

/v 后面跟需要创建的值的名称，/t 后面是值的类型，/d 后面是这个值的数据，/f 是强制不提示

```
reg add hkcu\test /v hello /t REG_SZ /d "this is test!" /f
```


![](https://img-blog.csdnimg.cn/20210104153843806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 删

```
删除HKEY_CURRENT_USER下的test键的hello值      
reg delete hkcu\test /v hello /f       
删除HKEY_CURRENT_USER下的test键      
reg delete hkcu\test /f
```


![](https://img-blog.csdnimg.cn/20210104155455178.png)

### 改

修改 HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\ 的 Terminal Server 键 的 fDenyTSConnections 值的数据为 0 (0x00000000)。

```
reg add HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f
```


![](https://img-blog.csdnimg.cn/20210104154951747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 查

```
reg query HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections
```


![](https://img-blog.csdnimg.cn/20210104155145603.png)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)