经常会有这种需求，当用 [nmap](https://so.csdn.net/so/search?q=nmap&spm=1001.2101.3001.7020) 扫描某个大网段的端口开放情况后，生成了xml格式的扫描报告。现在，需要将扫描的结果用excel表格呈现出来，只显示开放的端口号。

我们先来学习一下怎么解析[xml](https://so.csdn.net/so/search?q=xml&spm=1001.2101.3001.7020)文件把。

有这么一个xml文件，名为：test.xml

```
<books>      
    <book id="01">      
        <bookname>C++基础</bookname>      
        <author>张三</author>      
        <price>30</price>      
    </book>      
    <book id="02">      
        <bookname>java基础</bookname>      
        <author>李四</author>      1
        <price>40</price>      1
    </book>      1
    <book id="03">      1
        <bookname>Python高级</bookname>      1
        <author>王五</author>      1
        <price>50</price>      1
    </book>      1
</books>
```


看看我们的python代码

```
# -*- coding: utf-8 -*-      
"""      
Created on Wed Nov 13 17:57:37 2019      
@author: 小谢      
"""      
from xml.dom.minidom import parse       
doc=parse("test.xml")                    #加载xml文件      
root=doc.documentElement                 #获取元素的根节点，得到一个xml.dom.minidom.Element的类      1
print(root,"------",type(root))      1
books=root.getElementsByTagName('book')  #找到root根节点下所有book子节点，得到的是一个数组      1
print(books[0],type(books[0]))          #book数组中每一个元素，都是xml.dom.minidom.Element的类      1
for book in books:                       #遍历book子节点      1
    print("******************BOOK******************")      1
    if book.hasAttribute('id'):             #判断book是否有id属性      1
        print('书的ID是：%s'% book.getAttribute('id'))      1
    ##根据标签名找到，并且输出第一个元素      1
    bookname=book.getElementsByTagName("bookname")[0]   #得到标签名是bookname的第一个元素，返回的是一个xml.dom.minidom.Element的类      1
    print("书名是：%s"%bookname.childNodes[0].data)      #输出标签名的子节点的第一个值，并转为data类型       2
    author=book.getElementsByTagName("author")[0]      2
    print("作者是：%s"%author.childNodes[0].data)       2
    price=book.getElementsByTagName("price")[0]      2
    print("价格是：%s"%price.childNodes[0].data)
```


运行截图

![](https://img-blog.csdnimg.cn/20191115095000262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们现在来一步一步分析代码块。

下面，让我们写一个脚本来解析nmap生成的xml格式的文件，最后生成csv格式的表格。

```
# -*- coding: utf-8 -*-      
"""      
Created on Thu Nov 14 17:51:15 2019      
@author: 小谢      
"""      
from xml.dom.minidom import parse      
import csv       
doc=parse("nmap.xml")                   #先把xml文件加载进来      1
root=doc.documentElement                #获取元素的根节点，得到一个xml.dom.minidom.Element的类      1
hosts=root.getElementsByTagName('host') #找到所有是host的子节点，得到的是一个数组      1
f=open("nmap.csv","a",newline="")       1
csv_writer=csv.writer(f)      1
for host in hosts:      1
    ip_tag=host.getElementsByTagName("address")[0]      1
    ip=ip_tag.getAttribute('addr')      1
    print("ip地址为：%s"%ip)      1
    ports=host.getElementsByTagName("port")        #取得端口      1
    for port in ports:      2
        portid=port.getAttribute("portid")                          #端口号      2
        state_tag=port.getElementsByTagName("state")[0]      2
        if state_tag.getAttribute("state")=="open":                   #如果端口的状态是开放的      2
            print("端口：%s"%portid)      2
            try:      2
                server_tag=port.getElementsByTagName("service")[0]      2
                server_name=server_tag.getAttribute("name")                 #服务名      2
                server_product=server_tag.getAttribute("product")           #服务产品      2
                server_version=server_tag.getAttribute("version")           #服务版本      2
                print("服务名：%s"%server_name)      3
                print("服务产品：%s"%server_product)      3
                print("服务版本：%s"%server_version)      3
                server_product_version=server_product+" "+server_version      3
                row=[ip,portid,server_name,server_product_version]      3
                csv_writer.writerow(row)      3
            except Exception:      3
                row=[ip,portid,"未识别的服务"]      3
                csv_writer.writerow(row)      3
f.close()
```


**运行截图**

![](https://img-blog.csdnimg.cn/20191115104623614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-6)[预备知识](https://edu.csdn.net/skill/python/python-3-6)[模块管理](https://edu.csdn.net/skill/python/python-3-6)84711 人正在系统学习中