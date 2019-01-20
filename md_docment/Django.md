# Django

[TOC]

此django版本为1.11，如有不同请参考官方文档：https://docs.djangoproject.com/

## 安装

创建一个新目录，然后新建虚拟环境并激活，接着我们就来安装django

```
pip install django
```

使用django创建一个项目

```
django-admin startproject mysite
```

目录就变成下面这个样子了

```
├── mysite                      # 项目名称目录
│   └── mysite
│       ├── __init__.py
│       ├── settings.py         # django的全局设置文件
│       ├── urls.py             # url和视图函数在这里进行绑定
│       └── wsgi.py
│   ├── manage.py               # 这是django的管理文件，启动app
└── venv

```



## 运行



cd到mysite目录下，执行

```
python manage.py runserver
```

我们便可以在http://127.0.0.1:8000/这个地址看到django正常运行了。

如果你想要指定端口运行，则如下：

```
python manage.py runserver 8001   # 这里便指定8001为监听端口
```



下面我们来创建一个小模块，mysite是一个project，django中需要新建一个app来装载你的小功能、小模块。

```
python manage.py startapp polls
```

目录就变成下面这样了

```
├── mysite
│   ├── db.sqlite3
│   ├── manage.py
│   ├── mysite
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   ├── wsgi.py
│   └── polls
│       ├── admin.py
│       ├── apps.py
│       ├── __init__.py
│       ├── migrations/
│       ├── models.py       # 数据文件，存储数据对象，可以跟数据库表关联起来ORM操作
│       ├── tests.py        # 测试文件
│       └── views.py        # 编写视图函数的地方
└── venv
```

这样我们就可以在polls下面进行项目开发，views.py是编写视图函数的地方 。我们来写上第一个视图函数：

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

然后在polls下新建urls.py，用来定义自己的url，跟views关联起来，如：

```python
from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

再接着我们需要在mysite的urls.py中注册polls的url，以便提供访问：

```python
from django.conf.urls import url, include
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^polls', include('polls.urls'))
]
```



url有四个参数：

- regex: 正则表达式，与之匹配的 URL 会执行对应的第二个参数 view。
- view: 用于执行与正则表达式匹配的 URL 请求。
- kwargs: 视图使用的字典类型的参数。
- name: 用来反向获取 URL。

