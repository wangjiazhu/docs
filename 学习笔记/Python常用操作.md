# Python常用操作

- [Python常用操作](#python常用操作)
  - [排序算法实现](#排序算法实现)
    - [相关概念](#相关概念)
    - [代码实现](#代码实现)
  - [OS模块](#os模块)
    - [os模块方法](#os模块方法)
    - [os.path模块方法](#ospath模块方法)
  - [pymysql库](#pymysql库)
    - [连接数据库](#连接数据库)
    - [创建数据库表](#创建数据库表)
    - [数据库插入操作](#数据库插入操作)
    - [数据库查询操作](#数据库查询操作)
    - [数据库更新操作](#数据库更新操作)
    - [数据库删除操作](#数据库删除操作)
  - [Python数据类型转换](#python数据类型转换)
    - [常用的类型转换函数](#常用的类型转换函数)
    - [eval函数](#eval函数)
    - [repr函数](#repr函数)
  - [二叉树遍历操作](#二叉树遍历操作)
    - [定义树节点](#定义树节点)
    - [三种遍历方法](#三种遍历方法)
    - [构造二叉树](#构造二叉树)

## 排序算法实现

### 相关概念	

​		常见的内部排序算法有：**插入排序、希尔排序、选择排序、冒泡排序、归并排序、快速排序、堆排序、基数排序**等。用一张图概括：

![](https://s2.loli.net/2022/10/20/SYGVk4nlFzQoxPv.png)

- 关于时间复杂度

  **平方阶：直接插入、直接选择、冒泡排序**

  **线性对数阶：快速排序、堆排序、归并排序**

  **线性阶：基数排序、桶排序、计数排序**

  第四类：希尔排序

- 关于稳定性

  **稳定的排序算法：冒泡排序、插入排序、归并排序、基数排序**

  **不稳定的排序算法：选择排序、快速排序、希尔排序、堆排序**

- 名词解释

  n：数据规模

  k：“桶”的个数

  In-place：占用常数量内存，不占用额外内存

  Out-place：占用额外内存

  **<font color="green">稳定性：排序后2个相等键值的顺序和排序前它们的顺序相同</font>**

### 代码实现

***

1. 冒泡排序

```python
def bubble_sort(arr):
    for i in range(1, len(arr)):
        for j in range(0, len(arr) - i):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    return arr
```

***
2. 选择排序

```python
def select_sort(arr):
    for i in range(len(arr) - 1):
        minIndex = i
        for j in range(i + 1, len(arr)):
            if arr[j] < arr[minIndex]:
                minIndex = j
        if i != minIndex:
            arr[i], arr[minIndex] = arr[minIndex], arr[i]
    return arr
```

***
3. 归并排序

```python
def merge_sort(arr):
    if len(arr) < 2:
        return arr

    middleIndex = len(arr) // 2
    leftList, rightList = select_sort(arr[:middleIndex]), merge_sort(arr[middleIndex:])
    left = right = 0
    resList = []

    while left < len(leftList) and right < len(rightList):
        if leftList[left] < rightList[right]:
            resList.append(leftList[left])
            left += 1
        else:
            resList.append(rightList[right])
            right += 1

    if left < len(leftList):
        resList += leftList[left:]
    if right < len(rightList):
        resList += rightList[right:]
    return resList
```

***
4. 快速排序

```python
def quick_sort(arr, left, right):
    if left < right:
        pos = partition(arr, left, right)
        quick_sort(arr, left, pos)
        quick_sort(arr, pos + 1, right)


def partition(arr, left, right):
    tmp = arr[left]

    while left < right:
        while left < right and arr[right] >= tmp:
            right -= 1
        arr[left] = arr[right]
        while left < right and arr[left] <= tmp:
            left += 1
        arr[right] = arr[left]

    arr[left] = tmp
    return left
```

## OS模块

参考链接：https://www.runoob.com/python3/python3-os-file-methods.html

### os模块方法

```python
import os

#（1）获取脚本所在绝对路径{返回字符串路径}
print(os.path.abspath(__file__))

#（2）查看当前工作目录{返回字符串路径}
print(os.getcwd())

#（3）切换工作目录
os.chdir("/server/scripts")

#（4）修改文件或目录权限【权限字符必须是八进制数 0o0755对应linux的755权限】{无返回值}
os.chmod("test3.sh", 0o0755)

#（5）修改文件的属主和属组【1001是用户wjz的uid和gid】{无返回值}
os.chown("test3.sh", 1001, -1)		# 只修改uid
os.chown("test3.sh", -1, 1001)  	# 只修改gid
os.chown("test3.sh", 1001, 1001)	# 同时修改uid和gid

#（6）创建软连接{无返回值}
os.symlink('/server/scripts/test3.sh', '/root/ip.sh')

#（7）创建硬链接{无返回值}
os.link( '/server/scripts/test3.sh', '/root/ip')

#（8）获取指定目录下包含的文件或目录(包含隐藏文件){返回指定路径下的文件和文件夹列表}
rootList = os.listdir('/root')
print(rootList)

#（9）获取文件属性(相当于linux的ls -l，两个在查看非链接文件功能一致，区别在于如果查看的是软件姐文件){返回一个os.stat_result类型对象}
res = os.stat('/root/ip.sh')
print(res)
print(res.st_gid)			# 获取软连接文件所指的文件的属性

res2 = os.lstat('/root/ip.sh')
print(res2)
print(res.st_gid)			# 获取软连接文件本身属性

#（10）创建目录{mkdir()只能创建一级目录，如果上级目录不存在，报错OSError}
os.mkdir('/root/test', 0o0755)		# 在/root目录下创建一个test目录，并指定目录权限
os.makedirs('/root/test1/test2', 0o0755)	# 递归创建多层级目录，并指定目录权限

#（11）重命名文件或目录{无返回值}
os.rename('/root/ip', '/root/ip_rename')

#（12）删除一个文件
os.remove('/root/ip_name')	# {无返回值，如果删除的是一个目录，报错FileNotFoundError}

#（13）删除一个目录
os.rmdir('/test11')		# {无返回值，删除一个空目录，如果目录非空，报OSError异常}
os.removedirs("/test12")	# {无返回值， 递归删除目录，如果目录非空，报OSError异常}
```

### os.path模块方法

```python
import os

#（1）获取文件所在绝对路径{返回字符串路径}
absPath = os.path.abspath(__file__)			# 获取当前执行脚本的绝对路径
print(absPath)
print("真实路径：" + os.path.realpath('test02.py'))
print("绝对路径："+os.path.abspath('test02.py'))
#**  realpath()和abspath()都是获取文件的绝对路径   **#

os.chdir('/test')
absPath2 = os.path.abspath('test01.py')		# 获取工作目录下指定文件的绝对路径
print(absPath2)

#（2）获取文件名{返回文件名 不包含路径}
fileName = os.path.basename(absPath)
print(fileName)

#（3）获取文件路径{返回文件路径 不包含文件名}
dirName =os.path.dirname(absPath)
print(dirName)

#（4）判断路径是否存在{返回布尔值 True:路径存在  False:路径不存在}
existFlag = os.path.exists(absPath)
print(existFlag)

#（5）获取文件基本属性
atime = os.path.getatime(absPath)	# 返回最近访问时间（浮点型秒数）
mtime = os.path.getmtime(absPath)	# 返回最近文件修改时间
ctime = os.path.getctime(absPath)	# 返回文件 path 创建时间
size = os.path.getsize(absPath)	    # 返回文件大小，如果文件不存在就返回错误
print(f"atime:{atime} ctime:{ctime} mtime:{mtime} size:{size}")

#（6）判断指定路径相关
isAbs = os.path.isabs(absPath)	    # 判断是否为绝对路径
isFile = os.path.isfile(absPath)	# 判断路径是否为文件
isDir = os.path.isdir(absPath)	    # 判断路径是否为目录
isLink = os.path.islink(absPath)	# 判断路径是否为链接
isMount = os.path.ismount(absPath)	# 判断路径是否为挂载点
print(f"isAbsPath:{isAbs} isFile:{isFile} isDir:{isDir}")

#（7）把目录和文件名合成一个路径{可以将路径字符串进行拼接 os.path.join(p1, p2, p3)}
fullPath = os.path.join(dirName, fileName)
print(fullPath)

#（8）判断两个文件或目录的路径是否相同
sameFlag1= os.path.samefile('test01.py', '/root/test01.py')  # False
sameFlag2= os.path.samefile('/root/test01.py', '/root/test01.py')  # True
print(sameFlag, sameFlag2)

#（9）将路径切分为dirname和filename，{返回元组(dirname, filename)}
dName,fName = os.path.split(absPath)
print(dName,fName)

#（10）分割路径中的文件名与扩展名{返回元组(dirFileName, extName)}
dfName, extName = os.path.splitext(absPath)
print(dfName, extName)
```

## pymysql库

说明：pymysql是python3连接mysql数据库的第三方库。

### 连接数据库

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='root',
                     password='123456',
                     database='test')
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()

# 使用 execute()  方法执行 SQL 查询 
cursor.execute("SELECT VERSION()")

# 使用 fetchone() 方法获取单条数据.
data = cursor.fetchone()
print(data)

# 关闭数据库连接
db.close()

```

***

### 创建数据库表

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='root',
                     password='123456',
                     database='test')

# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()

# 使用 execute() 方法执行 SQL，如果表存在则删除
cursor.execute("DROP TABLE IF EXISTS CLASS")

# 使用预处理语句创建表
sql = """CREATE TABLE class (
         CLASS_NAME VARCHAR(20),
         CLASS_NO INT )"""

cursor.execute(sql)

# 关闭数据库连接
db.close()
```

***

### 数据库插入操作

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='root',
                     password='123456',
                     database='test')

# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()

# 插入SQL语句
sql = """insert into class values('网络工程', 2),('软件工程', 3)"""

try:
    # 执行sql语句
    cursor.execute(sql)
    # 提交到数据库执行
    db.commit()
except:
    # 如果发生错误则回滚
    db.rollback()

# 关闭数据库连接
db.close()
```

***

### 数据库查询操作

Python查询Mysql使用 fetchone() 方法获取单条数据, 使用fetchall() 方法获取多条数据。

- **fetchone():** 该方法获取下一个查询结果集。结果集是一个对象
- **fetchall():** 接收全部的返回结果行.
- **rowcount:** 这是一个只读属性，并返回执行execute()方法后影响的行数。

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='root',
                     password='123456',
                     database='test')

# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()

# 查询SQL语句
sql = """select * from class where CLASS_NO >= %s""" % (2,)
sql2 = """select * from student"""
try:
	# 执行SQL语句
    cursor.execute(sql)
    # 获取所有记录列表
    results = cursor.fetchall()
    for row in results:
        cName = row[0]
        cNo = row[1]
        # 打印结果
        print("cName=%s,cNo=%s" % \
              (cName, cNo))
except:
    print("Error: unable to fetch data")

try:
    # 执行SQL语句
    cursor.execute(sql2)
    # 获取所有记录列表
    results = cursor.fetchall()
    for row in results:
        stuNo = row[0]
        stuName = row[1]
        stuAge = row[2]
        # 打印结果
        print("stuNo=%s,stuName=%s,stuAge=%s" % \
              (stuNo, stuName, stuAge))
except:
    print("Error: unable to fetch data")

# 关闭数据库连接
db.close()
```

***

### 数据库更新操作

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host='localhost',
                     user='root',
                     password='123456',
                     database='test')

# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()

# sql 更新语句
sql = """update student set stu_age=stu_age+1 where stu_no='2020124069'"""

try:
    # 执行SQL语句
    cursor.execute(sql)
    # 提交到数据库执行
    db.commit()
except:
    # 发生错误时回滚
    db.rollback()

# 关闭数据库连接
db.close()
```

***

### 数据库删除操作

```python
import pymysql

# 打开数据库连接
db = pymysql.connect(host="localhost", database="test", user="root", password="123456")

# 创建数据库游标
cursor = db.cursor()

# 删除数据sql
sql = """delete from class where CLASS_NO=3"""

try:
    # 执行sql
    cursor.execute(sql)
    # 提交数据库
    db.commit()
except:
    # 出错，事务回滚
    db.rollback()

# 关闭数据库连接
db.close()
```

## Python数据类型转换

### 常用的类型转换函数

​	注意：int(x) float(x)中x表示的是字符串，str(x)中x是数值类型、repr(x)中x是非字符串类型或者表达式{list,int,tuple等}

![image-20220925134328989](img/image-20220925134328989.png)

### eval函数

​	eval是Python的一个[内置函数](https://so.csdn.net/so/search?q=内置函数&spm=1001.2101.3001.7020)，这个函数的作用是，返回传入字符串的表达式的结果。

```python
# 将一个列表形式的字符串，通过eval方法解析成一个列表
>>> a="[1,3,5,7,9]"
>>> print(a)
[1,3,5,7,9]
>>> print(type(a))
<type 'str'>
>>> b=eval(a)
>>> print(b)
[1, 3, 5, 7, 9]
>>> print(type(b))
<type 'list'>

# 通过eval解析一个字符串表达式的运算结果
>>> a=100
>>> b=200
>>> s="a+b"
>>> res=eval(s)
>>> print(res)
300

# 通过eval解析一个命令，获取相关内容
>>> print(eval("__import__('os').listdir('/root')"))
['.bash_logout', '.bash_profile', '.bashrc', '.cshrc', '.tcshrc', 'anaconda-ks.cfg', '.bash_history', '.pki', '.mysql_history', '.viminfo', 'ip.sh', 'test01.py', '.cache']

>>> print(eval("__import__('os').system('ls -l /')"))
total 16
lrwxrwxrwx.   1 root root    7 Nov 22  2021 bin -> usr/bin
dr-xr-xr-x.   5 root root 4096 Nov 22  2021 boot
drwxr-xr-x   20 root root 3240 Sep 14 22:19 dev
drwxr-xr-x.  81 root root 8192 Sep 25 13:27 etc
drwxr-xr-x.   4 root root   30 Mar 27 23:21 home
lrwxrwxrwx.   1 root root    7 Nov 22  2021 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 Nov 22  2021 lib64 -> usr/lib64
drwxr-xr-x.   2 root root    6 Apr 11  2018 media
```

### repr函数

​	repr() 函数将对象转化为供解释器读取的形式。{将对象转化为字符串形式}

```python
# 将列表转化为字符串(可通过eval方法还原回原本的列表类型)
>>> testList = [1, 3, 5, 7, 9, '100']
>>> print(testList)
[1, 3, 5, 7, 9, '100']
>>> print(type(testList))
<type 'list'>
>>> testStr = repr(testList)
>>> print(testStr)
[1, 3, 5, 7, 9, '100']
>>> print(type(testStr))
<type 'str'>

# 进制转换
# 1.  10进制转化为2进制{注意：2进制数以0b开头}
>>> print(bin(12345))
0b11000000111001
# 2.  10进制转化为8进制{注意：8进制数以0开头}
>>> print(oct(12345))
030071
# 3.  10进制转化为16进制{注意：16进制数以0x开头}
>>> print(hex(12345))
0x3039

# 将10进制的ascii值转化为对应的字符
>>> print(chr(97))
a
>>> print(chr(101))
e

# 将一个字符转化为其ascii值
>>> print(ord('a'))
97
>>> print(ord('F'))
70
```

## 二叉树遍历操作

### 定义树节点

```python
class Node:
    def __init__(self, value=None, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
```

### 三种遍历方法

```python
# 先序遍历
def pre_deep_func(root):
    if root is None:
        return
    print(root.value, end=' ')
    pre_deep_func(root.left)
    pre_deep_func(root.right)

# 中序遍历
def mid_deep_func(root):
    if root is None:
        return
    mid_deep_func(root.left)
    print(root.value, end=' ')
    mid_deep_func(root.right)

# 后续遍历
def post_deep_func(root):
    if root is None:
        return
    post_deep_func(root.left)
    post_deep_func(root.right)
    print(root.value, end=' ')
```

### 构造二叉树

假设构造的二叉树如下图所示：

<img src="https://s2.loli.net/2022/10/19/bSY7VuBj5m1IkRE.png" alt="img" style="zoom: 67%;" />

```python
if __name__ == '__main__':
	# 构造二叉树
    tree = Node(1, Node(2, Node(4), Node(5, Node(8), Node(9, Node(11)))),
                Node(3, Node(6), Node(7, Node(10))))
    
    pre_deep_func(tree)
    print()
    mid_deep_func(tree)
    print()
    post_deep_func(tree)
```

**结果：**

<img src="https://s2.loli.net/2022/10/19/k7szVX1Bn2dpLOv.png" alt="image-20221019164634566" style="zoom:150%;" />

## xlrd、xlwt、xlutils和openpyxl库

> **库使用说明：**
>
> 1. 操作xls格式的表格文件
>    1. 读取：xlrd
>    2. 写入：xlwt
>    3. 修改(追加):xlutils
> 2. 操作xlsx格式的表格文件
>    1. 读取/写入：openpyxl

---

### 操作xls格式的表格文件

```python
import xlrd
import xlwt
from xlutils.copy import copy


def write_excel_xls(path, sheet_name, value):
    index = len(value)  # 获取需要写入数据的行数
    workbook = xlwt.Workbook()  # 新建一个工作簿
    sheet = workbook.add_sheet(sheet_name)  # 在工作簿中新建一个表格
    for i in range(0, index):
        for j in range(0, len(value[i])):
            sheet.write(i, j, value[i][j])  # 像表格中写入数据（对应的行和列）
    workbook.save(path)  # 保存工作簿
    print("xls格式表格写入数据成功！")


def write_excel_xls_append(path, value):
    index = len(value)  # 获取需要写入数据的行数
    workbook = xlrd.open_workbook(path)  # 打开工作簿
    sheets = workbook.sheet_names()  # 获取工作簿中的所有表格
    worksheet = workbook.sheet_by_name(sheets[0])  # 获取工作簿中所有表格中的的第一个表格
    rows_old = worksheet.nrows  # 获取表格中已存在的数据的行数
    new_workbook = copy(workbook)  # 将xlrd对象拷贝转化为xlwt对象
    new_worksheet = new_workbook.get_sheet(0)  # 获取转化后工作簿中的第一个表格
    for i in range(0, index):
        for j in range(0, len(value[i])):
            new_worksheet.write(i + rows_old, j, value[i][j])  # 追加写入数据，注意是从i+rows_old行开始写入
    new_workbook.save(path)  # 保存工作簿
    print("xls格式表格【追加】写入数据成功！")


def read_excel_xls(path):
    workbook = xlrd.open_workbook(path)  # 打开工作簿
    sheets = workbook.sheet_names()  # 获取工作簿中的所有表格
    worksheet = workbook.sheet_by_name(sheets[0])  # 获取工作簿中所有表格中的的第一个表格
    for i in range(0, worksheet.nrows):
        for j in range(0, worksheet.ncols):
            print(worksheet.cell_value(i, j), "\t", end="")  # 逐行逐列读取数据
        print()


if __name__ == '__main__':
    book_name_xls = 'xls格式测试工作簿.xls'

    sheet_name_xls = 'xls格式测试表'

    value_title = [["姓名", "性别", "年龄", "城市", "职业"], ]

    value1 = [["张三", "男", "19", "杭州", "研发工程师"],
              ["李四", "男", "22", "北京", "医生"],
              ["王五", "女", "33", "珠海", "出租车司机"], ]

    value2 = [["Tom", "男", "21", "西安", "测试工程师"],
              ["Jones", "女", "34", "上海", "产品经理"],
              ["Cat", "女", "56", "上海", "教师"], ]

    write_excel_xls(book_name_xls, sheet_name_xls, value_title)
    write_excel_xls_append(book_name_xls, value1)
    write_excel_xls_append(book_name_xls, value2)
    read_excel_xls(book_name_xls)
```

---

### 操作xlsx格式的表格文件

```python
import openpyxl


def write_excel_xlsx(path, sheet_name, value):
    index = len(value)
    workbook = openpyxl.Workbook()
    sheet = workbook.active
    sheet.title = sheet_name
    for i in range(0, index):
        for j in range(0, len(value[i])):
            sheet.cell(row=i + 1, column=j + 1, value=str(value[i][j]))
    workbook.save(path)
    print("xlsx格式表格写入数据成功！")


def read_excel_xlsx(path, sheet_name):
    workbook = openpyxl.load_workbook(path)
    # sheet = wb.get_sheet_by_name(sheet_name)这种方式已经弃用，不建议使用
    sheet = workbook[sheet_name]
    for row in sheet.rows:
        for cell in row:
            print(cell.value, "\t", end="")
        print()


if __name__ == '__main__':
    book_name_xlsx = 'xlsx格式测试工作簿.xlsx'

    sheet_name_xlsx = 'xlsx格式测试表'

    value3 = [["姓名", "性别", "年龄", "城市", "职业"],
              ["111", "女", "66", "石家庄", "运维工程师"],
              ["222", "男", "55", "南京", "饭店老板"],
              ["333", "女", "27", "苏州", "保安"], ]

    write_excel_xlsx(book_name_xlsx, sheet_name_xlsx, value3)
    read_excel_xlsx(book_name_xlsx, sheet_name_xlsx)
```

