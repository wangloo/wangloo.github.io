---
title: "Python 基础知识"
date: 2023-04-01T10:30:35+08:00
tags: [Python]
---

# 为什么写

因为平常的工作中很少用到python, 但是不得不承认 python是一门优秀的语言, 
对于我目前要准备秋招的情况来看, 有的公司做题如果支持python那会简单很多,
同样的代码,用C写要100行, 换到python最多30搞定. 

有人说C++也可以啊,而且对于常用C的人来说学习门槛更低, 但是我实在是受不了
C++的语法, 包括但不限于模板、迭代器，STL操作，感觉有点四不像的味道。
当然，这只是我的个人习惯，使用C++的人也是很多的，还是根据自己的习惯
选择一套趁手的工具比较好。

因为我其他时间基本都在用C，所以在本篇文章中我会更多拿C来进行比较，这样更好记忆。

我学习 Python 的知识点来源:

1. 《Python 学习手册 第 4 版》李军等
2. UCB CS61A


# 列表 list

列表支持下标索引，所以它就像C语言中的“数组”，列表支持大小动态增长，所以更像“数组plus”，类似C++中的vector吧（不太确定）。

列表支持定长声明或变长声明，像创建一些flag列表，用下标进行查找时，定长创建就是必要的。

## 相关操作
### 创建一个列表

以下的创建操作都是支持的:
```python 
# 创建一个空列表
lst = []
# 创建一个带有初值的列表
lst = [1, 2]
# 创建二维空列表
# 应用的场景是: 按照索引来修改list, 此时如果单纯的初始化list=[],
# 那么对list[1].append()会提示超出范围. 所以我们要提前规定list的长度,
# 即将list声明为目标长度的二维list.
lst = [[] for _ in range(5)]
# 创建定长列表并附初值
lst = [0 for _ in range(5)]
# 创建二维定长列表并附处置0
lst = [[0 for _ in range(5)] for _ in range(5)]
```

以下的方式是错误的! 因为每个子列表都指向同一个对象, 修改一个会同步其他的
```python
lst = [[]] * 5
```

List 的高级构建方法，充分利用python的灵活性:

```python
>>> odds = [1, 3, 5, 7, 9]
>>> [x+1 for x in odds]
[2, 4, 6, 8, 10]
>>> [x for x in odds if 25 % x == 0]
[1, 5]
```

### 其他操作

- 直接使用 `+`, `*`运算符实现多个列表的组合, 与字符串类似
- append: 为列表增加一个对象(末尾)
- pop(pos): 删除列表中的某个元素(按位置)，并返回其值
- insert(pos, val): 在列表的任意位置插入对象
- remove: 删除列表中的某个元素(按内容)
- 多级列表是被支持的，像二维数组一样，但是操作比较复杂，一般不使用内置的列表来实现。
- ...



# Python 内置类型

实例的类型可以通过内置函数`type()`来查看。

## 字符串

定义一个 python 字符串

```python
S = 'Spam'
```

支持索引其中元素，也支持**反向索引**（即从右边开始计算），一般来说负的索引号会简单地与字符串的长度相加。

```py
>>> S[0]
'S'
>>> S[-1]
'm'
>>> S[len(S)-1]
'm'
```

python 也支持字符串分片操作，左边界的默认值为 0，右边界的默认值为字符串的长度。

```py
>>> S[1:]
'pam'
>>> S[0:3]
'Spa'
>>> S[:-1]
'Spa'
```

字符串对于`+`操作符的表现是字符串的连接，这体现了 python 的多态性。
字符串创建后无法直接通过索引改变其元素，可以转而通过同名对象覆盖来实现：

```py
>>> S[0] = 'z'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
>>> S = 'z' + S[1:]
>>> S
'zpam'
```

字符串支持的属性，例如`.find()`可以通过内置命令 `dir(S)`来查看。

> dir 函数仅仅给出了方法的名称，如果想查询其具体使用方法，可以利用内置函数`help(S.find)`。

### string 的三种形式

Python 允许字符串被包括在单引号或者双引号中(**它们的含义相同**），它也允许在三个引号（单或双均可）中包含**多行**字符串常量，当 Python 脚本中嵌入像 HTML 这样的内容时，这是很方便的。

```py
>>> msg = """
... aaaaa
... bbbbb
... ccccc
... """
>>> msg
'\naaaaa\nbbbbb\nccccc\n'
```


## 字典

键值对

支持通过 key 索引来修改， 所以与字符串和列表不同。

```py
>>> D = {}
>>> D['name'] = 'Bob'
>>> D['age'] = 40
>>> D
{'name': 'Bob', 'age': 40}
```

字典也支持嵌套操作，在 json 中常用

### 字典的操作

- keys() 返回 key 的列表
- value() 返回 value 的列表
- item(): 返回`(key,value)`构成的元组列表

### 字典的限制

- 在*python3.6*之前字典是 unordered, 即存储的顺序不按照加入时间; 而在*python3.6+*,
  字典改为 ordered.
- key 是唯一的, 如果你想让一个 key 对应多个值, 将其 value 设置为 list 是一种方案!
- list 只能是 value, 不能是 key

### 元组

元祖像一个**不可改变**的列表

```py
>>> T = (1, 2, 3, 4)
>>> T
(1, 2, 3, 4)
```

其专有的方法：

- index(x) 返回 x 所在元组中的下标
- count(x) 计算 x 在元组中出现的次数

> 为什么要使用元组？
> 一般来说，元组不如列表那样常用，因为它的不可被改变的特性。但是，这也赋予了元组天然的完整性约束，可应用在期望不会被改变的场景中

# 基础语法

## iterator

```py
l = [1, 2, 3]
k = iter(l)  # 得到一个可以遍历的iterator
next(k)      # get 1
next(k)      # get 2
```

iterator 任何情况下都可以等价于一个 for 循环!

list 的 iterator 像是一个 list, 不同的是它存在一个"监视者"会记录你当前所在的位置, 使得你可以调用一些方法(`next()`)
来得到当前位置的元素, 并监视下一个位置的元素.

当然, 不仅仅 list 有对应的 iterator, 对于其他数据结构来说亦是如此.

> 如果在遍历`k`的途中改变了遍历对象的结构(例如`lst.append(4)`), 该 iterator 将不可再用!
>
> 所以以下代码的行为是错误的:
>
> ```py
> lst = [1, 2, 3]
> for item in lst:
>   if item == 1:
>     lst.remove(1)
> ```
>
> 可以专门创建一个 `lst` 的副本用于循环:
>
> ```py
> for item in list(lst):
>  ...
> ```
>
> 但修改其中的值不影响(例如`lst[0] = 0`)

另一个有趣的事情是: 如果使用`list()`方法创建一个 iterator 的 list, python 实际的行为是
**调用`next()`直到结尾来得到元素并插入新的 list**. 所以下面的代码行为就容易理解了:

```py
t = iter([1, 2, 3])
list(t)  # [1, 2, 3]
list(t)  # []
```

# Python 内置功能函数

## 聚合可索引的类型

一些内置的功能函数可以将**可索引的**参数转换为有意义的**数值**. 例如 range, list 等.

### sum(iterable[, start])

返回可索引的参数元素之和(不能是字符串), 加上 start(默认是 0). 当 `iterable` 为空时, 返回 `start`

> 为什么要有一个`start`?
> 使得如果输入字符串组成的列表(可以是其他可索引的数据结构), 会抛出异常. 详见下面实例

```python
>>> sum([2, 3, 4])
9
>>> sum(['2', '3', '4'])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

其实现的原理是: 保证前面`iterable`中元素求和的结果与`start`的类型相同. 例如:

```python
>>> sum([2, 3, 4], 5)
14
>>> sum([[2, 3], [4]], [])
[2, 3, 4]
>>> sum([[2, 3], [4]])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'list'
```

### max

```python
max(iterable[, key=func]) -> value
max(a, b, c, ...[,key=func]) -> value
```

`key`是用来预处理数据的, 默认为 `lambda x: x`

# 其他特性

## `nonlocal`声明

使用方法:

```py
nonlocal <name>
```

效果: 在声明某个变量为`nonlocal`之后, 对其所有赋值语句, 都不会建立一个局部的绑定, 而是将该变量重新绑定到**当前 frame 向上能找到的第一个非局部 frame**中对该变量的绑定.

简单来说, 就是对该变量的所有赋值操作都改为操作能找到的第一个全局(这个全局是相对的)同名变量. 当然, 如果找不到一个同名全局变量, 就抛出异常.

下面的例子中, 如果在`withdrew()`中不将`balance`声明为 nonlocal:

- if 语句是可以通过的, 因为**访问**`balance`会找到当前 frame 的 parent,
  即`make_withdraw()`中声明的`balance`.
- 而 python 处理**赋值**操作就不一样了. 他会默认将`balance`绑定到局部, 而
  这就与 if 语句产生了冲突, 会抛出异常.

所以就需要`nonlocal`的声明, 告诉 python: **赋值语句也得去上面 frame 中找一个全局的.**

```py
# make_withdraw 建立一个银行账户, 并给出初始金额,
# 其返回一个函数withdrew(), 调用该函数的行为是从
# 银行账户里扣款
def make_withdraw(balance):
    def withdraw(amount):
        nonlocal balance
        if amount > balance:
            return 'Insufficient funds'
        balance = balance - amount
        return balance
    return withdraw

wd = make_withdraw(20)
wd(8)
```

## Python 动态类型

### 对象的垃圾回收

在 Python 中，每当一个变量被赋予了一个新的对象，之前对象占用的空间就会被回收。

```py
>>> x = 42
>>> x = 3.14
>>> x = "hello"
```

对象的引用值在此过程中被逐个回收，每次 x 指向一个新的对象，Python 将自动回收原来对象的内存空间。

Python 使用**引用计数**来实现该功能。为每个对象维护了一个引用计数器，记录了当前引用该对象的变量数目，一旦计数值变成 0，则其空间被回收。

### 深拷贝和浅拷贝

```py
L1 = [2, 3, 4]
L2 = L1
```

产生的结果是， L1 是一个包含了对象 2、3、4 的列表，当然列表自身也是个对象。L1 是一个变量，引用了该列表的对象。运行 L2 的赋值操作后，L1 和 L2 引用了相同的对象。

**此时如果使用索引对 L1 列表的某个元素进行修改，由于 L1 和 L2 引用的是同一个对象，相当于 L2 也做了修改**。这就是浅拷贝

浅拷贝是默认的，如果你希望使用深拷贝，即拷贝对象，而不是创建索引。对于列表来说，可以使用分片来实现

```py
L1 = [2, 3, 4]
L2 = L1[:]
```

对于其他的内置类型，有的也可以用 X.copy()方法；而且标准库的`copy`模块有一个通用的赋值任意对象类型的方法，也有一个用于**嵌套深拷贝**的特殊方法。

```py
import copy
X = copy.copy(Y)    # 只深拷贝top-level
X = copy.deepcopy(Y) # 嵌套进行深拷贝
```

### 判断相等

基于上述深拷贝和浅拷贝的理论，就有关于 Python 中变量判断相等的方法。
Python 中有两种不同的方法检查变量是否相等：

- `==` 操作符：测试两个变量引用的对象是否具有相同的值
- `is`操作符：检查对象的同一性，即是否指向**同一个对象**，是一种更加严格的相等性判断

## 字符串

### 特殊字符的转义

字符串中的`\n`会被识别为换行符，与 C 语言类似，但 Python 不识别结束符(`\0`)

在字符串第一个引号之前输入`r`，会关闭转义机制，将反斜杠当做普通字符来保持。常用与 Windows 环境中打开文件

```py
file = open(r'C:\my\path\file.txt', 'w')
```

### 索引和分片

分片的常见作用

#### 处理参数

在系统命令行中启动 python 程序时，获取附加的参数，需要使用内置的 sys 模块中的 argv 属性：

```py
# file echo.py
import sys
print(sys.argv)

% python echo.py -a -b -c
['echo.py', '-a', '-b', '-c']
```

通常你只对跟随在程序名后边的参数感兴趣，这就是分片的典型应用，`sys.argv[1:]`就能满足你

#### 处理文件

分片也常常用作清理输入文件的内容。如果知道一行会以换行符结尾，就可以使用`line[:-1]`，把这行去除最后一个字符之外的内容提取出来。

> 值得注意的是，去除换行符常常推荐采用`line.rstrip`方法，因为这个方法将会正确的处理**最后一行**的情况。

### 字符串和其他类型转换

字符串和整数

```py
>>> int("42"), str(42)
(42, '42')
```

字符和 ASCII 码

```py
>>> ord('s')
115
>>> chr(115)
's'
```

### 修改字符串

字符串是*不可变序列*，即不能通过索引来修改。

若想改变一个字符串，需要利用合并、分片这样的工具来建立一个新的字符串，并将结果赋值给原始字符串变量

```py
S = S[:4] + 'Bureger'
```

如果不得不对一个超长字符串进行多处的修改，为了优化脚本的性能和代码，可能需要将字符串转换为一个支持原处修改的对象，如 List

```py
>>> S = 'spammy'
>>> L = list(S)
>>> L
['s', 'p', 'a', 'm', 'm', 'y']
```

对于 L 可以使用索引来修改，完成后需要将其转换回字符串，可以使用字符串的`join`方法来实现:

```py
>>> S = ''.join(L)
```

### Python 中的 sprintf

```py
>>> 'That is %d %s bird!' % (1, 'dead')
That is 1 dead bird
```

### 字符串的方法

see Python standard manual

## 列表

列表与字典都是**其他对象的集合**。

列表能够完成 C 中结构体的工作，不需要手动实现。

Python 中的列表是：

- 任意对象的有序集合
- 通过偏移读取
- 可变长度、异构以及任意嵌套
- 对象引用的数组。类似与 C 语言中的指针数组

### 列表实现 LIFO

在某些应用中，会使用列表的`pop`和`append`方法，实现快速的 LIFO 对战结构。

## 字典

字典可以称作 Python 中最灵活的内置数据结构。字典当中的元素是通过 key 来存取，而不是通过偏移，类似 C++的 map。

作为内置类型，字典可以取代许多搜索算法和数据结构。有时也能执行其他语言中的记录、符号表的功能，可以表示稀疏（多数为空）的数据结构等。

字典的主要属性如下：

- 任意对象的无序集合
- 可变长、异构、任意嵌套
- 对象引用的散列表。字典的底层实现是散列表，一开始很小，根据要求增长。

> 列表和字典，不会经常用常量来创建，常使用动态方法

### 用字典模拟灵活的列表

列表对在其末尾外的元素赋值是非法的：

```py
>>> L = []
>>> L[99] = 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list assignment index out of range
```

虽然你可以预先分配足够的大空间，但这种情况用字典更为方便。字典的 key 为整数时，可以效仿列表在偏移赋值时增长：

```py
>>> D = {}
>>> D[99] = 'spam'
>>> D[99]
'spam'
>>> D
{99: 'spam'}
```

好处是， 此时的 D 仅有一个元素，看上去好像是有 100 个元素。

### 字典描述稀疏数据结构

### 字典创建的方法

共有四种不同的方案：

```py
{'name': 'mel', 'age': 45}

D = {}
D['name'] = 'mel'
D['age'] = 45

dict(name='mel', age=45)
dict([('name', 'mel'), ('age', 45)])
```

上面四种方案建立的字典是相同的，它们分别应用与不同的条件：

- 如果你可以事先拼出整个字典，那么第一种是很方便的
- 如果你需要一次动态地建立字典的一个字段，第二种比较合适
- 第三种关键字形式所需代码量比较少，但是 key 必须都是字符串才行
- 如果你需要在程序运行时把 key 和 value 逐步构建成序列，那么用第四种

如今的 Python 代码中，第三种方式比较流行。