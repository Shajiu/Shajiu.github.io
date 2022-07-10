---
title: Python字符串速查表
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-08 20:17:13
password:
summary:
tags:   专业
categories: 语言(Python)
---
Python字符串速查表

#### 一、创建字符串
在Python中处理文本数据是使用str对象，也成为字符串，字符串是由Unicode码构成的不可变序列，字符串字面值有多种不同的写法：
- ① 单引号：“允许包含有'双'引号”；
- ② 双引号：“允许包含'单'引号”；
- ③ 三重引号：'""'三重引号"","""三重双引号""";
使用三重引号的字符串可以跨越多行---其中所有的空白字符都将包含在改字符串字面值中。

``` python
s1="lemon"
s2="Python"
s3="""Hello  world"""
print(s1)
print(s2)
print(s3)
# 输出结果如下
lemon
Python
Hello  world
```

#### 二、访问字符串中的值
 字符串(String)支持用切片的方式来访问字符串中的值
 ```Python
s2="Python数据之美"
print(s2[0])
print(s2[5:])
# 输出的结果如下
P
n数据之美
  ```
打印的字符串里包含\，因为它是转义字符，所以打印这字符串，最前面加个r
``` python
print(r".\data")
print(r"D:no")
print("D:\no")
# 输出如下所示
.\data
D:no
D:
o
 ```


 #### 三、字符串格式化
 Python支持格式化字符串的输出，一般有三种方式可以实现，包括format、%、f-string。
 字符串格式化是Python字符串内容的重要组成部分，应用广泛：
- ① format方法

``` Python3
name="Lemon"
age=18
print("My name is {0},age is {1}".format(name,age))
# 输出如下所示：
My name is Lemon,age is 18
```
- ② %方法


``` Python3
name="Lemon"
age=18
print("My name is %s,age is %s"%(name,age))
# 输出如下所示：
My name is Lemon,age is 18
 ```

- ③ f-string方法


``` Python3
name="Lemon"
age=18
# python3.6以上的版本支持如下格式
print(f"My name is {name},age is {age}")
# 输出如下所示：
My name is Lemon,age is 18
 ```

#### 四、数字格式化
在进行字符串格式化时，经常遇到需要将数字转换为字符串，并且要按照某种特定的格式显示。数字格式转为字符串，可以用format，%或f-string方法实现
下面的数字格式化，主要以format方法来举例子。
-  ① 保留两位小数
-  ② 百分比和千分位格式
-  ③ 对齐方式
-  ④ 补零或字符
-  ⑤ 补正负符号

1、保留两位小数:
```python3
n1=3.1415926
n2=31415.926
n3=0.3141
n4=21
print("保留两位小数：%.2f"%(n1))
print("保留两位小数:{:.2f}".format(n1))
print(f"保留两位小数:{n1:.2f}")
# 输出结果为
保留两位小数：3.14
保留两位小数:3.14
保留两位小数:3.14
```

2、百分比和千分位格式
```python3
n1=3.1415926
n2=31415.926
n3=0.3141
n4=21
print("百分比格式:{:.2%}".format(n3))
print("既有千分位分隔符又有小数位:{:,.2f}".format(n2))
 ```
3、对齐方式
字符串对齐格式，设置默认宽度为8
```Python3
n1=3.1415926
n2=31415.926
n3=0.3141
n4=21
print("右对齐:{:>8}".format(n4))
print("左对齐:{:<8}".format(n4))
print("居中{:^8}".format(n4))
```
4、数字补零， 或者补特定符号， 比如‘ x ’
```python3
print(' 左边补零： {:0>4}'.format(n4)) # 左边补0 ， 宽度为4
print(' 右边补x ： {:x<5}'.format(n4)) # 右边补x ， 宽度为5
```
5、带符号保留小数点后两位
``` python3
# "+"
print(' 正数前加正号， 负数前加负:')
print('{:+.2f}'.format(n1))
print('{:+.2f}'.format(n2))
# "-"
print(' 正数前无符号， 负数前加负号:')
print('{:-.2f}'.format(n1))
print('{:-.2f}'.format(n2))
# 空格
print(' 正数前加空格， 负数前加负号:')
print('{: .2f}'.format(n1))
print('{: .2f}'.format(n2))
```
#### 五、字符串的基本运算
- ① 拼接字符串

``` python3
s1 = 'Hello, Dear friends, '
s2 = 'welcome to PyDataLab '
# 字符串拼接
print(s1 + s2)
```
- ② 字符串的大小写转换


``` python3
s="hello, welcome to PyDataLab"
print("每个单词的首字母大写",s.title())
print("段落的首字母都大写",s.capitalize())
print("所有字母小写",s.lower())
print("所有字母大写",s.upper())
print("大写转小写，小写转大写",s.swapcase())
```

#### 六、字符串分割
① 字符串的分割，通常有split 和partition 系列方法。
- split 系列：split 系列方法包括split() 、rsplit() 、splitlines() 等。split() 将一个字符串分隔成多个字符串组成的列表，不含分隔符; rsplit() 的功能与split() 类似，只不过是从字符串最后面开始分割；splitlines() 按照(\n, \r, \r\n 等) 分隔，分割成列表。


``` python3
s = 'hello, welcome to PyDataLab'
print("按照空格分割:",s.split())
print("按照某个字符分割",s.split("e"))
print("按照某个字符分割，只分割一次",s.split("e",1))
print("按照某个字符分割，只分割一次",s.rsplit("e",1))
print("去掉换行符， 以换行符分割成列表","1+2\n3+4".splitlines())
```
- partition 系列：partition 系列方法包括partition() 和rpartition() 。partition() 根据指定的分隔符(sep) 将字符串进行分割，从字符串左边开始索引分隔符sep, 索引到则停止索引，返回的是一个包含三个元素的元组(tuple)，即(head, sep, tail)。rpartition() 的功能与partition() 类似，只不过是从字符串最后面开始分割。


``` python3
s = 'hello, welcome to PyDataLab'
print("遇到第一个分隔符后就停止索引",s.partition("e"))
print("没有遇到分隔符， 返回原字符串和两个空字符串",s.partition("f"))
print("遇到第一个分隔符后就停止索引",s.rpartition("e"))
print("没有遇到分隔符， 返回两个空字符串和原字符串",s.rpartition("f"))
 ```


|方法|返回类型|是否包含分隔符|
| ----- | ------------------ | --- |
| split | 系列方法list(列表) | 否  |
|partition 系列方法| tuple(元组) |是|

② 去除字符串两边的空白
``` python3
s = 'hello, welcome to PyDataLab'
print("去除字符串两端的空白",s.rsplit())
print("去除字符串左侧的空白",s.lstrip())
```
③ 字符串编码
```python3
s1 = 'Python 数据之道'
s2 = s1.encode(encoding='utf-8')
print("编码encoder",s2)
s3=s2.decode(encoding="utf-8")
print("解码decoder",s3)
 ```

#### 八、is相关方法
相关的方法有isdigit() , isdecimal() , isnumeric()
``` python3
s4 = 'Hi PyDataLab'
s5 = '2021'
s6 = "Lemon2069"
print(s4.isdigit())
print(s5.isdecimal())
print(s6.isnumeric())
```
|方法| True|False|Error|
| --- | --- | --- | --- |
| isdigit()    |  Unicode 数字，byte 数字（单字节），全角数字（双字节），罗马数字   | 汉字数字    |无     |
|isdecimal()| Unicode 数字，全角数字（双字节）| 罗马数字，汉字数字|byte 数字（单字节)|
|  isnumeric() | Unicode 数字，全角数字（双字节），罗马数字，汉字数字  |  无 | byte 数字（单字节)  |

``` python3
s= 'PyDataLab'
print("判断字符串内全部是否为数字",s.isdigit())
print("判断字符串内全部是否为字符",s.isalpha())
print("判断字符串内由一个或多个空格组成",s.isspace())
print("判断字符串内全部 为数字和字符",s.isalnum())
print("判断字符串全部由大写组成",s.isupper())
print("判断字符串全部由小写组成",s.islower())
print("判断字符串形式为驼峰命名",s.istitle())
print("判断是什么类型",isinstance(s,str))
print(s,(str,int))
 ```
isinstance(obj,type)，其中type 可选类型为：int，float，bool，complex，str，bytes，unicode，list，dict，set，tuple并且type 可以为一个元组（tuple）。

``` python3
s= 'PyDataLab'
print("统计相同字符的个数",s.count("a"))
print("计算字符串的长度",len(s))
print("字符替换",s.replace('l',"L"))
print("限定替换次数不超过max",s.replace('l',"L",2))
print("判断是否以某字符开头， 区分大小写",s.startswith("H"))
print("判断是否以某字符开头",s.startswith("a"))
print("判断是否以某字符结尾",s.endswith("d"))
print("判断是否以某字符结尾， 区分大小写",s.endswith("d"))
print("用--分割字符串s中的每个单词","--".join(s))
print("返回字符串中最大的字符",max(s))
print("返回字符串中最小的字符",min(s))
 ```
