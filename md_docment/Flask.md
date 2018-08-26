

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