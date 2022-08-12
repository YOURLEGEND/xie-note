**目录**

[xlrd模块](#t0)

[打开.xls格式的文档](#t1)

[获取.xls文档的列](#t2)

[打印第一行的数据](#t3)

[打印所有数据](#t4)

[将.xls格式的文档转换为 .txt格式的](#t5)

[日期读出来是数字进行格式化](#%C2%A0%E6%97%A5%E6%9C%9F%E8%AF%BB%E5%87%BA%E6%9D%A5%E6%98%AF%E6%95%B0%E5%AD%97%E8%BF%9B%E8%A1%8C%E6%A0%BC%E5%BC%8F%E5%8C%96)

* * *

xlrd模块
------

xlrd模块是用来处理 .xls 格式的文档

### **打开.xls格式的文档**

```
import xlrd      
data=xlrd.open_workbook('test.xls')      
print(data)       
sheet1=data.sheet_by_index(0)      
nrows=sheet1.nrows           #行数      
ncols=sheet1.ncols           #列数      
print(nrows,ncols)       1
for iRow in range(1,nrows):         #依次打印出每行的每一个字段的数据      1
	for iCol in range(ncols):      1
		data=sheet1.cell_value(iRow,iCol)      1
		print(data)       1
##############################################      1
<xlrd.book.Book object at 0x000001BAC978E2B0>      1
6 8
```


### **获取.xls文档的列**

```
import xlrd      
data=xlrd.open_workbook('test.xls')      
line=data.sheets()      
print(line)           #打印所有的列      
print(line[1])        #打印第2列       
可以看到有三列      
#############################################################################      1
[<xlrd.sheet.Sheet object at 0x000001BACDE2EE10>, <xlrd.sheet.Sheet object at 0x000001BACDC55860>, <xlrd.sheet.Sheet object at 0x000001BACD7692E8>]      1
<xlrd.sheet.Sheet object at 0x000001BACDC55860>
```


### **打印第一行的数据**

```
import xlrd      
data=xlrd.open_workbook('test.xls')      
line=data.sheets()[0]      
print(line.row_values(0))       
################################      
['教工号', '单位', '姓名']
```


### **打印所有数据**

```
import xlrd      
data=xlrd.open_workbook('test.xls')      
line=data.sheets()[0]      
rows=line.nrows      
for i in range(rows):      
    print(int(line.row_values(i)[0]))
```


### **将.xls格式的文档转换为 .txt格式的**

```
import xlrd      
datas=xlrd.open_workbook('test.xls')      
allline=datas.sheets()     #数据中的所有列数      
lines=len(allline)      
line=datas.sheets()[0]   #选定第一列      
rows=line.nrows          #数据中的列数      
print("这个文档有%s列，有%s行"%(lines,rows))      
try:      
    with open("test.txt","w",encoding='utf-8') as f:      1
        for i in range(rows):      1
            for j in range(lines):      1
                data=line.row_values(i)[j]         1
                f.write(str(data))       1
                f.write("  ")              #用空格隔开      1
            f.write("\n")                  #每一行加换行符      1
except Exception as e:      1
    print("异常对象的类型是:%s"%type(e))      1
    print("异常对象的内容是:%s"%e)      1
finally:      2
    f.close()
```


###  日期读出来是数字进行格式化

我们现在有如下的文件

![](https://img-blog.csdnimg.cn/20200323171332293.png)

实际打印出来是这样的

![](https://img-blog.csdnimg.cn/2020032317140279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

读取excel表格的数据类型有6种：

*   0 empty
*   1 string
*   2 number
*   3 date
*   4 boolean
*   5 error

```
import xlrd      
import os      
import time      
from datetime import datetime      
from xlrd import xldate_as_tuple      
wb=xlrd.open_workbook("test.xls")      
sheet1=wb.sheet_by_index(0)      
nrows=sheet1.nrows      
ncols=sheet1.ncols       1
def isVaildDate(sDate):      1
    try:      1
        if ":" in sDate:      1
            time.strptime(sDate, "%Y-%m-%d %H:%M:%S")      1
        else:      1
            time.strptime(sDate, "%Y-%m-%d")      1
        return True      1
    except:      1
        return False       2
for iRow in range(0,nrows):      2
	for iCol in range(ncols):      2
		sCell=sheet1.cell_value(iRow,iCol)      2
		print(sCell)      2
		ctype=sheet1.cell(iRow,iCol).ctype      2
		print("ctype类型为：%s"%ctype)       2
		if ctype == 3:      2
			date = datetime(*xldate_as_tuple(sCell, 0))      3
			if len(str(sCell).split(".")[1])>1:      3
				cell = date.strftime('%Y-%m-%d %H:%M:%S')      3
			else:      3
				cell = date.strftime('%Y-%m-%d')      3
			print(cell)      3
		elif ctype == 1:      3
			if isVaildDate(sCell):      3
				t1 = time.strptime(sCell, "%Y-%m-%d")      3
				sDate = changeStrToDate(t1,"yyyy-mm-dd")      3
				print(sDate)      4
		print("*"*10)
```


![](https://img-blog.csdnimg.cn/20200323172525462.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-6)[预备知识](https://edu.csdn.net/skill/python/python-3-6)[模块管理](https://edu.csdn.net/skill/python/python-3-6)84711 人正在系统学习中