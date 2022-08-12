**目录**

[Django配置连接数据库](#t0)

[在Django中操作数据库](#t1)

[原生SQL语句操作数据库](#t2)

[ORM模型操作数据库](#t3)

[增删改查](#t4)

[后台管理](#t5)

[使用后台管理数据库](#t6)

* * *

模型是数据唯一而且准确的信息来源。它包含您正在储存的数据的重要字段和行为。一般来说，每一个模型都映射一个数据库表。

Django配置连接数据库
-------------

在操作数据库之前，首先先要连接数据库。这里我们以配置 MySQL  为例来讲解。 Django  连接数据库，不需要单独的创建一个连接对象。只需要在 settings.py  文件中的DATABASES配置如下：

```
DATABASES = {      
    'default': {      
        'ENGINE': 'django.db.backends.mysql',    #数据库引擎      
        'NAME': 'orm',                           #数据库的名字,这个数据库得存在      
        'USER':'root',                           #数据库用户      
        'PASSWORD':'root',                       #数据库密码      
        'HOST':'localhost',                      #连接的数据地址      
        'PORT':'3306',                           #端口号      
    }      1
}
```


在Django中操作数据库
-------------

在 Django 中操作数据库有两种方式。第一种方式就是使用原生 SQL语句操作，第二种就是使用 ORM模型来操作。

### 原生SQL语句操作数据库

在 Django 中使用原生 sql  语句操作其实就是使用 python db api  的接口来操作。如果你的 mysql  驱动使用的是 pymysql  ，那么你就是使用  pymysql  来操作的，只不过 Django  将数据库连接的这一部分封装好了，我们只要在 settings.py  中配置好了数据库连接信息后直接使用 Django  封装好的接口就可以操作了。示例代码如下：

```
# 使用django封装好的connection对象，会自动读取settings.py中数据库的配置信息      
from django.db import connection       
cursor = connection.cursor()   # 获取游标对象      
cursor.execute("select * from book")  # 拿到游标对象后执行sql语句      
rows = cursor.fetchall()  # 获取所有的数据       
for row in rows:  # 遍历查询到的数据      
    print(row)
```


以上的 execute  以及 fetchall  方法都是 Python DB API  规范中定义好的。任何使用 Python  来操作 MySQL  的驱动程序都应该遵循这个规范。所以不管是使用 pymysql  或者是 mysqlclient  或者是 mysqldb  ，他们的接口都是一样的。

**Python DB API下规范下cursor对象常用接口**：

*   description  ：如果 cursor  执行了查询的 sql  代码。那么读取 cursor.description  属性的时候，将返回一个列表，这个列表中装的是元组，元组中装的分别是 (name,type\_code,display\_size,internal\_size,precision,scale,null\_ok)  ，其中 name  代表的是查找出来的数据的字段名称，其他参数暂时用处不大。
*   rowcount  ：代表的是在执行了 sql  语句后受影响的行数。
*   close  ：关闭游标。关闭游标以后就再也不能使用了，否则会抛出异常。
*   execute(sql\[,parameters\])  ：执行某个 sql  语句。如果在执行 sql  语句的时候还需要传递参数，那么可以传给 parameters  参数。示例代码如下：cursor.execute("select \* from article where id=%s",(1,))
*   fetchone  ：在执行了查询操作以后，获取第一条数据。
*   fetchmany(size)  ：在执行查询操作以后，获取多条数据。具体是多少条要看传的 size  参数。如果不传 size  参数，那么默认是获取第一条数据。
*   fetchall  ：获取所有满足 sql  语句的数据

### ORM模型操作数据库

**ORM**  ，全称 Object Relational Mapping （对象关系映射），通过 ORM  我们可以通过类的方式去操作数据库，而不用再写原生的SQL语句。通过把表映射成类，把行作实例，把字段作为属性， ORM  在执行对象操作的时候最终还是会把对应的操作转换为数据库原生语句。

*   每个模型都是一个 Python 的类，这些类继承 [`django.db.models.Model`](../../ref/models/instances.html#django.db.models.Model)
*   模型类的每个属性都相当于一个数据库的字段。

综上诉说，Django 给你一个自动生成访问数据库的 API

**使用 ORM  有许多优点：**

*   易用性：使用 ORM  做数据库的开发可以有效的减少重复SQL语句的概率，写出来的模型也更加直观、清晰。
*   性能损耗小： ORM  转换成底层数据库操作指令确实会有一些开销。但从实际的情况来看，这种性能损耗很少（不足5%），只要不是对性能有严苛的要求，综合考虑开发效率、代码的阅读性，带来的好处要远远大于性能损耗，而且项目越大作用越明显。
*   设计灵活：可以轻松的写出复杂的查询。
*   可移植性： Django  封装了底层的数据库实现，支持多个关系数据库引擎，包括流行的 MySQL  、 PostgreSQL  和 SQLite  ，可以非常轻松的切换数据库。

**创建ORM模型：**  
ORM  模型一般都是放在 app  的 models.py  文件中。每个 app  都可以拥有自己的模型。并且如果这个模型想要映射到数据库中，那么这个 app  必须要放在 settings.py  的 INSTALLED\_APP  中进行安装。以下是写一个简单的 ORM  模型。示例代码如下：

```
#models.py文件      
from django.db import models      
#定义一个Book类，对应于数据库中的 orm_book　表      
class Book(models.Model):      
    name = models.CharField(max_length=20,null=False)      
    author = models.CharField(max_length=20,null=False)      
    pub_time = models.DateTimeField(default=datetime.now)      
    price = models.FloatField(default=0)
```


以上便定义了一个模型。这个模型继承自 django.db.models.Model  ，如果这个模型想要映射到数据库中，就必须继承自这个类。这个模型以后映射到数据库中，表名是模型名称的小写形式，为 book  。在这个表中，有四个字段，一个为 name  ，这个字段是保存的是书的名称，是 varchar  类型，最长不能超过20个字符，并且不能为空。第二个字段是作者名字类型，同样也是varchar  类型，长度不能超过20个。第三个是出版时间，数据类型是 datetime  类型，默认是保存这本书籍的时间。第四个是这本书的价格，是浮点类型。还有一个字段我们没有写，就是主键 id  ，在 django  中，如果一个模型没有定义主键，那么将会自动生成一个自动增长的 int  类型的主键，并且这个主键的名字就叫做 id  。 

**映射模型到数据库中：**  
将 ORM  模型映射到数据库中，总结起来就是以下几步：

1.  在 settings.py  中，配置好 DATABASES  ，做好数据库相关的配置。
2.  在 app  中的 models.py  中定义好模型，这个模型必须继承自 django.db.models  。
3.  将这个 app  添加到 settings.py  的 INSTALLED\_APP  中。
4.  在命令行终端，进入到项目所在的路径，然后执行命令 **python manage.py makemigrations**  来生成迁移脚本文件。
5.  同样在命令行中，执行命令 **python manage.py migrate**  来将迁移脚本文件映射到数据库中。

![](https://img-blog.csdnimg.cn/20190221140818829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到除了我们创建的表之外，还有其他的django默认创建的表

![](https://img-blog.csdnimg.cn/20190221141255624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **增删改查**

**增加**

```
from .models import  Book      
from django.http import HttpResponse       
#使用ORM添加一条数据到数据库中      
def index(request):      
    book=Book(name="西游记",author="吴承恩",price=100)      
    book.save()      
    return HttpResponse("添加成功")       1
#或者这样      1
def index(request):      1
    Book.objects.create(name="西游记",author="吴承恩",price=100)
```


**删**

```
#根据主键删除  pk:primary key      
book=Book.objects.get(pk=1)      
book.delete()      
return HttpResponse("删除成功")       
#根据字段删除      
book=Book.objects.filter(name="西游记")      
book.delete()      
return HttpResponse("删除成功")
```


**改**

```
#根据主键修改      
book=Book.objects.get(pk=1)      
book.price=200      
book.save()      
return HttpResponse("修改成功")
```


**查**

```
#根据主键查询      
#books = Book.objects.all()         查询所有数据      
book=Book.objects.get(pk=1)      
return HttpResponse(book)   # 返回的是 Book object (3)       
#根据字段查询  使用filter返回的是一个QuserSet对象，这个对象类似于列表，我们可以使用 .[0]来表示第一条数据，以此类推      
book=Book.objects.filter(name="西游记")          
return HttpResponse(book)    # 返回的是 <QuerySet [<Book: Book object (3)>]>
```


后台管理
----

django默认为我们创建了后台管理的页面

```
urlpatterns = [      
    path('admin/', admin.site.urls),      
]
```


现在我们要为django后台创建一个用户：**python manage.py  createsuperuser**

![](https://img-blog.csdnimg.cn/20190221154313495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问 http://127.0.0.1/admin即可

![](https://img-blog.csdnimg.cn/20190221154347429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后使用刚刚创建的用户名和密码登录即可

![](https://img-blog.csdnimg.cn/20190221154652113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用后台管理数据库

我们在应用下的admin.py中写入如下：

```
from django.contrib import admin      
from ORM.models import  Book      
admin.site.register(Book)
```


然后再访问后台页面，可以看到如下。我们就可以用后台页面管理我们的数据库了

![](https://img-blog.csdnimg.cn/20190221155446777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)