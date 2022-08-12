日志

域日志位置

```
#服务器登录日志 登录事件ID 4624，可判别出登录域控制器的机器      
C:\Windows\System32\winevt\Logs\Security.evtx       
#远程登录日志      
c:\windows\system32\winevt\logs\*.remote*
```


主机[远程登录](https://so.csdn.net/so/search?q=%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95&spm=1001.2101.3001.7020)日志

```
HKCU\Software\Microsoft\Terminal Server Client\Servers      
HKCU\Software\Microsoft\Terminal Server Client\Default
```


系统开机自启目录

```
C:\Users\用户名\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\      
C:\Users\Default\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\
```


Firefox浏览器密码、历史记录

```
#将下述三个文件导入到对应版本firefox的文件夹中，即可读取密码和历史记录      
C:\Users\用户名\AppData\Roaming\Mozilla\Firefox\Profiles\xxx.default-release\logins.json      
C:\Users\用户名\AppData\Roaming\Mozilla\Firefox\Profiles\xxx.default-release\key3.db      
C:\Users\用户名\AppData\Roaming\Mozilla\Firefox\Profiles\xxx.default-release\place.sqlite
```


最近使用的文件

```
#该目录下的最近使用文件是快捷方式      
C:\Users\用户名\AppData\Roaming\Microsoft\Windows\Recent\
```


 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)