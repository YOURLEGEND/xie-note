**目录**

[Fofa](#t0)

[逻辑运算符](#t1)

[查找使用指定应用的IP](#t2)

[查找使用指定协议的IP](#t3)

[查找开放指定端口的IP](#t4)

[查找IP或网段的信息](#t5)

[查找使用指定css或js的网站](#t6)

[使用Fofa API接口](#t7)

[其他](#t8)

* * *

Fofa
----

Fofa 是白帽汇推出的一款网络空间搜索引擎，它通过进行网络空间测绘，能够帮助研究人员或者企业迅速进行网络资产匹配。例如进行漏洞影响范围分析、应用分布统计、应用流行度等。在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)过程中，Fofa能帮助测试人员快速发现目标资产。Fofa目前支持的检索字段包括：domain，host，ip，title，server，header，body，port，cert，country，city，os，appserver，middleware，language，tags，user\_tag等等，支持的逻辑运算符包括：= ，==，!= ，&&，||。

### **逻辑运算符**

*   && ：表示逻辑与
*   || ：表示逻辑或
*   != : 表示逻辑非

```
app="Coremail" &&  country=CN          #查找使用coremail并且在中国境内的网站      
title="管理后台" ||  title="登录后台"   #查找title中含有管理后台或者登录后台的网站      
port!="80"                             #匹配端口不为80端口的服务
```


### 查找使用指定应用的地址

```
app="Coremail"                              #查找使用Coremail的网站      
app="Oracle-Weblogic_interface_7001"        #查找使用Weblogic的网站      
app="CCTV-Cameras"                          #查找使用九安视频监控的网站      
app="奇安信-天擎"                            #查找使用天擎的网站      
app="Apache-Shiro"                          #查找使用shiro的网站      
app="struts2"                               #查找使用struts2的网站      
app="TongDa-OA"                             #查找使用通达OA的网站      
app="致远互联-OA"                            #查找使用致远OA的网站      
app="泛微-协同办公OA"                        #查找使用泛微OA的网站      1
app="万户网络-ezOFFICE"                      #查找使用万户OA的网站      1
app="ATLASSIAN-Confluence"                  #查找使用confluence的网站      1
app="f5-BIGIP"                              #查找使用f5的网站      1
title="+ ID_VC_Welcome +"                   #查找Vcenter的网站       1
#搜索使用exchange的网站      1
app="Microsoft-Exchange"      1
title="Outlook Web App"       1
microsoft exchange 2013：       2
app="Microsoft-Exchange-2013"      2
app="Microsoft-Exchange-Server-2013-CU21"      2
app="Microsoft-Exchange-Server-2013-CU17"      2
app="Microsoft-Exchange-Server-2013-CU23"      2
app="Microsoft-Exchange-Server-2013-CU13"      2
app="Microsoft-Exchange-Server-2013-CU22"      2
app="Microsoft-Exchange-Server-2013-CU11"      2
app="Microsoft-Exchange-Server-2013-CU2"      2
app="Microsoft-Exchange-Server-2013-CU16"      2
app="Microsoft-Exchange-Server-2013-CU19"      3
app="Microsoft-Exchange-Server-2013-CU3"      3
app="Microsoft-Exchange-Server-2013-CU18"      3
app="Microsoft-Exchange-Server-2013-CU5"      3
app="Microsoft-Exchange-Server-2013-CU20"      3
app="Microsoft-Exchange-Server-2013-CU12"      3
app="Microsoft-Exchange-Server-2013-CU15"      3
app="Microsoft-Exchange-Server-2013-CU10"      3
app="Microsoft-Exchange-Server-2013-CU9"      3
app="Microsoft-Exchange-Server-2013-CU6"      3
app="Microsoft-Exchange-Server-2013-CU7"      4
app="Microsoft-Exchange-Server-2013-CU1"      4
app="Microsoft-Exchange-Server-2013-CU14"      4
app="Microsoft-Exchange-Server-2013-CU8"      4
app="Microsoft-Exchange-Server-2013-RTM"      4
app="Microsoft-Exchange-Server-2013-SP1"      4
app="Microsoft-Exchange-2013"       4
microsoft exchange 2016：       4
app="Microsoft-Exchange-Server-2016-CU19"      4
app="Microsoft-Exchange-Server-2016-CU3"      5
app="Microsoft-Exchange-Server-2016-CU12"      5
app="Microsoft-Exchange-Server-2016-RTM"      5
app="Microsoft-Exchange-Server-2016-CU7"      5
app="Microsoft-Exchange-Server-2016-CU17"      5
app="Microsoft-Exchange-Server-2016-CU2"      5
app="Microsoft-Exchange-Server-2016-CU1"      5
app="Microsoft-Exchange-Server-2016-CU14"      5
app="Microsoft-Exchange-Server-2016-CU5"      5
app="Microsoft-Exchange-Server-2016-CU11"      5
app="Microsoft-Exchange-Server-2016-CU9"      6
app="Microsoft-Exchange-Server-2016-CU16"      6
app="Microsoft-Exchange-Server-2016-CU10"      6
app="Microsoft-Exchange-Server-2016-CU6"      6
app="Microsoft-Exchange-Server-2016-CU13"      6
app="Microsoft-Exchange-Server-2016-CU18"      6
app="Microsoft-Exchange-Server-2016-CU8"      6
app="Microsoft-Exchange-Server-2016-CU4"      6
app="Microsoft-Exchange-2016-POP3-server"       6
microsoft exchange 2019：      7
app="Microsoft-Exchange-Server-2019-CU5"      7
app="Microsoft-Exchange-Server-2019-CU3"      7
app="Microsoft-Exchange-Server-2019-Preview"      7
app="Microsoft-Exchange-Server-2019-CU8"      7
app="Microsoft-Exchange-Server-2019-CU1"      7
app="Microsoft-Exchange-Server-2019-CU7"      7
app="Microsoft-Exchange-Server-2019-CU2"      7
app="Microsoft-Exchange-Server-2019-CU6"      7
app="Microsoft-Exchange-Server-2019-RTM"      7
app="Microsoft-Exchange-Server-2019-CU4"       8
microsoft exchange 2010：       8
app="Microsoft-Exchange-2010-POP3-server-version-03.1"      8
app="Microsoft-Exchange-Server-2010"
```


### 查找使用指定协议的IP

```
protocol=mysql       #查找使用mysql的ip      
protocol=mssql       #查找使用mssql的ip      
protocol=oracle      #查找使用oracle的ip      
protocol=redis       #查找使用redis的ip
```


### 查找开放指定端口的IP

```
port="3389"   #查找开放3389端口的主机      
ports="1433,3306,3389"      #查找开放了1433,3306,3389端口的主机      
ports=="1433,3306,3389"		#查找只开放了1433,3306,3389端口的主机
```


### 查找IP或网段的信息

```
ip="220.181.38.148"   #查找指定ip的信息      
ip="220.181.38.0/24"  #查找指定网段的信息
```


### 查找使用指定css或js的网站

有时候，我们碰到一个使用框架的网站，现在我们想找到所有使用该框架的网站。可以查看源代码，找到这个框架特有的css或js文件，然后将该css或js的路径复制粘贴到 Fofa进行查找

![](https://img-blog.csdnimg.cn/20200529155924203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200529155955744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200529155806118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200529155846727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用Fofa API接口

使用Fofa API接口需要有Fofa的会员。我这里以Bash脚本为例

```
echo 'domain="fofa.so"' | base64 - | xargs -I{} curl "https://fofa.so/api/v1/search/all?email=test@qq.com&key=b7573a06ec469af619aedbd4dce37f0f&qbase64={}" >  result.txt
```


![](https://img-blog.csdnimg.cn/20200627153300123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 其他

```
title="管理后台"     #查找title含有管理后台的ip      
header="thinkphp"   #查找响应头含有thinkphp的ip      
body="管理后台"      #查找响应包含有thinkphp的ip      
domain="baidu.com"  #查找指定根域的所有子域      
host="baidu"        #查找host字段中带有baidu的网站            
country="CN"        #查找中国境内的ip      
region="Jiangsu"    #查找江苏地区的ip      
cert="phpinfo.me"   #搜索证书(如https证书、imaps证书等)中含有"phpinfo.me"关键词的网站和IP      
server="apache"     #服务器使用apache的网站
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[FOFA网络空间资产搜索引擎使用指南](https://www.anquanke.com/post/id/84865 "FOFA网络空间资产搜索引擎使用指南")