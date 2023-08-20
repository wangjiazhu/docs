# Django项目

## 安装

```
pip install django==4.1
```

安装完成后，会在`python.exe`所在的目录下出现变化

```
python3.9
 - Script  # python基本命令
   - pip
   - django-admin  # 安装django后会出现在该目录下
 
 - Lib  # python基础库
   - json
   - sqlite3
   - tkinter
```

## 创建项目

### 打开终端

说明：若想将`django`项目放到哪个目录下，先通过终端进入该目录

### 执行命令

若`Script`目录加入环境变量，直接用`django-admin`命令创建，否则需要加上全路径

```
django-admin startproject 项目名称
```

创建后，生成一些默认文件

```
mysite
		-manage.py   				# 项目管理、项目启动、app创建
		─mysite
                settings.py			# 项目配置文件（APP、数据库、模板）
                urls.py				# URL和函数的对应关系
                asgi.py
                wsgi.py				# 接收网络请求
                __init__.py
```

### 创建APP

```
- 项目
  - APP1  用户管理
  - APP2  订单管理
  - APP3  后台管理
   ...
```

```
─app01
│  │  admin.py				# django 默认的admin后台管理
│  │  apps.py				# app启动类
│  │  models.py				# 对数据库进行操作
│  │  tests.py				# 单元测试
│  │  views.py				# 视图类
│  │  __init__.py
│  │
│  └─migrations				# 数据库变更记录
│          __init__.py

```

## 项目上手

1. 注册APP

2. 编写URL和视图的函数关系

3. 编写视图函数

   返回类型：

   ```
   HttpResponse("string")								# 字符串
   Render(resquest, "html文件路径")	# html页面（根据app顺序逐一寻找）若配置了模板路径，优先去模板路径找
   
   
   ```

   静态文件

   ​	可在每个app下创建static目录（也可配置STATIC_ROOT、STATIC_DIRS统一静态文件加载和存放位置）

   - js
   - css
   - 图片

   模板加载

   ​	可在每个app下创建templates目录（也可配置TEMPLATES中的DIRS来统一模板加载和存放位置）

   ​	模板语法：

   ​			在html页面中显示view中提供的数据，定义上下文，之后通过模板语法传给前端html页面

   ```html
   # 后端view视图
   view构造上下文
   {
   	"name": "张三",
   }
   
   
   # 前端页面
   # 传递一个文本
   {{ name }}
   
   运行django程序后，前端页面就会显示出该名  “张三”
   
   
   
   # 前端显示列表
   # View视图构造的上下文
   {
   	"name_list": ["赵大"，“牛二”, "张三", "李四", "王五"]
   }
   
   # 前端html模板语法
   {{ name_list.0 }}	# 赵大
   {{ name_list.1 }}	# 牛二
   {{ name_list.2 }}	# 张三
   ...
   {{ name_list.4 }}	# 王五
   ```

   ```html
   # 前端语法之循环
   
   {% for item  in name_name %}
   	<span>{{ item }}</span>
   {% endfor %}
   ```

   ```
   # 前端模板之字典
   # 后端View上下文
   {
   	"people": {
   		"name": "张三",
   		"age": 14,
   		"gender": "girl",
   	}
   }
   
   # 前端模板语法之取字典值
   {{ people.name }}	# 张三
   {{ people.age }}	# 14
   
   
   # 前端模板语法之遍历字典--key
   {% for item in people.keys %}
   	<span>{{ item }}</span>
   {% endfor %}
   
   # 前端模板语法之遍历字典--value
   {% for item in people.values %}
   	<span>{{ item }}</span>
   {% endfor %}
   ```

   ```
   # 模板语法之条件判断
   
   {% if name == "张三" %}
      输出1
   {% else %}
      输出2
   {% endif %}
   
   {% if name == "张三" %}
   	输出1
   {% elif name == "李四" %}
   	输出2
   {% else %}
   	输出3
   {% endif %}
   ```

   

4. 启动项目

   命令行去启动

   ```
   python manage.py runserver ip地址:端口号
   ```

   pycharm启动

   ​	注意：本质上也是命令行启动，只不过pycharm自动帮忙调用了该命令

5. 创建模型类

   

## 获取请求和响应的数据

## 数据库操作

在 `Django` 中操作数据库有两种方式

1. 通过 `pymysql`或者 `MySQLdb` 模块连接数据库并通过 `SQL` 语句操作数据库

   ```python
   import MySQLdb
   
   # 打开数据库连接
   db = MySQLdb.connect("localhost", "testuser", "test123", "TESTDB", charset='utf8' )
   
   # 使用cursor()方法获取操作游标 
   cursor = db.cursor()
   
   # 如果数据表已经存在使用 execute() 方法删除表。
   cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
   
   # 创建数据表SQL语句
   sql = """CREATE TABLE EMPLOYEE (
            FIRST_NAME  CHAR(20) NOT NULL,
            LAST_NAME  CHAR(20),
            AGE INT,  
            SEX CHAR(1),
            INCOME FLOAT )"""
   
   cursor.execute(sql)
   
   # 关闭数据库连接
   db.close()
   ```

   

2. 通过 `ORM` 封装

   ![1689422665241](E:\MarkTextImgs\1689422665241.png)

   推荐使用 `mysqlclient` 模块

注意：

- `ORM` 可以帮助创建表，但是无法创建数据库
- 操作表中的数据，不需要书写 `SQL` 语句

​	

### Django连接数据库

前提： `app_name` 已经注册到 `app` 中

1. `settings.py` 文件中 找到 `DATABASES` 模块进行配置

```python
DATABASES = {

    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '',
        'HOST': '',
        'USER': '',
        'PASSWORD': '',
        'PORT': ,
    }
}
```

2. 进入项目根目录，执行命令

```bash
# 执行该命令会为app中注册的应用创建表
python manage.py makemigrations

# 当模型类中新增字段后，只需要执行以下命令即可
python manage.py migrate

# 若是删除表中的字段，需要只想以上两条命令
```

### Django操作表中数据

```python
# 数据库添加记录
models.Department.objects.create(title="销售部")
models.Department.objects.create(title="IT部")
models.Department.objects.create(title="运营部")

models.UserInfo.objects.create(username="", password="", age=18)
models.UserInfo.objects.create(username="", password="", age=28)

# 数据库删除记录
models.UserInfo.objects.filter(username="huf").delete()
models.Department.objects.all().delete()

# 获取数据
user_list = models.UserInfo.objects.all()  # 获取所有记录(QuerySet)
user_list2 = models.UserInfo.objects.filter(id=1)  # 获取过滤后的记录(QuerySet)
print(user_list)
for obj in user_list:
    print(obj.username, obj.password, obj.age)

for obj in user_list2:
    print(obj.username, obj.password, obj.age)

# 更新数据
models.UserInfo.objects.filter(id=1).update(age=45)
```

# Django项目

## 创建Django项目

## 删除一些默认配置

## 创建应用app

```python
python manage.py startapp app01
```

## 注册APP

![1689858522269](E:\MarkTextImgs\1689858522269.png)

## 模型类

### 部门表

| id   | title |
| ---- | ----- |
| 1    | 销售  |
| 2    | 客服  |
| 3    | 运维  |

```python
from django.db import models

class Department(models.Model):
    """部门表"""
    # id = models.BigAutoField(primary_key=True, verbose_name="ID")
    title = models.CharField(max_length=32, verbose_name="部门名称")
```



### 用户表

| id   | username | passaword | age  | account | create_time | depart | gender |
| ---- | -------- | --------- | ---- | ------- | ----------- | ------ | ------ |
| 1    |          |           |      | 1000000 | 2020.10.5   | 3      | 男     |
| 2    |          |           |      | 300000  | 2020.7.20   | 1      | 女     |
|      |          |           |      |         |             |        |        |
|      |          |           |      |         |             |        |        |
|      |          |           |      |         |             |        |        |
|      |          |           |      |         |             |        |        |

```python
from django.db import models

class User(models.Model):
    username = models.CharField(max_length=16, verbose_name="姓名")
    password = models.CharField(max_length=64, verbose_name="密码")
    age = models.IntegerField(verbose_name="年龄")
    account = models.DecimalField(verbose_name="账户余额", max_digits=10, decimal_places=2, default=0)
    create_time = models.DateTimeField(verbose_name="创建时间")
    # to  与哪张表惯量
    # to-field 表中哪一个列关联

    # 外键所在的表数据删除-级联删除
    # on_delete=models.CASCADE 级联删除
    depart = models.ForeignKey(to="Department", to_field="id", on_delete=models.CASCADE)

    # 外键所在的表数据删除-置空
    # 　1.  on_delete=models.SET_NULL
    #  2.  null=True
    # 3. blank=True
    # depart = models.ForeignKey(to="Department", to_field="id", null=True, blank=True, on_delete=models.SET_NULL)
    gender_choices = ((1, '男'), (2, '女'))
    gender = models.SmallIntegerField(verbose_name="性别", choices=gender_choices)

```

## 创建数据库

```mysql
create database test2 default charset utf8 collate utf8_general_ci;
```

## Django中配置数据库

![1689861096072](E:\MarkTextImgs\1689861096072.png)

## Django命令生成数据库表和相应的迁移文件

```powershell
python manage.py makemigrations

python manage.py migrate
```

## 静态文件管理

![1689861426120](E:\MarkTextImgs\1689861426120.png)

## 部门管理

## 模板的继承

部门列表

添加部门

编辑部门

- 设置模板布局页面---**定义模板**

  ![1690005967095](E:\MarkTextImgs\1690005967095.png)

- 其他页面继承该布局页面----**继承模板**

  ![1690006020925](E:\MarkTextImgs\1690006020925.png)

只需要在 **模板** 中的指定位置定义 **block** 就可以减少代码的编写，而页面加载过程中会定位该位置并进行填充

## 用户管理

### 构造数据

```mysql
insert into app01_user(username,password,age,account,create_time,gender,depart_id) value("", "", 23, 10000, "2023-07-22", 1, 1);
```

```
+-------------+---------------+------+-----+---------+----------------+
| Field       | Type          | Null | Key | Default | Extra          |
+-------------+---------------+------+-----+---------+----------------+
| id          | bigint(20)    | NO   | PRI | NULL    | auto_increment |
| username    | varchar(16)   | NO   |     | NULL    |                |
| password    | varchar(64)   | NO   |     | NULL    |                |
| age         | int(11)       | NO   |     | NULL    |                |
| account     | decimal(10,2) | NO   |     | NULL    |                |
| create_time | datetime(6)   | NO   |     | NULL    |                |
| gender      | smallint(6)   | NO   |     | NULL    |                |
| depart_id   | bigint(20)    | NO   | MUL | NULL    |                |
+-------------+---------------+------+-----+---------+----------------+
```

![1690008261862](E:\MarkTextImgs\1690008261862.png)

### 显示用户列表

- 创建 `user_list.html` 页面

```html
{% extends "app01/layout.html" %}

{% block content %}
    <div class="container">
        <div style="margin-bottom: 10px">
            <a class="btn btn-primary" href="#" target="_blank">
                <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                新建用户
            </a>
        </div>

        <div class="panel panel-default">
            <!-- Default panel contents -->
            <div class="panel-heading">
                <span class="glyphicon glyphicon-th-list" aria-hidden="true"></span>
                用户列表
            </div>

            <!-- Table -->
            <table class="table">
                <thead>
                <tr>
                    <th>ID</th>
                    <th>用户名</th>
                    <th>性别</th>
                    <th>年龄</th>
                    <th>账户余额</th>
                    <th>入职时间</th>
                    <th>所属部门</th>
                    <th>密码</th>
                    <th>操作</th>
                </tr>
                </thead>
                <tbody>
                {% for user in user_list %}
                    <tr>
                        <th scope="row">{{ user.id }}</th>
                        <td>{{ user.username }}</td>
                        <td>{{ user.get_gender_display }}</td>
                        <td>{{ user.age }}</td>
                        <td>{{ user.account }}</td>
                        <td>{{ user.create_time|date:"Y-m-d" }}</td>
                        <td>{{ user.depart.title }}</td>
                        <td>{{ user.password }}</td>
                        <td>
                            <a class="btn btn-primary btn-xs" href="#">编辑</a>
                            <a class="btn btn-danger btn-xs" href="#">删除</a>
                        </td>
                    </tr>
                {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
{% endblock %}
```

- 创建用户列表 `URL`

```python
from django.urls import path
from app01 import views

urlpatterns = [

    # 用户管理
    path('user/list/', views.user_list),
]
```



- 创建用户列表视图

```python
# 用户管理
def user_list(requst):
    # 获取所有用户列表
    user_list = models.User.objects.all()

    # 构造上下文
    context = {
        "user_list": user_list,
    }

    return render(requst, "app01/user_list.html", context=context)
```

```
# 用 Python处理QuerySet中的对象方法
    # for user in user_list:
    #     # datetime对象转字符串
    #     create_time = user.create_time.strftime("%Y-%m-%d")
    #
    #     # choice选项内容获取
    #     gender = user.get_gender_display()
    #
    #     # forkey选项
    #     depart = user.depart.title  # 根据id自动关联表中的一个depart对象
    #
    #     print(create_time, gender, depart)
```

### 新建用户

- 定义 `URL`

```python
from django.urls import path
from app01 import views

urlpatterns = [

    # 用户管理
    path('user/list/', views.user_list),
    path('user/add/', views.user_add),
]
```



- 创建 `user_add.html` 

```html
{% extends "app01/layout.html" %}
{% block content %}
    <div class="container">
        <div class="panel panel-default">
            <div class="panel-heading">
                <h3 class="panel-title">新建用户</h3>
            </div>
            <div class="panel-body">
                <form method="POST" action="/user/add/">
                    {% csrf_token %}
                    <div class="form-group">
                        <label for="exampleInputEmail1">姓名</label>
                        <input type="text" class="form-control" placeholder="姓名" name="username">
                    </div>

                    <div class="form-group">
                        <label for="exampleInputEmail1">性别</label>
                        {#                        <input type="text" class="form-control" placeholder="性别" name="title">#}
                        <select class="form-control" name="gender">
                            {% for item in gender_choices %}
                                <option value="{{ item.0 }}">{{ item.1 }}</option>
                            {% endfor %}
                        </select>
                    </div>

                    <div class="form-group">
                        <label for="exampleInputEmail1">年龄</label>
                        <input type="text" class="form-control" placeholder="年龄" name="age">
                    </div>

                    <div class="form-group">
                        <label for="exampleInputEmail1">账户余额</label>
                        <input type="text" class="form-control" placeholder="账户余额" name="account">
                    </div>

                    <div class="form-group">
                        <label for="exampleInputEmail1">入职时间</label>
                        <input type="text" class="form-control" placeholder="入职时间" name="create_time">
                    </div>

                    <div class="form-group">
                        <label for="exampleInputEmail1">所属部门</label>
                        {#                        <input type="text" class="form-control" placeholder="所属部门" name="title">#}
                        <select class="form-control" name="depart">
                            {% for depart in depart_list %}
                                <option value="{{ depart.id }}">{{ depart.title }}</option>
                            {% endfor %}
                        </select>
                    </div>

                    <div class="form-group">
                        <label for="exampleInputEmail1">密码</label>
                        <input type="text" class="form-control" placeholder="密码" name="password">
                    </div>

                    <button type="submit" class="btn btn-primary">保存</button>
                </form>

            </div>
        </div>
    </div>
{% endblock %}
```



- 编写视图函数 `user_add`

```python
from django.shortcuts import render, redirect
from app01 import models

def user_add(request):
    if request.method == "GET":
        # 构造上下文
        context = {
            "gender_choices": models.User.gender_choices,
            "depart_list": models.Department.objects.all(),
        }
        # 返回页面
        return render(request, "app01/user_add.html", context=context)

    # 获取用户数据
    username = request.POST.get("username")
    gender = request.POST.get("gender")
    age = request.POST.get("age")
    account = request.POST.get("account")
    create_time = request.POST.get("create_time")
    depart_id = request.POST.get("depart")
    password = request.POST.get("password")

    # 保存用户数据
    models.User.objects.create(username=username, gender=gender, password=password,
                               age=age, account=account, create_time=create_time,
                               depart_id=depart_id)

    # 返回
    return redirect("/user/list/")
```

### Django组件

**注意：用户提交的时候没有对内容进行校验**

> 以上创建用户的方式在实际的开发中不可行
>
> - 用户提交的数据没有校验
> - 错误--页面没有给出对应的提示信息
> - 页面上每一个字段都需要我们重新写一遍
> - 关联的数据需要手动获取并循环展示到页面

#### Form组件

特点：小，便捷，只解决了以上四个问题的前三个问题

##### 1. 视图函数

```python
class MyForm(Form):
    user = forms.CharField(widget=forms.Input)
    pwd = forms.CharField(widget=forms.Input)
    email = forms.CharField(widget=forms.Input)
    
def user_add(request):
    if request.method == "GET":
        form = MyForm()
        return render(requst, "app01/user_add.html", {"form": form})
```

##### 2.user_add.html

```html
<form method="POST" action="/user/add/">
    {% for item in form %}
    	{{ item }}
    {% endfor %}
    
    {{ form.user }}
    {{ form.pwd }}
    {{ form.email }}
    
    # 不需要手写以下内容了
	<input type="text" class="form-control" placeholder="姓名" name="username">
    <input type="text" class="form-control" placeholder="姓名" name="username">
    <input type="text" class="form-control" placeholder="姓名" name="username">
</form>
```



#### ModelForm组件(适合表  增删改查)

##### 模型类

```python
class User(models.Model):
    username = models.CharField(max_length=16, verbose_name="姓名")
    password = models.CharField(max_length=64, verbose_name="密码")
    age = models.IntegerField(verbose_name="年龄")
    account = models.DecimalField(verbose_name="账户余额", max_digits=10, decimal_places=2, default=0)
    create_time = models.DateTimeField(verbose_name="创建时间")
    # to  与哪张表惯量
    # to-field 表中哪一个列关联

    # 外键所在的表数据删除-级联删除
    # on_delete=models.CASCADE 级联删除
    depart = models.ForeignKey(to="Department", to_field="id", on_delete=models.CASCADE)

    # 外键所在的表数据删除-置空
    # 　1.  on_delete=models.SET_NULL
    #  2.  null=True
    # 3. blank=True
    # depart = models.ForeignKey(to="Department", to_field="id", null=True, blank=True, on_delete=models.SET_NULL)
    gender_choices = ((1, '男'), (2, '女'))
    gender = models.SmallIntegerField(verbose_name="性别", choices=gender_choices)


```

##### 视图函数

```python
class MyForm(ModelForm):
    now_time = forms.CharField("xxx")
	class Meta:
		model = User
		fields = ["username", "gender", "age", "account" ..."now_time"]
   
def user_add(request):
    if request.method == "GET":
        form = MyForm()
        return render(requst, "app01/user_add.html", {"form": form})
```

##### user_add.html

```html
<form method="POST" action="/user/add/">
    {% for item in form %}
    	{{ item }}
    {% endfor %}
</form>
```

### 编辑用户

- 点击编辑，跳转到编辑页面（将当前用户的id携带过去）
- 编辑页面（默认数据，根据ID获取并设置到页面中）
- 在数据库更新

##### 接口信息

**请求方式：**`POST`

**请求地址：**`http://127.0.0.1/user/edit/<int:uid>/`

| 参数 | 是否必须 | 说明   |
| ---- | -------- | ------ |
| uid  | 是       | 用户id |
|      |          |        |

**返回值：**

```json
{
	“form”: form
}
```



## 视图函数使用

以查询一个订单的详细信息为例：

方式一：自行构造返回值对象

```python
def order_detail(request):
    """根据 editid 获取订单详细信息 """

    oid = request.GET.get('editid')
    # 方式1：获取整个订单对象（对应的数据库中包含所有字段的一条记录）
    order_object = models.Order.objects.filter(oid=oid).first()
    # 从数据库中获取到订单对象， 构造返回的订单内容
    if not order_object:
        return JsonResponse({'status': False, 'errors': '数据不存在'})
	# 通过对象需要自己构造数据返回字典
    data = {
        'title': order_object.title,
        'price': order_object.price,
        'status': order_object.status
    }
    return JsonResponse({
        'status': True,
        'data': data
    })
```

方式二：通过在查询对象的时候在后面加上 .values 来过滤需要获取的数据

```python
def order_detail(request):
    """根据 editid 获取订单详细信息 """

    oid = request.GET.get('editid')
    # 方式2: 获取订单对象中的指定字段，返回的是一个字典而非一个对象
    order_dict = models.Order.objects.filter(oid=oid).values('oid', 'title', 'status').first()
    # print(order_dict)
    if not order_dict:
        return JsonResponse({'status': False, 'errors': '数据不存在'})
    return JsonResponse({'status': True, 'data': order_dict})
```



