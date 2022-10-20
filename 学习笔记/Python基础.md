# Python基础

## Python环境的安装

- 官方网址：python.org
- 下载：Downliads-->All releases-->下载
- 安装：
  1. ![image-20220924213204638](https://s2.loli.net/2022/10/19/DPBdQYrNZ9R8XzL.png)
  2. ![image-20220924213219920](https://s2.loli.net/2022/10/19/3fAhQCaDiIsN4x8.png)
  3. ![image-20220924213245618](https://s2.loli.net/2022/10/19/yhnWxAY4GRqbtVd.png)
  4. ![image-20220924213259450](https://s2.loli.net/2022/10/19/RUC4QAuNDe1lSyv.png)

## Python版本

- python2.x

  2020年停止更新，源码不规范、重复代码较多。

- python3.x

  功能更强大且修复了很多bug，源码清晰且简单。

## Python解释器

- cpython

  将python代码转化为c语言能识别的二进制码

- jpython

  把python代码转化为java语言能识别的二进制码

- PyPy

  将所有代码一次性编译成二进制码，加快执行效率（模仿编译型语言）

- 其他语言解释器

  把python代码转化成其他语言能识别的二进制码

## 变量与数据类型

### 什么是变量？

- 一个实体的指代

  ```python
  # 查看变量的内存地址
  x = 2
  print(id(x))
  
  # 变量赋值 "="
  x = 100
  ```

- 变量被重新赋值后，原来的对象内存空间被挥手

- python没有常量类型(程序员规定命名都是大写字母，那么就视为常量)

### python的六大基本类型



### python的四大基本数据结构

## Python运算符

### 算数运算符

![image-20220925144629374](https://s2.loli.net/2022/10/19/bhg4VQiLcHAIP7w.png)

### 赋值运算符

![image-20220925144735812](https://s2.loli.net/2022/10/19/zC8s5j7YV6IUuQt.png)

### 比较（关系）运算符

![image-20220925144904430](https://s2.loli.net/2022/10/19/DQilMped3sPCk7J.png)

### 逻辑运算符

![image-20220925144956373](https://s2.loli.net/2022/10/19/84OWxICFbcLn6ND.png)

### 位运算符

| 运算符 |  含义  |      用法       |
| :----: | :----: | :-------------: |
|   &    |  位与  | ```str(12&8)``` |
|   \|   |  位或  | ```str(12|8)``` |
|   ^    | 位异或 | ```str(12^8)``` |
|   ~    | 位取反 | ```str(~12)```  |
|   <<   | 左移位 |                 |
|   >>   | 右移位 |                 |

### 运算符优先级

从低到高

| 运算符                                                | 描述                                   |
| ----------------------------------------------------- | -------------------------------------- |
| ```or```                                              | 布尔或运算                             |
| ```and```                                             | 布尔与运算                             |
| ```not```                                             | 布尔非运算                             |
| ```in```,```not in```,```is```,```is not```,<，！=... | 比较运算符、成员检测运算符、标识号检测 |
| ```+```、```-```                                      | 加法和减法                             |
| ```*```，```/```,```//```,```%```                     | 乘法、除法、整除、取余                 |
| ```**```                                              | 幂                                     |

实际上，通过圆括号自定义优先级

## Python变量

六种基本类型：数值（int、float）、字符串(str)、列表(list)、元组(tuple)、字典(dict)、集合(set)

注意：```True == 1``` ```False == 0```

**可变类型：列表、字典、集合**

**不可变类型：数值、字符串、元组**



### 列表

- 概念

可以用来装载**不同数据类型**的数据结构

- 特点
  - 有序
  - 可以状态任意数据类型
  - **可以更改**

- 表示方法
  - list()工厂函数新建一个列表
  - []生命一个列表

#### 常用方法

**添加元素**

- list.append()  向原列表末尾追加元素

  ```python
  l = []
  l.append(100)
  # 【100】
  ```

- ```+``` 实现两个列表的合并，返回一个新的列表

  ```python
  l1 = [1,3,5]
  l2 = [2,4,6]
  l = l1 + l2
  # [1,3,5,2,4,6]
  ```

  ```+=```实现原列表追加另一个列表（发现还是原地址）

  ```python
  l = ['a']
  print(id(l))
  l += ['b', 'c']
  print(id(l))
  ```

- list.insert(index, value)  在指定的位置插入元素

  ```python
  l = ['a']
  l.insert(0, 'str')
  print(l)
  # ['str', 'a']
  ```

**查询元素**

- 切片

  ```python
  l[0:10]
  
  l[20:30]
  
  l[-1]
  ```

- list.index(value)   返回value所在的下标

  注意：配合插入或者修改可以实现指定索引处的插入和更新操作

  ```python
  l = list(range(10,0, -1))
  print(l)
  l.insert(l.index(8)+1, 7)
  print(l)
  
  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # [10, 9, 8, 7, 7, 6, 5, 4, 3, 2, 1]
  ```

**更新**

- 索引赋值

- 切片赋值

  ```python
  l = list(range(10,0, -1))
  print(l)
  l[0:2] = 'abcdef'
  print(l)
  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # ['a', 'b', 'c', 'd', 'e', 'f', 8, 7, 6, 5, 4, 3, 2, 1]
  ```

  ```python
  arr = [x for x in range(15)]
  print(arr)
  
  # 第一种列表切片方法  slice(start,end,step)
  myslice = slice(0, 7, 2)
  res = arr[myslice]
  print(res)
  
  # 第二种列表切片方法	arr[start, end, step]
  res2 = arr[0:7:2]
  print(res2)
  
  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
  # [0, 2, 4, 6]
  # [0, 2, 4, 6]
  ```

  

**删除**

- list.pop() 末尾删除元素

  list.pop(index) 删除指定索引处的元素

  ```python
  l = list(range(10,0, -1))
  print(l)
  l.pop()
  print(l)
  
  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # [10, 9, 8, 7, 6, 5, 4, 3, 2]
  ```

- list.clear() 清除列表元素

- list.remove(value) 从列表中删除值value的元素

  ```python
  l = list(range(10,0, -1))
  print(l)
  l.remove(6)
  print(l)
  # [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # [10, 9, 8, 7, 5, 4, 3, 2, 1]
  ```

**排序**

- list.sort() 对原列表排序

- sorted(list)  对原列表排序后返回一个新的列表

- list.reverse() 对原列表反转

- reversed(list) 对原列表反转后返回一个对象，需要使用list()工厂函数得到反转后的列表

  ```python
  l1 = list(range(10, 0, -1))
  l2 = list(range(10, 0, -1))
  print(f"l1: {l1}")
  print(f"l2: {l2}")
  
  l1.sort()
  l2_sorted = sorted(l2)
  print(f"list.sort: {l1}")
  print(f"sorted(l2): {l2_sorted}")
  
  l1.reverse()
  l2_revsd = list(reversed(l2_sorted))
  print(f"l1.reverse: {l1}")
  print(f"reversed(l2): {l2_revsd}")
  
  # l1: [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # l2: [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # list.sort: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
  # sorted(l2): [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
  # l1.reverse: [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  # reversed(l2): [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
  ```

### 元组

- 概念

  不可修改的列表，常用来表示记录

- 特点

  - 有序

  - 可以装在数据类型

  - **不可更改**

- 表示方法

  - tuple()工厂函数创建一个元组

  - (,)用来声明一个元组

    ```python
    （1，）
    （1，2，3）
    ```

#### 常用方法

**检索、查询**

```同list的```

- 索引取值
- 切片
- index方法

### 字典

- 概念

  字典也称```hashtable```,通过hash(散列函数)将传入的key值生成地址来查找value

- 特点

  - 无序

    python3.6是有序的，但是之后的版本又改回无序

  - 字典中的key是可hash的(不可改变、字典中唯一)

  - key对应的内容是可更改的

  - 是可变类型

- 表示方法

  - dict()工厂函数创建一个字典

    ```python
    dict(a=2)
    {'a':2}
    ```

  - 通过{}创建一个空字典

    ```python
    a = {'a':100}
    ```

#### 常用方法

**增加**

- 键值对赋值

- d1.update(d2) 合并字典

  ```python
  d1 = {}
  d1['key1'] = 100
  print(d1)
  d2 = {'key2': 123, 'key3': 333}
  d1.update(d2)
  print(d1)
  # {'key1': 100}
  # {'key1': 100, 'key2': 123, 'key3': 333}
  ```

- dic.setdefault(key, defVal) 设置默认值，如果有复制，该方法不起效

  ```python
  print(d1)
  d1.setdefault('key3', 10010)
  d1.setdefault('key4', 11111)
  print(d1)
  
  # {'key1': 100, 'key2': 123, 'key3': 333}
  # {'key1': 100, 'key2': 123, 'key3': 333, 'key4': 1111} # key3 设置后不生效，key4生效
  ```

**查询**

- 键值查询  {如果查询的键不存在，报错}

- dic.get(key, defVal)  获取值  {如果查询的键不存在，返回默认值defVal}

  ```python
  print(d1)
  v1 = d1['key1']
  print(v1)
  # v2 = d1['key5']
  # print(v2)
  v3 = d1.get('key3')
  print(v3)
  v4 = d1.get('key5')
  print(v4)
  v5 = d1.get('key5', '空')
  print(v5)
  
  # {'key1': 100, 'key2': 123, 'key3': 333, 'key4': 11111}
  # 100
  # 333
  # None
  # 空
  ```

- dic.keys()  返回字典dic中所有的key值对象，通过list()工厂函数得到字典的所有key值

- dic.values() 返回字典dic中所有的value值对象，通过list()工厂函数得到字典所有的value值

- dic.items() 返回所有的键值对

**更新**

- 键值对更新
- dic.update(key， value) 

**删除**

- dic.pop(key)		删除当前key所对应的键值对，并返回删除的value
- dic.popitem()  随机返回一个键值对，并从原来的dic中删除该键值对

```python
# d1.pop('key2')
# print(d1)

rt = d1.popitem()
print(rt)
print(d1)

# ('key4', 11111)
# {'key1': 100, 'key2': 123, 'key3': 333}
```

 

### 集合

- 概念

  set是没有value的字典

- 特点

- 无序

- 表示方法

  - set()工厂函数创建一个集合

    ```python
    set([1,2,3])
    ```

  - 通过 {} 配合元组创建一个非空集合

#### 常用方法

**添加**

- s.add()

**检索**

- 索引
- in 成员检测

**更新**

- update()
- union()  返回一个新的集合

```python
s = {'a'}
print(s)
s.update({'c', 'd'})
print(s)
ss = s.union({'a', 'e', 'f'})
print(ss)

# {'a'}
# {'d', 'c', 'a'}
# {'c', 'f', 'd', 'a', 'e'}
```

**删除**

- remove(val)  删除指定的集合元素，如果元素不存在，报错
- discard(val) 删除指定的集合元素，如果元素不存在，不报错
- pop() 随机删除集合中的一个元素

### 命名空间、作用域

#### [命名空间](https://www.runoob.com/python3/python3-namespace-scope.html)

- 基本概念	

  ​	命名空间(Namespace)是从名称到对象的映射。
  ​	命名空间提供了在项目中避免名字冲突的一种方法。各个命名空间是独立的，没有任何关系的，所以一个命名空间中不能有重名，但不同的命名空间是可以重名而没有任何影响。

![img](https://s2.loli.net/2022/10/19/d3r7yafsqvQlUgX.jpg)

- **<font color="green">三种命名空间</font>**
  - **内置名称（built-in names**）， Python 语言内置的名称，比如函数名 abs、char 和异常名称 BaseException、Exception 等等。
  - **内置名称（built-in names**）， Python 语言内置的名称，比如函数名 abs、char 和异常名称 BaseException、Exception 等等。
  - **局部名称（local names）**，函数中定义的名称，记录了函数的变量，包括函数的参数和局部定义的变量。（类中定义的也是）。

- 命名空间查找顺序

  **<font color="red">局部的命名空间去 -> 全局命名空间 -> 内置命名空间</font>**

- 命名空间的生命周期：

  命名空间的生命周期取决于对象的作用域，如果对象执行完成，则该命名空间的生命周期就结束。

***

#### 作用域

- 相关概念
​	作用域就是一个 Python 程序可以直接访问命名空间的正文区域
​	程序创建，访问，改变一个变量时，都是在一个保存该变量的空间内进行，这个空间被称为命名空间，即作用域

- built-in  **内置变量**

  可以在Python环境中任何模块，任意位置访问和调用

- global  **全局变量**

  - 只作用域当前模块（可以理解为当前python脚本文件）
  - 可以理解为定义在函数、类外的变量就是全局变量，否则为局部变量
  - 如何将局部变量变为全局变量
    - 使用global关键字

- enclose  **自由变量、嵌套变量**

  在嵌套函数中，访问非函数体之外的非全局变量

  - 只作用于嵌套函数体

  - 最大的应用就是闭包

  - 将局部变量变成自由变量

    ```nolocal```关键字

    ```python
    def make_averager():
        total = 0
        count = 0
    
        def average(value):
            nonlocal total, count		# 将其指定为自由变量
            total += value
            count += 1
            return total / count
    
        return average
    
    
    fc = make_averager()
    print(fc((1)))
    print(fc((2)))
    print(fc((3)))
    ```

    

- local  **局部变量**

  - 只作用于当前函数体
  - **一旦变量在函数体中赋值**，那么该变量相对于该函数来说是局部变量

### 闭包和装饰器

#### 闭包

闭包指延申了作用域的函数，也就是作用域中的enclosed的概念

```python
def make_averager():
    series = []
    def average(value):
        series.append(value)
        return sum(series) / len(series)
    return average

fc = make_averager()
print(fc(1))
print(fc(2))
print(fc(3))
print(fc(4))

# 1.0
# 1.5
# 2.0
# 2.5
```



```python
def make_averager():
    total = 0
    count = 0

    def average(value):
        nonlocal total, count
        total += value
        count += 1
        return total / count

    return average


fc = make_averager()
print(fc((1)))
print(fc((2)))
print(fc((3)))
```

#### [装饰器](https://www.runoob.com/w3cnote/python-func-decorators.html)

- 实现原理

  闭包，本质将函数作为参数传递给一个可调用的对象

- 目的

  增加和拓展函数或者类的行为

- 装饰器实现过程

##### 将函数作为参数传递给另外一个函数

```python
def func1(x, y):
    return x + y


def func2(x, y):
    return x * y


def func_dectorator(func):
    def wapper(*args, **kwargs):
        # print(func(*args, **kwargs))
        return func(*args, **kwargs)

    return wapper


fc1 = func_dectorator(func1)
res1 = fc1(1, 2)  # 1 + 2
print(res1)
fc2 = func_dectorator(func2)
res2 = fc2(2, 3)  # 2 * 3
print(res2)
```

**结果：**

<img src="https://s2.loli.net/2022/10/19/VntJBNRbLrg8qph.png" alt="image-20221002210750951" style="zoom: 150%;" />

#### 装饰器的语法糖

```python
from functools import wraps
def func_dectorator(func):
    @wraps(func)
    def wapper(*args, **kwargs):
        print(f"函数名:"+func.__name__)
        return func(*args, **kwargs)

    return wapper


@func_dectorator	# @func_dectorator 等价于 func1 = func_dectorator(func1)
def func1(x, y):
    return x + y


@func_dectorator	# @func_dectorator 等价于 func2 = func_dectorator(func2)
def func2(x, y):
    return x * y

print(func1(2, 3))
print(func2(3, 4))
```

**注意：**```@wraps```**接受一个函数来进行装饰，并加入了复制函数名称、注释文档、参数列表等等的功能。这可以让我们在装饰器里面访问在装饰之前的函数的属性。**

**结果：**

<img src="https://s2.loli.net/2022/10/19/UHpN1W2cjhVsXgf.png" alt="image-20221002211732416" style="zoom: 150%;" />

#### 带参数的装饰器

​	带参数的装饰器，实质上就是在一个带有参数的函数中，嵌套了一个装饰器

```python
from functools import wraps
import time


def outer(time=None):
    def func_dectorator(func):
        @wraps(func)
        def wapper(*args, **kwargs):
            print(f"日期:{time}  函数名:{func.__name__}")
            return func(*args, **kwargs)

        return wapper

    return func_dectorator


@outer()
def func1(x, y):
    return x + y


@outer(time=time.time())
def func2(x, y):
    return x * y


print(func1(2, 3))
print(func2(2, 3))
```

**结果：**

<img src="https://s2.loli.net/2022/10/19/qpjg6R5wGyha4DM.png" alt="image-20221002214547383" style="zoom: 130%;" />

#### 装饰器类

​		装饰器类比函数式装饰器看起来更方便直观，结构清晰。举个例子，假设我们有将日志输出到日志文件的装饰器，但是在某些状况下，我们希望在保存日志的基础上、又将日志信息同步到邮箱、短信等需求时，装饰器类的优势体现出来了，通过类特有的继承方式，就可以重新构建，使其具有其他功能。

```python
import datetime
from functools import wraps


class Logit(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile

    def __call__(self, func):
        @wraps(func)
        def warpper(*args, **kwargs):
            logString = f"Date:{datetime.date.today()} Name:{func.__name__}"
            print(logString)
            with open(self.logfile, 'a') as f:
                f.write(logString + '\n')

            return func(*args, **kwargs)

        return warpper

    def printLog(self):
        pass


@Logit()
def func1(x, y):
    return x + y


@Logit()
def func2(x, y):
    return x * y


print(func1(2, 3))
print(func2(2, 3))
```

**结果：**

![image-20221002221629127](https://s2.loli.net/2022/10/19/K5tRv4HcUpmGFyw.png)

定义发送邮件的装饰器类，继承上面的Logit

```python
import datetime
from functools import wraps


class Logit(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile

    def __call__(self, func):
        @wraps(func)
        def warpper(*args, **kwargs):
            logString = f"Date:{datetime.date.today()} Name:{func.__name__}"
            print(logString)
            with open(self.logfile, 'a') as f:
                f.write(logString + '\n')
            self.notify()
            return func(*args, **kwargs)

        return warpper

    def notify(self):
        pass


class EmailLogit(Logit):
    def __init__(self, email="1072002783@qq.com", *args, **kwargs):
        self.email = email
        super(EmailLogit, self).__init__(*args, **kwargs)

    def notify(self):
        print(f"时间：{datetime.date.today()} 发送邮件：" + self.email)


#@EmailLogit()
@EmailLogit(email='2079832053@qq.com', logfile='outFnc1.log')
def func1(x, y):
    return x + y


@EmailLogit()
def func2(x, y):
    return x * y


print(func1(2, 3))
print(func2(2, 3))

```

**结果：**

![image-20221002222221581](https://s2.loli.net/2022/10/19/lH3mB2aQngYwtq9.png)

## Python函数

- 什么是函数？

  函数是一段可以被另外一段程序调用的程序代码，也称子程序、方法

- 特点

  - 可重复使用
  - 可相互调用

- 函数的目的

  实现代码复用

### 定义函数

```python
def test(arg):
	return "test"+str(arg)

res = test("wjz")
```

函数的组成：

- 参数列表

  - 必须参数（位置）

  - 关键字参数（函数参数默认值是None，需要根据关键字传递）

    ```python
    def test(arg1=None,arg2=Nne):
    	return "test"+str(arg1)
    ```

  - 不定长参数

    ```python
    def test(*args):
    	print(args)
    	retrun None
    
    test("fafa", 1213)
    # ('fafa', 1213)
    ```

    ```在装饰器中大量应用不定长参数```

    - ‘*’ 表示省略，省略了参数tuple（元组）
    - ‘**’ 省略了参数dict（字典）

    ```python
    def test(*args, **kwargs):
        print(args)
        print(kwargs)
        return None
    
    test("fafa", 1213, class01="fafdafsdf3", class02="213rwds"
    # {'class01': 'fafdafsdf3', 'class02': '213rwds'}
    ```

    

- 函数体

- 返回值

  如果不指定返回值，默认返回```None```

### 内置函数

​	Python自带的，可以全局调用```dir(__builtins__)```可以产看所有的内置函数

```python
from pprint import pprint
pprint(dir(__builtins__))
```

- 常见的内置函数
  - str、int、bool、bytes、float
  - list、set、tuple、dict
  - id
  - dir  查看当前类/函数（对象）包含的属性和方法
  - max、sum、min  对一个序列取最大值、和、最小值
  - range 返回一组数字区间中可迭代数字对象

### 匿名函数

​	就是没有名字的函数，一般都是供高阶函数调用

- lambda声明

- 函数体是纯表达式

  - 不能有判断语句

    例外：  ```lambda x: 返回值 if 表达式 else 返回值``` 

    eg: ```lambda x: True if x %2 == 0 else False```

  - 不能有循环语句

  - 不能有异常捕获

  - 不能有赋值语句

  - 不能有返回值

    默认表达式的运算结果就是返回值

  ```python
  f = lambda x,y:x+y
  print(f(2,3))
  
  # 排序用法
  arr = [[1, 2], [2, 1], [6, 5], [3, 4]]
  print(arr)
  arr.sort(key=lambda x: x[1])
  print(arr)
  
  # [[1, 2], [2, 1], [6, 5], [3, 4]]	# 原始顺序
  # [[2, 1], [1, 2], [3, 4], [6, 5]]  # 按照元素的第二个值进行了排序
  ```

### 高阶函数

​	接受函数作为参数 或者  把函数作为结果返回

- map()   映射：返回一个map对象

  ```python
  # 返回一个在原列表元素完成乘方运算的map对象
  arr = map(lambda x:x**2, [1,3,5])
  print(list(arr))
  ```

- filter()   过滤：返回一个filter对象

  ```python
  # 返回一个列表中元素值大于10的filter对象
  res = filter(lambda x:x>10, [x for x in range(30)])
  print(list(res))
  ```

### 递归函数

​	在函数中调用函数自身

- 核心思想： 将大任务拆分为子任务

## 字符串

### 字符串和字节序列

- 字符：

- 字节：

​	字符的二进制表现形式

- 码位：

​	计算机实际进行显示的内容通过码位	

```python
'真好'.encode('unicode_escape').decode()
'\\u771f\\u597d'

'\u771f\u597d'
'真好'
```

- 编码：

  - 字节序列(bytes)-->字符序列(string)	解码 decode
  - 字符序列(string)-->字节序列(bytes)   编码  encode 

  ```python
  b = '真好'.encode('utf-8')
  b.decode('utf-8')
  '真好'
  ```

- 乱码问题

  检查编码：没有通过字节序列来得出编码格式

  借助第三方库```chardet```实现

  ```python
  pip install chardet
  
  import chardet
  b = '你好'.encode('utf-8')
  chardet.detect(b)
  # {'encoding': 'utf-8', 'confidence': 0.7525, 'language': ''}
  ```

- 混合编码

  就是字符串是通过两种以上编码方式完成的

  ```python
  b.decode('utf-8', errors='ignore')
  
  b.decode('utf-8', errors='replace')
  ```

  

### 字符串的CRUD操作

- 字符串创建

  ```python
  a = 'a'
  a = a + ' hello'
  a
  # 'a hello'
  
  id(a) # 查询变量指向的对象地址
  
  ```

- 字符串查询

  'a hello'

  - 根据索引

  ```python
  a = 'a'
  a = a + ' hello'
  a
  'a hello'
  a[2]
  'h'
  ```

  - str.find()函数，返回最近对应字符的下标

  ```
  a.find('l')
  4
  ```

  - str.index()函数

  ```
  a.index('l')
  4
  ```

  **注意：index()找不到目标函数会报错，find()函数找不到会返回-1**

  - str.startswith() 和 str.endswith() 判断字符串以xxx开头或者结尾

  ```python
  >>> a='2022-10-02:13:04:56'
  >>> a.startswith('2022-10-02')
  True
  ```

- 字符串更新

  注意：str.replace()返回的是一个新的字符串

  - str.replace(oriStr, newStr)

  ```python
  >>> a = 'hello word, hello word'
  >>> a.replace('word', 'world')
  'hello world, hello world'
  ```

  - str.split('分隔符')     '拼接符'.join(strList)

    split()返回的是列表

    join()拼接的是列表，返回字符串

  ```python
  >>> a = 'hello world 你好'
  >>> a.split(' ')
  ['hello', 'world', '你好']
  >>> b = a.split(' ')
  >>> b
  ['hello', 'world', '你好']
  >>> c = ' '.join(b)
  >>> c
  'hello world 你好'
  ```

- 字符串删除

  - str.strip()  删除两端的空白字符
  - str.lstrip()
  - str.rstrip()

  ```python
  >>> a = '  dfdsa fgfg gfsg '
  >>> a.strip()
  'dfdsa fgfg gfsg'
  ```

  

### 字符串的输入和输出

输出

- 保存到文件

  ```python
  # w写 a追加 r读
  output = open('out.txt', 'w', encoding='utf-8')
  content = '你好啊'
  # 写如文件
  output.write(content)
  # 关闭文件句柄
  output.close()
  ```

  

输入

- 读取文件

  ```python
  input = open('out.txt', 'r', encoding='utf-8')
  content = input.read()
  print(content)
  ```

  

### 字符串的格式化输出

- format

  - 按照传入的参数默认顺序

  ```python
  a = "play"
  b = 'baskball'
  
  print("{}, {}".format(a, b))
  # play, baskball
  ```

  - 按照指定参数索引（实现重复打印）

  ```python
  a = "play"
  b = 'baskball'
  
  print("{0}, {1}, {1}, {0}".format(a, b))
  # play, baskball, baskball, play
  ```

  - 按照关键词参数

  ```python
  print("{a} {b} {b} {a}".format(a="hello", b="world"))
  # hello world world hello
  ```

  - 小数位表示

  ```python
  PI = '{:.2f}'.format(3.1415926)
  print(PI)
  ```

- 按变量名(推荐，但是只有3.6版本以上可以使用)

  ```python
  a = 'hhh'
  b = '123'
  print(f'{a} {b} {b} {a}')
  # hhh 123 123 hhh
  ```

- %格式化

  ```python
  outStr = "name:%s age:%d sex:%s" % ('wjz', 15, '男')
  print(outStr)
  ```

## 变量、对象、引用

- 对象：内存开辟的空间，用来存放赋值的内容
- 变量：对象地址的别名
- 引用：就是自动形成变量指向对象的指针

## 异常

- Exception  

  - 所有异常的基类

  - 尽量不要使用基类捕获异常

- 常见异常

  - indexError

    索引值超过序列长度（索引越界）

  - KeyError

    字典获取一个不存在的键值对

  - ValueError

    传入的参数错误

  - TypeError

    类型错误，常见于运算

  - SyntaxError

    语法报错

  - indentationError

    缩进错误（混用tab键和空格键）

    复制程序文本的时候执行报错

- 如何处理异常

  - 处理
  - 抛出新异常

  ```python
  def my_sub(a, b):
  	try:
  		return a / b
  	except ZeroDivsionError:
  		raise Exception("paramsError")
      finally:
      	pass
  
  my_sub(1,0)
  ```

  - 忽略异常（不推荐）

- 自定义异常

  ```python
  class ParamsError(Exception):
      pass
  
  
  def my_sub(a, b):
      try:
          return a / b
      except ZeroDivisionError:
          raise ParamsError("分母不为0")
      finally:
          pass
  
  my_sub(1, 0)
  ```

## 包、模块

### time模块

​	调用的都是系统级别的接口，提供时间的访问和转换等功能

- 获取当前时间

```python
import time

timeObj = time.localtime()	# 返回的是一个struct_time的时间对象
print(timeObj)

# time.struct_time(tm_year=2022, tm_mon=10, tm_mday=3, tm_hour=13, tm_min=59, tm_sec=30, tm_wday=0, tm_yday=276, tm_isdst=0)
```

​	**注意：返回的时间需要进一步进行一个格式化**

- 获取时间戳

```python
import time

timeStamp = time.time()
print(timeStamp)

# 1664777076.4226067
```

- 时间的格式化输出

```python
import time

timeObj = time.localtime()
nowTime = time.strftime('%Y-%m-%d %H:%M:%S', timeObj)	# 格式化日期
print(nowTime)

# 2022-10-03 14:08:05
```

- 运算

  ​		方法：time没有直接进行时间运算的函数，通过将时间对象转化为list，对相应的时间重新赋值，通过```time_struct_time```生成一个新的时间对象

```python
import time

timeStruct = list(time.localtime())
print(timeStruct)
timeStruct[2] = 6
print(time.struct_time(timeStruct))

# [2022, 10, 3, 14, 16, 4, 0, 276, 0]
# time.struct_time(tm_year=2022, tm_mon=10, tm_mday=6, tm_hour=14, tm_min=16, tm_sec=4, tm_wday=0, tm_yday=276, tm_isdst=0)
```

- 时间休眠

  当前程序休眠n个时间单位

```python
import time


def func():
    print(f"{time.strftime('%Y-%m-%d %H:%M:%S')} start time sleep!")
    time.sleep(3)
    print(f"{time.strftime('%Y-%m-%d %H:%M:%S')} end time sleep!")


func()
```

结果：

![image-20221003142451005](https://s2.loli.net/2022/10/19/KMZGnm9a1fgPHwI.png)

### datetime模块

​	对time进一步封装，提供了更方便使用的接口（如时间运算等）

- 获取时间

```python
import datetime

nowTime = datetime.datetime.today()
# nowTime = datetime.datetime.now(tz=None)   两种方法都可以获取本时区时间
print(nowTime)

# 2022-10-03 14:27:48.530054

#------------------------------------交互模式--------------------------------------#
# 注意，默认的datetime.datetime.today()返回的是一个对象，但是print()方法后默认调用了__str__方法
>>> import datetime
>>> datetime.datetime.today()
datetime.datetime(2022, 10, 3, 14, 29, 52, 397266)
>>> nowTime = datetime.datetime.today()
>>> nowTime
datetime.datetime(2022, 10, 3, 14, 30, 26, 682717)
>>> print(nowTime)
2022-10-03 14:30:26.682717
```

- 获取指定时区的时间```nowTime = datetime.datetime.now(tz=None)```指定时区```tz```参数即可。

```python
import datetime
import pytz

nowTime1 = datetime.datetime.now(tz=None)
nowTime2 = datetime.datetime.now(tz=pytz.timezone('Asia/Jakarta'))
print(nowTime1)
print(nowTime2)

# 2022-10-03 14:44:46.492441
# 2022-10-03 13:44:46.515382+07:00
```

- 获取UTC时间

```python
import datetime

utcTime = datetime.datetime.utcnow()	# 返回的是一个对象
print(utcTime)

# 2022-10-03 06:46:31.848266
```

- 时间格式转换

  - **datetime.datetime --> str**

  ```python
  import datetime
  
  nowTime1 = datetime.datetime.now(tz=None)
  print(nowTime1.strftime('%Y-%m-%d %H:%M:%S'))
  
  # 2022-10-03 14:49:51
  ```

  - **str --> datetime.datetime**

  ```python
  timeStr1 = '2022-10-03'
  timeObj1 = datetime.datetime.strptime(timeStr1, '%Y-%m-%d')
  
  timeStr2 = '2022-10-03 14:49:51'
  timeObj2 = datetime.datetime.strptime(timeStr2, '%Y-%m-%d %H:%M:%S')
  
  # 实际上timeObj1 和 timeObj2 是时间日期对象，调用print()方法打印的是字符串
  print(timeObj1)
  print(timeObj2)
  
  #---------------------------------交互模式------------------------------------#
  # 以下是交互模式下输出的内容，可以看到timeObj1 和 timeObj2 是时间日期对象
  >>> timeStr1 = '2022-10-03'
  >>> timeObj1 = datetime.datetime.strptime(timeStr1, '%Y-%m-%d')
  >>> 
  >>> timeStr2 = '2022-10-03 14:49:51'
  >>> timeObj2 = datetime.datetime.strptime(timeStr2, '%Y-%m-%d %H:%M:%S')
  >>> 
  >>> print(timeObj1)
  2022-10-03 00:00:00
  >>> print(timeObj2)
  2022-10-03 14:49:51
  >>> timeObj1
  datetime.datetime(2022, 10, 3, 0, 0)
  >>> timeObj2
  datetime.datetime(2022, 10, 3, 14, 49, 51)
  ```

  - **datetime.datetime --> timestamp**
  
  通过接口```timestamo()```转换
  
  ```python
  import datetime
  
  nowTime = datetime.datetime.now(tz=None)
  timeStamp = nowTime.timestamp()
  print(timeStamp)
  
  # 1664781467.542497
  ```
  
  - **timestamp --> datetime.datetime**
  
  ```python
  import datetime
  
  timeStamp = 1664781467.542497
  timeObj = datetime.datetime.fromtimestamp(timeStamp, tz=None)
  print(timeObj)
  
  # 2022-10-03 15:17:47.542497
  ```

- 时间运算

  - timedelta

    只作用于 datetime.datetime 格式

  ```python
  import datetime
  
  nowTime = datetime.datetime.now(tz=None)			# datetime.datetime 对象
  nowTime_1 = nowTime + datetime.timedelta(hours=1)	# 小时数加1 减1的时候是-1
  print(nowTime)
  print(nowTime_1)
  
  # 2022-10-03 15:27:29.075083
  # 2022-10-03 16:27:29.075083
  ```

  ![image-20221003153139369](https://s2.loli.net/2022/10/19/q7iNQ2GDgAjZetf.png)

## 类

- 什么是类?

  拥有相同功能或者属性的对象集合

- 类的创建

  ```python
  class ClassName(object):
      pass
  ```

- 类的实例化

  使用```new```创建对象

- 类的初始化

  类创建一个新的实例的时候，会调用```__init__```这个特殊方法

- 关于self

  self指代一个实例

### 面向对象

#### 继承

- ```__mro__```方法获取类的继承顺序
- ```super```方法调用指定类的前一个继承类的指定方法

```python
class A(object):
    def __init__(self):
        print(f'init A')


class B(object):
    def __init__(self):
        print(f'init B')


class C(A, B):
    def __init__(self):
        mro = C.mro()		# 获取类C的继承关系
        print(mro)
        super(mro[1], self).__init__()	# 使用super函数修改类的继承顺序

c = C()
```

#### 多态

- 覆盖（不同的类---指继承，子类和父类的方法名相同，但是实现的内容被子类重写了）
- 重载（同一个类---方法名相同，参数列表、返回值不同）

#### 封装

### 类属性、实例属性

- 类属性(类变量)

  ​		类变量在整个实例化的对象中是公用的。类变量定义在类中且在函数体之外。类变量通常不作为实例变量使用。

- 实例属性

  ​		在类的声明中，属性是用变量来表示的，这种变量就称为实例变量，实例变量就是一个用 self 修饰的变量。

### 类方法、静态方法、实例方法

- 类方法

  只能被类自身使用的方法是类方法

- 静态方法

  可以通过类名直接调用的方法

  ​		在一些工具类的编写的时候非常友好，比如编写一个操作数据库的类，其中每种操作数据库的方法就可以定义为一个静态方法，直接通过类名调用即可。

  eg：下面是一个工具类的例子，实现方式借助了类方法和实例方法

  ```python
  import pymysql
  
  
  class DB(object):
  
      @classmethod
      def __db(self, sql=None):
          # 这一部分未来可以放在一个配置文件中获取（待优化）
          host = '192.168.72.201'  # 指定数据库之际
          user = 'root'  # 指定数据库用户
          password = '123456'  # 指定数据库密码
          dbName = 'test'  # 指定要连接的数据库
  	    
          # 连接数据库
          db = pymysql.connect(host=host, user=user, passwd=password, db=dbName)
  
          cur = db.cursor()  # 生成游标
          flag = False  # sql执行成功与否标志
          content = None  # sql执行后返回的内容
  
          if sql:
              try:
                  cur.execute(sql)  # 执行sql
                  db.commit()  # 成功后提交事务
                  flag = True  # 修改标志flag
                  content = cur.fetchall()  # 获取返回的内容
              except:
                  db.rollback()
              finally:
                  db.close()
  
          return flag, content  # 返回
  	
      # 针对数据库查询的方法
      @staticmethod
      def get(sql=None):
          return DB.__db(sql)
  
  
  print(DB.get('select * from student'))
  ```

- 实例方法

  只有类进行实例化后才能使用的方法。

总结：定义私有方法或者私有属性的方式是在变量或者函数名之前加```__```，如```__getName()```。

## 接口和抽象基类

### 接口

​	对象公开方法的子集，让对象在系统中扮演特定的角色。

​	eg1：list类实现了增删改查的接口，只要有一个接口未实现，就不是list
​	eg2：tuple类实现了查的接口

- 协议

  非正式的接口，协议和继承没有接口，一个类可能实现多个接口，从而让实例扮演多个角色。

  list扮演者列表的角色，但同时也是一个序列，序列并不是一个实体类。

以下是协议或者接口实现的案例：将字典a 和 字典b通过 ```+```实现合并

```python
class MyDict(dict):
    def __add__(self, other):
        self.update(other)
        return self


dic1 = MyDict(a=1, b=2)
dic2 = MyDict(c=3, d=4)
print(id(dic1), dic1)
print(id(dic2), dic2)
dic1 += dic2
print(id(dic1), dic1)
```

<img src="https://s2.loli.net/2022/10/19/Jex3DMXn6PmYf5v.png" alt="image-20221003203944596" style="zoom:150%;" />

### 抽象基类

​	把客观事物封装成抽象的元类（区分概念和实现）

- 只要有abc.abstractmethod装饰器的类就是抽象基类

## requests模块

- 作用

  使用python程序模拟浏览器发起http请求

- 一个模拟请求的组成部分
  - url
  - method
  - body
  - headers

- 模拟请求

```python
import requests


def request_baidu():
    url = 'https://www.baidu.com/'
    body = ''
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }
    response = requests.get(url=url, headers=headers)
    print(response)


if __name__ == '__main__':
    request_baidu()
```

## 多线程和多进程

- 什么是进程和线程？

  - 进程：运行的程序。是计算机进行资源分配的基本单位

    ```进程是计算机分配资源的最小单位```

  - 线程：一个进程可以有多个线程，每个线程又可以独立完成一些任务。

    ```线程是CPU调度的最小单位```

- 多线程实现案例```threading```

```python
import requests
import time
from threading import Thread


def request_baidu(index):
    time.sleep(2)
    url = 'https://www.baidu.com/'
    body = ''
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }
    response = requests.get(url=url, headers=headers)
    print(f'序号:{index}  返回结果:{response.status_code}')


if __name__ == '__main__':
    for i in range(10):
        # 创建10个线程对象
        t = Thread(target=request_baidu, args=(i,))
        # 启动线程
        t.start()
```

**结果：**

![image-20221003212654661](https://s2.loli.net/2022/10/19/gRbVrdlxEQBiIos.png)

- 多进程案例```multiprocessing```

```python
import requests
import time

from multiprocessing import Process


def request_baidu(index):
    time.sleep(2)
    url = 'https://www.baidu.com/'
    body = ''
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }
    response = requests.get(url=url, headers=headers)
    print(f'序号:{index}  返回结果:{response.status_code}')


if __name__ == '__main__':
    for i in range(10):
        # 创建10个进程对象
        t = Process(target=request_baidu, args=(i,))
        # 启动进程
        t.start()
```

**结果：**

![image-20221003212823694](https://s2.loli.net/2022/10/19/ZoQMUp9Tlyuijaq.png)

### 多线程

- 如何等待所有的任务执行完成后，返回主进程

```python
# 保存当前开启的thread对象
thread_list = []
for i in range(10):
    # 创建10个线程对象
    t = Thread(target=request_baidu, args=(i,))
    thread_list.append(t)
    # 启动线程
    t.start()
# 调用join方法，等待所有任务结束后返回主进程，否者直接返回主进程，所有线程变为僵尸线程
for t in thread_list:
    t.join()
    print(f"all task done!")
```

- 如何拿到返回结果

  （同一个作用域）**定义一个全局变量，将所有线程的结果保存到全局变量或可变对象中**

  实现原理：多线程共享主进程的作用域，因此通过全局变量就可以获取多个线程的返回结果。

```python
from threading import Thread

res = []


def request_baidu(index):
    time.sleep(2)
    url = 'https://www.baidu.com/'
    body = ''
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }
    response = requests.get(url=url, headers=headers)
    print(f'序号:{index}  返回结果:{response.status_code}')
    res.append(response)


if __name__ == '__main__':
    thread_list = []
    for i in range(10):
        # 创建10个线程对象
        t = Thread(target=request_baidu, args=(i,))
        thread_list.append(t)
        # 启动线程
        t.start()
    for t in thread_list:
        t.join()
    print(f"all task done!")
    print(res)
```

**结果：**

![image-20221003213927396](https://s2.loli.net/2022/10/19/Cfd5qbHyMXK9cY2.png)

### 多进程

- 等待任务完成后返回主进程```join```

​	通过调用Process的join方法实现

```python
import requests
import time

from multiprocessing import Process


def request_baidu(index):
    time.sleep(2)
    url = 'https://www.baidu.com/'
    body = ''
    headers = {
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
    }
    response = requests.get(url=url, headers=headers)
    print(f'序号:{index}  返回结果:{response.status_code}')


if __name__ == '__main__':
    process_list = []
    for i in range(10):
        # 创建10个进程对象
        p = Process(target=request_baidu, args=(i,))
        process_list.append(p)
        # 启动进程
        p.start()
    for p in process_list:
        p.join()
    print(f'all tasks done!')
    print(process_list)
```

**结果：**

![image-20221003220643180](https://s2.loli.net/2022/10/19/xlqvudJ3fYHjkCh.png)

- 获取多进程返回结果

  ​		多进程彼此之间的作用域相互隔离，通过定义全局变量的方式显然是行不通的，因为全局变量旨在当前的进程中有效，在其他多进程中是不可见的。

  ​		多进程的实现实质上是代码段的拷贝，彼此之间的内存空间是相互独立的。

- 多进程和多线程的异同点

  - 相同点：

    - 都是对CPU工作时间段的描述，知识颗粒度不一样

      简单的说就是多进程和多线程都调用cpu的资源，但是进程可以启动多个线程去执行。

    - 在```linux```内核态不区分进程和线程。

  - 不同点：
    - 进程拥有自己的独立空间,建立数据表维护代码段、堆栈段，而线程共享进程中的资源，使用相同的地址空间，因此线程切换效率比进程切换效率高。
    - 线程共享进程的全局变量，静态变量等对象，线程间的通信更为方便，而进程之间的通信更加复杂，需要以```rpc```的方式进程
    - 多进程要比多线程更健壮，进程之间一般不会相互影响，而多线程可能出现一个线程崩溃，会导致整个进程发生崩溃。

### 全局解释器锁

- 计算密集型

  主要占用cpu

- IO密集型

  IO就是 input 和 output  （就是需要等待的任务）

  - 网络请求延迟
  - 数据库交互也会有延迟并且等待数据库的处理时间
  - 读写硬盘操作

- 多进程在处理计算密集型的程序比多线程快

  ​		全局解释器锁的存在，一个进程下只允许一个线程执行```python```程序的字节码(当前代码文件的二进制表示)。简单的说就是多个线程在争夺一个cpu的资源，但是遇到io操作会让渡cpu资源。

- 如何绕过GIL
  - 将多线程犯法改进为多进程
  - 将计算密集型任务转移给C扩展
  - 分布式计算引擎```spark、Apache```
  - 使用````PyPy```解释器，工业上几乎没有人这么用，原因是```PyPy```不成熟

### 进程通信IPC

- 文件

  通过读写文件来进行变量、数据、信息的传递
  
  - 读写冲突
  
    两个进程同时进行写操作、或者一个读，一个写操作，造成冲突
  
  - 解决写冲突
  
    - 互斥锁
  
    ```python
    from multiprocessing import Process, Lock
    
    
    def save_to_file(index, lock):
        lock.acquire()
        with open('test.log', 'a', encoding='utf-8') as f:
            f.write(str(index) + '\n')
        lock.release()
    
    
    if __name__ == '__main__':
        process_list = []
        lock = Lock()
        for i in range(10):
            p = Process(target=save_to_file, args=(i, lock))
            process_list.append(p)
            p.start()
        for p in process_list:
            p.join()
        print('all tasks done!')
    ```
  
    - 套接字（socket）
  
      通过一个协议，连接两个进程，主要用于网络请求
  
      eg:进程A向百度云上传文件、进程B向百度云下载文件，不会出现冲突
  
    ![image-20221004114208173](https://s2.loli.net/2022/10/19/1XMGU7fTl6hLSZE.png)
  
    - 管道
  
      用文件的内存缓冲区作为管道，实现进程间通信（基于内核实现）
  
      - 匿名管道
  
        主进程和子进程间通信
  
      - 具名管道
  
        不相关的进程可以互相通信
  
    ![image-20221004114221124](https://s2.loli.net/2022/10/19/fqEsaxQNeAIBuvP.png)
  
    - 消息队列
  
      就是存放消息的一个列表，也是位于内核空间中
  
    ```python
    from multiprocessing import Process, Queue
    
    
    def save_to_queue(index, my_queue):
        my_queue.put(index)
    
    
    if __name__ == '__main__':
        process_list = []
        my_queue = Queue()
        for i in range(10):
            p = Process(target=save_to_queue, args=(i, my_queue))
            process_list.append(p)
            p.start()
        for p in process_list:
            p.join()
        # 打印消息队列内容
        while True:
            print(my_queue.get())
    ```
  
    - 共享内存
  
      进程访问内核态的同一块内存
  
    ```from multiprocessing import Value, Array```
  
    其中Value表示单个值，Array表示一个数组
  
    - 信号量
  
      不是用来传递数据的，而是传递消息的
  
      *进程B等到进程A执行到某一步操作后，才会启动*
  
      进程A---发消息---内核---转发消息---进程B
  
      

### 线程通信

​	线程之间通信强调的是线程之间传递对象引用

- 共享变量

  - 线程安全

    线程有GIL锁，但是拿到GIL锁不代表可以一直执行下去。

    目前的计算机多线程也是A执行一会儿，B执行一会儿...

  **线程不安全的例子：**

  ```python
  from threading import Thread
  
  zero = 0
  
  
  def func():
      global zero
      for i in range(10 ** 7):
          zero += 1
          zero -= 1
  
  
  if __name__ == '__main__':
      thread_list = []
      for i in range(5):
          t = Thread(target=func)
          thread_list.append(t)
          t.start()
  
      for t in thread_list:
          t.join()
  
      print(zero)
  # 输出的结果可能不是0
  ```

  - 解决线程安全

    将重要操作包装成原子操作（不可分割）。

    - 加互斥锁

  ```python
  from threading import Thread, Lock
  
  zero = 0
  lock = Lock()
  
  
  def func():
      global zero
      for i in range(10 ** 6):
          lock.acquire()
          zero += 1
          zero -= 1
          lock.release()
  
  
  if __name__ == '__main__':
      thread_list = []
      for i in range(5):
          t = Thread(target=func)
          thread_list.append(t)
          t.start()
      for t in thread_list:
          t.join()
  
      print(zero)
  # 输出的结果一定是0
  ```

  

## 迭代器、生成器

### 迭代器

​	传统声明一个列表，里面的元素会立即写入内存，占用大量内存
​	迭代器可以一次只返回一个元素，占用内存小，在读取大文件和大的数据集合的时候有用

- 通过```iter()```方法返回一个迭代器对象

  非迭代器：```l1= list(range(10**7))```

  迭代器：```l2 = iter(range(10**7))```

- 原理

  迭代器概念上可以表示要给数据流、提供了数据的惰性返回功能。

  实现上：实现了```__next__```接口的对象

  ​	通过```next()```方法主动获取迭代器中的值，当迭代器中的值获取完毕后，再进行访问会报错，需要自行处理

  ```python
  l1 = iter(range(5))
  print(next(l1))
  print(next(l1))
  print(next(l1))
  print(next(l1))
  print(next(l1))
  print(next(l1))
  print(next(l1))
  ```

  

### 生成器

​	是一种特殊的迭代器，在迭代器惰性返回的基础上，提供了额外的功能，实现了程序执行的暂停。

- 声明一个生成器

  只要函数体中有```yield```关键词，那么该函数就是一个生成器



总结：生成器和迭代器的区别

​	同样提供了惰性返回功能。迭代器侧重于提供**数据**的惰性返回功能，生成器侧重于**指令**的惰性返回功能，提供程序的暂停。

### 协程

- 原理

  在生成器实现的基础上，又提供了传递的功能。

  通过```send```方法向生成器传递值，对生成器进行send操作一定要调用```next```方法进行预激，使其停留在第一个```yield```位置。

- 协程的实现

```python
def func_coro(a):
    print(f'初始值: {a}')
    b = yield a  # b的值不是被赋值，而是通过send方法传递值
    print(f'传递值: {b}')
    c = yield a + b
    print(f'传递值: {c}')


co = func_coro(1)
print(next(co))
print(co.send(2))
print(co.send(3))
```

**结果：**

![image-20221004125838441](https://s2.loli.net/2022/10/19/t5ODFUqXyIcV1Zi.png)

案例：```通过协程实现无限次传入当前值，与之前的结果求平均值```

```python
def func_corn():
    sum = 0
    length = 0

    while True:
        try:
            value = yield sum / length
        except ZeroDivisionError:
            value = yield 0
        sum += value
        length += 1


if __name__ == '__main__':
    co = func_corn()
    print(next(co))
    print(co.send(3))
    print(co.send(5))
    print(co.send(7))
    print(co.send(9))
```

- ```yield``` 和 ```yield from```
  - ```yield from```实现的协程一部程序比较难懂
  - ```yield from```用来驱动子程序中的循环并**返回最终值**

```python
def return_triple():
    while True:
        value = yield
        if value % 3 == 0:
            return value

def triple_recorder():
    while True:
        result = yield from return_triple()
        triple_array.append(result)

triple_array = []
coro = triple_recorder()
next(coro)
for i in range(100):
    coro.send(i)

print(triple_array)
```

## TCP-IP协议和抓包

- url

  ```https://www.baidu.com/```

  协议：路径（域名）

- url的作用

  定位到指定的资源，并且携带着访问该资源的方式(http,ftp...)

- uri 和 url

  ```uri```是统一资源标识符，定位资源的位置

  ```url```是```uri```的子集

### 输入网址后发生了什么？

- DNS解析
- 客户端和服务端进行TCP三次握手，建立连接
- 客户端发送请求
- 服务端接受请求，并响应给客户端
- 客户端浏览器接受到浏览器的返回后，进行渲染
- 客户端和服务端进行TCP四次挥手，释放连接

### TCP-IP五层协议

- 应用层

  为进程（客户端应用）和进程（服务器应用）之间提供服务，定义了应用之间进行数据交互的方式。

- 传输层
  - TCP（传输控制协议）
  - UDP（用户数据报协议）

- 网络层

  不同网络之间的主机进行通信--**路由**

  - IP
  - ICMP（ping命令）

- 数据链路层

  负责局域网两台主机之间的通信

- 物理层

  光纤、双绞线等传输介质，在设备之间传输比特流

### 抓包

​	抓包就是中间人攻击，对于服务器，它伪装成客户端，对于客户端，他伪装成服务器

- 抓包软件
  - Fiddler
  - wireshark

- 抓包的分类

  - Web抓包

    信任证书

    ```Tools---Options---Https--勾选Decrypt HTTPS traffic---点击右上角Action按钮---Trust Root Certificates```

  - App抓包

    夜神模拟器

    - 把模拟器的网络代理指向fiddler主机所在的IP地址，并且添加代理端口号

    - 模拟器安装fiddler信任证书

    - 一些手机APP有一定的反爬措施，第一件事就是修改请求协议

      - 双向验证

        需要客户端也带上证书

    - 解决措施

      - virtualxposed 和 justtrustme 的安装

## 路径管理

### 路径

- 绝对路径

  > 从根目录开始

- 相对路径

  > - .  代表相对路径
  > - .. 表示父级目录

### 路径列表

- 查看当前路径列表

  > import sys
  >
  > print(sys.path)

- 只有在路中列表中的包或者模块才可以导入和调用

- 路径搜索顺序
  - 当前的脚本路径，也就是执行文件的目录
  - `PYTHONPATH`路径
  - 虚拟环境路径
  - `site-packages`路径
    - 安装的库所在的路径

- 可以向路径列表中添加路径

  > sys.path.append(r'E:\\PycharmProjects\\简易爬虫项目\parser')

### 常见报错

- `ModuleNotFoundError: No module named 'xxx'`

  - 出错的原因

    没有将当前包所在的路径加入到路径列表中

- `ModuleNotFoundError: No module named 'parser.search'; 'parser' is not a package'`

  - 出错的原因

    - 自定义的包和内置的包名有冲突

      修改包的名字即可

    - 导入的不是一个包

  - 为什么在`pycharm`中不报错，在命令行中报错

    `pycharm`会自动将当前项目的根目录添加到路径列表中

- `ModuleNotFoundError: No module named '__main__.settings'; '__main__' is not a package`

  - **入口程序不可以使用相对路径**

  - `__main__`

    主程序模块会被修改为`__main_`

  - `top_level package`指的是`from`命令导入的首路径，而不是根目录结构

    - 将工作目录加入到列表中（执行命令所在的目录）
    - 进入到项目根目录下执行命令

    - 上述两个操作相当于将项目根目录加入到路径列表当中

### 注意事项

- 确定入口程序，没有一个锚定的路径就没有办法做相对路径的管理
- 将项目根目录加入到入口程序当中
- 进入到项目根目录下执行命令
- 项目的目录结构不要嵌套的太深
- 脚本文件或者临时运行单个模块中的方法，可以将根目录临时添加到路径例如你送当中

## Scrapy

### 概念

- 什么是scrapy

  基于`twisted`搭建异步爬虫框架。

  `scrapy`爬虫框架根据组件化设计理念和丰富的中间件，使其成为一个具备高性能和高扩展的框架

- `scrapy`提供的主要功能
  - **具有优先级功能的调度器**
  - **去重功能**
  - 失败后的重试机制
  - 并发限制
  - IP使用此时限制

- `scrapy`的使用场景

  - 不适合`scrapy`的使用场景
    - 业务很简单，对性能要求也没有那么高，则使用多进程和多线程、异步脚本即可
    - 业务非常复杂，请求之间没有顺序和失效时间限制
    - **不遵守框架的主要设计理念、那就不要使用框架**

  - 适合使用`scrapy`的项目
    - 数据量大，对性能有一定的要求，又需要**去重功能**和**优先级功能的调度器**。

- `scrapy`的组件

  ![Scrapy architecture](https://s2.loli.net/2022/10/19/IxtHGpDdqeM5VS2.png)

  - `ENGINE`从`SPIDER`中获取初始化请求任务

  - `ENGINE`得到`Request`之后送到`SCHDURE`，`SCHDURE`对请求调度后进行任务产出。

  - `SCHEDURE`返回下一个请求任务给`ENGINE`

  - `ENGINE`将请求任务给`DOWNLOADER`完成下载任务，途经下载器中间件。

  - 一旦下载器完成请求任务，生成一个`Response`对象，并将对象送给`ENGINE`，途经下载器中间件。

  - `ENGINE`收到`Response`对象，将该对象交给`SPIDER`进行解析和处理，途经爬虫中间件。

  - `SPIDER`解析返回结果

    - 将解析结果`ITEMS`发送给`ENGINE`

    - 生成一个新的`Request`任务发送给`ENGINE`

  - 若`ENGINE`接受的是`ITEMS`，则发送给`ITEMS PIPELINES`进行数处理，如果是`Resquest`请求，则发送给`SCHEDURE`

  - 周而复始，直到没有任务产出

### 安装

- 安装

​	`pip intall scrapy -i https://pypi.tuna.tsinghua.edu.cn/simple`

- 创建项目

  `scrapy startproject jd_crawler_scrapy`

- 目录结构

  - spiders（目录）

    存放`SPIDERS`项目文件，一个scrapy项目下可以有多个爬虫实例

  - items

    解析后的结构化结果

  - middlewares

    下载器中间件、爬虫中间件

  - pipelines

    处理items的组件，一般在此处完成items插入数据库表的操作

  - settings

    统一化的爬虫配置文件

  - scrapy.cfg

    项目配置文件

- scrapy爬虫项目

- 启动爬虫

  - 命令行方式

  ​	`scrapy crawl jd_search`

  - 脚本方式

    ```python
    from scrapy import cmdline
    
    command = "scrapy crawl jd_search".split()
    cmdline.execute(command)
    ```

### scrapy的中间件

- 请求头中间件

### scrapy的设置

- 设置中间件

- 设置pipeline

- log

  - LOG = 'E:\LOG'

  - LOG_LEVEL（按照严重程度排序）

    critical

    error

    warnning

    info

    debug

### scrapy item

只是对解析的结构化结果进行一个约束，在到达pipeline之前可以检查出错误