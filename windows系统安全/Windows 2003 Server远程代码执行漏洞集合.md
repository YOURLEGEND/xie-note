**目录**

[MS08-067](#t0)

[CVE-2017-7269](#t1)

* * *

### MS08-067

*   发布日期：2008/10/22
*   针对端口：139、445
*   漏洞等级：高危
*   漏洞影响：服务器服务中的漏洞可能允许远程执行代码
*   受影响的操作系统： Windows 2000;XP;**Server 2003**;Vista;Server 2008;7 Beta
*   漏洞原理：攻击者通过特制的RPC请求发给存在漏洞的主机，将可导致攻击者远程代码执行。攻击者可能未经身份验证即可利用此漏洞运行任意代码，此漏洞可能用于进行蠕虫攻击。

相关文章：[MS08\_067漏洞攻击](https://blog.csdn.net/qq_36119192/article/details/84557619)

### CVE-2017-7269

*   漏洞类型：缓冲区溢出
*   漏洞等级：高危
*   针对端口：80
*   受影响的系统：Microsoft Windows Server 2003 R2 开启 **WebDAV** 服务的 IIS 6.0
*   漏洞原理：Windows server 2003 R2版本IIS6.0的WebDAV服务中的ScStorgPathFromUrl函数存在缓冲区溢出漏洞，远程攻击者通过以 “if:<http://" 开头的长header PROPFIND请求，可以远程执行任意代码”
*   poc地址：[https://github.com/Al1ex/CVE-2017-7269](https://github.com/Al1ex/CVE-2017-7269)

相关文章：[CVE-2017-7269漏洞复现](https://blog.csdn.net/Fly_hps/article/details/79568430)