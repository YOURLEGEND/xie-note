**目录**

[注释符](#t0)

[数据库端口](#t1)

[数据库文件后缀名](#t2)

[特有的数据库](#t3)

[查看当前用户或权限](#t4)

[ASCII转换函数](#t5)

* * *

MySQL、SQLServer、Oracle、[PostgreSQL](https://so.csdn.net/so/search?q=PostgreSQL&spm=1001.2101.3001.7020)、Access五种数据库应该是目前市面上最流行的数据库了。我们进行渗透测试，碰到最多的也是这几种数据库。本文就这几种数据库在注入时的相同点和不同的做一下统计。

### 注释符

*   MySQL:  单行：#  、多行： /\*\*/
*   SQLServer：单行： --    多行： /\*\*/
*   Oracle：单行： --    多行： /\*\*/
*   PostgreSQL：单行：--    多行： /\*\*/
*   Access：无注释符

所以，除了Access无注释符。

MySQL的单行注释符为：#  其他三种数据库单行注释符均为：--

其余四种数据库的多行注释符均为/\*\*/

### 数据库端口

*   MySQL： 3306
*   SQLServer：  1433
*   Oracle：1521
*   PostgreSQL：5432
*   Access： 属于文件型数据库，所以不需要端口号

### 数据库文件后缀名

*   MySQL：数据文件：. myd 、索引文件：. MYI  、表定义文件：. frm
*   SQLServer： .mdf
*   Oracle：.dbf 和  .ora
*   PostgreSQL：无后缀名
*   Access：早期是 .mdb ，后来是 .accdb

### 特有的数据库

*   MySQL： information\_schema
*   SQLServer：sysobjects
*   Oracle：dual
*   PostgreSQL：
*   Access：msysobjects

### 查看当前用户或权限

**MySQL**

```
查询当前用户      
select user();      
select substring_index(user(), '@', 1) ;      
查询当前用户的权限      
select * from mysql.user where user = substring_index(user(), '@', 1) ;
```


![](https://img-blog.csdnimg.cn/20200104155048702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200104155151404.png)

**Access**数据库是文件类型数据库，没有用户和权限的概念

**SQLServer**

```
判断是否是SA权限      
select is_srvrolemember('sysadmin')           
判断是否是db_owner权限        
select is_member('db_owner')      
判断是否是public权限      
select is_srvrolemember('public')
```


![](https://img-blog.csdnimg.cn/20200105114756704.png)

**Oracle**

```
查看当前用户      
select * from user_users;      
查看当前用户拥有的角色           
select * from session_roles;      
查看当前用户拥有的权限      
select * from session_privs;
```


![](https://img-blog.csdnimg.cn/20200104154851764.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**PostgreSQL**

```
select user                       #查看用户      
select current_user               #查看当前用户
```


![](https://img-blog.csdnimg.cn/20200305220409897.png)![](https://img-blog.csdnimg.cn/20200305220426596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ASCII转换函数

*   MySQL：select  char(97)
*   SQLServer：select  char(97)
*   Oracle：select  chr(97)  from dual
*   PostgreSQL：select  chr(97) 
*   Access：select  chr(97) 

**MySQL**

![](https://img-blog.csdnimg.cn/20200305221856776.png)

**SQLServer**

![](https://img-blog.csdnimg.cn/20200305222010175.png)

**Oracle**

![](https://img-blog.csdnimg.cn/20200305222209451.png)

**PostgreSQL**

![](https://img-blog.csdnimg.cn/20200305222350386.png)

**Access**

select  chr(97)&chr(100)&chr(109)&chr(105)&chr(110)