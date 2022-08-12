**目录**

[钉钉设置机器人](#t0)

[Python脚本实现推送钉钉消息](#t1)

[消息类型](#t2)

[text类型](#t3)

[link类型](#t4)

[markdown类型](#t5)

[整体跳转ActionCard类型](#t6)

[独立跳转ActionCard类型](#t7)

[FeedCard类型](#t8)

* * *

钉钉设置机器人
=======

首先在钉钉设置钉钉机器人

群设置—> 智能群助手—>添加机器人—>自定义

![](https://img-blog.csdnimg.cn/20200113101234746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200113101426766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

添加完成，得到一个[Webhook](https://so.csdn.net/so/search?q=Webhook&spm=1001.2101.3001.7020) API地址

![](https://img-blog.csdnimg.cn/2020011310151923.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Python脚本实现推送钉钉消息
================

钉钉官方给出了机器人接口的文档：[https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq](https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq)

但是该文档只实现了JAVA和PHP的示例。以下是python脚本的示例。

```
# -*- coding: utf-8 -*-      
"""      
Created on Mon Jan 13 11:18:59 2020      
python3      
@author: 谢公子      
"""      
import json      
import requests       1
def send_msg(url,data):      1
    headers = {'Content-Type': 'application/json;charset=utf-8'}      1
    r = requests.post(url,data = json.dumps(data),headers=headers)      1
    return r.text      1
if __name__ == '__main__':      1
   data = {      1
            "msgtype": "text",       1
            "text": {      1
                "content": "hello,word!test"      1
            },       2
        }      2
   url = 'https://oapi.dingtalk.com/robot/send?access_token=xx'  #此处为钉钉机器人的webhook地址      2
   print(send_msg(url,data))
```


![](https://img-blog.csdnimg.cn/20200113112006704.png)

![](https://img-blog.csdnimg.cn/20200113112017959.png)

如果要实现签名认证，如下

![](https://img-blog.csdnimg.cn/20200113161619728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
# -*- coding: utf-8 -*-      
"""      
Created on Mon Jan 13 11:18:59 2020      
python3      
@author: mi      
"""      
import json      
import requests      
import time      1
import hmac      1
import hashlib      1
import base64      1
import urllib      1
from urllib import parse       1
def send_msg(url,data):      1
    headers = {'Content-Type': 'application/json;charset=utf-8'}      1
    r = requests.post(url,data = json.dumps(data),headers=headers)      1
    return r.text      2
def auth(secret):      2
    timestamp = round(time.time() * 1000)      2
    secret = secret        #秘钥      2
    secret_enc = bytes(secret.encode('utf-8'))      2
    string_to_sign = '{}\n{}'.format(timestamp, secret)     #把 timestamp+"\n"+密钥 当做签名字符串 string_to_sign      2
    string_to_sign_enc = bytes(string_to_sign.encode('utf-8'))      2
    hmac_code = hmac.new(secret_enc, string_to_sign_enc, digestmod=hashlib.sha256).digest()  #使用HmacSHA256算法计算签名，得到 hmac_code      2
    hmac_code_base64=base64.b64encode(hmac_code)  #将hmac_code进行Base64 encode      2
    sign = urllib.parse.quote(hmac_code_base64)   #进行urlEncode，得到最终的签名sign      2
    authlist=[timestamp,sign]      3
    return authlist      3
if __name__ == '__main__':      3
    data = {      3
    "msgtype": "link",       3
    "link": {      3
        "text": "",       3
        "title": "时代的火车向前开",       3
        "messageUrl": "https://www.dingtalk.com/s?__biz=MzA4NjMwMTA2Ng"      3
        }      3
    }      4
    authlist = auth("你的签名")      4
    url = "https://oapi.dingtalk.com/robot/send?access_token=xxx"+"&timestamp="+str(authlist[0])+"&sign="+authlist[1]      4
    print(send_msg(url,data))
```


消息类型
----

### text类型

```
{      
    "msgtype": "text",       
    "text": {      
        "content": "我就是我, 是不一样的烟火@156xxxx8827"      
    },       
    "at": {      
        "atMobiles": [      
            "156xxxx8827",       
            "189xxxx8325"      1
        ],       1
        "isAtAll": false      1
    }      1
}
```


![](https://img-blog.csdnimg.cn/20200113104252954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果图如下

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcuYWxpY2RuLmNvbS90ZnMvVEIxakZwcWFSeFJNS0pqeTBGZFhYYWlmRlhhLTQ5Ny0xMzMucG5n?x-oss-process=image/format,png)

### link类型

```
{      
    "msgtype": "link",       
    "link": {      
        "text": "这个即将发布的新版本，创始人xx称它为“红树林”。      
而在此之前，每当面临重大升级，产品经理们都会取一个应景的代号，这一次，为什么是“红树林”？",       
        "title": "时代的火车向前开",       
        "picUrl": "",       
        "messageUrl": "https://www.dingtalk.com/s?__biz=MzA4NjMwMTA2Ng      
    }      1
}
```


![](https://img-blog.csdnimg.cn/20200113104541907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果图如下

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9kaW5ndGFsa2RvYy5vc3MtY24tYmVpamluZy5hbGl5dW5jcy5jb20vaW1hZ2VzLzAuMC4yMDQvMTU3MDY3OTgyNzI2Ny02MjQzMjE2Yi1kMWMzLTQ4YjctOWIxZS0wZjBiNDIxMWI1MGIucG5n?x-oss-process=image/format,png)

### markdown类型

```
{      
     "msgtype": "markdown",      
     "markdown": {      
         "title":"杭州天气",      
         "text": "#### 杭州天气 @156xxxx8827\n" +      
                 "> 9度，西北风1级，空气良89，相对温度73%\n\n" +      
                 "> ![screenshot](https://gw.alicdn.com/tfs/TB1ut3xxbsrBKNjSZFpXXcXhFXa-846-786.png)\n"  +      
                 "> ###### 10点20分发布 [天气](http://www.thinkpage.cn/) \n"      
     },      1
    "at": {      1
        "atMobiles": [      1
            "156xxxx8827",       1
            "189xxxx8325"      1
        ],       1
        "isAtAll": false      1
    }      1
 }
```


![](https://img-blog.csdnimg.cn/20200113104701575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果图如下

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcuYWxpY2RuLmNvbS90ZnMvVEIxeUwzdGFVZ1FNZUpqeTBGZVhYWE9FVlhhLTQ5Mi0zODAucG5n?x-oss-process=image/format,png)

### 整体跳转ActionCard类型

```
{      
    "actionCard": {      
        "title": "乔布斯 20 年前想打造一间苹果咖啡厅，而它正是 Apple Store 的前身",       
        "text": "![screenshot](@lADOpwk3K80C0M0FoA)       
 ### 乔布斯 20 年前想打造的苹果咖啡厅       
 Apple Store 的设计正从原来满满的科技感走向生活化，而其生活化的走向其实可以追溯到 20 年前苹果一个建立咖啡馆的计划",       
        "hideAvatar": "0",       
        "btnOrientation": "0",       
        "singleTitle" : "阅读全文",      1
        "singleURL" : "https://www.dingtalk.com/"      1
    },       1
    "msgtype": "actionCard"      1
}
```


![](https://img-blog.csdnimg.cn/20200113104744332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果图如下

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcuYWxpY2RuLmNvbS90ZnMvVEIxZWhXQ2lCZkg4S0pqeTFYYlhYYkxkWFhhLTMzNC0yMTgucG5n?x-oss-process=image/format,png)

### 独立跳转ActionCard类型

```
{      
    "actionCard": {      
        "title": "乔布斯 20 年前想打造一间苹果咖啡厅，而它正是 Apple Store 的前身",       
        "text": "![screenshot](@lADOpwk3K80C0M0FoA)       
 ### 乔布斯 20 年前想打造的苹果咖啡厅       
 Apple Store 的设计正从原来满满的科技感走向生活化，而其生活化的走向其实可以追溯到 20 年前苹果一个建立咖啡馆的计划",       
        "hideAvatar": "0",       
        "btnOrientation": "0",       
        "btns": [      1
            {      1
                "title": "内容不错",       1
                "actionURL": "https://www.dingtalk.com/"      1
            },       1
            {      1
                "title": "不感兴趣",       1
                "actionURL": "https://www.dingtalk.com/"      1
            }      1
        ]      1
    },       2
    "msgtype": "actionCard"      2
}
```


![](https://img-blog.csdnimg.cn/20200113105145181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果图如下

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9kaW5ndGFsa2RvYy5vc3MtY24tYmVpamluZy5hbGl5dW5jcy5jb20vaW1hZ2VzLzAuMC4yMDQvMTU3MDY3OTkzOTcyMy1jMWZiNzg2MS01YmNiLTRjMzAtOWUxYi0wMzM5MzJmNmI3MmYucG5n?x-oss-process=image/format,png)

### FeedCard类型

```
{      
    "feedCard": {      
        "links": [      
            {      
                "title": "时代的火车向前开",       
                "messageURL": "https://www.dingtalk.com/s?__biz=MzA4NjMwMTA2Ng==&mid=2650316842&idx=1&sn=60da3ea2b29f1dcc43a7c8e4a7c97a16&scene=2&srcid=09189AnRJEdIiWVaKltFzNTw&from=timeline&isappinstalled=0&key=&ascene=2&uin=&devicetype=android-23&version=26031933&nettype=WIFI",       
                "picURL": "https://www.dingtalk.com/"      
            },      
            {      1
                "title": "时代的火车向前开2",       1
                "messageURL": "https://www.dingtalk.com/s?__biz=MzA4NjMwMTA2Ng==&mid=2650316842&idx=1&sn=60da3ea2b29f1dcc43a7c8e4a7c97a16&scene=2&srcid=09189AnRJEdIiWVaKltFzNTw&from=timeline&isappinstalled=0&key=&ascene=2&uin=&devicetype=android-23&version=26031933&nettype=WIFI",       1
                "picURL": "https://www.dingtalk.com/"      1
            }      1
        ]      1
    },       1
    "msgtype": "feedCard"      1
}
```


![](https://img-blog.csdnimg.cn/20200113104836979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果图如下

![](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZzAxLnRhb2Jhb2Nkbi5jb20vdG9wL2kxL0xCMVIyZXZRVlhYWFhYRGFwWFhYWFhYWFhYWA?x-oss-process=image/format,png)