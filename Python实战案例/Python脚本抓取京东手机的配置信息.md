以下代码是使用python抓取京东小米8手机的配置信息

首先找到小米8商品的链接：[https://item.jd.com/7437788.html](https://item.jd.com/7437788.html)

然后找到其配置信息的标签，我们找到其配置信息的标签为 <div  class="Ptable"> 

![](https://img-blog.csdn.net/20181014201344748?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后再分析其配置信息的页面的规律，我们发现都是dl中包含了dt和dd，而一个dt对应的一个dd，dt对应的是参数，dd对应的是参数具体的值

![](https://img-blog.csdn.net/20181014202006714?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

下面是源代码

```
import requests      
from bs4 import BeautifulSoup      
from pandas import Series      
from pandas import DataFrame       
response=requests.get("https://item.jd.com/7437788.html")      
html=response.text       1
soup=BeautifulSoup(html,"html.parser")      1
divSoup=soup.find("div",attrs={"class","Ptable"})  ##找到其配置信息的标签       1
data=DataFrame(columns=["参数","值"])      #定义一个二元的DataFrame      1
dls=divSoup.find_all("dl")       1
for dl in dls:      1
    dts=dl.find_all("dt")      1
    dds=dl.find_all("dd")      1
    if len(dts)==len(dds):      2
        for i in range(len(dts)):      2
            f=dts[i].getText();      2
            p=dds[i].getText();      2
            data=data.append(Series([f,p],index=["参数","值"]),ignore_index=True);      2
print(data)
```


这是最终抓取到的配置信息，一共有64行，这里我就不一一列举出来了 

![](https://img-blog.csdn.net/2018101420153580?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)