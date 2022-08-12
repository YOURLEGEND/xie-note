**目录**

[SSP&SSPI](#t0 "SSP&SSPI")

[Windows认证方式](#t1 "Windows认证方式")

[Windows本地认证](#t2 "Windows本地认证")

[NTLM认证](#t3 "NTLM认证")

[NTLM v1和NTLM v2的区别](#t4 "NTLM v1和NTLM v2的区别")

[LmCompatibilityLevel](#t5 "LmCompatibilityLevel")

[NTLM认证方式(工作组环境中)](#t6 "NTLM认证方式(工作组环境中)")

[NTLM认证方式(域环境中)](#t7 "NTLM认证方式(域环境中)")

[认证的应用](#t8 "认证的应用")

* * *

SSP&SSPI
========

在这之前，我们先了解几个基本的概念

**SSPI** (Security Service Provider Interface 或 Security Support Provider Interface) 安全服务提供接口，这是 Windows 定义的一套接口，该接口定义了与安全有关的功能函数，包含但不限于：

*   身份验证机制
*   为其他协议提供的 Session Security 机制。Session Security 指的是会话安全，即为通讯提供数据完整性校验以及数据的加、解密功能

该接口定义了与安全有关的功能函数，用来获取验证、信息完整性、信息隐私等安全功能，该接口只是定义了一套接口函数，但是并没有实现具体的内容。

**SSP** (Security Service Provider) 安全服务提供，SSPI 的实现者，微软自己实现了如下的 SSP，用于提供安全功能，包含但不限于：

*   NTLM SSP
*   Kerberos
*   Digest SSP
*   Negotiate SSP
*   Cred SSP
*   Schannel SSP
*   Negotiate Extensions SSP
*   PKU2U SSP

因为 SSPI 中定义了与 Session Security 有关的 API。所以，基本上层应用利用任何 SSP 与远程的服务进行了身份验证后，此 SSP 都会为本次连接生成一个随机 key。这个 key 往往被称为 Session Key。上层应用在经过身份验证后，可以选择性地使用这个 Key 对之后发往服务端或接收自服务端的数据进行签名或加密。

在系统层面，SSP就是一个dll，来实现身份验证等安全功能。不同的 SSP，实现的身份验证机制是不一样的。比如 NTLM SSP 实现的就是一种 Challenge based 挑战响应身份验证机制。而 Kerberos SSP 实现的就是基于 Ticket 的身份验证机制。我们可以编写自己的 SSP，然后注册到操作系统中，让操作系统支持更多的自定义的身份验证方法。

![](https://img-blog.csdnimg.cn/img_convert/402d2ba6f20ae11de236969a5e8dde36.gif)

Windows认证方式
===========

**认证是什么？**

认证就是承认和证明的意思。 就是你能证明你的身份。 比如你要通过网络访问一个受保护的资源，服务器需要认证你的身份。 你可以声称你是系统管理员， 但是你怎么证明你就是系统管理员呢？方法很多，最简单直接的方法就是证明你知道系统管理员的密码。    

认证的问题转化为： “你怎么证明你知道你所声称的用户的密码？” 这个问题了。 

一个简单暴力的证明方法是，让你直接提供所称用户的密码给服务器，然后服务器去数据库里面对比，看你提供的密码对不对。如果对，认证通过，否则失败。 常见的所谓 **Windows Forms** 认证，或者叫做 **Windows basic** 认证就是这种方式， 简单直接。 但是密码需要在网络上传输，安全问题就不说了，你懂的。

 怎样在不直接提供密码的情况下，间接证明你知道密码呢？ 

看起来很神奇，不是吗。  比如对面有两个人在互相说话（通信），说的都是明文，每一句你都能听懂。他们并没有说自己的密码就相互认证身份了，你听了半天，却不知道密码是什么。  更神奇的是， 他们认证之后，再说的话你就更听不懂了（Session Security，会话安全，如果他们协商会话安全之后，后续的通信都是安全加密的。）

Windows系统有几种认证体系：**NTLM** 和 **Kerberos**。**NTLM(New Technology LAN Manager)**[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)算法，Windows新的密码管理方式。在Windows2000以后，Windows机器都用NTLM算法在本地保存用户的密码，密码的NTLM哈希保存在 %SystemRoot%\\System32\\config\\SAM 文件中。 在渗透测试中，通常可从 Windows系统中的SAM文件 和 域控的 NTDS.dit 文件中获得所有用户的[Hash](https://so.csdn.net/so/search?q=Hash&spm=1001.2101.3001.7020)。也可以通过 Mimikatz 读取 lsass.exe 进程获得已登录用户的NTLM hash和明文值 。NTLM认证则是利用NTLM哈希进行的认证，主要有 **本地认证** 和 **网络认证** 两种方式。NTLM认证是Windows早期的认证方式，因向后兼容性而保留了下来。NTLM适用范围非常广，既可用于域内的认证服务， 也可用于没有域的工作组环境。

本地的SAM文件：

![](https://img-blog.csdnimg.cn/20190909232645147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

域控的ntds.dit数据库文件：

![](https://img-blog.csdnimg.cn/20190909232658867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Kerberos认证**用于域环境中，它是一种基于票据（Ticket）的认证方式。他的整个认证过程涉及到三方：客户端、服务端和KDC（Key Distribution Center）。在Windows域环境中，由DC(域控)来作为KDC。

**在域环境中，默认先使用kerberos进行认证，当使用kerberos认证出现错误时使用NTLM认证，包括不限于如下情况：**

*   客户端使用 IP 地址向服务器认证 
*   客户端向另一个有 Legacy NTLM 信任而非传递性林间信任的活动目录域林的服务器认证 
*   防火墙策略限制了 Kerberos 使用的端口（通常为88端口）
*   需要认证的是 Windows NT 系统

现在Windows主要用 **NTLMv2(工作组环境)** 和 **Kerberos(域环境)** 验证体系。NTLM只支持Windows，而Kerberos不仅支持Windows，还支持Linux。

不管是 NTLM 还是 Kerberos 认证，他都是底层的一种认证方式和上层应用无关。NTLM 消息并不和任何传输协议绑定，它的认证消息理论上可以通过任何方式传递。比如 NTLM 认证方式应用有 SMB NTLM 认证和 HTTP NTLM 认证等等

Windows本地认证
-----------

本地登录时，用户的密码存储在 %SystemRoot%\\system32\\config\\SAM 这个文件里。当用户输入密码进行本地认证的过程中，所有的操作都是在本地进行的。他其实就是将用户输入的密码转换为NTLM Hash，然后与SAM中的NTLM Hash进行比较。当用户注销、重启、锁屏后，操作系统会让winlogon显示登录界面，也就是输入框。当winlogon.exe接收输入后，将密码交给lsass进程，这个进程中会存一份明文密码，将明文密码加密成NTLM Hash，对SAM数据库比较认证。(winlogon.exe即Windows Logon Process，是Windows NT用户登陆程序，用于管理用户登录和退出。lsass进程用于微软Windows系统的安全机制。它用于本地安全和登陆策略。)

![](https://img-blog.csdnimg.cn/20190909232850146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

比如当用户输入密码123456后，那么操作系统会将123456转换为十六进制，经过Unicode转换后，再调用MD4加密算法加密，这个加密结果的十六进制就是NTLM Hash

```
123456 -> hex(16进制编码) = 313233343536      
313233343536 -> Unicode = 610064006d0069006e00      
610064006d0069006e00 -> MD4 = 209c6174da490caeb422f3fa5a7ae634
```


相关文章：[Windows中对用户密码的处理](https://blog.csdn.net/qq_36119192/article/details/83057161#Windows%E4%B8%AD%E5%AF%B9%E7%94%A8%E6%88%B7%E5%AF%86%E7%A0%81%E7%9A%84%E5%A4%84%E7%90%86 "Windows中对用户密码的处理")

NTLM认证
------

NTLM认证是一种 Challenge/Response 挑战响应验证机制，由三种消息组成：

*   type 1(协商，Negotiate)
*   type 2(挑战，Challenge)
*   type 3(响应，Response)

### NTLM v1和NTLM v2的区别

NTLM 有 NTLMv1 、NTLMv2 、NTLMsession v2 三个版本，目前使用最多的是NTLMv2版本。

NTLMv1 与NTLMv2 最显著的区别就是 Challenge `与加密算法`不同，共同之处就是都是使用的 `NTLM Hash` 。

**Challenge**：

*   `NTLM v1`： `8字节`
*   `NTLM v2`：`16字节`

**Net NTLM Hash**：

*   `NTLM v1`：DES加密算法
*   `NTLM v2`： HMAC-MD5加密算法

**NTLM v1**

v1是将 16字节的NTLM hash空填充为21个字节，然后分成三组，每组7比特，作为3DES加密算法的三组密钥，加密Server发来的Challenge。 将这三个密文值连接起来得到response。

**NTLM v2**

而v2是的加密算法是如下：

1.  ​将Unicode后的大写用户名与Unicode后的身份验证目标（在Type 3消息的"TargetName"字段中指定的域或服务器名称）拼在一起。请注意，用户名将转换为大写，而身份验证目标区分大小写，并且必须与“TargetName”字段中显示的大小写匹配。使用16字节NTLM哈希作为密钥，得到一个值。
2.  ​构建一个blob信息
3.  ​使用16字节NTLMv2哈希作为密钥，将HMAC-MD5消息认证代码算法加密一个值(来自type 2的Challenge与Blob拼接在一起)。得到一个16字节的NTProofStr。
4.  ​将NTProofStr与Blob拼接起来形成得到response。

至于选择哪个版本的响应由LmCompatibilityLevel决定。

Net-ntlm hash v1的格式为：

```
username::hostname:LM response:NTLM response:challenge
```


Net-ntlm hash v2的格式为：

```
username::domain:challenge:HMAC-MD5:blob
```


### LmCompatibilityLevel

此安全设置确定网络登录使用的质询/响应身份验证协议。此选项会影响客户端使用的身份验证协议的等级、协商的会话安全的等级以及服务器接受的身份验证的等级，其设置值如下:

*   发送 LM NTLM 响应: 客户端使用 LM 和 NTLM 身份验证，而决不会使用 NTLMv2 会话安全；域控制器接受 LM、NTLM 和 NTLMv2 身份验证。
*   发送 LM & NTLM - 如果协商一致，则使用 NTLMv2 会话安全: 客户端使用 LM 和 NTLM 身份验证，并且在服务器支持时使用 NTLMv2 会话安全；域控制器接受 LM、NTLM 和 NTLMv2 身份验证。
*   仅发送 NTLM 响应: 客户端仅使用 NTLM 身份验证，并且在服务器支持时使用 NTLMv2 会话安全；域控制器接受 LM、NTLM 和 NTLMv2 身份验证。
*   仅发送 NTLMv2 响应: 客户端仅使用 NTLMv2 身份验证，并且在服务器支持时使用 NTLMv2 会话安全；域控制器接受 LM、NTLM 和 NTLMv2 身份验证。
*   仅发送 NTLMv2 响应\\拒绝 LM: 客户端仅使用 NTLMv2 身份验证，并且在服务器支持时使用 NTLMv2 会话安全；域控制器拒绝 LM (仅接受 NTLM 和 NTLMv2 身份验证)。
*   仅发送 NTLMv2 响应\\拒绝 LM & NTLM: 客户端仅使用 NTLMv2 身份验证，并且在服务器支持时使用 NTLMv2 会话安全；域控制器拒绝 LM 和 NTLM (仅接受 NTLMv2 身份验证)。

默认值:

*   Windows 2000 以及 Windows XP: 发送 LM & NTLM 响应
*   Windows Server 2003: 仅发送 NTLM 响应
*   Windows Vista、Windows Server 2008、Windows 7 以及 Windows Server 2008 R2及以上: 仅发送 NTLMv2 响应

### NTLM认证方式(工作组环境中)

1.  客户端需要访问服务器的某个服务(前提是他得知道服务器的用户名和密码)，所以得进行身份认证。于是，客户端输入服务器的用户名和密码进行验证，客户端会缓存服务器密码的NTLM-Hash值。客户端发送**Type 1** **Negotiate 协商消息**去协商需要认证的主体，用户(服务器端的用户名)，机器以及需要使用的安全服务等等信息。
2.  服务端接收到客户端发送过来的 TYPE 1 消息，会读取其中的内容，并从中选择出自己所能接受的服务内容，加密等级，安全服务等等。然后传入 NTLM SSP，得到 NTLM\_CHALLENGE 消息（被称为 **Type 2** **Challenge 挑战消息**），并将此TYPE 2消息发回给客户端。此TYPE 2消息中包含了一个由服务端生成的16位随机值，此随机值被称为 Challenge，服务器将该Challenge保存起来
3.  客户端收到服务端返回的 Type 2 消息， 读取出服务端所支持的内容，并取出其中的随机值Challenge，用缓存的服务器端密码的**NTLM-Hash**对其进行加密(加密后的值为**Type3 Response消息**)。Response消息与用户名、主机名、Challenge值一起组合得到 **Net-NTLMHash**，最后将Net NTLM-Hash封装到 NTLM\_AUTH 消息中（被称为Authenticate认证消息），发往服务端
4.  服务器在收到 Type3的消息之后，用自己的密码的 NTLM Hash 对 Challenge 进行加密，然后与用户名、主机名、Challenge值组合成Net NTLM-Hash。并比较自己计算出的 Net NTLM-Hash 认证消息和客户端发送的认证消息是否匹配。如果匹配，则证明客户端掌握了正确的密码，认证成功，否则认证失败。

![](https://img-blog.csdnimg.cn/20190107114609252.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**wiresharek抓包Net-NTLMHash，使用Hashcat爆破**

抓的数据包：[NTLM2(工作组).pcapng](https://download.csdn.net/download/qq_36119192/11706066 "NTLM2(工作组).pcapng")

**10.211.55.6(WIN7)——>10.211.55.7(WIN2008)**

由于NTLM只是底层的认证协议，必须镶嵌在上层协议里面，消息的传输依赖于使用NTLM的上层协议，比如SMB。如下实验是基于NTLM Over SMB。

![](https://img-blog.csdnimg.cn/20210512170224639.png)

![](https://img-blog.csdnimg.cn/20210512170212909.png)

**认证成功的数据包**

![](https://img-blog.csdnimg.cn/20210512170418715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**认证失败的数据包**

![](https://img-blog.csdnimg.cn/20210512170324638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查看第二个数据包，获得Challenge，为：77effc5381037df8

![](https://img-blog.csdnimg.cn/20190909233113911.png)

查看第三个数据包，获得客户端加密后的Challenge，为：ba3237c701d9f397

![](https://img-blog.csdnimg.cn/20190909233134725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Net-NTLM Hash格式为：username::domain:challenge:HMAC-MD5:blob

*   username（要访问服务器的用户名）：xie
*   domain（访问者主机名或者ip）：WIN7
*   challenge（数据包2中服务器返回的challenge值）：77effc5381037df8
*   HMAC-MD5（数据包3中的NTProofStr）: b6b777ced0128e3f587fe08b98853e13
*   blob（blob对应数据为NTLMv2 Response去掉NTProofStr的后半部分）：0101000000000000f27fa3a7aa61d501ba3237c701d9f3970000000002000e00570049004e00320030003000380001000e00570049004e00320030003000380004000e00570049004e00320030003000380003000e00570049004e00320030003000380007000800f27fa3a7aa61d50106000400020000000800300030000000000000000100000000200000ca0ee75c65eaa5367775b826f949798912fa871a19e5d17f9b49587485a8e6620a001000000000000000000000000000000000000900240063006900660073002f003100390032002e003100360038002e00310030002e0031003500000000000000000000000000

所以最后，Net-NTLM Hash值为：

```
xie::WIN7:77effc5381037df8:b6b777ced0128e3f587fe08b98853e13:0101000000000000f27fa3a7aa61d501ba3237c701d9f3970000000002000e00570049004e00320030003000380001000e00570049004e00320030003000380004000e00570049004e00320030003000380003000e00570049004e00320030003000380007000800f27fa3a7aa61d50106000400020000000800300030000000000000000100000000200000ca0ee75c65eaa5367775b826f949798912fa871a19e5d17f9b49587485a8e6620a001000000000000000000000000000000000000900240063006900660073002f003100390032002e003100360038002e00310030002e0031003500000000000000000000000000
```


然后，用Hashcat爆破，传送门——> [使用Hashcat破解NTLMv2](https://blog.csdn.net/qq_36119192/article/details/100105816#%E4%BD%BF%E7%94%A8Hashcat%E7%A0%B4%E8%A7%A3NTLMv2 "使用Hashcat破解NTLMv2") 

### NTLM认证方式(域环境中)

1.  用户通过输入Windows帐号和密码登录客户端主机，客户端会缓存密码的哈希值NTLM-Hash。成功登录客户端的用户如果试图访问服务器资源，需要向对方发送一个请求，该请求利用 NTLM SSP 生成 **NTLM\_NEGOTIATE** 消息 （被称为**Type 1** **Negotiate 协商消息**），并将 TYPE 1 消息发送给服务端中，该TYPE 1消息中包含一个以明文表示的用户名以及其他的一些协商信息(认证的主体，机器以及需要使用的安全服务等等信息)
2.  服务端接收到客户端发送过来的 TYPE 1 消息，会读取其中的内容，并从中选择出自己所能接受的服务内容，加密等级，安全服务等等。然后传入 NTLM SSP，得到 **NTLM\_CHALLENGE** 消息（被称为 **Type 2** **Challenge 挑战消息**），并将此TYPE 2消息发回给客户端。此TYPE 2消息中包含了一个由服务端生成的16位随机值，此随机值被称为 Challenge，服务器将该Challenge保存起来。
3.  客户端收到服务端返回的 TYPE 2 消息， 读取出服务端所支持的内容，并取出其中的随机值Challenge，用缓存的服务端的密码的**NTLM-Hash**对其进行加密(加密后的值为**Type3 Response消息**)。Response消息与用户名、主机名、Challenge值一起组合得到 **Net-NTLMHash**，最后将Net NTLM-Hash封装到 **NTLM\_AUTH** 消息中（被称为Authenticate认证消息），发往服务端
4.  服务器接收到客户端发送来的 NTLM\_AUTH 的 TYPE 3 消息后，取出其中的Net NTLM-Hash值，并通过Netlogon协议向DC域控（Domain Control）发送针对客户端的验证请求。该请求主要包含以下三方面的内容：客户端用户名、原始的Challenge 和 加密后的Challenge(也就是Net NTLM-Hash)。
5.  DC根据用户名获取该帐号的密码哈希值 NTLM-Hash，用密码哈希值 NTLM-Hash 对原始的Challenge进行加密，然后与用户名、主机名、Challenge值一起组合得到Net NTLM-Hash。如果加密后的Challenge和服务器发送的一致，则意味着用户拥有正确的密码，验证通过，否则验证失败。DC将验证结果发给服务器。
6.  服务器根据DC返回的结果，对客户端进行回复

![](https://img-blog.csdnimg.cn/20190107114132294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**192.168.10.16(WIN7)——>192.168.10.152(WIN2003)          域控：192.168.10.15(WIN2008)**

![](https://img-blog.csdnimg.cn/20190909233622751.png)

![](https://img-blog.csdnimg.cn/20190909233638559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

认证的应用
-----

在域环境下，可以使用 Kerberos 或者 NTLM认证来实现对用户的身份认证。在很多企业的内部网络中(基本都是域环境)，都是使用Kerberos认证或NTLM认证，在Windows 2000以后，在域环境下，Kerberos是默认的认证方式。因为由于NTLM认证存在安全风险，所以用Kerberos认证的较多。Kerberos较之NTLM更高效、更安全，同时认证过程也相对复杂。

在非域环境下，一般都是使用NTLM进行认证。SMB服务和很多Web程序都是使用NTLM来实现对用户的身份认证。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[域内认证之Kerberos协议详解](https://xie1997.blog.csdn.net/article/details/104731625 "域内认证之Kerberos协议详解")

                  [渗透技巧——利用netsh抓取连接文件服务器的NTLMv2 Hash](https://xz.aliyun.com/t/1945 "渗透技巧——利用netsh抓取连接文件服务器的NTLMv2 Hash")

                  [Windows内网协议学习NTLM篇之NTLM基础介绍](https://www.anquanke.com/post/id/193149 "Windows内网协议学习NTLM篇之NTLM基础介绍")  

NTLM协议官方文档：[\[MS-NLMP\]: NT LAN Manager (NTLM) Authentication Protocol | Microsoft Docs](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/b38c36ed-2804-4868-a9ff-8dd3182128e4 "[MS-NLMP]: NT LAN Manager (NTLM) Authentication Protocol | Microsoft Docs")