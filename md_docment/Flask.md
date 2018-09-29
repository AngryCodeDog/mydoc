

# Flask

[TOC]





## 一、Flask基本操作

### 创建

所有 Flask 程序都必须创建一个程序实例。程序实例是 Flask 类的对象,如下所示创建：

```python
from flask import Flask
app = Flask(name)
```

Flask 类的构造函数只有一个必须指定的参数,即程序主模块或包的名字。一般我们都是使用`__name__`



### 路由

处理 URL 和函数之间关系的程序称为路由。
flask框架里面叫Blueprint，定义如下：
> 蓝本可以自定义路由，但蓝本中定义的路由出于休眠状态，知道蓝本注册到程序上时，路由才真正成为程序的一部分。

客户端发请求给 Web 服务器,Web 服务器再把请求发送给 Flask程序实例。Flask实例需要知道对每个 URL 请求需要运行哪些代码,所以内部保存了一个 URL 到Python 函数的映射关系。处理 URL 和函数之间关系的程序称为路由。


如route() 装饰器把一个函数绑定到对应的 URL 上。

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello World'
```
把 index() 函数注册为程序根地址的处理程序。像 index() 这样的函数称为视图函数（ view function）。

### 动态路由
Facebook 资 料 页 面 的 地 址 是 http://www.facebook.com/< your-name>， 用 户 名（ your-name）是地址的一部分。

```python
@app.route('/user/<name>')
def user(name):
return '<h1>Hello, %s!</h1>' % name
```
尖括号中的内容就是动态部分，任何能匹配静态部分的 URL都会映射到这个路由上。
路由中的动态部分默认使用字符串，不过也可使用类型定义。例如，路由 /user/< int:id>只会匹配动态片段 id 为整数的 URL。

### 视图函数

```python
@app.route('/')
def index():
    return '<h1>Hello World!</h1>'
```
index()即为视图函数，你请求根目录时候，flask把请求交给index()函数处理

还可以动态路由
```python
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, %s!</h1>' % name
```
### 上下文

Flask内置一些全局的上下文对象，我们可以通过这些获取很多请求对象的参数

| 变量名      | 上下文     | 说明                                                   |
| ----------- | ---------- | :----------------------------------------------------- |
| current_app | 程序上下文 | 当前激活程序的程序实例                                 |
| g           | 程序上下文 | 处理请求时用作临时存储的对象。每次请求都会重设这个变量 |
| request     | 程序上下文 | 请求对象,封装了客户端发出的 HTTP 请求中的内容          |
| session     | 程序上下文 | 用户会话,用于存储请求之间需要“记住”的值的词典          |

例如：

```python
from flask import request

@app.route('/')
def index():
	user_agent = request.headers.get('User-Agent')
	return '<p>Your browser is % s</p>' % user_agent
```

我们通过request获取了请求头的信息。关于这些全局的上下文对象，我们有机会再细说吧。先简单了解下这些。



### 请求钩子

有时在处理请求之前或之后执行代码会很有用。例如,在请求开始时,我们可能需要创建数据库连接或者认证发起请求的用户。

请求钩子使用修饰器实现。Flask 支持以下 4 种钩子

- before_first_request :注册一个函数,在处理第一个请求之前运行
- before_request :注册一个函数,在每次请求之前运行
- after_request :注册一个函数,如果没有未处理的异常抛出,在每次请求之后运行
- teardown_request :注册一个函数,即使有未处理的异常抛出,也在每次请求之后运行



我们拿我上一篇博客的hello world程序来示范一下：

```python
from flask import Flask, render_template

app = Flask(__name__, template_folder='templates')

@app.before_request
def before_request():
    print 'before_request'

@app.route('/')
def index():
    print 'request index'
    return render_template('hello.html')

@app.after_request
def after_request(response):
    print 'after_request'
    return response

if __name__ == '__main__':
    app.run(debug=True)
```
然后我们请求`http://localhost:5000`可以看到后台输出如下内容：

```
before_request
request index
after_request
127.0.0.1 - - [22/Aug/2018 10:41:35] "GET / HTTP/1.1" 200 -
```

注意after_request函数里面必须有response参数，并且返回，不然Flask就没有返回请求给客户端了。

我们可以在before_request里面做一些请求之前的数据加载，例如,  可以从数据库中加载已登录用户,并将其保存到 g.user 中。随后调用视图函数时,视图函数再使用 g.user 获取用户。

after_request里面我可以做一些关闭数据库连接，清理数据等的操作。

### 响应

Flask 调用视图函数后,会将其返回值作为响应的内容。大多数情况下,响应就是一个简单的字符串,作为 HTML 页面回送客户端，或者是直接返回一个html页面。

我们也可以自己定义返回对象，使用make_response函数：

```python
from flask import make_response

@app.route('/')
def index():
    response = make_response('<h1>hello world!</h1>')
    response.set_cookie('answer', '42')
	return response
```

这里我们创建了一个响应对象,然后设置了 cookie。



**重定向响应**

Flask 提供 redirect() 辅助函数,用于生成这种响应：

```python
from flask import redirect
@app.route('/')
def index():
	return redirect('http://www.example.com')
```

这样，客户端在请求`http://localhost:5000`时候，url就会被重定向到`http://www.example.com`这个页面。



## 二、模板

### 变量

我们之前学过动态路由，如下所示：

```python
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, %s!</h1>' % name
```

我们在请求url时候可以后面带一个名字，然后页面会显示`hello, xxx!`，一个很简单的回应，但是现在我需要返回一个html文件，并且要也是返回给客户端显示`hello，xxx!`，怎么办？

这时候就要用到Flask的模板功能了。

怎么做呢？看下面代码：

hello.html中：

```html
<h1>Hello, {{ name }}!</h1>
```

app.py中

```python
from flask import Flask, render_template

app = Flask(__name__, template_folder='templates')

@app.route('/user/<name>')
def user(name):
	return render_template('hello.html', name=name)
```

这样我们即可在`hello.html`中增加一个变量进去。

这就是Flask中模板起的作用，模板是一个包含响应文本的文件,其中包含用占位变量表示的动态部分,其具体值只在请求的上下文中才能知道。使用真实值替换变量,再返回最终得到的响应字符串,这一过程称为渲染。

为了渲染模板,Flask 使用了一个名为` Jinja2` 的强大模板引擎。

上例中的 name=name 是关键字参数左边的“name”表示参数名,就是模板中使用的占位符;右边的“name”是当前作用域中的变量,表示同名参数的值。

在模板中使用的 {{ name }} 结构表示一个变量,它是一种特殊的占位符,告诉模板引擎这个位置的值从渲染模板时使用的数据中获取。

Jinja2 能识别所有类型的变量,甚至是一些复杂的类型,例如列表、字典和对象。在模板中使用变量的一些示例如下:

```html
<p>A value from a dictionary: {{ mydict['key'] }}.</p>
<p>A value from a list: {{ mylist[3] }}.</p>
<p>A value from a list, with a variable index: {{ mylist[myintvar] }}.</p>
<p>A value from an object's method: {{ myobj.somemethod() }}.</p>
```

### 过滤器

Jinjia2还提供过滤器来修改变量，过滤器名添加在变量名之后,中间使用竖线分隔。如：

```
Hello, {{ name|capitalize }}
```

模板以首字母大写形式显示变量 name 的值。常用过滤器：

> safe 		渲染值时不转义
>
> capitalize 	把值的首字母转换成大写,其他字母转换成小写
>
> lower 		把值转换成小写形式
>
> upper 		把值转换成大写形式
>
> title 		把值中每个单词的首字母都转换成大写
>
> trim 		把值的首尾空格去掉
>
> striptags 	渲染之前把值中所有的 HTML 标签都删掉

safe要说明一下，如果一个变量的值为 `<h1>Hello</h1>` ,Jinja2 会将其渲染成 `&lt;h1&gt;Hello&lt;/h1&gt;,`浏览器能显示这个 h1 元素,但不会进行解释。这怎么理解呢，我们来看实例就好：

hello.html中：

```html
<h1>hello {{name}}</h1>
```

app.py中

```python
from flask import Flask, render_template

app = Flask(__name__, template_folder='templates')

@app.route('/home/<name>')
def home(name):
    name = '<h1>test</h1>'  # 这里对name进行重新赋值了，如果不赋值就是url中的name值
    return render_template('hello.html', name=name)

if __name__ == '__main__':
    app.run(debug=True)

```

这时候你输入`http://localhost:5000/home/1`,页面会显示：

> hello <h1>test</h1>!

你查看页面源代码发现是这样的：

```html
<h1>hello &lt;h1&gt;test&lt;/h1&gt;!</h1>
```

然后我们在hello.html中加入safe过滤器：

```
<h1>hello {{name|safe}}</h1>
```

再刷新页面可以看到页面显示正常了：

>hello
>
>test
>
>!

两个`<h1>`的标题嘛，查看源代码也是正常的：

```html
<h1>hello <h1>test</h1>!</h1>
```



完整的过滤器列表可在 Jinja2 文档(http://jinja.pocoo.org/docs/templates/#builtin-filters)中查看。

### 控制结构

控制结构就是所谓的if else , for , 宏定义，模板继承

```html
{ % if user % }
	Hello, {{ user }}!
{ % else % }
	Hello, Stranger!
{ % endif % }
```

for循环

```
<ul>
    { % for comment in comments % }
    	<li>{{ comment }}</li>
    { % endfor % }
</ul>
```

宏定义就是和定义函数差不多，使用macro定义函数：

```
{ % macro func_name(comment) % }
	<li>{{ comment }}</li>
{ % endmacro % }

<ul>
    { % for comment in comments % }
        {{ func_name(comment) }}
    { % endfor % }
</ul>
```

我们也可以定义宏到另外的html文件中，然后引入即可：

```
{ % import 'macros.html' as macros % }
<ul>
    { % for comment in comments % }
    	{{ macros.render_comment(comment) }}
    { % endfor % }
</ul>
```

### 模板继承

首先我们创建一个基础模板：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}{% endblock %}</title>
    {% block head %}{% endblock %}
</head>
<body>
    <h1>this is father text</h1>
    {% block body %}{% endblock %}
</body>
</html>
```

模板通过extends来继承，使用 `{% block %}` 标签定义了四个子模板可以重载的块，`{% block title %}`这里    title是指块的名字，子模板通过声明这个title模块就可以更改这个模块的内容。 block 标签所做的的所有事情就是告诉模板引擎: 一个子模板可能会重写父模板的这个部分。

然后我们创建一个子模板：

```html
{% extends 'base.html' %}

{% block title %}
    名字
{% endblock %}

{% block head %}
    <style>
    </style>
    <link rel="stylesheet" href="">
    <script>
    </script>
{% endblock %}

{% block body %}
    <h1>this is child content</h1>
{% endblock %}
```

修改app.py，增加接口

```python
@app.route('/home/test')
def test():
    return render_template('index.html')
```



浏览器打开页面，我们可以看到页面显示：

> this is father text
>
> this is child content

查看源代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>
    名字
</title>
    
    <style>
    </style>
    <link rel="stylesheet" href="">
    <script>
    </script>

</head>
<body>
    <h1>this is father text</h1>
    
    <h1>this is child content</h1>

</body>
</html>
```

### Flask-bootstrap的使用

bootstrap是Twitter开发的一个开源框架，它可以简单快速的创建简洁有吸引力的页面。详细了解请自行搜索了解。

这里我们使用Flask-bootstrap这个Flask扩展，使用pip安装即可：

```
(venv) $ pip install flask-bootstrap
```

然后我们在程序创建后初始化一下这个扩展：

```python
from flask import Flask, render_template
from flask_bootstrap import Bootstrap

app = Flask(__name__, template_folder='templates')
# 这里初始化bootstrap，并传入程序实例
bootstrap = Bootstrap(app)
```

再新建一个user.html文件：

```html
{% extends "bootstrap/base.html" %}
{% block title %}Flasky{% endblock %}
{% block navbar %}
    <div class="navbar navbar-inverse" role="navigation">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle"
                        data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="/">Flasky</a>
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li><a href="/">Home</a></li>
                </ul>
            </div>
        </div>
    </div>
{% endblock %}
{% block content %}
    <div class="container">
        <div class="page-header">
            <h1>Hello, {{ name }}!</h1>
        </div>
    </div>
{% endblock %}
```

在app.py中返回这个页面：

```python
@app.route('/home/<name>')
def home(name):
    return render_template('user.html', name=name)
```

打开浏览器访问，我们即可看到如下页面：

![这里写图片描述](https://img-blog.csdn.net/2018082407561957?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p6enlwcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 静态文件

Flask默认的静态文件存放目录是程序根目录中名为static的目录，我们可以把*.css，js，图片等静态资源放到这个目录下即可，结构如下：

```
├── app.py
├── LICENSE
├── requirement.txt
├── static
├── templates
└── venv
```

上图的static目录即是静态资源目录，venv是python虚拟环境，templates是模板文件目录。requirement.txt是python库的要求文件。



## 三、Flask web表单



Flask可以直接生成一个web表单返回，并坐一些表单的验证和限制。

Flask-WTF 能保护所有表单免受跨站请求伪造（Cross-Site Request Forgery， CSRF）的攻击。恶意网站把请求发送到被攻击者已登录的其他网站时就会引发 CSRF 攻击。  

为了实现 CSRF 保护， Flask-WTF 需要程序设置一个密钥。 Flask-WTF 使用这个密钥生成 加密令牌，再用令牌验证请求中表单数据的真伪。  

设置密钥的方式如下：

```python
app = Flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'
```

app.config是一个字典，可用来存储框架、扩展和程序本身的配置变量。  密钥要设置有难度的字符串。



我们来个简单的例子，安装Flask-WTF，

```
pip install flask-wtf
```

然后创建一个LoginForm.py文件并写如下代码 ：

```python
# encoding=utf-8

from flask_wtf import Form
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Length, Email

class LoginForm(Form):
    email = StringField('Email', validators=[DataRequired(), Length(1, 64),Email()])
    password = PasswordField('Password', validators=[
        DataRequired(), EqualTo('password2', message='Passwords must match.')])
    password2 = PasswordField('Confirm password', validators=[DataRequired()])
    remember_me = BooleanField('Keep me logged in')
    submit = SubmitField('Log In')
```
password和password2两个设置了密码匹配验证，服务端需要写`form.validate_on_submit()`即可返回验证结果，界面也会相应提示，如果不写`form.validate_on_submit()`那就不会验证的。

在程序入口文件app.py中写入如下内容：

```python
# encoding=utf-8

from flask import Flask, render_template
from flask_bootstrap import Bootstrap
from forms.LoginForm import LoginForm

app = Flask(__name__, template_folder='templates')
bootstrap = Bootstrap(app)
app.config['SECRET_KEY'] = 'hard to guess string'


@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    # 这里要做验证，否则前端不会有提示的
    if form.validate_on_submit():
        return 'login succeed'
    return render_template('auth/index.html', form=form)


if __name__ == '__main__':
    app.run(debug=True, port=5001)

```



我们用flask-bootstrap创建一个父页面，坐一些基本的架子搭建：

```html
{% extends "bootstrap/base.html" %}
{% block title %}Flasky{% endblock %}
{% block navbar %}
    <div class="navbar navbar-inverse" role="navigation">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle"
                        data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="/">Flasky</a>
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li><a href="/">Home</a></li>
                </ul>
            </div>
        </div>
    </div>
{% endblock %}
{% block content %}
    <div class="container">
        <div class="page-header">
            <h1>Hello, {{ name }}!</h1>
        </div>
    </div>
{% endblock %}

```

最后我们新建一个login.html，我们使用最简单的表单渲染方式`{{ wtf.quick_form(form) }}`生成页面：

```
{% extends "base.html" %}
{% import "bootstrap/wtf.html" as wtf %}
{% block title %}Flasky - Login{% endblock %}
{% block content %}
    <div class="container">
    <div class="page-header">
        <h1>Login</h1>
    </div>
    <div class="col-md-4">
        {{ wtf.quick_form(form) }}
    </div>
    </div>
{% endblock %}
```



## 四、数据库



这里我们来学习下python操作mysql。以下环境都是在Ubuntu 18.04当中。

flask使用SQLAlchemy来操作mysql。

### 安装

首先我们要安装mysql-python

```
pip install mysql-python
```

如果出现如下错误：

```
sh: 1: mysql_config: not found
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-install-GOmOTT/mysql-python/setup.py", line 17, in <module>
        metadata, options = get_config()
      File "setup_posix.py", line 43, in get_config
        libs = mysql_config("libs_r")
      File "setup_posix.py", line 25, in mysql_config
        raise EnvironmentError("%s not found" % (mysql_config.path,))
    EnvironmentError: mysql_config not found
```

则需要安装libmysqlclient-dev

```
sudo apt-get install libmysqlclient-dev
```



然后我们再安装SQLAlchemy：

```
pip install flask-sqlalchemy
```

这样后我们就可以用python来操作mysql 了。

### 基本操作

我们来个最简单的基本操作：

```python
# 文件名test.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
app = Flask(__name__)
app.config['SECRET_KEY'] = 'hardtoguessstring'
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://user:password@localhost:3306/testdb'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
db = SQLAlchemy(app)
class Role(db.Model):
    __tablename__ = 'roles' # 数据库表名，如果不指定，则会生成默认的，最好指定
    id = db.Column(db.Integer, nullable=False, primary_key=True, autoincrement=True)
    name = db.Column(db.String(16), nullable=False, server_default='', unique=True)
    def __repr__(self):
        return '<Role %r>' % self.name
class User(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, nullable=False, primary_key=True, autoincrement=True)
    username = db.Column(db.String(32), nullable=False, unique=True, server_default='', index=True)
    role_id = db.Column(db.Integer, nullable=False, server_default='0')
    def __repr__(self):
        return '<User %r,Role id %r>' %(self.username,self.role_id)
```

然后我们手动在数据库中创建一个testdb的数据库，再运行这个文件即可看到testdb中生成了roles，users表。

#### 一、连接数据库

```
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://user:password@host:port/dbname'
```

#### 二、创建所有表

```
python
>>> from hello import db,Role,User
>>> db.create_all()
```

#### 三、删除所有表

```
python
>>> from hello import db,Role,User
>>> db.drop_all()
```

#### 四、插入行

```
# 插入单行
python
>>> from hello import db,Role,User
>>> db.session.add(Role(name='Admin'))
>>> db.session.commit()
>>> db.session.add(Role(name='Moderator'))
>>> db.session.add(Role(name='User'))
>>> db.session.commit()

# 插入多行
python
>>> from hello import db,Role,User
>>> db.session.add_all([User(username='john',role_id=1),User(username='susan',role_id=3),User(username='david',role_id=3)])
>>> db.session.commit()
```

#### 五、更新行

```
python
>>> from hello import db,Role,User
>>> admin = Role.query.filter_by(name='Admin').first()
>>> admin.name='Administrator'
>>> db.session.commit()
```

#### 六、删除行

```
python hello.py shell
>>> from hello import db,Role,User
>>> mod = Role.query.filter_by(name='Moderator').first()
>>> db.session.delete(mod)
>>> db.session.commit()
```

#### 七、查询表

- 查询表中全部数据

  ```
  # 注意，此处的查询结果完全取决于代码示例中的
  # def __repr__(self)
  # 这段函数，请各位同学注意
  python
  >>> from hello import db,Role,User
  >>> Role.query.all()
  [<Role u'Administrator'>, <Role u'User'>]
  >>> User.query.all()
  [<User u'john',Role id 1L>, <User u'susan',Role id 3L>, <User u'david',Role id 3L>]
  ```

- 按照一个条件过滤数据记录(where)

  ```
  python
  >>> from hello import db,Role,User
  >>> Role.query.filter_by(name='Administrator').first()
  <Role u'Administrator'>
  >>> User.query.filter_by(role_id=3).all()
  [<User u'susan',Role id 3L>, <User u'david',Role id 3L>]
  >>> User.query.filter_by(role_id=3).first()
  <User u'susan',Role id 3L>
  ```

- 按照两个条件过滤数据记录(where and)

  ```
  python
  >>> from hello import db,Role,User
  >>> User.query.filter_by(role_id=3,username='susan').first()
  <User u'susan',Role id 3L>
  >>> User.query.filter_by(role_id=3,username='susan').all()
  [<User u'susan',Role id 3L>]
  ```

- 聚合(count)

  ```
  python hello.py shell
  >>> from hello import db,Role,User
  >>> User.query.filter_by(role_id=3,username='susan').count()
  1L
  >>> User.query.filter_by(role_id=3).count()
  2L
  >>> User.query.count()
  3L
  ```

- 求和(sum)

  ```
  python hello.py shell
  >>> from hello import db,Role,User
  >>> from sqlalchemy.sql import func
  >>> User.query.with_entities(func.sum(User.id)).all()
  [(Decimal('6'),)]
  >>> User.query.with_entities(func.sum(User.role_id)).all()
  [(Decimal('7'),)]
  ```

- 平均数(avg)

  ```
  python
  >>> from hello import db,Role,User
  >>> from sqlalchemy.sql import func
  >>> User.query.with_entities(func.avg(User.role_id)).all()
  [(Decimal('2.3333'),)]
  >>> User.query.with_entities(func.avg(User.id)).all()
  [(Decimal('2.0000'),)]
  ```

- 排序(order by)

  ```
  python
  >>> from hello import db,Role,User
  # 升序(asc)
  >>> User.query.order_by(User.role_id).all()
  [<User u'john',Role id 1L>, <User u'susan',Role id 3L>, <User u'david',Role id 3L>]
  # 降序(desc)
  >>> User.query.order_by(User.role_id.desc()).all()
  [<User u'susan',Role id 3L>, <User u'david',Role id 3L>, <User u'john',Role id 1L>]
  ```

- 分组(group by)

  ```
  python hello.py shell
  >>> from hello import db,Role,User
  >>> User.query.group_by(User.role_id).all()
  [<User u'john',Role id 1L>, <User u'susan',Role id 3L>]
  ```

- 限制(limit)

  ```
  python
  >>> from hello import db,Role,User
  >>> User.query.all()
  [<User u'john',Role id 1L>, <User u'susan',Role id 3L>, <User u'david',Role id 3L>]
  # limit 1
  >>> User.query.limit(1).all()
  [<User u'john',Role id 1L>]
  # limit 2,1
  >>> User.query.limit(1).offset(2).all()
  [<User u'david',Role id 3L>]
  >>> User.query.filter_by(role_id=3).all()
  [<User u'susan',Role id 3L>, <User u'david',Role id 3L>]
  # limit 1
  >>> User.query.filter_by(role_id=3).limit(1).all()
  [<User u'susan',Role id 3L>]
  # limit 1,1
  >>> User.query.filter_by(role_id=3).limit(1).offset(1).all()
  [<User u'david',Role id 3L>]
  ```



### 集成到项目中

实际情况中我们不可能在一个文件中写上n多个数据表类，肯定都是分开的，这样就有了Role.py、User.py这样的类出现，然而你发现创建他们需要db这个对象，而上面的代码中SQLAlchemy创建时候需要传入的初始化的app，这就矛盾了。

事实是SQLAlchemy创建时候可以不传入app，初始化时候再传入app进行赋值初始化即可。

所以正常步骤如下：

- 1、首先在`foundation.py`中初始化一个`db = SQLAlchemy()`这样是为了整个项目中只有这一个数据库对象。

- 2、在数据库表对象中引入这个db，集成`db.Model`，生成数据表对象。

- 3、在初始化项目时候，引入这个db，然后初始化app，并创建数据库表。

  ```
  db.app = app
  db.init_app(app)
  db.create_all()
  ```

数据库的创建操作只需要执行一次即可，所以这里我们再使用另外的一个库来操作数据库的初始化，这里使用Flask-Script来进行一些简单的操作。

Flask-Script 是一个 Flask 扩展,为 Flask 程序添加了一个命令行解析器。Flask-Script 自带了一组常用选项,而且还支持自定义命令。

我们经常用Flask-Script来执行一些在flask之外的代码，比如开发时候，我需要创建一个测试的数据库，或者清空数据库，或者我可以根据实际情况来启动开发版还是正式版。

我们来看一个简单的例子：

```
from flask_script import Manager
from flask import Flask
app = Flask(__name__)

manager = Manager(app)

@manager.command
def createall():
    db.create_all()
    db.session.commit()
```

上面这个例子中，creatall这个函数被加了`@manager.command`语句，这样我即可以在python中直接运行该方法：

```
python app.py creatall
```

执行如上语句后，数据库中的表即可被创建，这样就不用每次我们在app初始化后去都去创建数据库表了。



## 五、redis

### 安装

ubuntu下安装

```
sudo apt-get install redis
```

### 基本操作

连接方式：

- 严格连接模式：r=redis.StrictRedis(host="",port=)
- 更Python化的连接模式：r=redis.Redis(host="",port=)
- StrictRedis用于实现大部分官方的命令，并使用官方的语法和命令
- Redis与StrictRedis的区别是：Redis是StrictRedis的子类，用于向前兼容旧版本的redis-py，并且这个连接方式是更加"python化"的
- 连接池  
  为了节省资源，减少多次连接损耗，连接池的作用相当于总揽多个客户端与服务端的连接，当新客户端需要连接时，只需要到连接池获取一个连接即可，实际上只是一个连接共享给多个客户端。

如下我们使用连接池方式连接redis：

```python
import redis
pool = redis.ConnectionPool(host='localhost', port=6379, db=0, decode_responses=True)
r = redis.Redis(connection_pool=pool)
r.set('a','3')
print(r.get('a'))
```

```
3
```

**默认情况下，设置的值或取得的值都为bytes类型,如果想改为str类型,需要在连接时添加上decode_responses=True**

**增加**

在Redis中设置值，默认不存在则创建，存在则修改  

r.set('name', 'zhangsan')

```
参数：
     set(name, value, ex=None, px=None, nx=False, xx=False)  
     ex，过期时间（秒）  
     px，过期时间（毫秒）  
     nx，如果设置为True，则只有name不存在时，当前set操作才执行,同setnx(name, value)  
     xx，如果设置为True，则只有name存在时，当前set操作才执行 
```

```python
# 批量设置
r.mset(a1='1',a2='2')
#r.mset({'a1':'1','a2':'2'})

# 批量获取  r.mget()
print(r.mget('a1','a2'))
```

```
['1', '2']
```

**查找获取**

```python
r.get('key_name')

#根据字节获取子序列
r.set("name","zhangsan")
print(r.getrange("name",0,3))
```

```
zhan
```

**修改**

```python
#修改字符串内容，从指定字符串索引开始向后替换，如果新值太长时，则向后添加
r.set("name","zhangsan")
r.setrange("name",1,"z")
print(r.get("name")) 
r.setrange("name",6,"zzzzzzz")
print(r.get("name"))
```

```
zzangsan
zzangszzzzzzz
```

**删除**

```
delete(*names)
根据删除redis中的任意数据类型（string、hash、list、set、有序set）
```

```python
r.delete("gender")  # 删除key为gender的键值对
```

```
1
```

**获取长度**

```python
#返回name对应值的字节长度（一个汉字3个字节）
r.set("name","zhangsan")
print(r.strlen("name"))
```

```
8
```

**检查名字是否存在**

```
exists(name)
检测redis的name是否存在，存在就是True，False 不存在
```

#### hash操作

redis中的Hash 在内存中类似于一个name对应一个dic来存储 

**新增/修改**

hset('dic_name','key','value')

如果存在该dic，则增加（key,value）, 不存在则创建，如果key在dic里面是存在的，就修改该key的值

```python
#name对应的hash中设置一个键值对（不存在，则创建，否则，修改）
r.hset("dic_name","a1","aa")

print(r.hget("dic_name","a1"))
```

```
aa
```

**查找获取**

```python
#在name对应的hash中批量设置键值对,mapping:字典
dic={"a1":"aa","b1":"bb"}
r.hmset("dic_name",dic)

# 获取指定dic，指定key的value
print(r.hget("dic_name","b1"))#输出:bb

# 获取dic全部的键值对
print(r.hgetall("dic_name"))

# 在name对应的hash中获取多个key的值
li=["a1","b1"]
print(r.hmget("dic_name",li))
print(r.hmget("dic_name","a1","b1"))


#hlen(name) 获取hash中键值对的个数
print(r.hlen("dic_name"))

#hkeys(name) 获取hash中所有的key的值
print(r.hkeys("dic_name"))

#hvals(name) 获取hash中所有的value的值
print(r.hvals("dic_name"))
```



```python
#检查name对应的hash是否存在当前传入的key
print(r.hexists("dic_name","a1"))#输出:True
```



**删除**

```python
#删除指定name对应的key所在的键值对
r.hdel("dic_name","a1")
print(r.hgetall('dic_name'))

r.hset('dic_name','a1','aa')
print(r.hgetall('dic_name'))
```

#### List操作

redis中的List在在内存中按照一个name对应一个List来存储

```python
# 在name对应的list中添加元素，每个新的元素都添加到列表的最左边，没有该list则新建
r.lpush("list_name",2)
r.lpush("list_name",3,4,5)#保存在列表中的顺序为5，4，3，2

r.lpushx('list_name',6)  #在name对应的list中添加元素，只有name已经存在时，值添加到列表的最左边

r.rpushx('list_name',7)  #在name对应的list中添加元素，只有name已经存在时，值添加到列表的最右边

#rpush(name,values) 个新的元素都添加到列表的最右边

print(r.lrange('list_name',0,-1)) #分片获取元素
print(r.llen("list_name"))  # 获取list的长度
```

```
['6', '5', '4', '3', '2', '7']
6
```

 **在name对应的列表的某一个值前或后插入一个新值**

```
r.linsert("list_name","BEFORE","2","SS") #在列表内找到第一个元素2，在它前面插入SS
参数：
     name: redis的name
     where: BEFORE（前）或AFTER（后）
     refvalue: 列表内的值
     value: 要插入的数据
```

```python
r.linsert('list_name','BEFORE','2','ss')
print(r.lrange('list_name',0,-1))


r.lset('list_name', 0, 'test')
print(r.lrange('list_name',0,-1))
```

```
['6', '5', '4', '3', 'ss', '2', '7']
['test', '5', '4', '3', 'ss', '2', '7']
```

**删除（指定值进行删除，返回值)**

```python
#移除列表的左侧第一个元素，返回值则是第一个元素
print(r.lpop("list_name"))
print(r.lrange('list_name',0,-1))
```

```
test
['5', '4', '3', 'ss', '2', '7']
```

**删除（指定值进行删除，删除不返回)**

```
r.lrem(name, value, num)  

在name对应的list中删除指定的值  

参数：
name，redis的name
value，要删除的值
num， num=0，删除列表中所有的指定值；
num=2,从前到后，删除2个； num=1,从前到后，删除左边第1个
num=-2,从后向前，删除2个
```

**删除索引之外的值**

```
ltrim(name, start, end)

在name对应的列表中移除没有在start-end索引之间的值
参数：
name，redis的name
start，索引的起始位置
end，索引结束位置

```

**取值（根据索引号取值）**

```
lindex(name, index)
在name对应的列表中根据索引获取列表元素

```

**移动 从一个列表取出最右边的元素，同时将其添加至另一个列表的最左边**

```
rpoplpush(src, dst)

参数：
src，要取数据的列表的name
dst，要添加数据的列表的name

```

**移动 元素从一个列表移动到另外一个列表 可以设置超时**

```
brpoplpush(src, dst, timeout=0)
从一个列表的右侧移除一个元素并将其添加到另一个列表的左侧
参数：
src，取出并要移除元素的列表对应的name
dst，要插入元素的列表对应的name
timeout，当src对应的列表中没有数据时，阻塞等待其有数据的超时时间（秒），0 表示永远阻塞

```

**一次移除多个列表**

```
blpop(keys, timeout)
将多个列表排列，按照从左到右去pop对应列表的元素
参数：
keys，redis的name的集合
timeout，超时时间，当元素所有列表的元素获取完之后，阻塞等待列表内有数据的时间（秒）, 0 表示永远阻塞
更多：
r.brpop(keys, timeout) 同blpop，将多个列表排列,按照从右像左去移除各个列表内的元素

```



```python
r.lpush("list10", 3, 4, 5)
r.lpush("list11", 3, 4, 5)

while r.llen('list11') > 0:
    r.blpop(["list10", "list11"], timeout=2)
    print(r.lrange("list10", 0, -1), r.lrange("list11", 0, -1))

```

```
['4', '3'] ['5', '4', '3']
['3'] ['5', '4', '3']
[] ['5', '4', '3']
[] ['4', '3']
[] ['3']
[] []

```

**自定义增量迭代**

由于redis类库中没有提供对列表元素的增量迭代，如果想要循环name对应的列表的所有元素，那么就需要：

- 获取name对应的所有列表
- 循环列表

但是，如果列表非常大，那么就有可能在第一步时就将程序的内容撑爆，所有有必要自定义一个增量迭代的功能：

```python
def list_iter(name):
    """
    自定义redis列表增量迭代
    :param name: redis中的name，即：迭代name对应的列表
    :return: yield 返回 列表元素
    """
    list_count = r.llen(name)
    for index in range(list_count):
        yield r.lindex(name, index)

# 使用
for item in list_iter('list2'): # 遍历这个列表
    print(item)

```

#### Set操作

Set集合就是不允许重复的列表

- 新增  
  sadd(name,values)  
  name对应的集合中添加元素  

- scard(name) 获取元素个数 类似于len

- 获取集合中所有的成员
  - smembers(name)  获取集合中所有的成员
  - sscan(name, cursor=0, match=None, count=None)   获取集合中所有的成员--元组形式  
  - sscan_iter(name, match=None, count=None) 获取集合中所有的成员--迭代器的方式

- 移动  
  smove(src, dst, value)  
  将某个成员从一个集合中移动到另外一个集合  
- 删除--随机删除并且返回被删除值  
  spop(name)  
  从集合移除一个成员，并将其返回,说明一下，集合是无序的，所有是随机删除的  

- 删除--指定值删除  
  srem(name, values)  
  在name对应的集合中删除某些值  

```python
r.sadd('set_name','aa')
r.sadd('set_name','bb')
r.sadd('set_name','cc')

print(r.smembers('set_name'))
print(r.scard('set_name'))

print(r.sscan('set_name'))

# 迭代器方式，于增量迭代分批获取元素，避免内存消耗太大
for i in r.sscan_iter('set_name'):
    print(i)

r.spop('set_name')
print('s.spop=>' , r.smembers('set_name'))

r.srem('set_name','cc')
print('s.srem=>' , r.smembers('set_name'))


r.sadd('set1',22,20,40)
r.sadd("set2", 11, 22, 33)
r.smove('set1','set2',40)

print('set1=',r.smembers('set1'))
print('set2=',r.smembers('set2'))

```

```
{'aa', 'bb', 'cc'}
3
(0, ['bb', 'cc', 'aa'])
bb
cc
aa
s.spop=> {'aa', 'bb'}
s.srem=> {'aa', 'bb'}
set1= {'20', '22'}
set2= {'33', '40', '22', '11'}

```

**差集**

```
sdiff(keys, *args)
在第一个name对应的集合中且不在其他name对应的集合的元素集合

```

**差集--差集存在一个新的集合中**

```
sdiffstore(dest, keys, *args)
获取第一个name对应的集合中且不在其他name对应的集合，再将其新加入到dest对应的集合中

```

```python
r.sadd('set1',22,20,40)
r.sadd("set2", 11, 22, 33)
print(r.smembers("set1"))   # 获取集合中所有的成员
print(r.smembers("set2"))
print(r.sdiff("set1", "set2"))   # 在集合set1但是不在集合set2中
print(r.sdiff("set2", "set1"))   # 在集合set2但是不在集合set1中

```

```
{'40', '20', '22'}
{'33', '22', '11'}
{'40', '20'}
{'33', '11'}

```



```python
r.sdiffstore("set3", "set1", "set2")    # 在集合set1但是不在集合set2中
print(r.smembers("set3"))   # 获取集合3中所有的成员

```

```
{'40', '20'}

```

**交集**

```
sinter(keys, *args)
获取多一个name对应集合的交集

```

**交集--交集存在一个新的集合中**

```
sinterstore(dest, keys, *args)
获取多一个name对应集合的并集，再将其加入到dest对应的集合中

```

```python
print(r.sinter("set1", "set2")) # 取2个集合的交集

print(r.sinterstore("set3", "set1", "set2")) # 取2个集合的交集
print(r.smembers("set3"))
```

```
{'22'}
1
{'22'}

```

**并集**

```
sunion(keys, *args)
获取多个name对应的集合的并集

```

**并集--并集存在一个新的集合**

```
sunionstore(dest,keys, *args)
获取多一个name对应的集合的并集，并将结果保存到dest对应的集合中

```



```python
print(r.sunion("set1", "set2")) # 取2个集合的并集

print(r.sunionstore("set3", "set1", "set2")) # 取2个集合的并集
print(r.smembers("set3"))
```

```
{'11', '40', '33', '20', '22'}
5
{'11', '40', '33', '20', '22'}

```

**判断是否是集合的成员 类似in**

```
sismember(name, value)
检查value是否是name对应的集合的成员，结果为True和False

```

```python
print(r.sismember("set1", 40))
```

```
True

```

