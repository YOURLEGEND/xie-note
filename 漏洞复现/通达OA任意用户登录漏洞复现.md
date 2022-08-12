**目录**

[通达OA任意用户登录漏洞](#t0 "通达OA任意用户登录漏洞")

[漏洞描述](#t1 "漏洞描述")

[影响范围](#t2 "影响范围")

[漏洞复现](#t3 "漏洞复现")

* * *

通达OA任意用户登录漏洞
------------

### 漏洞描述

通达OA官方于4月17日发布安全更新。在该次安全更新中修复了包括任意用户登录在内的高危漏洞。攻击者通过构造恶意请求，可以直接绕过登录验证逻辑，伪装为系统管理身份登录OA系统。

### 影响范围

*   通达OA2017 (V10.x)
    
*   通达OAV11.x < 11.5.200417版本
    

### 漏洞复现

搭建好环境，这里环境使用的是通达OA2017版

![](https://img-blog.csdnimg.cn/20210103154132643.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用脚本获取cookie

```
python3 tongda-oA.py -v 2017 -url http://192.168.10.132:8080/      
如果是V11版本的话      
python3 tongda-oA.py -v 11 -url http://192.168.10.132:8080/         11.x版本
```


![](https://img-blog.csdnimg.cn/20210103154157580.png)

访问链接： [http://192.168.10.132:8080/general/index.php?isIE=0&modify\_pwd=0](http://192.168.10.132:8080/general/index.php?isIE=0&modify_pwd=0 "http://192.168.10.132:8080/general/index.php?isIE=0&modify_pwd=0")

提示用户未登陆

![](https://img-blog.csdnimg.cn/20210103154211829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

用插件将cookie中的PHPSESSID改为脚本获取的

![](https://img-blog.csdnimg.cn/20210103154225249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

刷新一下即可进入后台

![](https://img-blog.csdnimg.cn/20210103154234532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

漏洞环境：[通达OA2017；通达OA2017\_通达oa2017,oa通达-其它文档类资源-CSDN下载](https://download.csdn.net/download/qq_36119192/14016066 "通达OA2017；通达OA2017_通达oa2017,oa通达-其它文档类资源-CSDN下载")