# Python Flask学习



## 简单上手

python 的flask是个非常小巧简便的web框架，程序员惯例，我们先来个hello world的吧，首先创建一个文件夹webapp。
接着进入到webapp，我们先安装一下Flask

```
pip install flask
```

然后在该文件夹下新建python文件app.py，输入：
```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def index():
    return '<h1>Hello World!</h1>'
if __name__ == '__main__':
    app.run(debug=True) # 如果你启用了调试支持，服务器会在代码修改后自动重新载入，并在发生错误时提供一个相当有用的调试器。
```
好了，我们跑起来
```
$: python app.py
```
接着我们便可以看到输出：

```
Running on http://127.0.0.1:5000/
```

现在访问`http://127.0.0.1:5000/` 你就会看见hello world的网页了。

就是这么简单！



## Flask 学习

安装 Flask 最便捷的方式是使用虚拟环境。虚拟环境是 Python 解释器的一个私有副本,在
这个环境中你可以安装私有包,而且不会影响系统中安装的全局 Python 解释器。



虚拟环境使用第三方实用工具 virtualenv 创建。输入以下命令可以检查系统是否安装了
virtualenv :

```
$ virtualenv --version
```

如果结果显示错误,你就需要安装这个工具。



linux下安装：

````
sudo apt-get install python-virtualenv
````

或者是安装

`````
pip install virtualenvwrapper-win
`````


虚拟环境的使用就不多说了，参考：http://blog.csdn.net/xshalk/article/details/47301339
http://blog.csdn.net/iaau0908/article/details/54021518

ubuntu16.04下虚拟环境的安装：http://www.jianshu.com/p/44ab75fbaef2



然后来到你的项目目录下
    virtualenv venv
这样即可创建一个虚拟环境的目录，接着在自己项目目录下执行下面代码，激活虚拟环境

    venv\Scripts\activate.bat



当然，如果你没有激活虚拟环境，那么我们可以直接输入我想要调用的解释器的路径，如：

    $ flask/bin/pip install flask


### 1. 一个项目的大概结构

我们来看一个helloworld的简单的项目结构，虽然你一个文件也可以，但是我们还是有框架结构的好一些，方便以后开发嘛。

    project
        - app
            - static
            - templates
        - tmp
        - run.py

我们的应用程序包是放置于 app 文件夹中。子文件夹 static 是我们存放静态文件像图片，JS文件以及样式文件。子文件夹 templates 显然是存放模板文件。


一个复杂的项目结构：
    Foo/
    |-- bin/
    |   |-- foo
    |
    |-- foo/
    |   |-- tests/
    |   |   |-- __init__.py
    |   |   |-- test_main.py
    |   |
    |   |-- __init__.py
    |   |-- main.py
    |
    |-- docs/
    |   |-- conf.py
    |   |-- abc.rst
    |
    |-- setup.py
    |-- requirements.txt
    |-- README

简要解释一下:

bin/: 存放项目的一些可执行文件，当然你可以起名script/之类的也行。
foo/: 存放项目的所有源代码。(1) 源代码中的所有模块、包都应该放在此目录。不要置于顶层目录。(2) 其子目录tests/存放单元测试代码； (3) 程序的入口最好命名为main.py。
docs/: 存放一些文档。
setup.py: 安装、部署、打包的脚本。
requirements.txt: 存放软件依赖的外部Python包列表。
README: 项目说明文件。

Flask web里面的项目结构

    |-app/
        |-templates/  模板文件
        |-static/  静态文件（css、js等）
        |-main/
            |-__init__.py  创建蓝本
            |-errors.py  错误处理
            |-forms.py  表单类
            |-views.py  路由
        |-__init__.py  扩展和配置初始化、程序工厂函数、注册蓝本               
        |-email.py  邮件支持
        |-models.py  数据库模型
    |-migrations/  数据库迁移脚本
    |-tests/  测试
        |-__init__.py
        |-test*.py
    |-venv/  虚拟环境
    |-requirements.txt  需求文件
    |-config.py  程序配置
    |-manage.py  启动脚本



### 2. 路由

处理 URL 和函数之间关系的程序称为路由。
route() 装饰器把一个函数绑定到对应的 URL 上。
如：
```
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello World'
```
把 index() 函数注册为程序根地址的处理程序。像 index() 这样的函数称为视图函数（ view function）。



#### 2.1 动态路由

Facebook 资 料 页 面 的 地 址 是 http://www.facebook.com/< your-name>， 用 户 名（ your-name）是地址的一部分。

```
@app.route('/user/<name>')
def user(name):
return '<h1>Hello, %s!</h1>' % name
```
尖括号中的内容就是动态部分，任何能匹配静态部分的 URL都会映射到这个路由上。
路由中的动态部分默认使用字符串，不过也可使用类型定义。例如，路由 /user/< int:id>只会匹配动态片段 id 为整数的 URL。

#### 2.2 路由注册



### 3. 模板



就是html页面，页面和逻辑分离；
和vue.js一样的有模板代码，templelate文件夹下放的都是模板文件；
模板里面可以使用动态语句，在html中使用

    {{这里面写动态语句}}

## Form

## 数据库