上次无意之中看到一个网站，里面全是美女的照片，我就心想，哪天有时间了得把这网站的所有美女照片都得爬下来。今天有时间，写了点代码，爬去了网站的所有照片。附上战果！图片实在是太多了，爬半个多小时

![](https://img-blog.csdnimg.cn/20181112155231524.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181112155304976.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181112155451684.png)

先附上所有的源代码：

```
# -*- coding: utf-8 -*-      
"""      
Created on Fri Nov  9 17:07:44 2018      
@author: 小谢      
"""      
import requests      
from bs4 import BeautifulSoup      
import os      
import random      1
import csv      1
import time      1
urls=[]      1
urlls=[]      1
datas=[]      1
i=0      1
def Download(name,url,dirname):      1
    dir=dirname+"//"      1
    path=os.path.join(dir,name)      1
    response=requests.get(url)      2
    try:      2
        with open(path,"wb") as f:      2
            f.write(response.content)      2
            f.close()      2
            global i      2
            i=i+1      2
    except Exception as e:      2
        print(e)      2
#获取每一个分类的URL和名字      2
def Geturl():      3
    resp=requests.get("http://www.27270.com/ent/meinvtupian/")      3
    resp.encoding="gbk"  #设置网页编码      3
    html=resp.text      3
    soup=BeautifulSoup(html,"html.parser")      3
    divSoup1=soup.find("div",attrs={"id":"NewTagListBox"})      3
    aas=divSoup1.find_all("a")      3
    for a in aas:      3
        tup=(a['href'],a.string)      3
        urls.append(tup)  #将主页面的各个分栏的链接和名字加入urls元组中      3
def GetImages(url,dirname):      4
    print("*"*50)      4
    if os.path.exists(dirname):      4
        pass      4
    else:      4
        os.mkdir(dirname)   #创建目录      4
    try:      4
        resp=requests.get(url)      4
        resp.encoding="gbk"  #设置网页编码      4
        html=resp.text      4
        soup=BeautifulSoup(html,"html.parser")      5
        divSoup=soup.find("ul",attrs={'class':'w110 oh Tag_list'})      5
        lis=divSoup.find_all("li")      5
        fp=open("meinv.csv","a",newline="")      5
        csv_writer=csv.writer(fp)      5
        for li in lis:      5
            img=li.find("img")      5
            alt=img['alt']      5
            name=alt+".jpg"      #图片的名字      5
            src=img['src']       #图片的下载地址      5
            tup=(name,src,dirname)      6
            Download(name,src,dirname)      6
            csv_writer.writerow(tup)      6
            print(tup)      6
            datas.append(tup)            #Download(data[0],data[1],dirname)      6
        fp.close()      6
    except Exception as e:      6
        print(e)      6
def GetUrls():      6
    Geturl()  #获取所有分栏的页面      6
    for url in urls:      7
        ur=url[0][:-5]    #将每个分栏的url链接去除最后的 .html      7
        for i in range(11):      7
            i+=1      7
            if i==1:      7
                uuu=ur+".html"      7
                a=(uuu,url[1])      7
                urlls.append(a)      7
            else:      7
                uuu=ur+"_"+str(i)+".html"      7
                a=(uuu,url[1])      8
                urlls.append(a)      8
def main():      8
    GetUrls()  #获取所有页面的url      8
    for ur in urlls:      8
        print(ur[0],ur[1])      8
        GetImages(ur[0],ur[1])      8
        time.sleep(3)  #没抓取一个页面延时3秒      8
if __name__=='__main__':      8
    start=time.time()      8
    main()      9
    end=time.time()      9
    print("一共爬去了%s张照片，一共花费了%s的时间"%(str(i),(end-start)))
```


网站链接：[http://www.27270.com/ent/meinvtupian/](http://www.27270.com/ent/meinvtupian/)

![](https://img-blog.csdnimg.cn/20181109220005590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

爬取网站的第一步，就是先分析网站的结构。我们可以看到，上面这里有分类

![](https://img-blog.csdnimg.cn/20181109221656681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们右键检查元素，发现这些分类都有规律

![](https://img-blog.csdnimg.cn/20181109221811980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们写一个函数获得每个分类的链接和名字，将链接和名字以元组的形式存储在我们的全局变量 urls中

```
def Geturl():      
    resp=requests.get("http://www.27270.com/ent/meinvtupian/")      
    resp.encoding="gbk"  #设置网页编码      
    html=resp.text      
    soup=BeautifulSoup(html,"html.parser")      
    divSoup1=soup.find("div",attrs={"id":"NewTagListBox"})      
    aas=divSoup1.find_all("a")      
    for a in aas:      
        tup=(a['href'],a.string)      1
        urls.append(tup)  #将主页面的各个分栏的链接和名字加入urls元组中
```


然后我们开始分析每一个分类的规律了，每一个分类都有很多栏，而每一个栏的url都有规律。

![](https://img-blog.csdnimg.cn/20181109222523993.png)

```
http://www.27270.com/tag/875.html      
http://www.27270.com/tag/875_2.html      
http://www.27270.com/tag/875_3.html      
......
```


所有我们得构造每一个分类的链接，因为每一个分类的栏目数量不同，所以我们选了最多的14。将每一分类的每一页的每张图片的url加入urlls列表中

```
def GetUrls():      
    Geturl()  #获取所有分栏的页面      
    for url in urls:      
        ur=url[0][:-5]    #将每个分栏的url链接去除最后的 .html      
        for i in range(14):      
            i+=1      
            if i==1:      
                uuu=ur+".html"      
                a=(uuu,url[1])      1
                urlls.append(a)      1
            else:      1
                uuu=ur+"_"+str(i)+".html"      1
                a=(uuu,url[1])      1
                urlls.append(a)
```


接下来的函数是获取图片的URL和名字，

```
def GetImages(url,dirname):      
    print("*"*50)      
    if os.path.exists(dirname):      
        pass      
    else:      
        os.mkdir(dirname)   #创建目录      
    try:      
        resp=requests.get(url)      
        resp.encoding="gbk"  #设置网页编码      1
        html=resp.text      1
        soup=BeautifulSoup(html,"html.parser")      1
        divSoup=soup.find("ul",attrs={'class':'w110 oh Tag_list'})      1
        lis=divSoup.find_all("li")      1
        fp=open("meinv.csv","a",newline="")      1
        csv_writer=csv.writer(fp)      1
        for li in lis:      1
            img=li.find("img")      1
            alt=img['alt']      1
            name=alt+".jpg"      #图片的名字      2
            src=img['src']       #图片的下载地址      2
            tup=(name,src,dirname)         2
            Download(name,src,dirname)  #下载图片      2
            csv_writer.writerow(tup)  #写入文件中      2
            print(tup)      2
            datas.append(tup)            #Download(data[0],data[1],dirname)      2
        fp.close()      2
    except Exception as e:      2
        print(e)
```


最后这个函数是下载图片

```
def Download(name,url,dirname):      
    dir=dirname+"//"      
    path=os.path.join(dir,name)      
    response=requests.get(url)      
    try:      
        with open(path,"wb") as f:      
            f.write(response.content)      
            f.close()      
            global i      1
            i=i+1   #每下载一张图片加1      1
    except Exception as e:      1
        print(e)
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-166)[网络爬虫](https://edu.csdn.net/skill/python/python-3-166)[动态渲染页面爬取](https://edu.csdn.net/skill/python/python-3-166)84853 人正在系统学习中