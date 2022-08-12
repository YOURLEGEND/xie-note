**目录**

[连接数据库](#t0)

[增](#t1)

[删](#t2)

[改](#t3) 

[查](#t4)

* * *

我们使用Mysql数据库，因为它是开源的，免费的。

python中使用mysql-connector模块来连接mysql的，默认是没有该模块的，需要我们自己安装：pip  install  mysql-connector

连接数据库
-----

使用 connect 方法连接数据库，参数是我们主机ip、数据库的用户名和密码

```
>>import mysql.connector      
>>conn=mysql.connector.connect( host='localhost', user='root', passwd='root')        
>>print(conn)      
<mysql.connector.connection.MySQLConnection object at 0x000001D87739AF98>       
#还可以指定port 、db 、charset 等参数      
mydb=mysql.connector.connect( host='localhost', user='root', passwd='root',port=3306,db="test",charset="utf8")  #选定test数据库，因为默认就是3306端口，所以port参数可不写
```


### 增

```
#创建名叫python的数据库      
import mysql.connector      
conn=mysql.connector.connect( host='localhost', user='root', passwd='root')      
mydb=conn.cursor()          #获取到操作数据库的句柄      
mydb.execute("CREATE DATABASE python")  #创建一个名字为 python 的数据库       
#在python数据库下创建名为test的数据表      
import mysql.connector      
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db="python") #选定python数据库      1
mydb=conn.cursor()                 #获得操作数据库的句柄      1
mydb.execute("CREATE TABLE test(id INT(4),name VARCHAR(255))")  #创建test数据表       1
#在python数据库下的test数据表中增加一条数据      1
import mysql.connector      1
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db='python')      1
mydb=conn.cursor()                 #获得操作数据库的句柄      1
mydb.execute("INSERT INTO test value(1,'hello')")    #增加一条记录      1
conn.commit()                      #数据表内容有更新，必须用到该语句       2
#批量插入      2
import mysql.connector      2
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db="python") #选定python数据库      2
mydb=conn.cursor()                 #获得操作数据库的句柄      2
sql="INSERT INTO test (id,name) VALUES (%s,%s)" #sql语句      2
val=[(1,'aaa'),(2,'bbb'),(3,'ccc'),(4,'ddd')]      2
mydb.executemany(sql,val)         #执行多条语句      2
conn.commit()                    #数据表内容有更新，必须使用到该语句      2
print("更有%s行发生改变！"%mydb.rowcount)  #mydb.rowcount返回发生改变的行数
```


### 删

```
#删除python数据库下的test数据表中的id=1的数据      
import mysql.connector      
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db='python')      
mydb=conn.cursor()                 #获得操作数据库的句柄      
mydb.execute('DELETE FROM test WHERE id=1')    #删除python数据库下的test数据表中id=1的数据       
#删除python数据库下的test数据表      
import mysql.connector      
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db='python')      1
mydb=conn.cursor()                 #获得操作数据库的句柄      1
mydb.execute('DROP TABLE test')    #删除python数据库下的test数据表       1
#删除python数据库      1
import mysql.connector      1
conn=mysql.connector.connect( host='localhost', user='root', passwd='root')      1
mydb=conn.cursor()                 #获得操作数据库的句柄      1
mydb.execute('DROP DATABASE python')    #删除python数据库
```


### 改 

```
#修改python数据库下test数据表中id=1的数据，将其name修改      
import mysql.connector      
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db='python')      
mydb=conn.cursor()                 #获得操作数据库的句柄      
mydb.execute("UPDATE test set name='world' where id=1")    #增加一条记录      
conn.commit()                        #数据表内容有更新，必须用到该语句           
#修改python数据库下test数据表的名字为test2      1
import mysql.connector      1
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db='python')      1
mydb=conn.cursor()                 #获得操作数据库的句柄      1
mydb.execute("ALTER table test rename to test2")    #将test数据表改名为test2
```


### 查

```
#查询所有数据库      
import mysql.connector      
conn=mysql.connector.connect( host='localhost', user='root', passwd='root')      
mydb=conn.cursor()                 #获得操作数据库的句柄      
mydb.execute("show databases;")    #显示所有数据库      
for row in mydb:      
    print(row)       
#查询python数据库下的所有数据表      1
import mysql.connector      1
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db='python')      1
mydb=conn.cursor()                 #获得操作数据库的句柄      1
mydb.execute("show tables;")       #显示 python 数据库下的所有的表，返回一个元组型数据      1
for row in mydb:      1
    print(row)       1
#查询python数据库下的test数据表中的数据      1
import mysql.connector      2
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db="python",charset="utf8")  #选择test数据库      2
mydb=conn.cursor()                 #获得操作数据库的句柄      2
mydb.execute("select*from test")  #执行查询语句，相当于查询 python.test 表中的数据      2
result=mydb.fetchall()             #获取所有返回的结果，返回的是列表型数据       2
#查询python数据库下的test数据表中的数据前3条数据      2
#查询python数据库下的test数据表中的数据      2
import mysql.connector      2
conn=mysql.connector.connect( host='localhost', user='root', passwd='root',db="python",charset="utf8")  #选择test数据库      2
mydb=conn.cursor()                 #获得操作数据库的句柄      3
mydb.execute("select*from test")  #执行查询语句，相当于查询 python.test 表中的数据      3
result=mydb.fetchmany(3)             #返回前3条结果
```


相关文章：[SQL语句](https://blog.csdn.net/qq_36119192/article/details/82875868)