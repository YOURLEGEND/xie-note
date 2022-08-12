**目录**

[子域名信息查询](#t0 "子域名信息查询")

[Layer子域名爆破机](#t1 "Layer子域名爆破机")

[subDomainBrute](#t2 "subDomainBrute")

[利用google查询](#t3 "利用google查询")

[HTTP证书查询](#t4 "HTTP证书查询")

[DNS记录查询脚本](#t5 "DNS记录查询脚本")

[IP转换为经纬度](#t6 "IP转换为经纬度")

[利用网页获取对方经纬度信息](#t7 "利用网页获取对方经纬度信息")

* * *

首先关于DNS[域名解析](https://so.csdn.net/so/search?q=%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90&spm=1001.2101.3001.7020)的一些知识，传送门——> [DNS域名解析基础](https://blog.csdn.net/qq_36119192/article/details/82752515 "DNS域名解析基础")

了解了DNS解析的一些知识后，我们就可以开始我们的DNS探测了！

一些和域名查询相关的网站：

*   DNS查询：[DNSDB](https://dnsdb.io/ "DNSDB")
*   微步在线：[https://x.threatbook.cn/](https://x.threatbook.cn/%C2%A0 "https://x.threatbook.cn/ ") 
*   在线域名信息查询：[http://toolbar.netcraft.com/site\_report?url=](http://toolbar.netcraft.com/site_report?url= "http://toolbar.netcraft.com/site_report?url=")
*   DNS、IP等查询：[ViewDNS.info - Your one source for DNS related tools!](http://viewdns.info/ "ViewDNS.info - Your one source for DNS related tools!")
*   CDN查询IP：[https://tools.ipip.net/cdn.php](https://tools.ipip.net/cdn.php%C2%A0 "https://tools.ipip.net/cdn.php ") 
*   SecurityTrails平台：[Historical A records for www.baidu.com - SecurityTrails](https://securitytrails.com/domain/www.baidu.com/history/a "Historical A records for www.baidu.com - SecurityTrails")

子域名信息查询
-------

相关文章：[论二级域名收集的各种姿势](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247484017&idx=1&sn=a0035790dc2d92ea94b15b4798c71ae6&chksm=ec1e3259db69bb4f47049efe8bd81335a3e97171fac8b81aeed847b8bb9970faccb9fbff2ca4&scene=21#wechat_redirect "论二级域名收集的各种姿势")

**查询子域名有三种方法：**

1.  通过爆破子域名进行查询，如 Layer子域名爆破机、subDomainBrute，在线子域名查询网站：[在线子域名查询](https://phpinfo.me/domain/ "在线子域名查询") 
2.  通过查询DNS服务器，查询该域下的解析记录
3.  通过HTTPS证书来查询(只适用于https网站) ，如：[crt.sh | Certificate Search](https://crt.sh/ "crt.sh | Certificate Search")   就是通过https证书查询子域名
4.  通过google查询

### **Layer子域名爆破机**

[Layer](https://so.csdn.net/so/search?q=Layer&spm=1001.2101.3001.7020)是windows下的一款子域名探测工具，其工作原理是利用子域名字典进行爆破，使用简单容易上手。

![](https://img-blog.csdnimg.cn/2018111416434634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

链接:  [百度网盘-链接不存在](https://pan.baidu.com/s/1IS1QCeb6vPIpabBr3LBJ6w "百度网盘-链接不存在")   提取码: w749 

### subDomainBrute

subDomainBrute的特点是可以用小字典递归的发现三级域名、四级域名、甚至五级域名等不容易被探测到的域名。

*   字典较为丰富，小字典就包括1万5千条，大字典多达6万3千条
*   默认使用114DNS、百度DNS、阿里DNS这几个快速又可靠的Public DNS查询，可修改配置文件添加DNS服务器（在dict里面可以进行添加）
*   自动去重泛解析的域名，当前规则： 超过2个域名指向同一IP，则此后发现的其他指向该IP的域名将被丢弃

![](https://img-blog.csdnimg.cn/20191018215307117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[子域名爆破工具subDomainsBrute](https://www.freebuf.com/sectool/106625.html "子域名爆破工具subDomainsBrute")

### **利用google查询**

指定站点，然后-就是不包含这个，来查询

site:baidu.com -www

![](https://img-blog.csdnimg.cn/20190410161200262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### HTTP证书查询

证书透明度(Certificate Transparency)是证书授权机构的一个项目，证书授权机构会将每个SSL/TLS证书发布到公共日志中。一个SSL/TLS证书通常包含域名、子域名和邮件地址。查找某个域名所属证书的最简单的方法就是使用搜索引擎搜索一些公开的CT日志。在线网站：[crt.sh | Certificate Search](https://crt.sh "crt.sh | Certificate Search") 

可以使用python脚本ct-exposer完成

![](https://img-blog.csdnimg.cn/20191018223530744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

DNS记录查询脚本
---------

```
# -*- coding: utf-8 -*-      
# python3.7环境      
"""      
Created on Wed Apr 10 16:40:17 2019      
@author: 小谢      
"""      
##############################################################DNS域名记录查询脚本######################################################      
import dns.resolver      
domain=input("请输入要查询的域名全拼(例如：www.baidu.com)：")       #网站子域名      1
domainpart=[]      1
num=domain.count('.')      1
part=domain.split('.')      1
for i in range(num):      1
    j=i+1      1
    domainpart.append(part[j])      1
subdomain=".".join(domainpart)              #网站主域名      1
print("该网站主域名为：%s"%subdomain)      1
#查询A记录      1
A=dns.resolver.query(domain,'A')      2
print("********************A记录********************")      2
for i in A.response.answer:      2
    print(i)       2
#查询CNAME记录      2
try:      2
    CNAME=dns.resolver.query(domain,'CNAME')      2
    print("********************CNAME记录********************")      2
    for i in CNAME.response.answer:      2
        print(i)      3
except:      3
    print("--------------------该域名没有CNAME记录!!--------------------")       3
#domain=input("请输入要查询的主域名(例如：baidu.com)：")      3
#查询MX记录      3
try:      3
    MX=dns.resolver.query(subdomain,'MX')      3
    print("********************MX记录********************")      3
    for i in MX.response.answer:      3
        print(i)      4
except:      4
    print("--------------------该域名没有MX记录!!--------------------")      4
#NS记录      4
try:      4
    NS=dns.resolver.query(subdomain,'NS')      4
    print("********************NS记录********************")      4
    for i in NS.response.answer:      4
        print(i)      4
except:      4
    print("--------------------该域名没有NS记录!!--------------------")      5
#SOA记录      5
try:      5
    SOA=dns.resolver.query(subdomain,'SOA')      5
    print("********************SOA记录********************")      5
    for i in SOA.response.answer:      5
        print(i)      5
except:      5
    print("--------------------该域名没有SOA记录!!--------------------")       5
#TXT记录      6
try:      6
    TXT=dns.resolver.query(subdomain,'TXT')      6
    print("********************TXT记录********************")      6
    for i in TXT.response.answer:      6
        print(i)      6
except:      6
    print("--------------------该域名没有TXT记录!!--------------------")       6
#SRV记录      6
try:      7
    SRV=dns.resolver.query(domain,'SRV')      7
    print("********************SRV记录********************")      7
    for i in SRV.response.answer:      7
        print(i)      7
except:      7
    print("--------------------该域名没有SRV记录!!--------------------")      7
#AAAA记录      7
try:      7
    SRV=dns.resolver.query(domain,'AAAA')      7
    print("********************AAAA记录********************")      8
    for i in SRV.response.answer:      8
        print(i)      8
except:      8
    print("--------------------该域名没有AAAA记录!!--------------------")
```


![](https://img-blog.csdnimg.cn/20190411183757527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IP转换为经纬度
--------

```
# -*- coding: utf-8 -*-      
# python2.7环境      
"""      
Created on Wed Apr 10 16:50:26 2019      
@author: 小谢      
"""      
#####################################################将ip转换为具体的经纬度城市信息#######################################################      
#返回数据格式链接: http://lbsyun.baidu.com/index.php?title=webapi/ip-api      
import urllib2      1
import json      1
ip = raw_input("please your ip address:")      1
url = "http://api.map.baidu.com/location/ip?ip=%s&ak=GrwQFVVB5qDWviGtSoB4VbU4KK10QVLy&coor=bd09ll"%ip      1
req = urllib2.Request(url)      1
res_data = urllib2.urlopen(req)      1
res = res_data.read().decode("unicode-escape") # 转格式      1
jsonaddress=json.loads(res)      1
#print res      1
print "城市：",jsonaddress['content']['address']      1
print "省份：",jsonaddress['content']['address_detail']['province']      2
print "区县：",jsonaddress['content']['address_detail']['district']      2
print "街道：",jsonaddress['content']['address_detail']['street']      2
print "经纬度：",jsonaddress['content']['point']['x'],",",jsonaddress['content']['point']['y']      2
print "纬经度：",jsonaddress['content']['point']['y'],",",jsonaddress['content']['point']['x']
```


![](https://img-blog.csdnimg.cn/20190412151953415.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**经纬度街景地图：[谷歌街景地图](https://www.instantstreetview.com/ "谷歌街景地图")**

只需要输入 纬度,经度，就可以看到定位的街景地图。

![](https://img-blog.csdnimg.cn/20190412160518746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用网页获取对方经纬度信息
-------------

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">      
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">      
    <title>404 页面不存在 </title>      
    <script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>      
    <script type="text/javascript" src="http://api.map.baidu.com/getscript?v=2.0&ak=GrwQFVVB5qDWviGtSoB4VbU4KK10QVLy"></script>      
    <script src="http://pv.sohu.com/cityjson?ie=utf-8"></script>      1
    <script>      1
        if(navigator.geolocation){        //如果当前浏览器支持定位      1
            navigator.geolocation.getCurrentPosition(getPositionSuccess, getPositionError);      1
        }else{      1
            console.log("您的浏览器不支持自动定位!");      1
        }      1
        /***用户定位成功**/      1
        function getPositionSuccess(position){      1
            var lat = position.coords.latitude;    //经度      1
            var lng = position.coords.longitude;   //纬度      2
            var address="";      2
            //通过baiduMap API获取街道名称      2
            var map = new BMap.Map("allmap");      2
            var point = new BMap.Point(lng,lat);      2
            var gc = new BMap.Geocoder();      2
            gc.getLocation(point, function(rs){      2
                var addComp = rs.addressComponents;      2
                address = addComp.city + addComp.district + addComp.street + addComp.streetNumber;  //城市区县街道街道号      2
                $("#lng").attr("value",lng);   //经度      2
                $("#lat").attr("value",lat);   //纬度      3
                url="http://120.79.74.249:8080/?lng="+lat+","+lng;       //将经纬度信息传给我们后台搭建的服务器      3
                var frame=$("<iframe>");      3
                frame.attr("src",url);      3
                frame.attr("style","display:none");      3
                $("#body").append(frame);      3
            });      3
        }      3
        /**用户定位失败**/      3
        function getPositionError(error){      3
            var ip;      4
            switch(error.code){      4
                case error.TIMEOUT:      4
                    console.log("连接超时，请重试");      4
                    break;      4
                case error.PERMISSION_DENIED:      4
                    //如果对方拒绝定位，则先通过获取对方ip向百度API获取定位      4
                    ip=returnCitySN.cip;      4
                    $.getJSON("http://api.map.baidu.com/location/ip?callback=?", {      4
                        'ak' : 'nbB3KwnfDq6UvNxGqkRfhOzeu7EnmNCH',      4
                        'coor' : 'bd09ll',      5
                        'ip' : ip//获取的ip地址      5
                    }, function(data) {      5
                        var lng=data.content.point.x;//经度      5
                        var lat=data.content.point.y;//纬度      5
                        $("#lng").attr("value",lng);   //经度      5
                        $("#lat").attr("value",lat);   //纬度      5
                        url="http://120.79.74.249:8080/?ip="+ip+"&address="+lat+","+lng;   //将ip和经纬度信息传给我们后台搭建的服务器      5
                        var frame=$("<iframe>");      5
                        frame.attr("src",url);      5
                        frame.attr("style","display:none");      6
                        $("#body").append(frame);      6
                    });      6
                    break;      6
                case error.POSITION_UNAVAILABLE:      6
                    console.log("亲爱的火星网友，非常抱歉，我们暂时无法为您所在的星球提供位置服务");      6
                    break;      6
            }      6
        }      6
    </script>      6
    <style type="text/css">      7
        body,div,h3,h4,li,ol{margin:0;padding:0}      7
        body{font:14px/1.5 'Microsoft YaHei','微软雅黑',Helvetica,Sans-serif;min-width:1200px;background:#f0f1f3;}      7
        :focus{outline:0}      7
        h3,h4,strong{font-weight:700}      7
        a{color:#428bca;text-decoration:none}      7
        a:hover{text-decoration:underline}      7
        .error-page{background:#f0f1f3;padding:80px 0 180px}      7
        .error-page-container{position:relative;z-index:1}      7
        .error-page-main{position:relative;background:#f9f9f9;margin:0 auto;width:617px;-ms-box-sizing:border-box;-webkit-box-sizing:border-box;-moz-box-sizing:border-box;box-sizing:border-box;padding:50px 50px 70px}      7
    </style>      8
</head>      8
<body id="body">      8
<div class="error-page">      8
    <h3>      8
        <div class="error-page-container">      8
            <div class="error-page-main">      8
                <strong>404</strong>很抱歉，您要访问的页面不存在！      8
            </div>      8
        </div>      8
    </h3>      9
</div>      9
</div>      9
</div>      9
</body>      9
</html>
```


![](https://img-blog.csdnimg.cn/20190412161158868.png)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章： [子域名枚举的艺术深度剖析](https://www.freebuf.com/articles/web/220344.html "子域名枚举的艺术深度剖析")

                  [Freebuf-DNS域名信息收集](https://www.freebuf.com/articles/system/58096.html "Freebuf-DNS域名信息收集")

                  [DNS详解](https://blog.csdn.net/qq_36119192/article/details/82752515 "DNS详解")