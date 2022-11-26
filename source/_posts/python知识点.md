---
title: python知识点
date: 2022-10-19 21:36:05
cover: https://tvax1.sinaimg.cn/large/008waiCQgy1h8ivhct4bzj32h41bz4qp.jpg
tags: Python
categories: python
description: python？一个写脚本的工具罢了。
---





## Python 基础语法

### 输入

```
input()函数都以字符串类型返回结果，他可以这样使用 input("请输入数字：")      
#格式：<变量>=input(<提示性文字>)
在这里搭配的有eval()，最基础使用的是去掉一个双引号
print 默认输出是换行的，如果要实现不换行需要在变量末尾加上 end=""
转义符 \
反斜杠可以用来转义，使用 r 可以让反斜杠不发生转义。 如 r"this is a line with \n" 则 \n 会显示，并不是换行。
```



### 注释

```
# 单行注释

"""
多行注释1
多行注释2
多行注释3
"""
'''
多行注释1
多行注释2
多行注释3
'''
Copy
```

### 变量

- 可改变的量为变量，指向内存的一块空间，当不使用时即会被回收
- 变量名只能由数字、字母和下划线组成，不能用关键字，不能数字开头，建议不要用中文
- 变量名尽量见名知意
- Python 中常量，一般通过全部大写字母来约定俗成

```
# 查询关键字
import keyword
print(keyword.kwlist)

# 变量的交换
a = 10
b = 19
a, b = b, a
print(a)  # 19
print(b)  # 10
Copy
```

### 布尔

- True：除了 False 都是 True
- False：0、0.0、0j、’’、[]、()、set()、{}、None

```
# bool()，强制将其他类型转为bool
print(bool(10))  # True
print(bool(0))  # False
print(bool(0.0))  # False
print(bool('0'))  # True
print(bool(''))  # False
print(bool({}))  # False
print(bool([]))  # False
print(bool(set()))  # False
print(bool(0j))  # False
print(bool(None))  # False
Copy
```

### 数字

- int 整型，二进制，八进制，十进制，十六进制
- float 浮点型，小数
- conplex 复数，实部+虚部

```
# type()，获取变量的类型
# id()，获取变量内存地址

a = 0b11
print(a, type(a))  # 3 <class 'int'>
b = 0o11
print(b, type(b))  # 9 <class 'int'>
c = 0x11
print(c, type(c))  # 17 <class 'int'>
d = 1.1
print(d, type(d))  # 1.1 <class 'float'>
e = 2e2
print(e, type(e))  # 200.0 <class 'float'>
f = 1 + 2j
print(f, type(f))  # (1+2j) <class 'complex'>
g = complex(2, 3)
print(g, type(g))  # (2+3j) <class 'complex'>
h = True
print(h, type(h))  # True <class 'bool'>
i = False
print(i, type(i))  # False <class 'bool'>
Copy
```

#### 类型转换

```
# int()，强制将int、float、bool、纯数字字符串转为int
print(int(10))  # 10
print(int(10.66))  # 10
print(int(True))  # 1
print(int(False))  # 0
print(int('12345678'))  # 12345678

# float()，强制将int、float、bool、纯数字字符串转为float
print(float(10))  # 10.0
print(float(10.66))  # 10.66
print(float(True))  # 1.0
print(float(False))  # 0.0
print(float('12345678'))  # 12345678.0

# complex()，强制将int、float、bool、纯数字字符串、complex转为complex
print(complex(10))  # (10+0j)
print(complex(10.66))  # (10.66+0j)
print(complex(True))  # (1+0j)
print(complex(False))  # 0j
print(complex('12345678'))  # (12345678+0j)
print(complex(1+2j))  # (1+2j)

# 当两个不同类型的数据进行运算的时候，低精度默认向高精度转换
# bool -> int -> float -> complex
# 不要用小数作比较，存在精度损耗
Copy
```

#### 进制转换

```
# 2 -> 10
a = 0b11
print(int(a))  # 3

# 8 -> 10
b = 0o11
print(int(b))  # 9

# 16 -> 10
c = 0x11
print(int(c))  # 17

# 10 -> 2
d = 3
print(bin(d))  # 0b11

# 10 -> 8
print(oct(d))  # 0o3

# 10 -> 16
print(hex(d))  # 0x3
Copy
```

### 运算符

#### 算术运算符

```
# +、-、*、/、//、%、**
# / 永远返回浮点数类型
# // 直接舍弃小数部分
# % 求余数，** 乘方
# 混合运算优先级顺序，() 高于 ** 高于 *,/,//,% 高于 +,-

print(5 + 2)  # 7
print(5.0 + 2)  # 7.0
print(5 - 2)  # 3
print(5 * 2)  # 10
print(5 / 2)  # 2.5
print(4 / 2)  # 2.0
print(5 // 2)  # 2
print(5 % 2)  # 1
print(5 ** 2)  # 25
Copy
```

#### 赋值运算符

```
# =，将等号右侧的结果赋值给等号左边得到变量
# 单变量赋值
num = 1
print(num)  # 1

# 多变量赋值
a, b, c = 1, 1.1, "hello"
print(a)  # 1
print(b)  # 1.1
print(c)  # hello

# 多变量赋相同值
a = b = 100
print(a)  # 100
print(b)  # 100

# 变量交换值
a = 10
b = 20
a, b = b, a
print(a)  # 20
print(b)  # 10

# +=，c += a 等价于 c = c + a
# -=，c -= a 等价于 c = c - a
# *=，c *= a 等价于 c = c * a
# /=，c /= a 等价于 c = c / a
# //=，c //= a 等价于 c = c // a
# %=，c %= a 等价于 c = c % a
# **=，c **= a 等价于 c = c ** a

# 注意：先计算右侧结果，在进行复合赋值运算
a = 10
a *= 1 + 2
print(a)  # 30
Copy
```

#### 比较运算符

```
# ==，判断相等，如果两侧操作数结果相等，则为 True，反之为 False
# !=，不等于，如果两侧操作数不相等，则为 True，反之为 False
# >，如果运算符左侧操作数结果大于右侧操作数结果，则为 True，反之为 False
# <，如果运算符左侧操作数结果小于右侧操作数结果，则为 True，反之为 False
# >=，如果运算符左侧操作数结果大于等于右侧操作数结果，则为 True，反之为 False
# <=，如果运算符左侧操作数结果小于等于右侧操作数结果，则为 True，反之为 False

print(1 == 1)  # True
print(2 != 1)  # True
print(3 > 2)  # True
print(2 < 3)  # True
print(3 >= 2)  # True
print(2 <= 3)  # True
Copy
```

#### 逻辑运算符

```
# and，与，都真才真，若前一个表达式为假则不会再继续运算后面的表达式，因此建议假可能性大的放前面，优化程序
# or，或，一真则真，若钱一个表达式为真则不会再继续运算后面的表达式，因此建议真可能性大的放前面，优化程序
# not，非，取反
Copy
```

### 流程控制

#### if语句

```
# if判断
if 条件1：
    条件1成立的代码
    ......
elif 条件2:
    条件2成立的代码
    ......
else:
    条件都不成立执行的代码
    ......

# 打印成绩等地，A
num = 100
if num > 90:
    print('A')
elif num > 60:
    print('B')
else:
    print('C')
Copy
```

#### 三目运算符

```
# 条件成立执行的表达式 if 条件 else 条件不成立执行的表达式
a = 10 if 5 > 3 else 5
print(a)  # 10
Copy
```

#### while语句

```
# 单 while 循环
while 条件：
    条件成立执行代码1
    条件成立执行代码2
    ......

# 循环打印 5 次，hello world
n = 1
while n <= 5:
    print('hello world')
    n += 1
“”“
hello world
hello world
hello world
hello world
hello world
”“”

# while else，else 为循环执行之后执行的代码
n = 1
while n <= 5:
    print('hello world')
    n += 1
else:
    print('loop done')
“”“
hello world
hello world
hello world
hello world
hello world
loop done
”“”
Copy
```

#### break语句

```
# 跳出循环
Copy
```

#### continue语句

```
# 跳过此次循环，转而执行下一次循环
Copy
```

#### for语句

```
# 单 for 循环
for 临时变量 in 序列：
    重复执行代码1
    重复执行代码2
    ......

# for 循环打印 hello world
for word in "hello world":
    print(word)
"""
h
e
l
l
o
 
w
o
r
l
d
"""
# for else，else 为循环执行之后执行的代码
for word in "hello world":
    print(word)
else:
    print("loop done")
"""
h
e
l
l
o
 
w
o
r
l
d
loop done
"""
Copy
```

### 字符串

- 可用 `"...."`、`'...'` 以及 `"""..."""` 来表示字符串
- 字符串可用 `/`来转义特殊字符，字符串前加 `r`，即表示原始字符串
- 不可变类型

#### 索引

```
# 索引从0开始，有负索引，从-1开始，因为 -0 = 0
# 索引如果越界，会报错 IndexError
word = 'Python'
print(word[0])  # P
print(word[-1])  # n
print(word[5])  # n
print(word[-6])  # P
Copy
```

#### 切片

```
# 切片越界自动处理，并不会报错
word = 'Python'
print(word[0:2])  # Py
print(word[::2])  # 设置步进为 2，Pto
print(word[2:])  # thon
print(word[:])  # Python
print(word[3:])  # hon
print(word[1:8])  # ython
Copy
```

#### 格式化输出

```
# format()
# # 格式{:[填充][对齐 < > = ^][符号 + = ][宽度][千位分隔符 , _][.保留位数][类型 b c d o x n...]}
print('{}、{}、{}'.format(1, 2, 3))  # 1、2、3
print('{0}、{1}、{2}'.format(a, b, 3))  # 1、2、3
print('{2}、{1}、{0}'.format(1, b, c))  # 3、2、1
print('{x}、{y}'.format(x=3, y=2))  # 3、2
print('{0[0]}、{0[1]}'.format([1, 2]))  # 1、2


# f-string 使用基本和 format() 格式相同
# {}中不允许出现\，如需使用可创建临时变量
word = 'Python'
print(f'I like {word}')  # I like Python
print(f'I like {word!s}')  # 调用str()  I like Python
print(f'I like {word!a}')  # 调用ascii()  I like 'Python'
print(f'I like {word!r}')  # 调用repr()  I like 'Python'
print(f'I like {repr(word)}')  # I like 'Python'
Copy
```

#### 常用函数

```
# str()，强制转化成字符串
print(str(1))  # '1'
print(str(1.1))  # '1.1'
print(str(True))  # 'True'
print(str([1, 2, 3]))  # '[1, 2, 3]'
print(str({"str": "123"}))  # "{'str': '123'}"

# + 拼接字符串，* 复制多份
str1 = "hello "
str2 = "python"
print(str1 + str2)  # hello python
print(str1 * 3)  # hello hello hello 

# repr()，不转移字符原型化输出字符串（pycharm 里面 run 会自动优化输出，使用命令行更直观）
print(str('hello world'))  # 'hello world'
print(repr('hello world'))  # "'hello world'"

# len()，查看字符串长度
hello_str = "hello world i like python and c plus and everything hello my friend"
print(len(hello_str))  # 67

# find(sub[, start[, end]])，检测 sub 子串是否包含在字符串 [start,end] 中，找到返回下标，没找到返回 -1
# rfind()，从右边开始找
print(hello_str.find("world"))  # 6
print(hello_str.find("worlds"))  # -1
print(hello_str.find("hello", 5, 100))  # 52

# index(sub[, start[, end]])，检测 sub 子串是否包含在字符串 [start,end] 中，找到返回下标，没找到报错
# rindex()，从右边开始找
print(hello_str.index("world"))  # 6
print(hello_str.index("worlds"))  # ValueError: substring not found
print(hello_str.index("hello", 5, 100))  # 52

# count()，返回子串出现的次数
print(hello_str.count("hello"))  # 2
print(hello_str.count("i"))  # 4

# replace(old, new[, count])，返回字符串的副本，将 new 替代 old，并指定 count 次数
new_str = hello_str.replace("hello", "fuck")
print(hello_str)  # hello world i like python and c plus and everything hello my friend
print(new_str)  # fuck world i like python and c plus and everything fuck my friend
new_str = hello_str.replace("hello", "fuck"， 1)
print(new_str)  # fuck world i like python and c plus and everything hello my friend

# split(sep=None, maxsplit=-1)，返回一个由字符串内单词组成的列表，使用 sep 作为分隔字符串，如果给出了 maxsplit，则最多进行 maxsplit 次拆分，默认以空字符串作为分隔符
print(hello_str.split())  # ['hello', 'world', 'i', 'like', 'python', 'and', 'c', 'plus', 'and', 'everything', 'hello', 'my', 'friend']
print(hello_str.split(maxsplit=1))  # ['hello', 'world i like python and c plus and everything hello my friend']

# strip([chars])，返回原字符串的副本，移除其中的前导和末尾字符，默认移除前后空白，可指定对应 chars
# lstrip()，只删除左边，rstrip()，只删除右边
hello_str = "   hello world  "
print(hello_str)  # '   hello world  '
print(hello_str.strip())  # 'hello world'

# join(iterable)，返回一个由 iterable 中的字符串拼接而成的字符串
str_list = ["my", "name", "is", "ReaJason"]
print(" ".join(str_list))  # my name is ReaJason
str_dict = {"name": "ReaJason", "age": 18}
print(" ".join(str_dict))  # name age

# capitalize()，返回原字符串的副本，其首个字符大写，其余为小写
print("hello world".capitalize())  # Hello world

# lower()，返回原字符串的副本，其所有区分大小写的字符均转换为小写
# islower()，判断字符串是否全为小写
print("HELLO WORLD".lower())  # hello world
print("HELLO WORLD".islower())  # False
print("hello world".islower())  # True

# title()，返回原字符串的标题版本，其中每个单词第一个字母为大写，其余字母为小写
print("hello world".title())  # Hello World

# upper()，返回原字符串的副本，其中所有区分大小写的字符均转换为大写
# isupper()，判断字符串是否全为大写
print("hello world".upper())  # HELLO WORLD
print("hello world".isupper())  # False
print("HELLO WORLD".isupper())  # True

# ljust(width[, fillchar])，返回长度为 width 的靠左对齐字符串，使用指定的 fillchar 填充空位
print("hello world".ljust(20))  # 'hello world         '
print("hello world".ljust(20, "-"))  # 'hello world---------'

# rjust(width[, fillchar])，返回长度为 width 的靠右对齐字符串，使用指定的 fillchar 填充空位
print("hello world".rjust(20))  # '         hello world'
print("hello world".rjust(20, "-"))  # '---------hello world'

# center(width[, fillchar])，返回长度为 width 的居中对齐字符串，使用指定的 fillchar 填充空位
print("hello world".center(20))  # '    hello world     '
print("hello world".center(20, "-"))  # '----hello world-----'

# startswith(prefix[, start[, end]])，如果字符串以指定的 prefix 开始则返回 True，否则返回 False
print("hello world".startswith("hello"))  # True
print("hello world".startswith("h"))  # True
print("hello world".startswith("world"))  # False

# endswith(suffix[, start[, end]])，如果字符串以指定的 suffix 结束返回 True，否则返回 False
print("hello world".endswith("world"))  # True
print("hello world".endswith("d"))  # True
print("hello world".endswith("hello"))  # False

# isalpha(),如果字符串中的所有字符都是字母，并且至少有一个字符，返回 True ，否则返回 False
print("hello world".isalpha())  # False

# isdigit()，如果字符串中的所有字符都是数字，并且至少有一个字符，返回 True ，否则返回 False
print("1234".isdigit())  # True

# isalnum()，如果字符串中的所有字符都是字母或数字且至少有一个字符，则返回 True ， 否则返回 False
print("1234".isalnum())  # True
print("hello world".isalnum())  # False
print("hello1world".isalnum())  # True

# isspace()，如果字符串中只有空白字符且至少有一个字符则返回 True ，否则返回 False
print("   ".isspace())  # True
Copy
```

### 列表

- 用 `[]` 组合复合类型（不限定只能一种类型）
- 使用方括号，其中的项以逗号分隔: `[a]`, `[a, b, c]`
- 使用一对方括号来表示空列表: `[]`
- 可变数据类型，可获取，可修改，有序

```
list1 = [1, 'Python', 13.14, True, 1+1j, [1, 2, 3], 'ReaJason']
Copy
```

#### 索引

```
print(list1[0])  # 1
print(list1[-7])  # 1
print(list1[-1])  # ReaJason
print(list1[6])  # ReaJason
print(list1[5][1])  # 2
list1[5][1] = 250
print(list1)  # [1, 'Python', 13.14, True, (1+1j), [1, 250, 3], 'ReaJason']

# 修改指定索引数据
list1[0] = 10
print(list1)  # [10, 'Python', 13.14, True, (1+1j), [1, 2, 3], 'ReaJason']
Copy
```

#### 切片

```
print(list1[0:2])  # [1, 'Python']
print(list1[0:5:2])  # [1, 13.14, (1+1j)]
print(list1[::-1])  # ['ReaJason', [1, 2, 3], (1+1j), True, 13.14, 'Python', 1]
Copy
```

#### 遍历

```
# for 遍历列表
list1 = [1, 'Python', 13.14, True, 1+1j, [1, 2, 3], 'ReaJason']
for i in list1:
    print(i)

"""
1
Python
13.14
True
(1+1j)
[1, 2, 3]
ReaJason
"""

# enumarate()，返回索引和值的元组
list1 = [1, 'Python', 13.14, True, 1+1j, [1, 2, 3], 'ReaJason']
for index, value in enumerate(list1):
    print(f"{index}：{value}")
"""
0：1
1：Python
2：13.14
3：True
4：(1+1j)
5：[1, 2, 3]
6：ReaJason
"""
Copy
```

#### 常用函数

```
# list()，强制转换成列表
print(list('12345678'))  # ['1', '2', '3', '4', '5', '6', '7', '8']

# + 拼接列表，* 复制元素多份
list1 = [1, 2, 3]
list2 = ["hello", "python",]
print(list1 + list2)  # [1, 2, 3, 'hello', 'python']
print(list2 * 2)  # ['hello', 'python', 'hello', 'python']

# len()，获取列表元素个数
list2 = [1, 2, 3, 4, 5, 1, 2]
print(len(list2))  # 7

# count(x)，返回元素 x 在列表中出现的次数
print(list2.count(1))  # 2

# reverse()，直接翻转原数组的所有元素
list2.reverse()
print(list2)  # [5, 4, 3, 2, 1]

# sort(key=None, reverse=False)，对列表中的元素进行排序
list2.sort()  # [1, 1, 2, 2, 3, 4, 5]
print(list2)
list2.sort(reverse=True)
print(list2)  # [5, 4, 3, 2, 2, 1, 1]

# index(x[, start[, end]])，返回列表中第一个值为 x 的元素的索引，如果不存在会抛出 ValueError 异常
name_list = ["ReaJason", "Tom", "Lucy", "LiLy"]
print(name_list.index("Tom"))  # 1
print(name_list.index("Toms"))  # ValueError: 'Toms' is not in list

# in，not in，判断元素是否在列表中，为通用方法，适用于字符串，字典，元组，集合
print("ReaJason" in name_list)  # True
print("Dazzling" in name_list)  # False
print("ReaJason" not in name_list)  # False
print("Dazzling" not in name_list)  # True

# append(x)，在列表末尾添加元素x
name_list.append("Jack")
print(name_list)  # ['ReaJason', 'Tom', 'Lucy', 'LiLy', 'Jack']

# extend(iterable)，使用可迭代对象中的所有元素来扩展列表
name_list.extend([1, 2, 3, 4, 5])
print(name_list)  # ['ReaJason', 'Tom', 'Lucy', 'LiLy', 1, 2, 3, 4, 5]

# insert(i,x)，在指定i索引位置插入元素x、
name_list.insert(1, "Dazzling")
print(name_list)  # ['ReaJason', 'Dazzling', 'Tom', 'Lucy', 'LiLy']


# del，删除目标元素或变量
del name_list[0]
print(name_list)  # ['Tom', 'Lucy', 'LiLy']
del name_list
print(name_list)  # NameError: name 'name_list' is not defined

# pop()，pop(i)，删除列表最后一个元素，删除列表指定i索引位置的元素
x = name_list.pop()
y = name_list.pop(0)
print(x)  # LiLy
print(y)  # ReaJason
print(name_list)  # ['Tom', 'Lucy']

# remove(x)，移除列表中从左到右第一个值为x的元素
name_list.remove("Tom")
print(name_list)  # ['ReaJason', 'Lucy', 'LiLy']

# clear()，清空列表
name_list.clear()
print(name_list)  # []

# copy()，返回列表的一个浅拷贝
name_list2 = name_list.copy()
name_list2.append("Dazzling")
print(name_list)  # ['ReaJason', 'Tom', 'Lucy', 'LiLy']
print(name_list2)  # ['ReaJason', 'Tom', 'Lucy', 'LiLy', 'Dazzling']
Copy
```

#### 列表推导式

```
# range(start, stop[, step])，range 构造器的参数必须为整数，用来生成序列
print(list(range(10)))  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(list(range(10, 0, -1)))  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
print(list(range(0)))  # []

# 列表推导式，返回一个列表
list1 = [i for i in range(10)]
print(list1)  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(list(range(10)))  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# 带 if 的列表推导式，如果 i 为偶数，则选择
list1 = [i for i in range(10) if i%2 == 0]
print(list1)  # [0, 2, 4, 6, 8]

# 多个 for 循环实现列表推导式，将多层次列表展开
arr = [[1, 2, 3], [4, 5, 6]]
list1 = [j for i in arr for j in i]
print(list1)  # [1, 2, 3, 4, 5, 6]
Copy
```

### 元组

- 用 `（）` 组合复合类型（不限定只能一种类型）
- 使用一对圆括号来表示空元组: `()`
- 使用一个后缀的逗号来表示单元组: `a,` 或 `(a,)`
- 使用以逗号分隔的多个项: `a, b, c` or `(a, b, c)`
- 元素数据不可修改，有序
- 索引切片和 list 一样

```
tuple1 = (1, 'Python', 13.14, True, 1 + 1j, [1, 2, 3], 'ReaJason')
Copy
```

#### 常用函数

```
# tuple()，强制转换成元组
print(tuple('12345678'))  # ('1', '2', '3', '4', '5', '6', '7', '8')

# len()，获取列表元素个数
print(len(tuple1))  # 7

# count(x)，返回元素 x 在列表中出现的次数
num_tuple = (1, 1, 2, 3, 4, 5)
print(num_tuple.count(1))  # 2
print(num_tuple.count(2))  # 1

# index(x[, start[, end]])，返回列表中第一个值为 x 的元素的索引，如果不存在会抛出 ValueError 异常
print(num_tuple.index(1))  # 2
print(num_tuple.index(6))  # ValueError: tuple.index(x): x not in tuple
Copy
```

### 字典

- 符号为：`{}`
- 数据以键值对形式出现
- 各个键值对之间用逗号隔开
- 字典是无序的对象集合，使用键-值（key-value）存储，拥有极快的查询速度
- 字典是可变类型，键（key）必须使用不可变类型
- 同一个字典中，键（key）必须是唯一的

#### 字典创建

```
# 使用 {key1: value1, key1: value1}的形式 创建字典
dict1 = {"name": "ReaJason", "age": 22, "gender": "female"}

# 使用dict构造器创建
dict2 = dict(name="ReaJason", age=22, gender="female")

# 创建空字典
dict3 = {}
dict4 = dict()
Copy
```

#### 增删改查

```
dict1 = {"name": "ReaJason", "age": 22, "gender": "female"}

# 增，d[key] = value
dict1['id'] = 10
print(dict1)  # {'name': 'ReaJason', 'age': 22, 'gender': 'female', 'id': 10}

# 改，d[key] = value
dict1['age'] = 18
print(dict1)  # {'name': 'ReaJason', 'age': 18, 'gender': 'female'}

# 删，del d[val]，将 d[key] 从 d 中移除。 如果映射中不存在 key 则会引发 KeyError
del dict1['name']
print(dict1)  # {'age': 22, 'gender': 'female'}

# clear()，移除字典中的所有元素
dict1.clear()
print(dict1)  # {}

# 查，d[key]，返回 d 中以 key 为键的项。 如果映射中不存在 key 则会引发 KeyError
print(dict1['name'])  # ReaJason
print(dict1['id'])  # KeyError: 'id'

# get(key[, default])，如果 key 存在于字典中则返回 key 的值，否则返回 default。 如果 default 未给出则默认为 None，因而此方法绝不会引发 KeyError
print(dict1.get('name'))  # ReaJason
print(dict1.get('id'))  # None
print(dict1.get('id', 1))  # 1
Copy
```

#### 常用函数

```
dict1 = {"name": "ReaJason", "age": 22, "gender": "female"}

# len(d)，返回字典 d 中的项数
print(len(dict1))  # 3

# key in d，如果 d 中存在键 key 则返回 True，否则返回 False
print("name" in dict1)  # True

# key not in d，如果 d 中不存在键 key 则返回 True，否则返回 False
print("id" not in dict1)  # True

# keys()，返回由字典键组成的一个新视图，类似于字典所有键组成的列表
print(dict1.keys())  # dict_keys(['name', 'age', 'gender'])

# values()，返回由字典值组成的一个新视图，类似于字典所有值组成的列表
print(dict1.values())  # dict_values(['ReaJason', 22, 'female'])

# items()，返回由字典项 ((键, 值) 对) 组成的一个新视图
print(dict1.items())  # dict_items([('name', 'ReaJason'), ('age', 22), ('gender', 'female')])

# 字典遍历，获取 key 和 value
for key,value in dict1.items():
    print(f"{key}：{value}")
"""
name：ReaJason
age：22
gender：female
"""

# update([other])，使用来自 other 的键/值对更新字典，覆盖原有的键，原地更新
dict2 = {"hobby": "learning", "id": 1}
dict1.update(dict2)
print(dict1)  # {'name': 'ReaJason', 'age': 22, 'gender': 'female', 'hobby': 'learning', 'id': 1}

dict3 = {"name": "Dazzling", "age": 18, "gender": "male"}
dict1.update(dict3)
print(dict1)  # {'name': 'Dazzling', 'age': 18, 'gender': 'male'}
Copy
```

#### 字典推导式

```
# 字典推导式用于快速生成字典，以及提取目标数据
cookies = "anonymid=jy0ui55o-u6f6zd; depovince=GW; _r01_=1;"
cookies = {cookie.split("=")[0]:cookie.split("=")[1] for cookie in cookies.split("; ")}
print(cookies)  # {'anonymid': 'jy0ui55o-u6f6zd', 'depovince': 'GW', '_r01_': '1;'}

# 其他用法同列表推导式
Copy
```

### 集合

- 多个元素的无序组合，集合是无序的，不支持索引操作
- 集合元素是唯一的，可用于去重

#### 集合创建

```
# 使用 {value1, value2}
set1 = {1, 2, 3, 1, 3, 4}

# 使用set()方法
set2 = set([1, 2, 3, 1, 3, 4])

# 创建空集合
set3 = set()
Copy
```

#### 增删改查

```
set1 = {10, 20}

# add(elem)，将元素 elem 添加到集合中
set1.add(30)
set1.add(10)
print(set1)  # {10, 20, 30}

# update(*others)，更新集合，添加来自 others 中的所有元素
list1 = [10, 20, 30, 40, 50]
set1.update(list1)
print(set1)  # {40, 10, 50, 20, 30}

# remove(elem)，从集合中移除元素 elem。 如果 elem 不存在于集合中则会引发 KeyError
set1.remove(10)
print(set1)  # {20}
set1.remove(10)  # KeyError: 10

# discard(elem)，如果元素 elem 存在于集合中则将其移除
set1.discard(10)
print(set1)  # {20}
set1.discard(10)

# pop()，从集合中移除并返回任意一个元素。 如果集合为空则会引发 KeyError
set1.pop()
set1.pop()
print(set1)  # set()
set1.pop()  # KeyError: 'pop from an empty set'

# x in s，检测 x 是否为 s 中的成员
print(10 in set1)  # True
print(100 in set1)  # False

# x not in s，检测 x 是否非 s 中的成员
print(10 not in set1)  # False
print(100 not in set1)  # True
Copy
```

### 函数

- 将一段具有独立功能的代码块整合到一个整体并命名。在需要的地方调用，实现更高效的代码复用
- 函数定义参数可有可无，返回值也一样，函数必须先定义后使用
- 函数设计要尽量短小，嵌套层次不宜过深
- 函数申明应该做到合理、简单、易于使用
- 函数参数设计应考虑向下兼容
- 一个函数只做一件事，尽量保证函数语句粒度的一致性

#### 定义函数

```
# 使用 def 定义函数，return 返回需要返回的值（非必需）
def 函数名(参数1（可选）, 参数2（可选）):
    函数内代码
    ......
    return 返回值（可选）

# 定义一个实现加法的函数
def a_add_b(a, b):
    return a + b


result = a_add_b(10, 90)
print(result)  # 100
Copy
```

#### 说明文档

```
def a_add_b(a, b):
    """
    我是a_add_b的说明文档：一个实现加法的函数
    :param a: 参数 1
    :param b: 参数 2
    :return: 返回值
    """
    return a + b

# help(函数名称)，查看函数的说明文档
help(a_add_b)

"""
Help on function a_add_b in module __main__:

a_add_b(a, b)
    一个实现加法的函数
    :param a: 参数 1
    :param b: 参数 2
    :return: 返回值
"""
Copy
```

#### 变量作用域

- 局部变量，定义在函数内部，作用范围为该函数内

```
def test():
    a = 100
    print(a)

test()  # 100
print(a)  # NameError: name 'a' is not defined
Copy
```

- 全局变量，定义在全局，当前 py 文件内都可访问到

```
a = 100

def test():
    print(a)

test()  # 100
print(a)  # 100
Copy
```

- global()，可被用来表明特定变量生存于全局作用域并且应当在其中被重新绑定

```
a = 100

print(f"全局变量a：{a}")  # 全局变量a：100


def test1():
    a = 200  # 局部变量，
    print(f"test1函数的a：{a}")


def test2():
    global a  # 修改全局变量
    a = 300
    print(f"test2函数的a：{a}")

test1()  # test1函数的a：200
test2()  # test2函数的a：300
print(f"全局变量a：{a}")  # 全局变量a：300
Copy
```

- nonlocal()，表明特定变量生存于外层作用域中并且应当在其中被重新绑定

```
def test():
    a = 200
    
    def test1():
        print(f"test1函数的a：{a}")
        
    def test2():
        nonlocal a  # 修改局部变量
        print(f"test2函数的a：{a}")
        a = 100
        print(f"test1函数的a：{a}")
    test1()
    test2()

test()
"""
test1函数的a：200
test2函数的a：200
test1函数的a：100
"""
Copy
```

#### 函数参数

- 位置参数

```
# 位置参数与形参一一对应
def user_info(name, age, gender):
    print(f"name：{name}，age：{age}，gender：{gender}")


user_info("ReaJason", 18, "female")  # name：ReaJason，age：18，gender：female
user_info(18, "female", "ReaJason")  # name：18，age：female，gender：ReaJason
Copy
```

- 关键字参数

```
# 当函数调用时既有位置参数也有关键字参数时，关键字参数必须写在最后
def user_info(name, age, gender):
    print(f"name：{name}，age：{age}，gender：{gender}")


user_info(name="ReaJason", age=18, gender="female")  # name：ReaJason，age：18，gender：female
user_info(age=18, gender="female", name="ReaJason")  # name：ReaJason，age：18，gender：female
user_info("ReaJason", 18, gender="female")  # name：ReaJason，age：18，gender：female
user_info(name="ReaJason", age=18, "female")  # SyntaxError: positional argument follows keyword argument
Copy
```

- 默认参数

```
# 所有位置参数必须在默认参数之前
def user_info(name, age, gender="female"):
    print(f"name：{name}，age：{age}，gender：{gender}")


user_info("ReaJason", 18)  # name：ReaJason，age：18，gender：female
user_info("ReaJason", 18, "male")  # name：ReaJason，age：18，gender：male
Copy
```

- 可变长参数

```
# *args，传进去的所有参数都会被 args 变量收集，根据参数位置合并为一个元组
def user_info(*args):
    print(args)


user_info("ReaJason", 18, "female", ["learning", "gaming"])
# ('ReaJason', 18, 'female', ['learning', 'gaming'])

# **kwargs，传进去的所有参数都会被 kwargs 变量收集，将关键字参数做为 key 后面的值作为 value 合并为一个一个字典
def user_info(**kwargs):
    print(kwargs)


user_info(name="ReaJason", age=18, gender="female", hobby=["learning", "gaming"])
# {'name': 'ReaJason', 'age': 18, 'gender': 'female', 'hobby': ['learning', 'gaming']}

# 当位置参数，默认参数，与可变长参数在同一个函数定义中，相对位置为 位置参数 > 默认参数 > 可变长参数
Copy
```

#### lambda函数

```
# 定义匿名函数
a_add_b = lambda a, b: a+b
print(a_add_b(10, 90))  # 100

# 用于列表排序，选定 key

Copy
```

#### map函数

```
# 求一个序列或者多个序列进行函数映射之后的值，列表推导得方式更好
def func(x):
    return x ** 2

list1 = [1, 2, 3, 4, 5]
list2 = [i ** 2 for i in list1]  # 推荐方式
result1 = map(func, list1)
result2 = map(lambda x: x ** 2, list1)
print(result1)  # <map object at 0x00000177FD39D848>
print(list(result1))  # [1, 4, 9, 16, 25]
print(list(result2))  # [1, 4, 9, 16, 25]
print(list2)  # [1, 4, 9, 16, 25]
Copy
```

#### reduce函数

```
# 对一个序列进行压缩运算，得到一个值
import functools  # import 导入模块

list1 = [1, 2, 3, 4, 5, 6]
result = functools.reduce(lambda x, y: x + y, list1)
print(result)
Copy
```

#### filter函数

```
# 过滤掉序列中不符合函数条件的元素，列表推导得方式更好
def func(x):
    return x % 2 == 0

list1 = [1, 2, 3, 4, 5, 6]
list2 = [i for i in list1 if i % 2 == 0]  # 推荐方式
result1 = filter(func, list1)
result2 = filter(lambda x: x % 2 == 0, list1)
print(result1)  # <filter object at 0x000001D589779048>
print(list(result1))  # [2, 4, 6]
print(list(result2))  # [2, 4, 6]
print(list2)  # [2, 4, 6]
Copy
```

### 文件操作

#### 文件的读

- 关于文件读四个模式
- `r`，以只读的方式打开文件，未找到文件会报错，文件的指针将会放在文件的开头，这是默认模式，`r` 打开文本文件
- `rb`，以二进制格式打开一个文件用于只读。文件指针放在文件的开头。这是默认模式，`rb` 打开非文本文件
- `r+`，打开一个文件用于读写，准确来说是读并且追加。文件指针将会放在文件的开头
- `rb+`，以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头

```
# f.read(size)，读取文件指定 size 大小字节的数据，每执行一次往后移动指定位数，如果未指定 size，则读取文件所有数据
# text.txt 文本内容如下：
"""
hello world
"""

f = open("text.txt", "r")
content = f.read(5)
print(content)  # hello
content = f.read(6)
print(content)  #  world
f.close()

f = open("text.txt", "r")
content = f.read()
print(content)  # hello world
f.close()

# f.readlines()，以列表的形式读取文件中的所有行
# text.txt 文本内容如下：
"""
hello world
hello python
"""

f = open("text.txt", "r")
content = f.readlines()
print(content)  # ['hello world\n', 'hello python']
f.close()

# f.readline()，从文件中读取一行，换行符（\n）留在字符串的末尾，中间空白行是 \n 
# text.txt 文本内容如下：
"""
hello world

hello python
"""

f = open("text.txt", "r")
content = f.readline()
print(f"第一行{content!r}")  # 第一行'hello world\n'
content = f.readline()
print(f"第二行{content!r}")  # 第二行'\n'
content = f.readline()
print(f"第三行{content!r}")  # 第三行'hello python'
content = f.readline()
print(f"第四行{content!r}")  # 第四行''，此时文件一共三行，已读完，并不报错，输出空白字符
f.close()
Copy
```

#### 文件的写

- 关于文件写四个模式
- `w`，打开一个文件只用于写入。如果文件已存在则先清空后写入，如果没有文件则创建文件
- `wb`，以二进制格式打开一个文件只用于写入。
- `w+`，打开一个文件用于读写，巴拉巴拉
- `wb+`，以二进制格式打开一个文件用于读写，巴拉巴拉

```
# f.write(string) 会把 string 的内容写入到文件中，并返回写入的字符数
f = open("text1.txt", "w")
f.write("hello world")  # 将字符串写入文件中
f.close()

# text1.txt 文本内容：
"""
hello world
"""
Copy
```

#### 文件的追加

- 关于文件写四个模式
- `a`，打开一个文件用于追加。如果文件存在，新内容将写在文件已有内容之后。文件不存在则创建新文件进行写入。
- `ab`，`a+`，`ab+`

```
f = open("text2.txt", encoding="utf-8", mode="a")
f.write("我是第一句")
f.close()

# text1.txt 文本内容：
"""
我是第一句
"""
Copy
```

#### 其他函数

```
# f.tell() 返回一个整数，给出文件对象在文件中的当前位置，表示为二进制模式下时从文件开始的字节数，以及文本模式下的意义不明的数字
# text.txt 文本内容：
"""
hello world
"""
f = open("text.txt", "r")
print(f.tell())  # 0
content = f.read(5)
print(content)  # hello
print(f.tell())  # 5
f.close()

# f.seek(offset, whence)，通过向一个参考点添加 offset 来计算位置；参考点由 whence 参数指定。 whence 的 0 值表示从文件开头起算，1 表示使用当前文件位置，2 表示使用文件末尾作为参考点
f = open("text.txt", "r")
print(f.tell())  # 0
f.seek(6)
content = f.read()
print(content)  # world
print(f.tell())  # 11
f.close()
Copy
```

#### with

```
# with open(filename, mode, encoding) as f，打开文件不再需要 f.close，with 会自动处理
# 不设置读写模式，默认为 r 模式打开文件
with open("text.txt", encoding="utf-8") as result:
    print(result.read())

"""
hello world

hello python
"""
Copy
```

## Python 类与对象

1. 类是对一系列具有相同**特征**和**行为**的事物的统称，是一个抽象概念，特征即属性，行为即方法
2. 对象是类的一个实例，先有类，后有对象
3. 类名遵循大驼峰命名如：`HelloWorld`
4. 属性和方法可以在类中指定也可以动态添加
5. 面向对象的三个特点：封装、继承、多态

### 类的定义

```
"""
class 类名:
    代码
"""
# self 指的是调用该函数的对象
# 定义一个动物类，拥有 name 属性，eat 方法
class Animal:
    name = None
    
    def eat(self):
        print(f"{self.name} 正在吃...")

# 实例化一个动物：猫，获取属性通过 对象名.属性名
cat = Animal()  # 获得一个 Animal 的实例，拥有 name 属性，和 eat 方法
cat.name = "猫"  # 指定 cat 的 name 属性的值为猫
print(cat.name)  # 获取 cat 的 name 属性的值 # 猫
cat.eat()  # 调用 cat 的 eat 方法
#  猫 正在吃...
Copy
```

### 魔法方法

#### init 方法

```
# __init__ 为初始化方法
class Animal:

    def __init__(self, name):
        self.name = name

    def eat(self):
        print(f"{self.name} 正在吃...")


cat = Animal("猫")  # 传入 name 参数，用于初始化一个猫的实例
print(cat)  # <__main__.Animal object at 0x0000028581CED688>
cat.eat()  # 猫 正在吃...
Copy
```

#### str 方法

```
# 当 print 对象的时候，默认打印的是对象的内存地址，定义 __str__ 方法，再次打印对象则输出的是 __str__ 方法的返回值
class Animal:

    def __init__(self, name):
        self.name = name
    
    def __str__(self):
        return f"这是一只{self.name}"
    
    def eat(self):
        print(f"{self.name} 正在吃...")
    
    

dog = Animal("狗")  # 传入 name 参数，用于初始化一个狗的实例
print(dog)  # 这是一只狗
dog.eat()  # 狗 正在吃...
Copy
```

#### del 方法

```
# 当删除对象的时候，python 解释器会默认调用 __del__ 方法（析构方法）
class Animal:
    
    def __init__(self, name):
        self.name = name
    
    def __str__(self):
        return f"这是一只{self.name}"
    
    def eat(self):
        print(f"{self.name} 正在吃...")
    
    def __del__(self):
        print(f"{self.name} 死了")
    

dog = Animal("狗")  # 传入 name 参数，用于初始化一个狗的实例
print(dog)  # 这是一只狗
dog.eat()  # 狗 正在吃...
#  狗 死了  # 程序结束，对象删除，所以调用了__del__方法，系统收回内存
Copy
```

### 继承

1. 所有类默认继承 object 类
2. 子类继承父类的所有属性和方法
3. 子类可以重写父类方法
4. 多继承，一个子类可以有多个父类

#### 单继承

```
class A:
    def __init__(self):
        self.num = 1
    
    def print_num(self):
        print(f"我的数字是：{self.num}")


class B(A):
    pass  # pass用来代码占位，没有任何实际意义


b = B()  # 创建 B 类的一个实例对象
print(b.num)  # 1 # 打印 b 实例的 num 属性的值，找不到去父类找
b.print_num()  # 我的数字是：1 # 调用 b 实例的 print_num 方法，找不到去父类找
Copy
```

#### 重写方法

```
class A:
    def __init__(self):
        self.num = 1
    
    def print_num(self):
        print(f"A的数字是：{self.num}")


class B(A):
    def print_num(self):
        print(f"B的数字是：2")


b = B()
b.print_num()  # B的数字是：2
Copy
```

#### 多继承

```
class A:
    def __init__(self):
        self.num = 1
    
    def print_num(self):
        print(f"A的数字是：{self.num}")


class B:
    def __init__(self):
        self.num = 2
    
    def print_num(self):
        print(f"B的数字是：{self.num}")

class C(B, A):
    pass
    


c = C()  # 创建 C 类的一个实例对象
print(c.num)  # 2 # 打印 c 实例的 num 属性的值，找不到去父类找
c.print_num()  # B的数字是：2 # 调用 c 实例的 print_num 方法，找不到去父类找

# __mro__()方法获取继承顺序，即子类未找到属性或方法向上查找父类的顺序
print(C.__mro__)  
# (<class '__main__.C'>, <class '__main__.B'>, <class '__main__.A'>, <class 'object'>)
# 如打印顺序可知，C 找不到的话，找 B，再 A，最后 object类
Copy
```

#### 多层继承

```
class A:
    def __init__(self):
        self.num = 1
    
    def print_num(self):
        print(f"A的数字是：{self.num}")


class B:
    def __init__(self):
        self.num = 2
    
    def print_num(self):
        print(f"B的数字是：{self.num}")


class C(B, A):
    pass


class D(C):
    pass


d = D()
d.print_num()  # B的数字是：2
print(D.__mro__)
# (<class '__main__.D'>, <class '__main__.C'>, <class '__main__.B'>, <class '__main__.A'>, <class 'object'>)
Copy
```

#### super()

```
# 子类调用父类的方法
class A:
    def __init__(self):
        self.num = 1
    
    def print_num(self):
        print(f"A的数字是：{self.num}")


class B(A):
    def print_num(self):
        super(B, self).print_num()  # A的数字是：1
        print(f"B的数字是：2")


b = B()
b.print_num()  # B的数字是：2
Copy
```

#### 私有属性

```
# 设置属性和方法，不继承给子类，在属性和方法名前加 __（双下划钱）
class A:
    def __init__(self):
        self.num = 1
        self.__score = 100
    
    def print_num(self):
        print(f"A的数字是：{self.num}")


class B(A):
    def __init__(self):
        super(B, self).__init__()  # 继承A类的初始化方法
    
    def print_num(self):
        print(f"B的数字是：{self.num}")

b = B()
print(b.num)  # 1
print(b.__score)  # AttributeError: 'B' object has no attribute '__score'


# 修改私有属性，get，set
class A:
    def __init__(self):
        self.num = 1
        self.__score = 100
    
    def print_num(self):
        print(f"A的数字是：{self.num}")
    
    def get_score(self):
        return self.__score
    
    def set_score(self, score):
        self.__score = score

a = A()
# print(a.__score)
print(a.get_score())  # 100
a.set_score(1000)
print(a.get_score())  # 1000
Copy
```

### 多态

1. 多态是指一类事物有多种形态
2. 子类重写父类方法，调用不同子类对象的相同父类方法，可以产生不同的执行结果

```
class Animal:
    
    def work(self):
        print("动物在叫，人坏掉")


class Cat(Animal):
    
    def work(self):
        print("猫在叫，人坏掉")
    

class Dog(Animal):
    
    def work(self):
        print("狗在叫，人坏掉")


class Person:
    
    def work_with_pet(self, cls):
        cls.work()


cat = Cat()
dog = Dog()
person = Person()
person.work_with_pet(cat)  # 猫在叫，人坏掉
person.work_with_pet(dog)  # 狗在叫，人坏掉
Copy
```

### 类方法

- 当方法中需要使用类对象（如访问私有类属性等），定义类方法
- 类方法一般和类属性配合使用

```
# @classmethod，第一个参数必须是类对象，一般为 cls
class Animal:
    __name = "动物"
    
    @classmethod
    def get_name(cls):
        return cls.__name


a = Animal()
print(a.get_name())  # 动物
Copy
```

### 静态方法

- 静态方法既不需要传递类对象也不需要传入实例对象
- 静态方法也能够通过实例对象和类对象去访问

```
# @staticmethod
class Animal:
    
    @staticmethod
    def eat():
        print("吃就完事了")

a = Animal()
a.eat()  # 吃就完事了
Copy
```

## Python 错误和异常

### 语法错误

语法错误又称解析错误，可能是你在学习Python 时最容易遇到的错误

```
# 符号或者缩进语法错误等等
Copy
```

### 异常

即使语句或表达式在语法上是正确的，但在尝试执行时，它仍可能会引发错误。 在执行时检测到的错误被称为 异常，异常不一定会导致严重后果。

```
try:
    可能出现异常的代码
except 异常类型:
    出现异常之后执行的代码

# r 模式打开文件，文件不存在会抛出异常
try:
    f = open("text.txt", "r")
except:
    f = open("text.txt", "w")
Copy
```

#### 捕获指定异常

```
try:
    print("12345")
    f = open("123.txt", "r")  # 只读模式打开，未找到文件，抛出异常
    print("1111")  # 上面异常，下面代码将不再执行
    print(num)    # 未定义 num，会抛出 NameError 异常错误信息
except (NameError, IOError) as result: # 将错误信息赋值给result
    print("产生错误了")
    print(result)

"""
12345
产生错误了
[Errno 2] No such file or directory: '123.txt'
"""
Copy
```

#### 捕获所有异常

```
try:
    print("12345")
    f = open("123.txt", "r")
    print("1111")
    print(num)
except Exception as result: # 将错误信息赋值给result
    print("产生错误了")
    print(result)

"""
12345
产生错误了
[Errno 2] No such file or directory: '123.txt'
"""
Copy
```

#### else 语句

```
# 没有异常发生执行的代码，用 else 语句
Copy
```

#### finally 语句

```
# 无论异常是否发生，一定都会执行的代码，用 finally 语句，常用于资源的清理
Copy
```

#### 自定义异常

```
class PhoneNumError(Exception):
    
    def __init__(self, phone):
        self.phone = phone
    
    def __str__(self):
        return f"{self.phone}，手机位数错误，应为11位"


try:
    phone_num = "1029321212"
    if len(phone_num) < 11:
        raise PhoneNumError(phone_num)
except Exception as e:
    print(e)  # 1029321212，手机位数错误，应为11位
Copy
```

## Python 模块与包

### 模块

模块是一个包含Python定义和语句的文件。文件名就是模块名后跟文件后缀 `.py`

自定义模块名尽量不要与已有模块同名

#### 模块的导入

```
import 模块名
import 模块名1,模块名2       # （不推荐）
from 模块名 import 模块内函数
from 模块名 import *        # （不推荐）
import 模块名 as 别名
from 模块名 import 模块内函数 as 别名
Copy
```

#### 模块搜索顺序

1. 当前目录
2. python 环境变量默认目录下
3. python 默认路径

```
# __all__，设置可导出的函数
# 当导出模块后，只能使用__all__ 列表中的函数
Copy
```

### 包

包是一种通过用“带点号的模块名”来构造 Python 模块命名空间的方法，将有联系的模块组织到一个文件夹，且含有 `__init__` 文件

#### 导入包

```
import 包名.模块名
from 包名 import 模块名
# 必须在__init__文件中，添加__all__ = []，控制允许导入的模块列表
```

## 外部库

### datetime库

```
主要用datetime库里面的datetime类，日期和时间的表示类主要方法(即属性)：
①datetime.now()返回一个datetime类型的，表示当前的日期和时间，精确到微妙
②datetime.utcnow()返回一个datetime类型，获得当前日期和时间，不过是UTC(时间标准时间)
③datetime(year，month，day，hour=0，minute=0，second=0，microsecond=0)指定一个日期和时间，返回一个datetime类型
在这个datetime类里还有一些常用的属性，如min，max，year
他常用的时间格式化方法
① 填一个datetime类型的名字.isoweekday()返回1到7，即根据一个时间计算，星期一到星期日
②strftime(format)根据格式化字符串format进行格式化显示，如strftime("%Y-%m-%d %H:%M%S"),分别是年月日，时分秒
```

