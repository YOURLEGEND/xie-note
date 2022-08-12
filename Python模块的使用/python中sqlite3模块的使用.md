SQLite
------

[SQLite](https://so.csdn.net/so/search?q=SQLite&spm=1001.2101.3001.7020)是一款轻型的关系型数据库管理系统，它包含在一个相对小的C库中。它是D.RichardHipp建立的公有领域项目。它的设计目标是嵌入式的，而且已经在很多嵌入式中使用了它，它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就够了。它能够支持Windows/Linux/Unix等等主流的操作系统，同时能够跟很多程序语言相结合，比如 Tcl、C#、PHP、Java等，还有ODBC接口，同样比起Mysql、PostgreSQL这两款开源的世界著名数据库管理系统来讲，它的处理速度比他们都快。SQLite第一个版本诞生于2000年5月。 至2019年已经有19个年头，SQLite也迎来了一个版本 SQLite 3已经发布。

```
import sqlite3       
conn=sqlite3.connect("test.db")   #打开数据库，如果存在则返回一个连接对象，如果不存在则会创建一个数据库      
c=conn.cursor()          #获得一个操作句柄      
c.execute('''CREATE TABLE COMPANY      
       (ID INT PRIMARY KEY     NOT NULL,      
       NAME           TEXT    NOT NULL,      
       AGE            INT     NOT NULL,      
       ADDRESS        CHAR(50),      1
       SALARY         REAL);''')              #执行sql语句      1
conn.commit()            #提交      1
conn.close()             #关闭连接
```


更多，参考文章：[https://www.runoob.com/sqlite/sqlite-python.html](https://www.runoob.com/sqlite/sqlite-python.html)