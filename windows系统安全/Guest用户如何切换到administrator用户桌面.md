先以guest用户[RDP](https://so.csdn.net/so/search?q=RDP&spm=1001.2101.3001.7020)登录，管理员权限打开 regedit

![](https://img-blog.csdnimg.cn/20210420214006201.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

HKEY\_LOCAL\_MACHINE——>SAM——>SAM——>权限

![](https://img-blog.csdnimg.cn/20210420214053183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210420214237693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后注册表查看——>刷新

![](https://img-blog.csdnimg.cn/20210420214305512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

HKEY\_LOCAL\_MACHINE——>SAM——>SAM——>Domains——>Account——>Users——>000001F4 ，名称F ——>修改[二进制](https://so.csdn.net/so/search?q=%E4%BA%8C%E8%BF%9B%E5%88%B6&spm=1001.2101.3001.7020)数据(B)

![](https://img-blog.csdnimg.cn/20210420214527522.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

右键全选，然后复制

![](https://img-blog.csdnimg.cn/20210420214622408.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后查看HKEY\_LOCAL\_MACHINE——>SAM——>SAM——>Domains——>Account——>Users——>Names——> Guest，查看类型是0x1f5

![](https://img-blog.csdnimg.cn/20210420214705149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后查看HKEY\_LOCAL\_MACHINE——>SAM——>SAM——>Domains——>Account——>Users——>000001F5，名称F ——>修改二进制数据(B)

![](https://img-blog.csdnimg.cn/20210420214828673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后全选，粘贴

![](https://img-blog.csdnimg.cn/20210420214911622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后再将SAM文件权限改回即可。

然后注销，以guest用户身份重新登录即可进入administrator用户桌面.

![](https://img-blog.csdnimg.cn/20210420215218636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[后渗透之远程桌面](https://mp.weixin.qq.com/s/T_P5hRAI4rCHX6hDHGIR8w "后渗透之远程桌面")