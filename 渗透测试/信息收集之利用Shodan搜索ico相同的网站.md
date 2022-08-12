在进行红蓝对抗的时候，想查找某个集团的[子网](https://so.csdn.net/so/search?q=%E5%AD%90%E7%BD%91&spm=1001.2101.3001.7020)站，于是，我们可以利用Shodan搜索相同 ico 的网站。

```
import mmh3      
import requests      
import urllib3      
urllib3.disable_warnings()      
from requests.packages.urllib3.exceptions import InsecureRequestWarning      
requests.packages.urllib3.disable_warnings(InsecureRequestWarning)      
response = requests.get('https://www.baidu.com/favicon.ico',verify=False)      
favicon = response.content.encode('base64')      
hash = mmh3.hash(favicon)      1
print "http.favicon.hash:"+str(hash)
```


![](https://img-blog.csdnimg.cn/2020042717411450.png)

然后Shodan搜索

```
http.favicon.hash:-1507567067
```


![](https://img-blog.csdnimg.cn/20200427174535110.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这种查找资产的方法对于大公司效果比较好。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)