# Django项目

> 以 `mysite` 为例作为 project、以 `app01` 为例作为 `mysite` 下的应用展开说明

```
pip install django==4.0.9
pip install django-redis=5.2.0
pip install djangorestframework==3.13.1
pip install mysqlclient=2.2.0
```

```
django-admin startproject mysite
python manage.py startapp app01 
```



## `setting.py`配置

### 数据库	

> 以配置MySQL为例

​	

#### 单默认数据库

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '',
        'PORT': 3306,
    }
}

```



#### 多后端数据库

##### 配置示例

```python
 # 指定数据库路由
DATABASE_ROUTERS = ['mysite.db_router.DatabaseAppRouter']

# 定义数据库路由表
DATABASE_APPS_MAPPING = {
    'default': 'default',
    'app01': 'app01',
}

#　配置多后端数据库
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'default',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '',
        'PORT': 3306,
    },
    
    'app01': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '',
        'PORT': 3306,
    },
}
```



##### 配置方法

1. 在 `setting.py `中定义路由表并指定数据库路由

```python
 # 指定数据库路由
DATABASE_ROUTERS = ['mysite.db_router.DatabaseAppRouter']

# 定义数据库路由表
DATABASE_APPS_MAPPING = {
    # 'app_label' : 'database_name'
    'default': 'default',	# database_name 是 DATABASES 中的名字,不是MySQL中的数据库名
    'app01': 'app01',
}
```



2. 在 `mysite` **项目目录下**创建数据库路由 `db_router.py`

```python
from django.conf import settings

# 在 setting.py 中定义路由表
DATABASE_MAPPING = settings.DATABASE_APPS_MAPPING


class DatabaseAppRouter(object):
    """控制不同的模型类操作不同的数据库
    """
    def db_for_read(self, model, **hints):
        """指向特定的数据库进行读操作
        """
        if model._meta.app_label in DATABASE_MAPPING:
            return DATABASE_MAPPING[model._meta.app_label]
        return None

    def db_for_write(self, model, **hints):
        """指向特定的数据库进行写操作
        """
        if model._meta.app_label in DATABASE_MAPPING:
            return DATABASE_MAPPING[model._meta.app_label]
        return None

    def allow_relation(self, obj1, obj2, **hints):
        """允许不同数据库之间的关联操作
        """
        db_obj1 = DATABASE_MAPPING.get(obj1._meta.app_label)
        db_obj2 = DATABASE_MAPPING.get(obj2._meta.app_label)
        if db_obj1 and db_obj2:
            if db_obj1 == db_obj2:
                return True
            else:
                return False
        return None

    # for Django 1.4 - Django 1.6
    def allow_asyncdb(self, db, model):
        """明确apps只出现在相关数据库中
        """
        if db in DATABASE_MAPPING.values():
            return DATABASE_MAPPING.get(model._meta.app_label) == db
        elif model._meta.app_label in DATABASE_MAPPING:
            return False
        return None

    # for Django 1.7 - Django 1.11
    def allow_migrate(self, db, app_label, model_name=None, **hints):
        """明确App只出现在相关的数据库中
        
        根据 app_label 的值只能在相应的数据库中创建一个表,如果删除该 def 或
        不指定过滤条件,则一个Model会在每个数据库中都创建一张表 
        """
        if db in DATABASE_MAPPING.values():
            return DATABASE_MAPPING.get(app_label) == db
        elif app_label in DATABASE_MAPPING:
            return False
        return None
```



3. 配置多后端数据库

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'default',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '',
        'PORT': 3306,
    },
    
    'app01': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '',
        'PORT': 3306,
    },
}
```



4.  `app01` 下的模型类指定 `app_label`

```python
from django.db import models

# Create your models here.

class User(models.Model):
    id = models.BigIntegerField(primary_key=True)
    name = models.CharField(max_length=200, blank=True, null=True)
    age = models.SmallIntegerField()
    ...

    class Meta:
        managed = True
        db_table = 'user'
        app_label = 'app01'	# 指定app_label,必须与setting中的app_label保持一致
```



### Redis缓存

#### 单缓存

> 单缓存只有default这一个缓存选项，默认所有的缓存数据都会保存到default里面

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://xxx:6379/0',
        "KEY_PREFIX": 'mysite', # 指定缓存的前缀名称(单缓存可以不指定)
        "VERSION": '1.0' + ':' + 'user' + ':' + 'user_id' # 指定缓存的目录结构(单缓存可以不指定)
    }, 
}
```

![image-20231119220834291](https://s2.loli.net/2023/11/19/OwDglM79LcWvCIt.png)

##### 使用方法

```python
from django.core.cache import cache

user_id_list = []
for user in user_serializer_list:
    if not cache.get(f"{user['id']}"):    # cache.get(key) 读redis
        cache.set(f"{user['id']}", user)  # cache.set(key,val) 写redis
    else:
        print(cache.get(f"{user['id']}"))
        user_id_list.append(f"{user['id']}")

all_keys = cache.get_many(user_id_list)  # cache.get_many(key_ietor) 一次性从reidis读多个值
print(f"all_keys:{all_keys}")
```



#### 多缓存

> 设置多个缓存，通过设置不同的缓存名称进行区分

```python
# 设置缓存列表
cache_list = ['default', 'user:user_id',]
cache_dict = {}

# 循环生成每个缓存配置
for _name in cache_list:
    _cache = {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://192.168.154.181:6379/0',
        "KEY_PREFIX": 'mysite',
        "VERSION": '1.0' + ':' + _name
    }
    cache_dict[_name] = _cache

# 将生成的缓存配置放入CACHES字典
CACHES = {
    **cache_dict
}
```



##### 使用方法

```python
from django.core.cache import caches


cache_default = caches['default']       # 使用default缓存
cache_user = caches['user:user_id']     # 使用user缓存

cache_user.get_many([0, 1])             # 从user缓存中获取user_id为0和1的用户信息（一次性读取多个缓存记录）
cache_default.get(key)				   # 从default缓存中获取key对应的信息（一次性读取一个缓存记录）

cache_user.set()
```

一个缓存数据表中数据的demo

```python
from django.shortcuts import render
from django.core.cache import caches
from rest_framework import status
from rest_framework.views import APIView
from rest_framework.response import Response
import logging
import json

from app01.models import User
from app01.serializers import UserSerializer
from app01.tools.dataformat import DataFormat

logger = logging.getLogger("mysite")


class CacheMysite(object):
    def __init__(self) -> None:
        self.return_data = DataFormat().data_format()
        self.caches_default = caches["default"]
        self.caches_t_user_id = caches["t_user:id"]
        self.caches_t_user_name = caches["t_user:name"]

    def caches_user(self, user_serializer_list):
        # caches_dict = {}
        for user in user_serializer_list:
            # caches_dict[user.get("id")] = user
            # print(user)
            self.caches_t_user_id.set(f"{user.get('id')}", user, 3600)
            self.caches_t_user_name.set(f"{user.get('name')}", user, 3600)
        # self.caches_t_user_id.set_many(caches_dict)
        self.return_data["type"] = "caches"
        self.return_data["data"].append("t_user caches complete!")
        return self.return_data


# Create your views here.
class UserView(APIView):
    def get(self, request):
        user_obj = User.objects.all()
        user_serializer = UserSerializer(instance=user_obj, many=True).data
        user_serializer_list = json.loads(json.dumps(user_serializer))
        cc = CacheMysite()
        return_data = cc.caches_user(user_serializer_list=user_serializer_list)
        return Response(data=return_data, status=status.HTTP_200_OK)
```



### 日志

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,  # 是否禁用已经存在的日志器
    'formatters': {  # 日志器信息的显示格式
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(lineno)d %(message)s',
        },
        'simple': {
            'format': '%(levelname)s %(module)s %(lineno)d %(message)s',
        },
    },
    'filters': {  # 对日志进行过滤
        'require_debug_true': {  # django在debug模式下才输出日志
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {  # 日志处理方法
        'console': {  # 向终端输出日志
            'level': 'INFO',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple',
        },

        'file': {  # 向文件输出日志
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            # linux 环境下， 所有的路径信息都是用os.path模块生成的，windows下使用 / 拼接的
            'filename': BASE_DIR / 'logs/mysite.log',  # windows环境日志文件的位置
            'maxBytes': 300 * 1024 * 1024,
            'backupCount': 10,
            'formatter': 'verbose',
        },
    },

    'loggers': {  # 日志器
        'mysite': {  # 定义一个名为 mysite 的日志器
            'handlers': ['console', 'file'],  # 同时向终端与文件输出日志
            'propagate': True,  # 是否继续传递日志
            'level': 'INFO',  # 日志器接收的最低日志级别
        }
    },
}
```



##### 使用方法

```python
import logging

# 声明日志器
logger = logging.getLogger('mysite')	# mysite是setting中定义的日志器名

class UserView(APIView):
    def get(self, request):
        user_obj = User.objects.all()
        user_serializer = UserSerializer(instance=user_obj, many=True).data
        user_serializer_list = json.loads(json.dumps(user_serializer))

        for user in user_serializer_list:
            # print(user)
            if not cache.get(f"{user['id']}"):
                logger.error(user)		# 不存在 error 级别日志
            else:
                print(cache.get(f"{user['id']}"))
                logger.info(user)		# 存在 info 级别日志
    return Response(data=user_serializer_list, status=status.HTTP_200_OK)
```



### 时区及语言

```python
LANGUAGE_CODE = 'zh-hans'

TIME_ZONE = 'Asia/Shanghai'
```



### REST_FRAMEWORK

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.CursorPagination',
    'PAGE_SIZE': 100
}
```



##### 使用方法

```python
import json

# rest_framework 相关使用
from rest_framework import status, serializers
from rest_framework.views import APIView
from rest_framework.response import Response

from app01.models import User
from app01.serializers import UserSerializer
from app01.tools.dataformat import DataFormat

# 序列化器类
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'

class UserView(APIView):
    def get(self, request):
        user_obj = User.objects.all()
        user_serializer = UserSerializer(instance=user_obj, many=True).data
        user_serializer_list = json.loads(json.dumps(user_serializer))
      	
        return_data = DataFormat().data_format()  
        return_data["data"] = user_serializer_list
        
        return Response(data=return_data, status=status.HTTP_200_OK) 
```



### 静态资源

```
STATIC_URL = 'static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'	  # 将静态文件收集至该独立目录下
STATICFILES_DIRS = [BASE_DIR / 'static']  # 配置静态文件加载路径
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.StaticFilesStorage'
```

如何启动django项目的时候将静态资源文件加载到静态文件目录？

1. 需要在项目启动脚本中执行以下命令：

```bash
python manage.py collectstatic --no-input	# 直接使用django框架自带的web服务启动

uwsgi --ini uwsgi.ini --static-map /static=/app/staticfiles	# 使用uwsgi启动django项目
```

