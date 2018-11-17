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

## 模板路径配置

第一张方法：在pycharm项目中新建templates文件，然后再templates中新建各个app名字的文件，在settings中更改

第二张方法：在app中创建templates文件，把app名字添加注册到settings中的INSTALLED_APPS

然后就可以在views.py中引用templates中的HTML文件

## 模板变量的引入

通过render进行渲染，可以引入字符串，列表，元组，字典，函数，类对象

## 过滤器

语法 {{ str|lower }}      小写输出

​         注意：使用参数时， | 两边不能有空格

## 静态文件引用

在项目目录下创建static文件，在settings.py中进行配置，

```python
STATIC_URL = '/static/'
STATICFILES_DIRS=[
	os.path.join(BASE_DIR,‘static')
]
```

## 模板便签

常用标签：标签语法  由 {% 和 %} 来定义的，例如{%tag%} {%endtag%}

 ## 模板的继承与引用

继承：

{% extends '继承页面的地址'%}

需要修改时，用{%block 名称 %} 内容 {% endblock %}包裹，就可以修改内容。

在继承基础之上修改，使用{{ block.super }}

引用：

{% include  ‘music.html’ %}

## 自定义过滤器及标签

 自定义过滤器：在当前项目文件新建文件夹common，在common文件中新建文件夹templatetags，并在settings中进行app注册

```python
from django import template

register = template.Library()
@register.filter              #方法1
def my_lower(value):
	return value.lower()
register.filter(my_lower)       #方法2
```

使用时进行{% load 文件名 %}就可以使用过滤器



简单标签：

```python
views.py

from django.shortcuts import render

def index(request):
	return render(request,'index.html',
	context={'format_string':'%Y-%m-%d %H:%M:%S'})
```

```python
from django import template
import datetime

@register.simple_tag
def current_time1(format_string):
	return datetime.datetime.now().strfting(format_string)

@register.simple_tag(takes_context=True)   #表示可以接收上下文传参
def current_time2(context):
    format_string = context.get('format_string')
    return datetime.datetime.now().strfting(format_string)
```

使用{% current_time1 '%Y-%m-%d %H:%M:%S' %}   输出当前时间

或是{% current_time %}输出当前时间



## django模型基础



数据库的配置：

1.在settings.py中配置DATABASES

```python
DATABASES = {
		'default': {
        'ENGINE': 'django.db.backends.mysql',      # 数据库引擎
        'NAME': 'mydb',                            #数据库名称
        'USER': 'admin',                      # 链接数据库的用户名
        'PASSWORD': 'Root110qwe',             # 链接数据库的密码
        'HOST': '127.0.0.1',             # mysql服务器的域名和ip地址
        'PORT': '3306',               # mysql的一个端口号,默认是3306
    			}
		}
```

2.在虚拟环境中安装pymysql:      pip insutall pymysql

   设置连接器为pymysql     在主目录下的__ init __.py 文件添加下面两句

```python
import pymysql
pymysql.install_as_MySQLdb()
```

## 使用Django中的模型

1.在app下面的models.py中创建django的模型类

```python
models.py
from django.db import models

class User(models.Model):
	id = models.AutoField(primary_key=True)   #主键可省略，django会自动加上
	name = models.CharField(max_length=30)
	age = models.IntegerField()
	
	def __str__(self):     #在查询时可看
		return 'User<id=%s,name=%s,age=%s>' % (self.id, self.name, self.age)
```

## 将模型类映射到数据库：

1.首先执行以下命令，创建映射文件

```python
python mange.py makemigrations
```

2.将映射文件中的映射数据提交到数据库中

```
python mange.py migrate
```

命令后面可添加app名称，表示指定对某个app进行映射，不写表示所有app都执行映射。

## 数据的增删改查

1.在视图函数中导入User模型类，添加数据

```python
from .models import User
def add_user(request):
	#方法1
	xiaoming = User(name='xiaoming',age=18)
	xiaoming.save()
	#方法2
	xiaohong = User()
	xiaohong.name = 'xiaohong'
	xiaohong.age = 18
	xiaohong.save()
	#方法3
	User.objects.create(name='xiaoming',age=18)
	#方法4
	User.objects.get_or_create(name='xiaoming',age=21)
	return HttpResponse('add success!!!')
```

2.在视图函数中导入User模型类，实现简单的查找

```python
def search_user(request):
	#查询所以记录对象
	rs = User.object.all()
	#查询一个
	rs = User.object.get(id=1)
	#查询满足条件的对象
	rs = User.object.filter(name='xiaoming')
	print(rs)
	return HttpResponse('query success!!!')
```

all()和filter()方法返回的是QuerySet对象

get()方式返回的单个对象，如果符合条件的有多个，则get报错



3.在视图函数中导入User模型，然后使用下面的方法更新数据

```python
def update_user(request):
	#先查找到再属性赋值修改
	rs = User.objects.get(name='xiaoming')
	rs.name = 'xiaohu'
	rs.save()
	#使用update方法直接修改
	User.objects.filter(name='xiaoming').update(name='XX')
	User.objects.all().update(age=22)
	return HttpResponse('Update success!!!')
```

4.在视图函数中导入User模型,然后使用下面方法删除数据

```python
def delete_user(request):
	#User.objects.get(id=1).delete()
	#User.objects.filter(age=18).delete()
	#User.objects.all().delete()
	return HttpResponse('delete success!!!')
```

