**目录**

[CDN](#t0 "CDN")

[如何判断网站是否有CDN？](#t1 "如何判断网站是否有CDN？")

[绕过CDN查找网站真实ip](#t2 "绕过CDN查找网站真实ip")

* * *

CDN
---

在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)过程中，经常会碰到网站有CDN的情况。CDN即内容分发网络，主要解决因传输距离和不同运营商节点造成的网络速度性能低下的问题。说的简单点，就是一组在不同运营商之间的对接点上的高速缓存服务器，把用户经常访问的静态数据资源直接缓存到节点服务器上，当用户再次请求时，会直接分发到离用户近的节点服务器上响应给用户，当用户有实际数据交互时才会从远程Web服务器上响应，这样可以大大提高网站的响应速度及用户体验。

**CDN的优势**

*   提高用户访问速率，优化用户使用体验。
*   隐藏真实服务器的IP
*   提供WAF功能，目前很多CDN也提供了WAF的功能，我们的访问请求会先经过CDN节点的过滤，该过滤可对SQL注入、XSS、Webshell上传、命令注入、恶意扫描等攻击行为进行有效检测和拦截。CDN节点将认为无害的数据提交给真实的主机服务器。

**几种访问方式的不同**

*   传统访问：用户访问域名-->解析服务器IP-->访问目标主机
*   普通CDN：用户访问域名-->CDN节点-->真实服务器IP-->访问目标主机
*   带WAF的CDN：用户访问域名-->CDN节点（云WAF）-->真实服务器IP-->访问目标主机

**CDN的配置**

1.  将域名的 NS 记录指向 CDN 厂商提供的 DNS 服务器。
2.  给域名设置一个 CNAME 记录，将它指向CDN厂商提供的另一个域名。

![](https://img-blog.csdnimg.cn/20200105204334358.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

所以在渗透测试中，为了要知道网站服务器的真实IP，我们必须绕过[CDN](https://so.csdn.net/so/search?q=CDN&spm=1001.2101.3001.7020)查找出网站的真实ip地址。

查询域名相关的网站：

*   DNS查询：[https://dnsdb.io/zh-cn/](https://dnsdb.io/zh-cn/%C2%A0 "https://dnsdb.io/zh-cn/ ") 
*   微步在线：[https://x.threatbook.cn/](https://x.threatbook.cn/%C2%A0 "https://x.threatbook.cn/ ") 
*   在线域名信息查询：[http://toolbar.netcraft.com/site\_report?url=](http://toolbar.netcraft.com/site_report?url= "http://toolbar.netcraft.com/site_report?url=")
*   DNS、IP等查询：[ViewDNS.info - Your one source for DNS related tools!](http://viewdns.info/ "ViewDNS.info - Your one source for DNS related tools!")
*   CDN查询IP：[https://tools.ipip.net/cdn.php](https://tools.ipip.net/cdn.php%C2%A0 "https://tools.ipip.net/cdn.php ") 
*   SecurityTrails平台：[Historical A records for www.baidu.com - SecurityTrails](https://securitytrails.com/domain/www.baidu.com/history/a "Historical A records for www.baidu.com - SecurityTrails")

### 如何判断网站是否有CDN？

很简单，使用不同地方的 ping 服务，查看对应 IP 地址是否唯一，如果不唯一则极有可能是使用了CDN

ping测试网站有：

*   [全球Ping测试,在线ping工具 - 网络工具](https://wepcc.com/ "全球Ping测试,在线ping工具  -  网络工具")
*   [多个地点Ping服务器,网站测速 - 站长工具](http://ping.chinaz.com/ "多个地点Ping服务器,网站测速 - 站长工具")
*   [网站测速工具\_超级ping \_多地点ping检测 - 爱站网](http://ping.aizhan.com/ "网站测速工具_超级ping _多地点ping检测 - 爱站网")

### 绕过CDN查找网站真实ip

![](https://img-blog.csdnimg.cn/20200105223111226.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**(1)查询子域名：**毕竟 CDN 还是不便宜的，所以很多站长可能只会对主站或者流量大的子站点做了 CDN，而很多小站子站点又跟主站在同一台服务器或者同一个C段内，此时就可以通过查询子域名对应的 IP 来辅助查找网站的真实IP。传送门——> [子域名信息查询](https://blog.csdn.net/qq_36119192/article/details/84068625#%E5%AD%90%E5%9F%9F%E5%90%8D%E4%BF%A1%E6%81%AF%E6%9F%A5%E8%AF%A2 "子域名信息查询")

**(2)查询主域名**：以前用CDN的时候有个习惯，只让WWW域名使用cdn，秃域名不适用，为的是在维护网站时更方便，不用等cdn缓存。所以试着把目标网站的www去掉，ping一下看ip是不是变了，您别说，这个方法还真是屡用不爽。

**(3)邮件服务器：**一般的邮件系统都在内部，没有经过CDN的解析，通过目标网站用户注册或者RSS订阅功能，查看邮件，寻找邮件头中的邮件服务器域名IP，ping这个邮件服务器的域名，就可以获得目标的真实IP(必须是目标自己的邮件服务器，第三方或者公共邮件服务器是没有用的)。

**(4)查看域名历史解析记录：**也许目标很久之前没有使用CDN，所以可能会存在使用 CDN 前的记录。所以可以通过网站[Netcraft | Internet Research, Cybercrime Disruption and PCI Security Services](https://www.netcraft.com "Netcraft | Internet Research, Cybercrime Disruption and PCI Security Services") 来观察域名的IP历史记录。

**(5)****国外访问**：国内的CDN往往只对国内用户的访问加速，而国外的CDN就不一定了。因此，通过国外在线代理网站[CA App Synthetic Monitor](https://asm.ca.com/en/ping.php "CA App Synthetic Monitor") 访问 ，可能会得到真实的ip地址。

**(6)Nslookup查询**：查询域名的NS记录、MX记录、TXT记录等很有可能指向的是真实ip或同C段服务器。传送门：[各种解析记录](https://blog.csdn.net/qq_36119192/article/details/82752515#%E5%90%84%E7%A7%8D%E8%A7%A3%E6%9E%90%E8%AE%B0%E5%BD%95 "各种解析记录")

**(7)网站漏洞**：利用网站自身存在的漏洞，很多情况下会泄露服务器的真实IP地址

**(8)Censys查询SSL证书找到真实IP：**利用“Censys网络空间搜索引擎”搜索网站的SSL证书及HASH，在https://crt.sh上查找目标网站SSL证书的HASH，然后再用Censys搜索该HASH即可得到真实IP地址。

```
443.https.tls.certificate.parsed.extensions.subject_alt_name.dns_names:***trade.com
```


 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

 参考文章：[11种绕过CDN查找真实ip的方法](https://www.cnblogs.com/qiudabai/p/9763739.html "11种绕过CDN查找真实ip的方法")

                  [绕过CDN查找网站真实ip](http://www.cnblogs.com/blacksunny/p/5771827.html "绕过CDN查找网站真实ip")

                  [绕过 CDN 寻找真实 IP 地址的各种姿势](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247491648&idx=1&sn=64d948bfc39f7122ce7aa5da63536b42&chksm=ec1dd068db6a597e27843ea34b430216b8397ac278fe6ccc1691ed6e251e51ac5e18f681b879&scene=0&xtrack=1&key=750ed215a501f466e38c6e860749e4772b6eae3e6dfc5c0ca0d318d4b14a496996ad2eea5852ba607df0aaaee0fac675b19f03f8ed0b3f4a84153821a759d4131dde18b3f5b7ebd1350af5ae7139ceec&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=irTZVyrE8xZpBUIJ5x3YZIVIIl%2Bc7G9LNBLHZyp7zJHangzzaA61Hwqw2925WxMV "绕过 CDN 寻找真实 IP 地址的各种姿势")

                  [绕过CDN查找真实IP方法总结](https://mp.weixin.qq.com/s?__biz=Mzg4MzA4Nzg4Ng==&mid=2247484123&idx=1&sn=50f0e7b05d53b496e017ff7bd60e6d96&chksm=cf4d8dbaf83a04ac751aafaa442ca54448d57a58bac803c318ad3951bd357ee34cf33616bfb2&mpshare=1&srcid=0102QfHWbVhC6J8gJhbqMMWd&sharer_sharetime=1577954448927&sharer_shareid=dd3a9a786977f9fed6661275e7a22c6a&from=timeline&scene=2&subscene=1&clicktime=1577955210&enterid=1577955210&ascene=14&devicetype=Windows+10&version=62070158&nettype=WIFI&abtest_cookie=AQABAAgAAQCdhh4AAAA%3D&lang=zh_CN&exportkey=A%2FX%2BhkGmHwJwSbs0POWneno%3D&pass_ticket=usxq7aPUmoxfEMk3GF09vvBrMn7xf27OtSxls7QwiVOQF0y5zi0QE34f1Y19nJSF&wx_header=1&key=0a32225dc04df511f9536543c334f04d36d635f37679f026292191debb957a10e119623af486d70a0acd792394403bc75ee9691203480340d91634eacba6dc93b647c32b27284269cd961f05fa318a22&uin=MjIwMDQzNjQxOQ%3D%3D "绕过CDN查找真实IP方法总结")

                  Web安全攻防书籍