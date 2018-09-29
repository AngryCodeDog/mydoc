# Django

初始目录

```
├── db.sqlite3        # django默认会使用sqlite数据库
├── djangott          # 项目名称目录
│   ├── __init__.py   
│   ├── settings.py   # django的全局设置文件
│   ├── urls.py       # url和视图函数在这里进行绑定
│   ├── wsgi.py
├── manage.py        # 这是django的管理文件，启动app
├── templates        # 这是存放html文件的目录，要在settings中进行配置
└── venv             # 这是virtualenv生成的虚拟环境目录
```

我们可以通过

```
django-admin startapp messages
```

在django下创建一个新模块，django里面称为app

比如我们生成一个messages的app，目录就变成下面这样了

```
├── db.sqlite3
├── djangott
├── manage.py
├── messages           # 这里就是新生成的app目录
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations/
│   ├── models.py     # 数据文件，存储数据对象，可以跟数据库表关联起来ORM操作
│   ├── tests.py      # 测试文件
│   ├── views.py      # 编写视图函数的地方
├── templates
└── venv
```

这样我们就可以在messages下面进行项目开发，views.py是编写视图函数的地方







