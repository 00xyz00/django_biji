## Django安装

```
pip install django
```

## 在Ubuntu中创建虚拟环境及删除虚拟环境

```
mkvirtualenv -p /usr/bin/python3/ envname
rmvirtualenv envname 
```

## Django新建项目命令（通用方式）

```
django-admin startprojece projectname
```

## 项目目录及文件说明

├── djtest11

│    ├──__ init__.py        空文件，告诉Python这是一个Python包

│    ├── settings.py        配置文件，包含数据库信息，调试标注，静态文件等

│    ├── urls.py             Django项目的URL声明

│    └── wsgi.py           部署服务器

└── manage.py

将settings.py文件中的ALLOWED_HOSTS=[ ]，改为ALLOWED_HOSTS=["*"]，表示能够允许任意ip的客户端访问。

## 开启服务命令

```
python manage.py runserver 0.0.0.0:8000
```

开启服务后可通过浏览器查看

## 创建视图函数

1、在项目目录下创建views.py并配置函数

2、在urls.py中定义对应的url

```python
from django.http import HttpResponse  

def hello(request):
    return HttpResponse('hello to django!')
```

```python
from django.contrib import admin
from django.urls import path
from .views import hello

urlpatterns = [
    path('admin/', admin.site.urls),
    path('hello/', hello)
]
```

## 新建APP

创建命令：  

```
python manange.py startapp app_name
```

## url.py中的path基本规则：

```
path('test/<xx>/<int:age>',views.test) 
```

   尖括号<> 能从url中捕获值     

## re_path

```
re_path('hello/$',views.re_test5),

re_path('hello/(?P<yy>[0-9]+)/',views.test6),
```

```python
def re_test5(request):
	return HttpResponse('这是用的re_path设置的')

def test6(request,yy):
	print(yy,type(yy))
	return HttpResponse('hello %s'%yy)
```

## include作用

一个项目有一个总的urls.py，各个app中也可以创建各自的urls.py，用include()函数在project的urls.py文件进行路由分配

## python中的 *agrs 和 **kwargs

*args可以接收普通参数，该类型为元组

**kwagrs可以接收关键字参数，该类型为字典

## 页面重定向

rediect