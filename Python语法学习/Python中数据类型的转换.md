bytes<-->str
------------

```
a="hello"  #str字符型       
#str转换为bytes类型      
b=a.encode("utf-8")        
或  b=bytes(a.encode("utf-8") )       
或  b=bytes(a,encoding="utf-8")       
#bytes类型转换为str类型      
c=b.decode("utf-8")  或  c=str(b,encoding="utf-8")
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-14)[基础语法](https://edu.csdn.net/skill/python/python-3-14)[数据类型](https://edu.csdn.net/skill/python/python-3-14)84785 人正在系统学习中