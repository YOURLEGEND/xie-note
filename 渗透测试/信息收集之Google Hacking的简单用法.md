**目录**

[Google Hacking](#t0 "Google Hacking")

[基本搜索](#t1 "基本搜索")

[高级搜索](#t2 "高级搜索")

[Index of](#t3 "Index of")

[inurl](#t4 "inurl")

[查找有可能存在SQL注入的网站](#t5 "查找有可能存在SQL注入的网站")

* * *

Google Hacking
==============

Google Hacking 是利用谷歌搜索的强大，来在浩瀚的互联网中搜索到我们需要的信息。轻量级的搜索可以搜素出一些遗留后门，不想被发现的后台入口，中量级的搜索出一些用户信息泄露，源代码泄露，未授权访问等等，重量级的则可能是mdb文件下载，CMS 未被锁定install页面，网站配置密码，php远程文件包含漏洞等重要信息。

利用Google搜索我们想要的信息，需要配合谷歌搜索引擎的一些语法：

基本搜索
----

*   逻辑与：and
*   逻辑或： or 
*   逻辑非： -
*   完整匹配："关键词" 
*   通配符：\* ?

高级搜索
----

**intext:**

寻找正文中含有关键字的网页，例如：  **intext:后台登录**   将只返回正文中包含  后台登录 的网页

![](https://img-blog.csdnimg.cn/20181118222521550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**intitle:**

寻找标题中含有关键字的网页，例如：  **intitle:后台登录**   将只返回标题中包含 后台登录 的网页，**intitle:后台登录  密码**  将返回标题中包含黑客而正文中包含中国的网页

![](https://img-blog.csdnimg.cn/20181118222255124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**allintitle:**

用法和intitle类似，只不过可以指定多个词，例如：  **alltitle:后台登录 管理员**   将返回标题中包含黑客 和中国的网页

![](https://img-blog.csdnimg.cn/20181118222721979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**inurl：**

将返回url中含有关键词的网页：例如：**inurl:Login**   将返回url中含有 Login 的网页

![](https://img-blog.csdnimg.cn/20181118222824787.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查找管理员登录页面

![](https://img-blog.csdnimg.cn/20181118224757336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查找后台数据库管理页面

![](https://img-blog.csdnimg.cn/20181118224246771.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**allinurl:**

用法和inurl类似，只不过可以指定多个词，例如：**inurl:Login admin**  将返回url中含有 Login 和 admin 的网页

![](https://img-blog.csdnimg.cn/20181118222845789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**site:**

指定访问的站点，例如： **site:baidu.com  inurl:Login**   将只在baidu.com 中查找url中含有 Login的网页

![](https://img-blog.csdnimg.cn/20181118223004177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**filetype:**

指定访问的文件类型，例如：**site:baidu.com filetype:pdf**      将只返回baidu.com站点上文件类型为pdf的网页

![](https://img-blog.csdnimg.cn/20181118223126191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**link:**

指定链接的网页，例如：**link:www.baidu.com**   将返回所有包含指向 www.baidu.com 的网页

![](https://img-blog.csdnimg.cn/20181118223325888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**related:**

相似类型的网页，例如：**related:www.xjtu.edu.cn**  将返回与 www.xjtu.edu.cn  相似的页面，相似指的是网页的布局相似

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9yU3lkMmNjbHYyY2trYndUc0J2bkRKcGI4OW84V014dlh1Q3JLM25tNERIRU13RkRWMHlsblBBZmlhNW1ZclpoODdncEZUVkt6cGs4NUVOdGljekdNWTdRLzY0MA?x-oss-process=image/format,png)

**cache:**

网页快照，谷歌将返回给你他存储下来的历史页面，如果你同时制定了其他查询词，将在搜索结果里以高亮显示，例如：**cache:www.hackingspirits.com  guest**  ，将返回指定网站的缓存，并且正文中含有guest

**info:**

返回站点的指定信息，例如：**info:www.baidu.com**   将返回百度的一些信息

**define:**

返回某个词语的定义，例如：**define:Hacker**　　将返回关于Hacker的定义

**phonebook:**

电话簿查询美国街道地址和电话号码信息。例如：**phonebook:Lisa+CA**  将返回名字里面包含Lisa并住在加州的人的所有名字

**查找网站后台**

*   site:xx.com intext:管理
*   site:xx.com inurl:login
*   site:xx.com intitle:后台

**查看服务器使用的程序**

*   site:xx.com filetype:asp
*   site:xx.com filetype:php
*   site:xx.com filetype:jsp
*   site:xx.com filetype:aspx

**查看上传漏洞**

*   site:xx.com inurl:file
*   site:xx.com inurl:load

Index of
--------

利用  Index of  语法去发现允许目录浏览的web网站，就像在本地的普通目录一样。下面是一些有趣的查询：

*   index of /admin
*   index of /passwd
*   index of /password
*   index of /mail
*   "index of /" +passwd
*   "index of /" +password.txt
*   "index of /" +.htaccess
*   "index of /root"
*   "index of /cgi-bin"
*   "index of /logs"
*   "index of /config"

inurl
-----

而上面这些命令中用的最多的就是 inurl: 了，利用这个命令，可以查到很多意想不到的东西

*   利用 allinurl:winnt/system32/ 查询：列出的服务器上本来应该受限制的诸如“system32” 等目录，如果你运气足够好，你会发现“system32” 目录里的“cmd.exe” 文件，并能执行他，接下来就是提升权限并攻克了。
*   查询 allinurl:wwwboard/passwd.txt  将列出所有有“WWWBoard Password vulnerability”漏洞的服务器，阅读更多请参见下面链接。
*   查询  inurl:.bash\_history  将列出互联网上可以看见 “inurl:.bash\_history” 文件的服务器。这是一个命令历史文件，这个文件包含了管理员执行的命令，有时会包含一些敏感信息比如管理员键入的密码。
*   查询 inurl:config.txt  将看见网上暴露了“inurl:config.txt”文件的服务器，这个文件包含了经过哈希编码的管理员的密码和数据库存取的关键信息。

还有一些其他一些使用“inurl:”和“allinurl:”查询组合的例子

*   inurl:admin filetype:txt
*   inurl:admin filetype:db
*   inurl:admin filetype:cfg
*   inurl:mysql filetype:cfg
*   inurl:passwd filetype:txt
*   inurl:”wwwroot/\*.”
*   inurl:adpassword.txt
*   inurl:webeditor.php
*   inurl:file\_upload.php
*   inurl:gov filetype:xls “restricted”
*   index of ftp +.mdb allinurl:/cgi-bin/ +mailto

查找有可能存在SQL注入的网站
===============

inurl:id=1

![](https://img-blog.csdnimg.cn/20200222235449563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

更多关键字：[google hack 之 sql注入](https://github.com/xuanhun/HackingResource/blob/master/web%E5%AE%89%E5%85%A8/google%20hack%20%E4%B9%8Bsql%E6%B3%A8%E5%85%A5.md "google hack 之 sql注入")